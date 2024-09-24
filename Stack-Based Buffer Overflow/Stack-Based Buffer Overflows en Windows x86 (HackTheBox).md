# Debugging Windows Programs

Para identificar y explotar con éxito los desbordamientos de búfer en los programas de Windows, necesitamos depurar el programa para seguir su flujo de ejecución y sus datos en la memoria. Hay muchas herramientas que podemos usar para la depuración, como [Immunity Debugger](https://www.immunityinc.com/products/debugger/index.html) , [OllyDBG](http://www.ollydbg.de/) , [WinGDB](http://wingdb.com/) o [IDA Pro](https://www.hex-rays.com/products/ida/) . Sin embargo, estos depuradores están desactualizados (Immunity/OllyDBG) o necesitan una licencia pro para su uso (WinGDB/IDA).

En este módulo, utilizaremos [x64dbg](https://github.com/x64dbg/x64dbg) , que es un excelente depurador de Windows orientado específicamente a la explotación binaria y la ingeniería inversa. `x64dbg`es una herramienta de código abierto desarrollada y mantenida por la comunidad y también admite la depuración x64 (a diferencia de Immunity), por lo que podemos seguir usándola cuando queramos pasar a desbordamientos de búfer x64 de Windows.

Además del depurador en sí, utilizaremos un complemento de explotación binaria para llevar a cabo de manera eficiente muchas tareas necesarias para identificar y explotar desbordamientos de búfer. Un complemento popular es [mona.py](https://github.com/x64dbg/mona) , que es un excelente complemento de explotación binaria, aunque ya no se mantiene, no es compatible con x64 y se ejecuta en Python2 en lugar de Python3. Por lo tanto, en su lugar, utilizaremos [ERC.Xdbg](https://github.com/Andy53/ERC.Xdbg) , que es un complemento de explotación binaria de código abierto para x64dbg.


# Local Buffer Overflow

## Fuzzing Paramenters



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

