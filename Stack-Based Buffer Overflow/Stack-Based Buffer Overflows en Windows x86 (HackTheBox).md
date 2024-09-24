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

![](../Images/Pasted%20image%2020240924085743.png)

# Local Buffer Overflow

## Fuzzing Paramenters

### Fuzzing de archivo abierto

```
```powershell-session
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
```
```
### 

## Controlling EIP



## Indetyfing Bad Characters





## Finding a Return Instruction



## Jumping to Shellcode


# # Remote Buffer Overflow

## Remote Fuzzing




## Building a Remote Exploit



## Remote Exploitation


# Referencias

https://academy.hackthebox.com/module/89/section/931

