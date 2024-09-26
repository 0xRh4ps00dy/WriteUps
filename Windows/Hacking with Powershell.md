# Introducción

PowerShell es el lenguaje de scripting de Windows y el entorno de shell creado utilizando el marco .NET.

Esto también permite que Powershell ejecute funciones .NET directamente desde su shell. La mayoría de los comandos de Powershell, llamados _cmdlets,_ están escritos en .NET. A diferencia de otros lenguajes de programación y entornos de shell, la salida de estos _cmdlets_  son objetos, lo que hace que Powershell esté orientado a objetos.

Esto también significa que ejecutar cmdlets le permite realizar acciones en el objeto de salida (lo que hace que sea conveniente pasar la salida de un _cmdlet_  a otro). El formato normal de un _cmdlet_ se representa mediante **Verbo-Sustantivo** ; por ejemplo, el _cmdlet_  para enumerar comandos se llama`Get-Command`

Los verbos comunes que se utilizan incluyen:

- Get
- Start
- Stop 
- Read
- Write
- New
- Out

Para obtener la lista completa de verbos aprobados, visita [este](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7) enlace.

# Comandos básicos

