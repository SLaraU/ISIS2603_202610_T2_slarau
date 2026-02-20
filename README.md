# Back del proyecto TallerPruebas
Escenarios Regla 1:
1. Éxito: Se mueve un valor igual o menor al saldo , diferente y mayor a 0, de una cuenta existente a un bolsillo existente.
2. Fallo: El dinero que se quiere mover es mayor al saldo de la cuenta.
3. Fallo: La cuenta no existe.
4. Fallo: El bolsillo no existe.
5. Fallo: El dinero que se quiere mover es menor o igual a 0.

Escenarios Regla 2:
1. Éxito: Una cuenta existente le transfiere a otra cuenta distinta y existente un monto menor, y distinto de cero, al saldo de la cuenta origen de forma existosa, con ambos saldos actualizados.
2. Fallo: La cuenta origen no existe.
3. Fallo: la cuenta destino no existe.
4. Fallo: La cuenta destino es igual a la cuenta origen.
5. Fallo: la cuenta origen no cuenta con el saldo suficiente.
6. Fallo: La cuenta origen transfiere un valor igual o menor a 0.

Regla 1:
    Escenario: Éxito: Se mueve un valor igual o menor al saldo , diferente y mayor a 0, de una cuenta existente a un bolsillo existente.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000, existe bolsillo "Ahorro".
    Acción (Input): Mover $500 de cuenta #123 a "Ahorro".
    Resultado Esperado (Output/BD): Se actualiza en BD los saldos, restando $500 de la cuenta #123 y sumando $500 a "Ahorro".

    Escenario: El dinero que se quiere mover es mayor al saldo de la cuenta.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000, existe bolsillo "Ahorro".
    Acción (Input): Mover $1500 de cuenta #123 a "Ahorro".
    Resultado Esperado (Output/BD): Lanza Excepción: BusinessLogicException("Saldo insuficiente"). No guarda nada nuevo.

    Escenario: La cuenta no existe.
    Estado Inicial (BD): No existe cuenta #123.
    Acción (Input): Mover $1500 de cuenta #123 a "Ahorro".
    Resultado Esperado (Output/BD): Lanza Excepción EntityNotFoundException("No existe la cuenta especificada"). No guarda nada nuevo.

    Escenario: El bolsillo no existe.
    Estado Inicial (BD): Existe la cuenta #123 con saldo $1000, pero no el bolsillo "Ahorro".
    Acción (Input): Mover $500 de cuenta #123 a "Ahorro".
    Resultado Esperado (Output/BD): Lanza Excepción EntityNotFoundException("No existe el bolsillo especificada"). No guarda nada nuevo.

    Escenario: La cuenta origen transfiere un valor igual o menor a 0.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000, existe bolsillo "Ahorro".
    Acción (Input): Mover -$500 de cuenta #123 a "Ahorro".
    Resultado Esperado (Output/BD): Lanza Excepción BusinessLogicException("Monto a transferir negativo"). No guarda nada nuevo.


Regla 2:
    Escenario: Una cuenta existente le transfiere a otra cuenta distinta y existente un monto menor, y distinto de cero, al saldo de la cuenta origen de forma existosa, con ambos saldos actualizados.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000, existe cuenta #456 con saldo $500.
    Acción (Input): Mover $500 de cuenta #123 a cuenta #456.
    Resultado Esperado (Output/BD): Se actualiza en BD los saldos, restando $500 de la cuenta #123 y sumando $500 a la cuenta #456.

    Escenario: La cuenta origen no existe.
    Estado Inicial (BD): Existe cuenta #456 con saldo $500
    Acción (Input): Mover $500 de cuenta #123 a cuenta #456.
    Resultado Esperado (Output/BD): Lanza Excepción EntityNotFoundException("No existe la cuenta origen especificada"). No guarda nada nuevo.

    Escenario: La cuenta destino no existe.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000
    Acción (Input): Mover $500 de cuenta #123 a cuenta #456.
    Resultado Esperado (Output/BD): Lanza Excepción EntityNotFoundException("No existe la cuenta destino especificada"). No guarda nada nuevo.

    Escenario: La cuenta destino es igual a la cuenta origen.
    Estado Inicial (BD): Existe la cuenta #123 con saldo $1000.
    Acción (Input): Mover $500 de cuenta #123 a cuenta #123.
    Resultado Esperado (Output/BD): Lanza Excepción BusinessLogicException("La cuenta origen es igual a la cuenta destino"). No guarda nada nuevo.

    Escenario: La cuenta origen transfiere un valor igual o menor a 0.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000, existe cuenta #456 con saldo $500.
    Acción (Input): Mover -$500 de cuenta #123 a cuenta #456.
    Resultado Esperado (Output/BD): Lanza Excepción BusinessLogicException("Monto a transferir negativo"). No guarda nada nuevo.


