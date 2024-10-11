

# Registros

| Commando | Descripción | Ejemplo |
| -------- | ----------- | ------- |
|          |             |         |
|          |             |         |


# Flags

| Commando | Descripción | Ejemplo |
| -------- | ----------- | ------- |
|          |             |         |
|          |             |         |

# Instrucciones de assembler

| Commando                                      | Descripción                                                                                                                                                                                                                                                   | Ejemplo                                                                                                                                                                  |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| NOP                                           | No operación.                                                                                                                                                                                                                                                 | `NOP`                                                                                                                                                                    |
| PUSH                                          | Agrega un valor al stack.                                                                                                                                                                                                                                     | `PUSH 401008` Agrega el número.<br><br>`PUSH [401008]` Agrega el contenido de una dirección de memoria. (Debemos ir al DUMP a ver que hay allí, será lo que se agregará) |
| POP                                           | Quita la primer el primer valor del stack y lo coloca en la posición que indicamos a continuación del POP.                                                                                                                                                    | `POP EAX`                                                                                                                                                                |
| PUSHAD                                        | Guarda el contenido de los registros en la pila en un orden determinado. Así pues, pushad equivale a: push EAX, ECX, EDX, EBX, ESP, EBP, ESI, EDI.                                                                                                            | `PUSHAD`                                                                                                                                                                 |
| POPAD                                         | Toma los valores del stack y los manda a los registros como si fuera un POP a cada uno. Así popad equivale a: pop EDI, ESI, EBP, ESP, EBX, EDX, ECX, EAX.                                                                                                     | `POPAD`                                                                                                                                                                  |
| MOV                                           | Mueve el segundo operando al primero.                                                                                                                                                                                                                         | `MOV EAX, EBX`                                                                                                                                                           |
| MOVSX (Move with Sign-Extension)              | Copia el contenido del segundo operando, que puede ser un registro o una posición de memoria, en el primero (de doble longitud que el segundo), rellenándose los bits sobrantes por la izquierda con el valor del bit más significativo del segundo operando. | `MOVSX EAX,BX`                                                                                                                                                           |
| MOVZX (Move with Zero-Extend)                 | Igual a movsx, pero en este caso, los espacios sobrantes se rellenan siempre con ceros o sea no depende de si el segundo operando es positivo o no.                                                                                                           | `MOVSX EAX,BX`                                                                                                                                                           |
| LEA (Load Effective Address)                  | Similar a la instrucción mov, pero el primer operando es un registro de uso general y el segundo una dirección de memoria. Esta instrucción es útil sobre todo cuando esta dirección de memoria responde a un cálculo previo.                                 | `LEA EAX,DWORD PTR DS:[ECX+38]`                                                                                                                                          |
| XCHG (Exchange Register/Memory with Register) | Esta instrucción intercambia los contenidos de los dos operandos.                                                                                                                                                                                             | `XCHG EAX,ECX`                                                                                                                                                           |

# Instrucciones  matemáticas

| Commando                        | Descripción                                                                                                                                                                                                                                                                                                                                     | Ejemplo                                   |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| INC                             | Incrementa el operando                                                                                                                                                                                                                                                                                                                          | `INC EAX`                                 |
| DEC                             | Decrementa el operando                                                                                                                                                                                                                                                                                                                          | `DEC EAX`                                 |
| ADD                             | Add suma ambos operandos y guarda el resultado en el primero.                                                                                                                                                                                                                                                                                   | `ADD EAX,1`                               |
| ADC (ADD WITH CARRY)            | Suman ambos operandos y se le suma el valor del CARRY FLAG O FLAG C y se guarda en el primer operando.                                                                                                                                                                                                                                          | `ADC EDX, 3`                              |
| SUB                             | Resta o substracción o sea la operación contraria a ADD, lo que hace es restar el segundo operando al primero y guardar el resultado en el primer operando.                                                                                                                                                                                     | `SUB EAX,2`                               |
| SBB                             | Operación contraria a ADC, es este caso se restan ambos operandos y se le resta el valor del CARRY FLAG O FLAG C y se guarda en el primer operando.                                                                                                                                                                                             | `SBB EDX,3`                               |
| MUL                             | Multiplica sin considerar los signos de los números que va a multiplicar, y usa un solo operando el otro operando es siempre EAX aunque no se escribe y el resultado lo guarda en EDX:EAX                                                                                                                                                       | `MUL ECX`                                 |
| IMUL (multiplicación con signo) | La instrucción IMUL no solo es multiplicar con signo de la misma forma que lo hacia MUL<br>Es igual que antes ECX por EAX y el resultado se guarda en EDX:EAX salvo que se considera el signo de los operandos.<br>Además de la similitud con la instrucción anterior IMUL permite poner mas de un operando, lo que no estaba permitido en MUL. | `IMUL EBP, DWORD PTR [ESI+74], 0FF800002` |
| DIV (Unsigned Divide)           | DIV solo tiene un operando y no considera los signos y el resultado se guarda en EDX:EAX                                                                                                                                                                                                                                                        |                                           |
| IDIV (Signed Divide)            | IDIV siempre considera los signos si usa un solo operando será como DIV y guardara en EDX:EAX y en el caso de dos operandos dividirá ambos y guardara en el primero, y en el de tres operandos dividirá el segundo y el tercero y guardara en el primero.                                                                                       |                                           |
| XADD (Exchange and Add)         | Es como realizar en una sola instrucción XCHG y ADD.                                                                                                                                                                                                                                                                                            | `XADD EAX,ECX`                            |
| NEG                             | Esta instrucción tiene la finalidad de cambiar de signo el número representado o sea que si tenemos el número 32 en hexa y le aplicamos NEG el resultado será el negativo del mismo.                                                                                                                                                            | `NEG EAX`                                 |

# Instrucciones lógicas

| Commando | Descripción                                                                               | Ejemplo       |
| -------- | ----------------------------------------------------------------------------------------- | ------------- |
| AND      | El resultado es 1 si los dos bits son 1, y 0 en cualquier otro caso.                      | `AND ECX,EAX` |
| OR       | El resultado es 1 si uno o los dos operandos es 1, y 0 en cualquier otro caso.            |               |
| XOR      | El resultado es 1 si uno y sólo uno de los dos operandos es 1, y 0 en cualquier otro caso |               |
| NOT      | Simplemente invierte el valor del único operando de esta función                          |               |



# Comparacion 

| Commando | Descripción | Ejemplo |
| -------- | ----------- | ------- |
|          |             |         |
|          |             |         |


# Instrucciones 

| Commando | Descripción | Ejemplo |
| -------- | ----------- | ------- |
|          |             |         |
|          |             |         |


# Instrucciones 

| Commando | Descripción | Ejemplo |
| -------- | ----------- | ------- |
|          |             |         |
|          |             |         |


# Referencias

[Ricardo Narvaja](https://ricardonarvaja.info/)
