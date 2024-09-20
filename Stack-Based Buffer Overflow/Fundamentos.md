Antes de la explotación del Buffer Overflow tenemos que entender que ocurre cuando ejecutamos este tipo de ataque, para ello vamos a empezar desde lo más básico.

# Introducción

La CPU (Unidad Central de Procesamiento), es la parte de nuestro ordenador que se encarga de ejecutar el “código máquina”. El código máquina es una serie de instrucciones que la CPU procesa. Siendo estas instrucciones, cada una de ellas un comando básico que ejecuta una operación específica, como mover datos, cambiar el flujo de ejecución del programa, hacer operaciones aritméticas, operaciones lógicas, etc.

Las instrucciones de la CPU son representadas en hexadecimal. Sin embargo, ésta misma instrucciones son traducidas a código mnemotécnico (un lenguaje mas legible), y es esto lo que conocemos como código ensamblador (ASM).

Entonces, de forma gráfica, la diferencia entre el código máquina y el lenguaje ensamblador es la siguiente:

![](../Images/Pasted%20image%2020240920104314.png)

Cada CPU tiene su Conjunto de Instrucciones, en inglés: `Instruction Set Architecture (ISA)`.

El ISA es una serie de instrucciones que el programador o el compilador debe entender y usar para poder escribir un programa correctamente para esa CPU y máquina en específico.

En otras palabras, ISA es lo que el programador puede ver, es decir, memoria, registros, instrucciones, etc. Da toda la información necesaria para el que quiera escribir un programa en ese lenguaje maquina.

## Registros

Cuando se habla de que un procesador es de 32 o 64 bits, se refiere al ancho de los registros de la CPU. Cada CPU tiene un conjunto de registros que son accesibles cuando se requieren. Se podría pensar en los registros como variables temporales usadas por la CPU para obtener y almacenar datos.

Hay registros que tienen una función específica, mientras que hay otros que solo sirven como se dice arriba, para obtener y almacenar datos.

En este caso, nos vamos a centrar e los registros GPRs (Registros de Propósito General):

![](../Images/Pasted%20image%2020240920104330.png)

En la primera columna como vemos, pone “Nomenclatura x86”, esto es porque dependiendo de los bits del procesador, la nomenclatura es distinta:

![](../Images/Pasted%20image%2020240920104349.png)

- En las CPU de 8 bits, se añadia el sufijo L o H dependiendo de si se trataba de un Low byte o High Byte.
- En las CPU de 16 bits, el sufijo era la X (sustituyendolo por la L o H de las CPU de 8 bits), excepto en el ESP, EBP, ESI y EDI, donde simplemente quitaron la L.
- En las CPU de 32 bits, como vemos, se añade el prefijo E, refiriendose a Extender.
- Por último, en las CPU de 64 bits, la E se reemplaza por la R.

Además de los 8 GPR, hay otro registro que será muy importante para nosotros, se trata del EIP (en la nomenclatura x86). El EIP (Extended Instruction Pointer) contiene la dirección de la próxima instrucción del programa.

## Proceso en la memoria

Cuando se ejecuta un proceso, se organiza en la memoria de la siguiente forma:

![](../Images/Pasted%20image%2020240920104403.png)

La memoria se divide en 4 regiones: Text, Data, Heap y Stack.

- `Text`: es establecido por el programa y contiene su código, éste área está establecida de solo lectura.
- `Data`: esta region se divide en datos inicializados y datos no inicializados.
    - Los datos inicializados incluyen objetos como variables estáticas y globales que ya han sido predefinidas y pueden ser modificadas.
    - Los datos no inicializados, llamados también BSS (Block Started by Symbol), también inicializan variables, pero estas se inicializan como 0 o sin ninguna inicializacion explícita, por ejemplo: `static int t`.
- `Heap`: aquí es donde se encuentra la memoria dinámica, es decir, durante la ejecución, el programa puede requerir mas memoria de lo que estaba previsto, por ello, a través de llamadas al sistema como brk o sbrk y todo controlado a través del uso de malloc, realloc y free, se consigue un área expandible en base a lo necesario.
- `Stack`: es el área donde ocurre todo, vamos a dedicarle un punto.

