# Vulnerable Program

Ahora estamos escribiendo un programa C simple llamado ``bow.c`` con una función vulnerable llamada ``strcpy()``.

## Bow.c

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int bowfunc(char *string) {

	char buffer[1024];
	strcpy(buffer, string);
	return 1;
}

int main(int argc, char *argv[]) {

	bowfunc(argv[1]);
	printf("Done.\n");
	return 1;
}
```
## Desactivar ASLR

Los sistemas operativos modernos tienen protecciones integradas contra este tipo de vulnerabilidades, como la aleatorización del diseño del espacio de direcciones (ASLR). Con el fin de aprender los conceptos básicos de la explotación del desbordamiento de búfer, vamos a desactivar estas funciones de protección de memoria:

```shell-session
student@nix-bow:~$ sudo su
root@nix-bow:/home/student# echo 0 > /proc/sys/kernel/randomize_va_space
root@nix-bow:/home/student# cat /proc/sys/kernel/randomize_va_space

0
```
## Compilación

A continuación, compilamos el código C en un binario ELF de 32 bits.

```shell-session
student@nix-bow:~$ sudo apt install gcc-multilib
student@nix-bow:~$ gcc bow.c -o bow32 -fno-stack-protector -z execstack -m32
student@nix-bow:~$ file bow32 | tr "," "\n"

bow: ELF 32-bit LSB shared object
 Intel 80386
 version 1 (SYSV)
 dynamically linked
 interpreter /lib/ld-linux.so.2
 for GNU/Linux 3.2.0
 BuildID[sha1]=93dda6b77131deecaadf9d207fdd2e70f47e1071
 not stripped
```
# Funciones C vulnerables

Existen varias funciones vulnerables en el lenguaje de programación C que no protegen la memoria de forma independiente. Estas son algunas de ellas:

- `strcpy`
- `gets`
- `sprintf`
- `scanf`
- `strcat`
- ...

# Presentacion del GDB

GDB, o GNU Debugger, es el depurador estándar de los sistemas Linux desarrollado por el Proyecto GNU. Se ha adaptado a muchos sistemas y es compatible con los lenguajes de programación C, C++, Objective-C, FORTRAN, Java y muchos más.

GDB nos proporciona las características habituales de trazabilidad como puntos de interrupción o salida de traza de pila y nos permite intervenir en la ejecución de programas. También nos permite, por ejemplo, manipular las variables de la aplicación o llamar a funciones independientemente de la ejecución normal del programa.

Usamos `GNU Debugger`( `GDB`) para visualizar el binario creado en el nivel de ensamblador. Una vez que hayamos ejecutado el binario con `GDB`, podemos desensamblar la función principal del programa.

## GDB - Sintaxis de AT&T

```shell-session
student@nix-bow:~$ gdb -q bow32

Reading symbols from bow...(no debugging symbols found)...done.
(gdb) disassemble main

Dump of assembler code for function main:
   0x00000582 <+0>: 	lea    0x4(%esp),%ecx
   0x00000586 <+4>: 	and    $0xfffffff0,%esp
   0x00000589 <+7>: 	pushl  -0x4(%ecx)
   0x0000058c <+10>:	push   %ebp
   0x0000058d <+11>:	mov    %esp,%ebp
   0x0000058f <+13>:	push   %ebx
   0x00000590 <+14>:	push   %ecx
   0x00000591 <+15>:	call   0x450 <__x86.get_pc_thunk.bx>
   0x00000596 <+20>:	add    $0x1a3e,%ebx
   0x0000059c <+26>:	mov    %ecx,%eax
   0x0000059e <+28>:	mov    0x4(%eax),%eax
   0x000005a1 <+31>:	add    $0x4,%eax
   0x000005a4 <+34>:	mov    (%eax),%eax
   0x000005a6 <+36>:	sub    $0xc,%esp
   0x000005a9 <+39>:	push   %eax
   0x000005aa <+40>:	call   0x54d <bowfunc>
   0x000005af <+45>:	add    $0x10,%esp
   0x000005b2 <+48>:	sub    $0xc,%esp
   0x000005b5 <+51>:	lea    -0x1974(%ebx),%eax
   0x000005bb <+57>:	push   %eax
   0x000005bc <+58>:	call   0x3e0 <puts@plt>
   0x000005c1 <+63>:	add    $0x10,%esp
   0x000005c4 <+66>:	mov    $0x1,%eax
   0x000005c9 <+71>:	lea    -0x8(%ebp),%esp
   0x000005cc <+74>:	pop    %ecx
   0x000005cd <+75>:	pop    %ebx
   0x000005ce <+76>:	pop    %ebp
   0x000005cf <+77>:	lea    -0x4(%ecx),%esp
   0x000005d2 <+80>:	ret    
End of assembler dump.
```

## GDB - Change the Syntax to Intel

```shell-session
(gdb) set disassembly-flavor intel
(gdb) disassemble main

Dump of assembler code for function main:
   0x00000582 <+0>:	    lea    ecx,[esp+0x4]
   0x00000586 <+4>:	    and    esp,0xfffffff0
   0x00000589 <+7>:	    push   DWORD PTR [ecx-0x4]
   0x0000058c <+10>:	push   ebp
   0x0000058d <+11>:	mov    ebp,esp
   0x0000058f <+13>:	push   ebx
   0x00000590 <+14>:	push   ecx
   0x00000591 <+15>:	call   0x450 <__x86.get_pc_thunk.bx>
   0x00000596 <+20>:	add    ebx,0x1a3e
   0x0000059c <+26>:	mov    eax,ecx
   0x0000059e <+28>:	mov    eax,DWORD PTR [eax+0x4]
<SNIP>
```

## Change GDB Syntax permanently

```shell-session
student@nix-bow:~$ echo 'set disassembly-flavor intel' > ~/.gdbinit
```

# Explotaition
# 

# 

# 

# Generating Shell

## Identification of the Return Address