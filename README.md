# Back del proyecto TallerPruebas
Escenarios Regla 1:
1. Éxito: Se mueve un valor igual o menor al saldo , diferente y mayor a 0, de una cuenta existente a un bolsillo existente.
2. Fallo: El dinero que se quiere mover es mayor al saldo de la cuenta.
3. Fallo: La cuenta no existe.
4. Fallo: El bolsillo no existe.
5. Fallo: El dinero que se quiere mover es menor o igual a 0.

Escenarios Regla 2:
1. Éxito: Una cuenta existente le transfiere a otra cuenta distinta y existente un monto menor, y distinta de cero, al saldo de la cuenta origen de forma existosa, con ambos saldos actualizados.
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

    Escenario: El dinero que se quiere mover es mayor al saldo de la cuenta.
    Estado Inicial (BD): Existe cuenta #123 con saldo $1000, existe bolsillo "Ahorro".
    Acción (Input): Mover $1500 de cuenta #123 a "Ahorro".
    Resultado Esperado (Output/BD): Lanza Excepción: BusinessLogicException("Saldo insuficiente"). No guarda nada nuevo.


## Enlaces de interés

* [BookstoreBack](https://github.com/Uniandes-isis2603/bookstore-back) -> Repositorio de referencia para el Back

* [Jenkins](http://157.253.238.75:8080/jenkins-isis2603/) -> Autentíquese con sus credencias de GitHub
* [SonarQube](http://157.253.238.75:8080/sonar-isis2603/) -> No requiere autenticación
