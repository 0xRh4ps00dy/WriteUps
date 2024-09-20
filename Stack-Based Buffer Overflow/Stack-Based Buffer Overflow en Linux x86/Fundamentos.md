Antes de la explotación del Buffer Overflow tenemos que entender que ocurre cuando ejecutamos este tipo de ataque, para ello vamos a empezar desde lo más básico.

# Introducción

La CPU (Unidad Central de Procesamiento), es la parte de nuestro ordenador que se encarga de ejecutar el “código máquina”. El código máquina es una serie de instrucciones que la CPU procesa. Siendo estas instrucciones, cada una de ellas un comando básico que ejecuta una operación específica, como mover datos, cambiar el flujo de ejecución del programa, hacer operaciones aritméticas, operaciones lógicas, etc.

Las instrucciones de la CPU son representadas en hexadecimal. Sin embargo, ésta misma instrucciones son traducidas a código mnemotécnico (un lenguaje mas legible), y es esto lo que conocemos como código ensamblador (ASM).

Entonces, de forma gráfica, la diferencia entre el código máquina y el lenguaje ensamblador es la siguiente:

![main qimg 3b6822a74141d7ce28aba2583b237313 lq](https://qph.fs.quoracdn.net/main-qimg-3b6822a74141d7ce28aba2583b237313-lq "Fundamentos para Stack based Buffer Overflow 4")

Referencia: [Quora](https://www.quora.com/Is-assembly-language-a-source-code-or-object-code)

Cada CPU tiene su Conjunto de Instrucciones, en inglés: `Instruction Set Architecture (ISA)`.

El ISA es una serie de instrucciones que el programador o el compilador debe entender y usar para poder escribir un programa correctamente para esa CPU y máquina en específico.

En otras palabras, ISA es lo que el programador puede ver, es decir, memoria, registros, instrucciones, etc. Da toda la información necesaria para el que quiera escribir un programa en ese lenguaje maquina.

## Registros

Cuando se habla de que un procesador es de 32 o 64 bits, se refiere al ancho de los registros de la CPU. Cada CPU tiene un conjunto de registros que son accesibles cuando se requieren. Se podría pensar en los registros como variables temporales usadas por la CPU para obtener y almacenar datos.

Hay registros que tienen una función específica, mientras que hay otros que solo sirven como se dice arriba, para obtener y almacenar datos.

En este caso, nos vamos a centrar e los registros GPRs (Registros de Propósito General):

![image 63](https://deephacking.tech/wp-content/uploads/2021/10/image-63.png.webp "Fundamentos para Stack based Buffer Overflow 5")

Registros de Propósito General

En la primera columna como vemos, pone “Nomenclatura x86”, esto es porque dependiendo de los bits del procesador, la nomenclatura es distinta:

![image 64](https://deephacking.tech/wp-content/uploads/2021/10/image-64.png.webp "Fundamentos para Stack based Buffer Overflow 6")

Referencia: [decoder.cloud](https://decoder.cloud/2017/01/25/idiots-guide-to-buffer-overflow-on-gnulinux-x64-architecture/)

- En las CPU de 8 bits, se añadia el sufijo L o H dependiendo de si se trataba de un Low byte o High Byte.
- En las CPU de 16 bits, el sufijo era la X (sustituyendolo por la L o H de las CPU de 8 bits), excepto en el ESP, EBP, ESI y EDI, donde simplemente quitaron la L.
- En las CPU de 32 bits, como vemos, se añade el prefijo E, refiriendose a Extender.
- Por último, en las CPU de 64 bits, la E se reemplaza por la R.

















Las excepciones de memoria son la reacción del sistema operativo ante un error en el software existente o durante la ejecución de este. Esto es responsable de la mayoría de las vulnerabilidades de seguridad en los flujos de programas en la última década. A menudo se producen errores de programación, lo que lleva a desbordamientos de búfer debido a la falta de atención al programar con lenguajes poco abstractos como `C`o `C++`.

Para entender cómo funciona a nivel técnico, necesitamos familiarizarnos con cómo:

- La memoria se divide y se utiliza.
- El depurador muestra y nombra las instrucciones individuales.
- El depurador se puede utilizar para detectar dichas vulnerabilidades.
- Podemos manipular la memoria.

Los exploits normalmente solo funcionan para una versión específica del software y del sistema operativo.

## La memoria

Cuando se llama al programa, las secciones se asignan a los segmentos del proceso y los segmentos se cargan en la memoria según lo describe el `ELF`archivo.
### .texto

La `.text`sección contiene las instrucciones del ensamblador del programa. Esta área puede ser de solo lectura para evitar que el proceso modifique accidentalmente sus instrucciones. Cualquier intento de escribir en esta área provocará inevitablemente un error de segmentación.
### .datos

La `.data`sección contiene variables globales y estáticas que son inicializadas explícitamente por el programa.
### .bss

Varios compiladores y enlazadores utilizan la `.bss`sección como parte del segmento de datos, que contiene variables asignadas estáticamente representadas exclusivamente por 0 bits.
### Heap

`Heap memory`Se asigna desde esta área. Esta área comienza al final del segmento ".bss" y crece hasta las direcciones de memoria superiores.
### Stack

`Stack memory`es una `Last-In-First-Out`estructura de datos en la que se almacenan las direcciones de retorno, los parámetros y, según las opciones del compilador, los punteros de marco. `C/C++`Las variables locales se almacenan aquí e incluso se puede copiar código a la pila. El `Stack`es un área definida en `RAM`. El enlazador reserva esta área y normalmente coloca la pila en el área inferior de la RAM por encima de las variables globales y estáticas. Se accede al contenido a través de `stack pointer`, establecido en el extremo superior de la pila durante la inicialización. Durante la ejecución, la parte asignada de la pila crece hasta las direcciones de memoria inferiores.

Las protecciones de memoria modernas ( `DEP`/ `ASLR`) evitarían los daños causados ​​por desbordamientos de búfer. DEP (Prevención de ejecución de datos), regiones marcadas de memoria como "de solo lectura". Las regiones de memoria de solo lectura son donde se almacenan algunas entradas del usuario (Ejemplo: La pila), por lo que la idea detrás de DEP era evitar que los usuarios cargaran shellcode a la memoria y luego establecieran el puntero de instrucción al shellcode. Los piratas informáticos comenzaron a utilizar ROP (Programación orientada al retorno) para evitar esto, ya que les permitía cargar el shellcode a un espacio ejecutable y usar llamadas existentes para ejecutarlo. Con ROP, el atacante necesita saber las direcciones de memoria donde se almacenan las cosas, por lo que la defensa contra esto fue implementar ASLR (Aleatorización del diseño del espacio de direcciones) que aleatoriza dónde se almacena todo, lo que hace que ROP sea más difícil.

Los usuarios pueden sortear ASLR filtrando direcciones de memoria, pero esto hace que los exploits sean menos fiables y, a veces, imposibles. Por ejemplo, el ["Servidor FTP Freefloat"](https://www.exploit-db.com/exploits/46763) es fácil de explotar en Windows XP (antes de DEP/ASLR). Sin embargo, si la aplicación se ejecuta en un sistema operativo Windows moderno, el desbordamiento del búfer existe, pero actualmente no es fácil explotarlo debido a DEP/ASLR (ya que no se conoce ninguna forma de filtrar direcciones de memoria).