1. Sobre @Transactional: En la Regla 1, usted debe restar dinero de una cuenta y sumarlo a un bolsillo. Son dos operaciones de guardado distintas (accountRepository.save y pocketRepository.save). 
    a. Pregunta: ¿Qué pasaría si el servidor se apaga justo después de restar el dinero de la cuenta, pero antes de sumarlo al bolsillo?
    Si el servidor se apaga en la mitad del proceso, existe el riesgo de que la operación quede a medias y que haya una inconsistemcia de datos, donde el dinero se resta de una cuenta pero no se suma al producto destino. Por eso, es necesario un elemento que permita asegurar la completitud del proceso
    b. Investigación: ¿Cómo ayuda la anotación @Transactional a evitar que ese dinero se "pierda" en el limbo? (Explique el concepto de atomicida).
    Como la lógica está marcada con @Transactional, esta se asegura de que la operación se realice como una unidad, de tal forma que se hace un commit exitoso al final de todo el proceso con ambos datos actualizados, o (como en el caso que falla antes de temrinar) la base de datos hace un rollback y se asegura de que no haya una inconsistencia de datos.

2. Sobre la Inyección de Dependencias:
    a. En su código usó @Autowired para obtener el AccountRepository dentro de PocketService.
    b. Pregunta: ¿Por qué es mejor usar @Autowired en lugar de hacer AccountRepository repo = new AccountRepository() manualmente dentro del servicio? ¿Qué ventaja nos da esto a la hora de hacer pruebas?
    La ventaja que da @Autowired para los Services es la mejora de acoplamiento, es decir, e código es menos dependiente de las dependencias y permite un nivel de asbtracción donde no se necesita declarar e instanciar el repositorio y para las pruebas, esto permite simplificar la dependencia que hay con la base de datos.

3. Lógica vs. Controlador:
    a. Pregunta: Si quisiéramos validar que el monto de una transacción no sea negativo, ¿Por qué se recomienda poner ese if en la clase Service y no directamente en el Controller (API) o en la pantalla del celular (Frontend)?
    Si se quiere validar esta condición, es óptimo revisarlo como una regla de negocio en la clase Service, donde se verifica que se cumplan las restricciones impuestas por la lógica del proyecto. Si esto se intenta verificar en ambientes distintos, las pruebas sobre el funcionamiento de la aplicación puede verse afectado, con el manejo de errores que debería implementarse y sería complicado o imposible en ambientes distintos como el Front y el Controller. Así, se mantiene un orden donde cada parte del proyecto tiene una función, el back con la lógica, el front con la muestra de información y el controller como un gestor de peticiones, que no debería mezclar sus roles asignando responsabilidades y tareas donde no deben.

4. Pruebas Unitarias:
    a. Investigación: Investigue qué es Test Driven Development (TDD) y explique sus principios fundamentales (Red, Green, Refactor). ¿Qué ventajas tiene utilizar esta metodología de desarrollo?
    El TDD es un enfoque de programación donde el orden de desarrollo se invierte, y se diseñan las pruebas antes del código. Así, la primera parte del proceso es RED, donde la prueba hecha falla porque la lógica está implementada, luego en Green se programa la lógica para que pase las pruebas mínimas realizadas inicialmente y, por último, en Refactor se pule el código implementado, optimizando y limpiando código. Así, con este enfoque se puede conseguir un mejor código, se disminuyen errores y se puede realizar una documentación mejor de los programas diseñados.
    b. Proponga al menos tres (3) casos de prueba adicionales, más allá de los ya descritos en la Parte A
    Añadidos a los anteriores, algunos casos e prueba adicionales pueden ser los frontera, como una transferencia de 0. Otra es las transferencias en sentido opuesto y sus errores, como los casos de saldo insuficiente o bolsillo inexistente, siendo este ahora el producto origen y verificar la reversibilidad de las acciones.


5. Tipos de Assert: En JUnit, la validación final se realiza mediante "Asserts". Investigue y explique brevemente cuáles son y para qué sirven los tipos de aserciones, dando un ejemplo de en qué caso usaría cada uno.
    En JUnit, los asserts sirven para verificar si un proceso obtiene un resultado esperado o un comportamiento predicho de acuerdo con las reglas establecidas. Entre estas estan el assertEquals, assertNotEquals, assertTrue, assertFalse, assertNull, etc, y dependen del tipo de comportamiento y test que se quiera comprobar. Por ejemplo, si quiero verificar que algo es un valor en especifico, puedo utilizar assertTrue para comprobar la comparación entre los valores esperados y los dados por el programa. De esta forma, si se devuelve el assert que se espera, el test hecho se considera completo y aprobado. Podríamos agruparlas de la siguiente forma:

    | Assert               | ¿Para qué sirve?     | Cuándo usarlo                  |
    | -------------------- | -------------------- | ------------------------------ |
    | `assertEquals`       | Comparar valores     | Verificar resultados exactos   |
    | `assertNotEquals`    | Verificar diferencia | Confirmar que algo cambió      |
    | `assertTrue/False`   | Evaluar condición    | Métodos booleanos              |
    | `assertNull/NotNull` | Verificar existencia | Validar objetos                |
    | `assertThrows`       | Validar excepción    | Reglas que deben fallar        |
    | `assertAll`          | Agrupar validaciones | Verificar múltiples resultados |


## Enlaces de interés

* [BookstoreBack](https://github.com/Uniandes-isis2603/bookstore-back) -> Repositorio de referencia para el Back

* [Jenkins](http://157.253.238.75:8080/jenkins-isis2603/) -> Autentíquese con sus credencias de GitHub
* [SonarQube](http://157.253.238.75:8080/sonar-isis2603/) -> No requiere autenticación
