

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

| Commando                        | Descripción                                                                                                                                                                               | Ejemplo      |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| INC                             | Incrementa el operando                                                                                                                                                                    | `INC EAX`    |
| DEC                             | Decrementa el operando                                                                                                                                                                    | `DEC EAX`    |
| ADD                             | Add suma ambos operandos y guarda el resultado en el primero.                                                                                                                             | `ADD EAX,1`  |
| ADC (ADD WITH CARRY)            | Suman ambos operandos y se le suma el valor del CARRY FLAG O FLAG C y se guarda en el primer operando.                                                                                    | `ADC EDX, 3` |
| SUB                             | Resta o substracción o sea la operación contraria a ADD, lo que hace es restar el segundo operando al primero y guardar el resultado en el primer operando.                               | `SUB EAX,2`  |
| SBB                             | Operación contraria a ADC, es este caso se restan ambos operandos y se le resta el valor del CARRY FLAG O FLAG C y se guarda en el primer operando.                                       | `SBB EDX,3`  |
| MUL                             | Multiplica sin considerar los signos de los números que va a multiplicar, y usa un solo operando el otro operando es siempre EAX aunque no se escribe y el resultado lo guarda en EDX:EAX | `MUL ECX`    |
| IMUL (multiplicación con signo) |                                                                                                                                                                                           | `IMUL ECX`   |

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