## Stack

El stack es un bloque o estructura de datos con modo de acceso LIFO (Last In, First Out; último en entrar, primero en salir), y está ubicado en la **High Memory**. Se puede pensar en el stack como un array usado para almacenar direcciones de retornos de funciones, pasar argumentos a funciones y almacenar variables locales.

Algo curioso del stack, es que crece hacia **Low Memory**, es decir, crece hacia abajo, hacia **0x00000000**.

Siendo el stack de modo de acceso LIFO, existen dos operaciones principales, antes de entrar a explicar cada una de ellas, voy a definir un registro importante para entender estas dos operaciones:

–> ESP (Stack Pointer): es un registro el cual apunta siempre a la cima del stack (top of the stack). En este punto hay que darse cuenta que, como el stack crece hacia abajo, es decir, hacia Low Memory, conforme el ESP esté mas cerca del 0x00000000 mas grande es el stack.

- Operación PUSH

La operación de PUSH lo que hace es restar al ESP. En CPU de 32 bits, resta 4 mientras que en 64, 8. Si lo pensamos bien, si el PUSH en vez de restar, sumase, estariamos sobreescribiendo/perdiendo datos.

Ejemplo de PUSH T:

![](../Images/Pasted%20image%2020240920104627.png)

Ahora, por ejemplo, de forma mas técnica, si el valor inicial del ESP fuese `0x0028FF80`, e hiciésemos un PUSH 1, el ESP disminuiria -4, conviertiéndose en `0x0028FF7C` y entonces, el 1 se pondría en la cima del stack.

De forma detallada, la dirección iria cambiando tal que:

