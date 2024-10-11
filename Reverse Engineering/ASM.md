

# Registros

|     |     |
| --- | --- |
|     |     |
|     |     |


# Flags

|     |     |
| --- | --- |
|     |     |
|     |     |

# Instrucciones de assembler

| Commando | Descripción                                                                                                                                               | Ejemplo                                                                                                                                                                  |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| NOP      | No operación.                                                                                                                                             | `NOP`                                                                                                                                                                    |
| PUSH     | Agrega un valor al stack.                                                                                                                                 | `PUSH 401008` Agrega el número.<br><br>`PUSH [401008]` Agrega el contenido de una dirección de memoria. (Debemos ir al DUMP a ver que hay allí, será lo que se agregará) |
| POP      | Quita la primer el primer valor del stack y lo coloca en la posición que indicamos a continuación del POP.                                                | `POP EAX`                                                                                                                                                                |
| PUSHAD   | Guarda el contenido de los registros en la pila en un orden determinado. Así pues, pushad equivale a: push EAX, ECX, EDX, EBX, ESP, EBP, ESI, EDI.        | `PUSHAD`                                                                                                                                                                 |
| POPAD    | Toma los valores del stack y los manda a los registros como si fuera un POP a cada uno. Así popad equivale a: pop EDI, ESI, EBP, ESP, EBX, EDX, ECX, EAX. | `POPAD`                                                                                                                                                                  |
| MOV      | Mueve el segundo operando al primero.                                                                                                                     | `MOV EAX, EBX`                                                                                                                                                           |
|          |                                                                                                                                                           | ``                                                                                                                                                                       |
|          |                                                                                                                                                           | ``                                                                                                                                                                       |


# Instrucciones 

|     |     |
| --- | --- |
|     |     |
|     |     |

# Instrucciones 

|     |     |
| --- | --- |
|     |     |
|     |     |


# Instrucciones 

|     |     |
| --- | --- |
|     |     |
|     |     |


# Instrucciones 

|     |     |
| --- | --- |
|     |     |
|     |     |


# Instrucciones 

|     |     |
| --- | --- |
|     |     |
|     |     |
