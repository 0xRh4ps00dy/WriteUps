# Debugging Windows Programs

Para identificar y explotar con éxito los desbordamientos de búfer en los programas de Windows, necesitamos depurar el programa para seguir su flujo de ejecución y sus datos en la memoria. Hay muchas herramientas que podemos usar para la depuración, como [Immunity Debugger](https://www.immunityinc.com/products/debugger/index.html) , [OllyDBG](http://www.ollydbg.de/) , [WinGDB](http://wingdb.com/) o [IDA Pro](https://www.hex-rays.com/products/ida/) . Sin embargo, estos depuradores están desactualizados (Immunity/OllyDBG) o necesitan una licencia pro para su uso (WinGDB/IDA).

En este módulo, utilizaremos [x64dbg](https://github.com/x64dbg/x64dbg) , que es un excelente depurador de Windows orientado específicamente a la explotación binaria y la ingeniería inversa. `x64dbg`es una herramienta de código abierto desarrollada y mantenida por la comunidad y también admite la depuración x64 (a diferencia de Immunity), por lo que podemos seguir usándola cuando queramos pasar a desbordamientos de búfer x64 de Windows.

Además del depurador en sí, utilizaremos un complemento de explotación binaria para llevar a cabo de manera eficiente muchas tareas necesarias para identificar y explotar desbordamientos de búfer. Un complemento popular es [mona.py](https://github.com/x64dbg/mona) , que es un excelente complemento de explotación binaria, aunque ya no se mantiene, no es compatible con x64 y se ejecuta en Python2 en lugar de Python3. Por lo tanto, en su lugar, utilizaremos [ERC.Xdbg](https://github.com/Andy53/ERC.Xdbg) , que es un complemento de explotación binaria de código abierto para x64dbg.

## Instalación

### x64dbg

Para instalarlo `x64dbg`, podemos seguir las instrucciones que se muestran en su [página de GitHub](https://github.com/x64dbg/x64dbg) , ir a la página [de la última versión](https://github.com/x64dbg/x64dbg/releases/tag/snapshot) y descargar el `snapshot_<SNIP>.zip`archivo. Una vez que lo descarguemos en nuestra máquina virtual Windows, podemos extraer el `zip`contenido del archivo, cambiar el nombre de la `release`carpeta a algo como `x64dbg`y moverla a nuestra `C:\Program Files`carpeta o guardarla en cualquier carpeta que queramos.

Finalmente, podemos hacer doble clic `C:\Program Files\x64dbg\x96dbg.exe`para registrar la extensión de shell y agregar un acceso directo a nuestro Escritorio.

### ERC

Para instalar el `ERC` plugin, podemos ir a la [página de lanzamiento](https://github.com/Andy53/ERC.Xdbg/releases) y descargar el `zip`archivo que coincida con nuestra VM (`x64`o `x32`), que en nuestro caso es `ERC.Xdbg_32-<SNIP>.zip`. Una vez que lo descarguemos en nuestra VM de Windows, podemos extraer su contenido en `x32dbg`la carpeta plugins ubicada en `C:\Program Files\x64dbg\x32\plugins`.

Cuando se complete el proceso, el complemento debería estar listo para usarse. Por lo tanto, una vez que ejecutemos `x32dbg`, podemos escribir `ERC --help` en la barra de comandos en la parte inferior para ver `ERC`el menú de ayuda de .

Para visualizar la `ERC`salida del , debemos pasar a la `Log` pestaña haciendo clic sobre ella o haciendo clic en `Alt+L`, como podemos ver a continuación:

![](../../Images/Pasted%20image%2020240924085743.png)

# Local Buffer Overflow

## Fuzzing Paramenter

El primer paso en cualquier ejercicio de vulnerabilidad binaria es realizar pruebas de fuzzing en varios parámetros y cualquier otra entrada que acepte el programa para ver si nuestra entrada puede hacer que la aplicación se bloquee. Si alguna de nuestras entradas logra que el programa se bloquee, revisamos qué causó el bloqueo del programa. Si vemos que el programa se bloqueó porque nuestra entrada sobrescribió el `EIP` registro, es probable que tengamos una vulnerabilidad de desbordamiento de búfer basada en pila. Todo lo que queda es explotar esta vulnerabilidad con éxito, lo que puede variar en dificultad según el sistema operativo, la arquitectura del programa y las protecciones.
### Fuzzing en campos de texto

```
```powershell-session
python -c "print('A'*10000)"
```
### Fuzzing de archivo abierto

```powershell-session
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
```
## Controlling EIP

Nuestro próximo paso sería controlar con precisión qué dirección se coloca en `EIP` , de modo que se ejecute cuando el programa regrese de la función con la `ret` instrucción. Para ello, primero debemos calcular nuestro desplazamiento exacto de `EIP` , lo que significa qué tan lejos `EIP` está del comienzo de la entrada. Una vez que conocemos el desplazamiento, podemos llenar el búfer que conduce a `EIP` con cualquier dato basura y luego colocar la dirección de instrucción que queremos que se ejecute en la ubicación de `EIP`.

La mejor manera de calcular el desplazamiento exacto de `EIP` es mediante el envío de un patrón único y no repetitivo de caracteres, de modo que podamos ver los caracteres que lo completan `EIP` y buscarlos en nuestro patrón único. Como es un patrón único y no repetitivo, solo encontraremos una coincidencia, que nos daría el desplazamiento exacto de `EIP`.

### Creando un patrón único

Podemos generar un patrón único `pattern_create` en nuestra máquina atacante o directamente en nuestro depurador `x32dbg` con el `ERC` complemento. 

Para hacerlo en nuestra máquina, podemos usar el siguiente comando:

```shell-session
/usr/bin/msf-pattern_create -l 5000

Aa0Aa1Aa2...SNIP...3Gk4Gk5Gk
```

Siempre es más fácil hacer todo en Windows para evitar saltar entre dos máquinas virtuales. Veamos cómo podemos obtener el mismo patrón con `ERC`.

Si usamos el `ERC --help` comando, veremos la siguiente guía:

```cmd-session
--Pattern
Generates a non repeating pattern. A pattern of pure ASCII characters can be generated up to 20277 and up to  
66923 if special characters are used. The offset of a particular string can be found inside the pattern by 
providing a search string (must be at least 3 chars long).
    Pattern create: ERC --pattern <create | c> <length>
    Pattern offset: ERC --pattern <offset | o> <search string>
```

Como podemos ver, podemos usar `ERC --pattern c 5000` para obtener nuestro patrón. Entonces, usemos este comando y veamos qué obtenemos: 

![](../Images/Pasted%20image%2020240924122728.png)

Este patrón es el mismo que obtuvimos con la `msf-pattern_create` herramienta, por lo que podemos usar cualquiera de los dos. Ahora podemos ir a nuestro escritorio para buscar la salida guardada en un archivo llamado `Pattern_Create_1.txt`.

### Escribiendo nuestro exploit

Escribiremos nuestro exploit en Python3, ya que contiene bibliotecas integradas que nos ayudan en este proceso, como `struct` y `requests`.

Podemos empezar creando una nueva función con `def eip_offset():`, y luego crear nuestra `payload`variable como un `bytes`objeto y pegar entre paréntesis la `Ascii:` salida de `Pattern_Create_1.txt`. Así, podemos hacer clic en la barra de búsqueda de Windows en la parte inferior y escribir `IDLE`, lo que abriría el editor de Python3, y luego hacer clic `ctrl+N` para comenzar a escribir un nuevo script de Python donde podemos comenzar a escribir nuestro código. Nuestro código inicial debería verse así:

```python
def eip_offset():
    payload = bytes("Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac"
                    ...SNIP...
                    "Gi3Gi4Gi5Gi6Gi7Gi8Gi9Gj0Gj1Gj2Gj3Gj4Gj5Gj6Gj7Gj8Gj9Gk0Gk1Gk2Gk3Gk4Gk5Gk",
					"utf-8")
```

A continuación, bajo la misma `eip_offset()` función, escribiremos `payload` en un archivo llamado `pattern.wav`, agregando las siguientes líneas:

```python
with open('pattern.wav', 'wb') as f:
    f.write(payload)
```

Por último, debemos llamar a nuestra `eip_offset()` función agregando la siguiente línea al final. De lo contrario, la función no se ejecutará:

```python3
eip_offset()
```

<<<<<<< HEAD:Stack-Based Buffer Overflow/Stack-Based Buffer Overflows en Windows x86 (HackTheBox).md
Ahora, podemos guardar este exploit en nuestro escritorio como `win32bof_exploit.py` y ejecutarlo. Para ejecutarlo mientras aún estamos en nuestro escritorio `IDLE`, podemos hacer clic en `Run > Run Module` o hacer clic en `F5`:

![](../Images/Pasted%20image%2020240924122717.png)
## Indetyfing Bad Characters
=======
Ahora, podemos guardar este exploit en nuestro escritorio como `win32bof_exploit.py`y ejecutarlo. Para ejecutarlo mientras aún estamos en nuestro escritorio `IDLE`, podemos hacer clic en `Run > Run Module`o hacer clic en `F5`.

### Cálculo de la compensación del EIP

Ahora podemos usar el valor de `EIP`para calcular el desplazamiento. Podemos hacerlo nuevamente en nuestra máquina con `msf-pattern_offset`(la contraparte de `msf-pattern_create`), usando el valor hexadecimal en `EIP`, de la siguiente manera:

```shell-session
0xRh4ps00dy@htb[/htb]$ /usr/bin/msf-pattern_offset -q 31684630
>>>>>>> origin/main:Stack-Based Buffer Overflow/Windows/Stack-Based Buffer Overflows en Windows x86 (HackTheBox).md

[*] Exact match at offset 4112
```

Usando ERC en x32dbg o x64dbg debemos obtener el valor ASCII de los bytes hexadecimales que se encuentran en `EIP`, haciendo clic derecho en `EIP`y seleccionando `Modify Value`, o haciendo clic en `EIP`y luego presionando Enter. Una vez que lo hagamos, veremos varias representaciones del `EIP`valor, siendo ASCII la última:

![](../../Images/Pasted%20image%2020240924205944.png)

El valor hexadecimal encontrado en `EIP`representa la cadena `1hF0`. Ahora, podemos usar `ERC --pattern o 1hF0`para obtener el desplazamiento del patrón:

![](../../Images/Pasted%20image%2020240924210004.png)

### Control del EIP

Nuestro paso final es asegurarnos de que podemos controlar qué valor va en `EIP`. Conociendo el desplazamiento, sabemos exactamente a qué distancia `EIP` está nuestro del inicio del búfer. Por lo tanto, si enviamos `4112` bytes, los siguientes 4 bytes serían los que llenarían `EIP`.

Agreguemos otra función, `eip_control()`, a nuestra `win32bof_exploit.py` y creemos una `offset` variable con el desplazamiento que encontramos. Luego, crearemos una `buffer` variable con una cadena de `A`bytes tan larga como nuestro desplazamiento para llenar el espacio del búfer y una `eip`variable con el valor que queremos `EIP` que sea, que usaremos como `4`bytes de `B`. Finalmente, agregaremos ambas a una `payload`variable y las escribiremos en `control.wav`, de la siguiente manera:

```python
def eip_control():
    offset = 4112
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip
    
    with open('control.wav', 'wb') as f:
        f.write(payload)

eip_control()
```

## Indetyfing personajes malos

Antes de comenzar a utilizar el hecho de que podemos controlar `EIP` y subvertir el flujo de ejecución del programa, necesitamos determinar los caracteres que debemos evitar usar en nuestra carga útil.

Como estamos atacando un parámetro de entrada (un archivo abierto en este caso), se espera que el programa procese nuestra entrada. Por lo tanto, dependiendo del procesamiento que cada programa ejecute sobre nuestra entrada, ciertos caracteres pueden indicarle al programa que ha llegado al final de la entrada. Esto puede suceder incluso aunque todavía no haya llegado al final de la entrada.

Por ejemplo, un carácter incorrecto muy común es un byte nulo `0x00`, utilizado en Assembly como un terminador de cadena, que le dice al procesador que la cadena ha terminado. Entonces, si nuestra carga útil incluye un byte nulo, el programa puede dejar de procesar nuestro shellcode, pensando que ha llegado al final de la misma. Esto hará que nuestra carga útil no se ejecute correctamente y nuestro ataque fallará. Más ejemplos son `0x0a`y `0x0d`, que son la nueva línea `\n` y el retorno de carro `\r`, respectivamente. Si estuviéramos explotando un desbordamiento de búfer en una entrada de cadena que se espera que sea una sola línea (como una clave de licencia), estos caracteres probablemente terminarían nuestra entrada prematuramente, lo que también haría que nuestra carga útil falle.

Para identificar caracteres incorrectos, tenemos que enviar todos los caracteres después de completar la `EIP` dirección, que se encuentra después de `4112`+ `4` bytes. Luego, verificamos si el programa eliminó alguno de los caracteres o si nuestra entrada se truncó prematuramente después de un carácter específico.

Para ello necesitaríamos dos archivos:

1. Un `.wav` archivo con todos los caracteres para cargar en el programa.
2. Un `.bin` archivo para comparar con nuestra entrada en memoria

Podemos utilizar `ERC` para generar el `.bin` archivo y generar una lista de todos los caracteres para crear nuestro `.wav` archivo. Para ello, podemos utilizar el `ERC --bytearray` comando:

![](../../Images/Pasted%20image%2020240925071440.png)

Esto también crea dos archivos en nuestro escritorio:

- `ByteArray_1.txt`: Que contiene la cadena de todos los caracteres que podemos usar en nuestro exploit de Python
- `ByteArray_1.bin`: Que podemos usar `ERC` más adelante para comparar con nuestra entrada en la memoria.
### Actualizando nuestro exploit

El siguiente paso sería generar un `.wav` archivo con la cadena de caracteres generada por `ERC`. Nuevamente escribiremos una nueva función `bad_chars()`, y usaremos un código similar a la `eip_control()` función, pero usaremos los caracteres bajo `C#`in `ByteArray_1.txt`. Crearemos una nueva lista de bytes `all_chars = bytes([])`, y pegaremos los caracteres entre los corchetes. Luego escribiremos en `chars.wav` el mismo `payload`from `eip_control()`, y agregaremos después `all_chars`. La función final se vería así:

```python
def bad_chars():
    all_chars = bytes([
        0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07,
        ...SNIP...
        0xF8, 0xF9, 0xFA, 0xFB, 0xFC, 0xFD, 0xFE, 0xFF
    ])
    
    offset = 4112
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip + all_chars
    
    with open('chars.wav', 'wb') as f:
        f.write(payload)

bad_chars()
```
### Comparando nuestra entrada



## Finding a Return Instruction



## Jumping to Shellcode


# Remote Buffer Overflow

## Remote Fuzzing




## Building a Remote Exploit



## Remote Exploitation


# Referencias

https://academy.hackthebox.com/module/89/section/931

