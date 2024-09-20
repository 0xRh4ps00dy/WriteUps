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
## Funciones C vulnerables

Existen varias funciones vulnerables en el lenguaje de programación C que no protegen la memoria de forma independiente. Estas son algunas de ellas:

- `strcpy`
- `gets`
- `sprintf`
- `scanf`
- `strcat`
- ...

