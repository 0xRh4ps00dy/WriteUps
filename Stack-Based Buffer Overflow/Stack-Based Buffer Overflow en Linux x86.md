Las excepciones de memoria son la reacción del sistema operativo ante un error en el software existente o durante la ejecución de este. Esto es responsable de la mayoría de las vulnerabilidades de seguridad en los flujos de programas en la última década. A menudo se producen errores de programación, lo que lleva a desbordamientos de búfer debido a la falta de atención al programar con lenguajes poco abstractos como `C`o `C++`.

Para entender cómo funciona a nivel técnico, necesitamos familiarizarnos con cómo:

- La memoria se divide y se utiliza.
- El depurador muestra y nombra las instrucciones individuales.
- El depurador se puede utilizar para detectar dichas vulnerabilidades.
- Podemos manipular la memoria.

Los exploits normalmente solo funcionan para una versión específica del software y del sistema operativo.

## ## La memoria

Cuando se llama al programa, las secciones se asignan a los segmentos del proceso y los segmentos se cargan en la memoria según lo describe el `ELF`archivo.

#### texto

La `.text`sección contiene las instrucciones del ensamblador del programa. Esta área puede ser de solo lectura para evitar que el proceso modifique accidentalmente sus instrucciones. Cualquier intento de escribir en esta área provocará inevitablemente un error de segmentación.
#### .datos

La `.data`sección contiene variables globales y estáticas que son inicializadas explícitamente por el programa.
#### .bss

Varios compiladores y enlazadores utilizan la `.bss`sección como parte del segmento de datos, que contiene variables asignadas estáticamente representadas exclusivamente por 0 bits.

#### Heap

`Heap memory`Se asigna desde esta área. Esta área comienza al final del segmento ".bss" y crece hasta las direcciones de memoria superiores.

---

#### Stack

`Stack memory`es una `Last-In-First-Out`estructura de datos en la que se almacenan las direcciones de retorno, los parámetros y, según las opciones del compilador, los punteros de marco. `C/C++`Las variables locales se almacenan aquí e incluso se puede copiar código a la pila. El `Stack`es un área definida en `RAM`. El enlazador reserva esta área y normalmente coloca la pila en el área inferior de la RAM por encima de las variables globales y estáticas. Se accede al contenido a través de `stack pointer`, establecido en el extremo superior de la pila durante la inicialización. Durante la ejecución, la parte asignada de la pila crece hasta las direcciones de memoria inferiores.

Las protecciones de memoria modernas ( `DEP`/ `ASLR`) evitarían los daños causados ​​por desbordamientos de búfer. DEP (Prevención de ejecución de datos), regiones marcadas de memoria como "de solo lectura". Las regiones de memoria de solo lectura son donde se almacenan algunas entradas del usuario (Ejemplo: La pila), por lo que la idea detrás de DEP era evitar que los usuarios cargaran shellcode a la memoria y luego establecieran el puntero de instrucción al shellcode. Los piratas informáticos comenzaron a utilizar ROP (Programación orientada al retorno) para evitar esto, ya que les permitía cargar el shellcode a un espacio ejecutable y usar llamadas existentes para ejecutarlo. Con ROP, el atacante necesita saber las direcciones de memoria donde se almacenan las cosas, por lo que la defensa contra esto fue implementar ASLR (Aleatorización del diseño del espacio de direcciones) que aleatoriza dónde se almacena todo, lo que hace que ROP sea más difícil.

Los usuarios pueden sortear ASLR filtrando direcciones de memoria, pero esto hace que los exploits sean menos fiables y, a veces, imposibles. Por ejemplo, el ["Servidor FTP Freefloat"](https://www.exploit-db.com/exploits/46763) es fácil de explotar en Windows XP (antes de DEP/ASLR). Sin embargo, si la aplicación se ejecuta en un sistema operativo Windows moderno, el desbordamiento del búfer existe, pero actualmente no es fácil explotarlo debido a DEP/ASLR (ya que no se conoce ninguna forma de filtrar direcciones de memoria).