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

`Get-Command` y `Get-Help` son tus mejores amigos.
## Get-Help

`Get-Help` muestra información sobre un _cmdlet._ Para obtener ayuda con un comando en particular, ejecute lo siguiente:

`Get-Help Command-Name`

También puedes entender exactamente cómo usar el comando si pasas el `-examples`indicador. Esto devolvería un resultado como el siguiente: 

Ejecutar el cmdlet Get-Help para explicar un comando

```powershell
PS C:\Users\Administrator> Get-Help Get-Command -Examples

NAME
    Get-Command

SYNOPSIS
Gets all commands.

Example 1: Get cmdlets, functions, and aliases

PS C:\>Get-Command
```

## Get-Command

`Get-Command` obtiene todos los _cmdlets_ instalados en el equipo actual. Lo bueno de este _cmdlet_  es que permite la coincidencia de patrones como la siguiente:

`Get-Command Verb-*` o `Get-Command *-Noun`

## Manipulación de objetos

Puede pensar en los métodos como funciones que se pueden aplicar a la salida del _cmdlet_  y puede pensar en las propiedades como variables en la salida de un _cmdlet_ . Para ver estos detalles, pase la salida de un _cmdlet_  al `Get-Member` _cmdlet:_

`Verb-Noun | Get-Member`

## Creación de objetos a partir de cmdlets anteriores

Una forma de manipular objetos es extraer las propiedades de la salida de un _cmdlet_ y crear un nuevo objeto. Esto se hace mediante el `Select-Object` _cmdlet._ 

A continuación se muestra un ejemplo de cómo enumerar los directorios y simplemente seleccionar el modo y el nombre:

```powershell
PS C:\Users\Administrator> Get-ChildItem | Select-Object -Property Mode, Name
Mode   Name
----   ----
d-r--- Contacts
d-r--- Desktop
d-r--- Documents
d-r--- Downloads
d-r--- Favorites
d-r--- Links
d-r--- Music
d-r--- Pictures
d-r--- Saved Games
d-r--- Searches
d-r--- Videos
```

## Filtrado de objetos

Al recuperar objetos de salida, es posible que desee seleccionar objetos que coincidan con un valor muy específico. Puede hacerlo utilizando el `Where-Object`filtro en función del valor de las propiedades. 

El formato general para utilizar este _cmdlet_ es 

`Verb-Noun | Where-Object -Property PropertyName -operator Value`

`Verb-Noun | Where-Object {$_.PropertyName -operator Value}`

La segunda versión utiliza el `$_` operador para iterar a través de cada objeto pasado al `Where-Object` _cmdlet_ .

**PowerShell es bastante sensible, así que no pongas comillas alrededor del comando.**

El `-operator` tiene una lista de los siguientes operadores:

- `-Contains`: si algún elemento en el valor de la propiedad coincide exactamente con el valor especificado
- `-EQ`: si el valor de la propiedad es el mismo que el valor especificado
- `-GT`: si el valor de la propiedad es mayor que el valor especificado

Para obtener una lista completa de operadores, utilice [este](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-6) enlace.

A continuación se muestra un ejemplo de comprobación de los procesos detenidos:

Demostrar el uso de operadores únicamente para mostrar servicios detenidos

```powershell
PS C:\Users\Administrator> Get-Service | Where-Object -Property Status -eq Stopped

Status   Name               DisplayName
------   ----               -----------
Stopped  AJRouter           AllJoyn Router Service
Stopped  ALG                Application Layer Gateway Service
Stopped  AppIDSvc           Application Identity
Stopped  AppMgmt            Application Management
Stopped  AppReadiness       App Readiness
Stopped  AppVClient         Microsoft App-V Client
Stopped  AppXSvc            AppX Deployment Service (AppXSVC)
Stopped  AudioEndpointBu... Windows Audio Endpoint Builder
Stopped  Audiosrv           Windows Audio
Stopped  AxInstSV           ActiveX Installer (AxInstSV)
Stopped  BITS               Background Intelligent Transfer Ser...
Stopped  Browser            Computer Browser
Stopped  bthserv            Bluetooth Support Service
-- cropped for brevity--
```

## Ordenar objeto

Cuando un _cmdlet_ genera una gran cantidad de información, es posible que deba ordenarla para extraerla de manera más eficiente. Para ello, canalice la salida de un _cmdlet_  hacia el `Sort-Object` _cmdlet_ .

El formato del comando sería:

`Verb-Noun | Sort-Object`