![image 87](https://deephacking.tech/wp-content/uploads/2021/10/image-87.png.webp "Fundamentos para Stack based Buffer Overflow 9")

Ejemplo de operación PUSH

- Operacion POP

El caso del POP es igual pero al contrario, en 32 bits también se suma 4 y en 64, 8. El POP lo que haría en este caso sería quitar el valor que está en la cima del stack, es decir, los datos que se encuentren en la dirección donde apunta ahora mismo el ESP. Estos datos que se quitan normalmente se almacenarian en otro registro.

Ejemplo de POP T:

![](../Images/Pasted%20image%2020240920104655.png)

De nuevo, de forma mas técnica, si por ejemplo, despues del PUSH 1 anterior, el ESP vale `0x0028FF7C`, haciendole la operacion `POP EAX` quitariamos lo previamente empujado, haciendo que el ESP volviese a valer `0x0028FF80`, y, además, haciendo que el valor quitado, se copie al registro EAX (en este caso).

Es importante saber que el valor que se quita no se elimina o se vuelve 0. Se queda en el stack hasta que otra instrucción lo sobreescriba.

## Funciones

Ahora que se entiende mejor el stack, vamos a ver las funciones. Éstas, alteran el flujo normal del programa y cuando una función acaba, el flujo vuelve a la parte desde donde ha sido llamada.

Hay dos fases importantes aquí:

- **Prólogo**: es lo que ocurre al principio de cada función. Crea el stack frame correspondiente.
- **Epílogo**: exactamente lo contrario al prólogo. Ocurre al final de la funcion cuando ésta acaba. Su propósito es restaurar el stack frame de la función que llamó a la que acaba de terminar.

Por lo que el stack consiste en `stack frames` (porciones o areas del stack), que son empujadas (PUSH) cuando se llama a una función y quitadas (POP) cuando devuelve el valor esa funcion.

Cuando una funcion empieza, se crea un stack frame que se asigna a la dirección actual del ESP.

Cuando la funcion termina, ocurren dos cosas:

- El programa recibe los parámetros pasados a la subrutina
- El EIP se resetea a la dirección de la llamada inicial.

Dicho de otra forma, el stack frame mantiene el control de la direccion donde cada `subrutina`/`stack frame` debe volver cuando acaba.

Vamos a ver un ejemplo práctico básico para que se vea todo mas claro:

![](../Images/Pasted%20image%2020240920105149.png)

Ejemplo código simple en C

1. El programa empieza por la funcion main. El primer stack frame que debe ser empujado (PUSH) al stack es `main() stack frame`. Así que una vez se inicia, un nuevo stack frame se crea, el main() stack frame.
2. Dentro de `main()`, se llama a la función `a()`, por lo que el ESP está apuntando a la cima del stack de `main()` y aquí se crea el stack frame para `a()`.
3. Dentro de `a()`, se llama a `b()`, por lo que estando el ESP en la cima del stack frame de `a()` se crea el stack frame para `b()`.
4. A la hora de acabar y que cada funcion vaya llegando a su return, es el proceso contrario, entraremos en detalle en el siguiente ejemplo.

![](../Images/Pasted%20image%2020240920105159.png)

POC (proof of concept)

- Ejemplo mas complejo y en detalle:

![](../Images/Pasted%20image%2020240920105207.png)

Ejemplo código en C

Cuando una función comienza, lo primero que se añade al stack son los parámetros, en este caso, el programa comienza en la función `main()` y añade mediante PUSH al stack los parámetros `argc` y `argv` , en orden de derecha a izquierda (siempre es así).

El stack se vería asi:

`High Memory`

![](../Images/Pasted%20image%2020240920105220.png)

`Low Memory`

Ahora, se hace la llamada (CALL) a la función main(). Se empuja el contenido del EIP (Instruction Pointer) al stack y se apunta al primer byte después del CALL.

Este punto es importante porque tenemos que saber la dirección de la siguiente instrucción para poder seguir una vez la función llamada retorne.

`High Memory`

![](../Images/Pasted%20image%2020240920105233.png)

`Low Memory`

Ahora estamos dentro de la funcion main(), se tiene que crear un nuevo stack frame para ésta funcion. El stack frame es definido por el EBP (Frame Pointer) y el ESP (Stack Pointer).

Como no queremos perder información del anterior stack frame, debemos guardar el EBP actual del stack, ya que si no hacemos esto cuando retornemos, no sabremos que información pertenecía al anterior stack frame, la que llamó a main().

Una vez se ha guardado el valor del EBP, el EBP se actualiza y apunta a la cima del stack. En este punto, el EBP y el ESP apuntan al mismo sitio

`Low Memory`

![](../Images/Pasted%20image%2020240920105242.png)

`High Memory`

Desde este punto, el nuevo stack frame comienza encima del anterior (old stack frame).

Toda esta secuencia de instrucciones llevadas a cabo hasta ahora es lo que se conoce como “prólogo”. Esta fase ocurre en todas las funciones. Las instrucciones llevadas a cabo hasta ahora, en ensamblador, serían las siguientes:

1. `push ebp`
2. `mov ebp, esp`
3. `sub esp, X` // Donde X es un número

El stack antes de estas tres instrucciones es el siguiente:

`Low Memory`

![image 75](https://deephacking.tech/wp-content/uploads/2021/10/image-75.png.webp "Fundamentos para Stack based Buffer Overflow 17")

`High Memory`

La primera instrucción (`push ebp`), guarda el EBP empujándolo al stack, correspondiendose en el stack a “old EBP”, para que se pueda restaurar una vez la función retorne.

Ahora el EBP está apuntando a la cima del anterior stack frame (old stack frame).

Con la segunda instrucción (`mov ebp, esp`), conseguimos que el ESP se mueva donde está el EBP, creando ahora si, un nuevo stack frame:

`Low Memory`

![image 76](https://deephacking.tech/wp-content/uploads/2021/10/image-76.png.webp "Fundamentos para Stack based Buffer Overflow 18")

`High Memory`

Recordemos que en este punto, el EBP y el ESP están ubicados en la misma dirección.

La tercera instrucción (`sub esp, X`), mueve el ESP disminuyendo su valor (que como el stack crece hacia abajo, está por así decirlo, aumentando). Esto es necesario para hacer espacio para las variables locales de la función.

Esta instrucción básicamente está haciendo la siguiente operación:

- ESP = ESP – X

Dejando el stack, en la siguiente forma:

`Low Memory`

![image 77](https://deephacking.tech/wp-content/uploads/2021/10/image-77.png.webp "Fundamentos para Stack based Buffer Overflow 19")

`High Memory`

Ahora que el prólogo ha terminado, el stack frame para la función main() está completado. Ahora, hemos creado un hueco, que se puede ver en la imagen superior, para las variables locales.

Pero ocurre un problema, el ESP no está apuntando a la memoria que hay después del “old EBP”, sino que está apuntando a la cima del stack. Por lo que si hacemos un PUSH para añadir cada variable local, no se estaría empujando a la memoria reservada para ellas.

Así que no podemos usar este tipo de operación.

Así que, en este caso, trayendo el código para recordarlo:

![image 78](https://deephacking.tech/wp-content/uploads/2021/10/image-78.png.webp "Fundamentos para Stack based Buffer Overflow 20")

Código en C

Vamos a tener que usar otro tipo de operación, y será la siguiente:

- `MOV DWORD PRT SS:[ESP+Y], 0B`

Teniendo en cuenta que 0B es 11, y estamos hablando de la primera variable declarada en main() como podemos ver en el código. Esta instrucción significa:

- Mueveme el valor 0B a la dirección de memoria que apunte ESP+Y. Siendo Y un número y ESP+Y una dirección de memoria entre EBP y ESP.

Este proceso se repetirá para todas las variables que se tengan que declarar. Una vez completado, el stack tendrá esta forma:

`Low Memory`

![image 79](https://deephacking.tech/wp-content/uploads/2021/10/image-79.png.webp "Fundamentos para Stack based Buffer Overflow 21")

`High Memory`

Despues de colocar las 3 variables, el main() ejecutará la siguiente instrucción. En términos generales, el main() seguirá con su ejecución.

En este caso, el main() ahora llama a la función test(), por lo que otro stack frame se creará.

El proceso será el mismo que lo visto hasta ahora:

- PUSH a los parámetros de la función
- Llamada a la función
- Prólogo (entre otras cosas, actualizará el EBP y el ESP para el nuevo stack frame)
- Almacenará las variables locales en el stack

Al final de todo este proceso, el stack se verá de esta forma:

`Low Memory`

![image 80](https://deephacking.tech/wp-content/uploads/2021/10/image-80.png.webp "Fundamentos para Stack based Buffer Overflow 22")

`High Memory`

Hasta este punto, solo hemos visto la mitad del proceso, el cómo se crea los stack frames. Ahora vamos a ver como se destruyen, es decir, que ocurre cuando se ejecuta una sentencia return, que es también, lo que se conoce como “epílogo”.

En el epílogo, ocurre lo siguiente:

- Se devuelve el control al caller (a quien llamó a la función)
- El ESP se reemplaza con el valor actual de EBP, haciendo que ESP y EBP apunten al mismo sitio. Ahora se hace un POP a EBP para que se recupere el anterior EBP.
- Se vuelve al caller haciendo un POP al EIP y luego saltando a él.

El epílogo se puede representar como:

- leave
- ret

En instrucciones en ensamblador correspondería a:

1. `mov esp, ebp`
2. `pop ebp`
3. `ret`

Cuando se ejecuta la primera instrucción (`mov esp, ebp`), el ESP valdrá lo mismo que el EBP y por lo tanto, el stack obtiene la siguiente forma:

`Low Memory`

![image 89](https://deephacking.tech/wp-content/uploads/2021/10/image-89.png.webp "Fundamentos para Stack based Buffer Overflow 23")

`High Memory`

Con la segunda instrucción (`pop ebp`), se hace un POP al EBP (donde también se encuentra en este momento el ESP). Por lo que al quitarlo del stack. El “old EBP” vuelve a ser el principal, y de esta forma, se ha restaurado el anterior stack frame:

`Low Memory`

![image 90](https://deephacking.tech/wp-content/uploads/2021/10/image-90.png.webp "Fundamentos para Stack based Buffer Overflow 24")

`High Memory`

Con la tercera instrucción (`ret`), se vuelve a la dirección de retorno del stack(referencia: [docs.oracle.com)](https://docs.oracle.com/cd/E19455-01/806-3773/instructionset-67/index.html#:~:text=The%20ret%20instruction%20transfers%20control,stack%20by%20a%20call%20instruction.&text=The%20optional%20numeric%20(16%2D%20or,is%20popped%20from%20the%20stack.)

Con esto, conseguimos que el ESP apunte a “old EIP”, de tal forma que el stack quede de la siguiente forma:

`Low Memory`

![image 91](https://deephacking.tech/wp-content/uploads/2021/10/image-91.png.webp "Fundamentos para Stack based Buffer Overflow 25")

`High Memory`

En este punto, todo se ha restaurado correctamente, y el programa ya seguiría a la siguiente instrucción después de la llamada a test(). Y cuando acabe, ocurre el mismo proceso.

## Endianness

La forma de representar y almacenar los valores en la memoria es en Endianness, donde dentro de éste formato hay 2 tipos:

- big-endian
- little-endian

![image 84](https://deephacking.tech/wp-content/uploads/2021/10/image-84.png.webp "Fundamentos para Stack based Buffer Overflow 26")

Referencia: [skmp.dev](https://skmp.dev/blog/negative-addressing-bswap/)

Ejemplo:

Si representamos el número 11, en 4 bytes y en hexadecimal, obtenemos el siguiente valor:

- 0x000000**0B**

Siendo 0B = 11

Si por ejemplo, víesemos que en la dirección de memoria 0x0028FEBC se encuentra 0x0000000B, si estamos en un **sistema que usa Little Endian**, podriamos entender en que dirección de memoria está cada byte con lo siguiente:

A 0x000000**0B**, hacemos la operación de la imagen, quedandose tal que:

0B

00

00

`00`

El último valor, el resaltado, tiene como dirección de memoria la ya vista arriba: 0x0028FEBC, por lo que podríamos obtener los demás valores tal que:

`high memory`

0B : 0x0028FEBF

00 : 0x0028FEBE

00 : 0x0028FEBD

**`00 : 0x0028FEBC`**

`low memory`

En forma de ecuación por así decirlo, podriamos expresarlo de la siguiente manera:

a = 0x0028FEBC

0B : a + 3

00 : a + 2

00 : a + 1

00 : a

Si el sistema hubiese sido Big Endian sería exactamente al revés, las direcciones hubieran correspondido a:

`high memory`

00 : 0x0028FEBF

00 : 0x0028FEBE

00 : 0x0028FEBD

**0B : 0x0028FEBC**

`low memory`

Es importante saber la diferencia ya que lo necesitaremos para escribir payloads de cara a un Buffer Overflow.

## NOPS – No Operation Instruction

El NOP es una instrucción del lenguaje ensamblador que no hace nada. Si un programa se encuentra un NOP simplemente saltará a la siguiente instrucción. El NOP es normalmente representado en hexadecimal como 0x90, en los sistemas x86.

El NOP-sled es una técnica usada durante la explotación de buffer overflows. Su propósito es llenar ya sea una gran porcion o pequeña del stack de NOPS. Esto nos permitirá controlar que instrucción queremos ejecutar, la cual será la que normalmente se coloque despues del NOP-Sled.

![image 85](https://deephacking.tech/wp-content/uploads/2021/10/image-85.png.webp "Fundamentos para Stack based Buffer Overflow 27")

La razon de ésto es porque quizas el buffer overflow en cuestion del programa, necesite un tamaño y dirección específico porque será la que el programa esté esperando. O también nos puede facilitar conseguir que el EIP apunte a nuestro payload/shellcode.

[  
](https://newsletter.deephacking.tech/)

















# Referencias

https://deephacking.tech/fundamentos-para-buffer-overflow/