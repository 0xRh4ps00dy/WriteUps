![](../../../Imágenes/Attachments/Manager.png)

Manager empieza realizando fuerza bruta sobre Kerberos para encontrar usuarios en el dominio, y luego haciendo un spray de contraseñas usando el nombre de usuario de cada usuario como contraseña. Una vez tenemos las primeras credenciales podemos acceder a la instancia de base de datos MSSQL y utilizar la función xp_dirtree para explorar el sistema de archivos. Gracias a una copia de seguridad del servidor web, obtenemos unas credenciales de usuario que nos dan acceso al sistema mediante el servicio WINRM. Una vez dentro del sistema escalamos privilegios gracias la vulnerabilidad ESC7 obtenida mediante una configuración errónea.

### **Enumeración**

En primer lugar, realizamos la enumeración básica de puertos con **Nmap**:

![](../../../Imágenes/Attachments/image-2.png)

A continuación, realizamos el escaneo de servicios/versión y scripts de los puertos que hemos encontrado abiertos:

```
> nmap -sCV -oN scans/targetTCPPorts $target -p 53,80,88,135,139,389,445,464,593,1433,3268,3269,5985,9389,49667,49669,49670,49671,49731,56082,60677,62324

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-15 17:07 CET
Nmap scan report for 10.10.11.236
Host is up (0.25s latency).

PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Manager
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-03-15 23:06:40Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
|_ssl-date: 2024-03-15T23:08:13+00:00; +6h58m52s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   10.10.11.236:1433: 
|     Target_Name: MANAGER
|     NetBIOS_Domain_Name: MANAGER
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: manager.htb
|     DNS_Computer_Name: dc01.manager.htb
|     DNS_Tree_Name: manager.htb
|_    Product_Version: 10.0.17763
| ms-sql-info: 
|   10.10.11.236:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2024-02-28T12:02:32
|_Not valid after:  2054-02-28T12:02:32
|_ssl-date: 2024-03-15T23:08:15+00:00; +6h58m51s from scanner time.
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-03-15T23:08:13+00:00; +6h58m52s from scanner time.
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
|_ssl-date: 2024-03-15T23:08:13+00:00; +6h58m52s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49731/tcp open  msrpc         Microsoft Windows RPC
56082/tcp open  msrpc         Microsoft Windows RPC
60677/tcp open  msrpc         Microsoft Windows RPC
62324/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-03-15T23:07:36
|_  start_date: N/A
|_clock-skew: mean: 6h58m51s, deviation: 0s, median: 6h58m51s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 103.00 seconds
```

Con esta información podemos observar puertos abiertos con servicios DNS, HTTP, LDAP, SMB, WINRM o MSSQL. Por lo tanto, podemos concretar que nos encontramos enfrente de un entorno Active Directory de Windows.

Además, **nmap** también nos ha brindado dos dominios, **manager.htb** y **dc01.manager.htb**. Así que los añadimos en el fichero **/etc/hosts**.

Ahora probemos a enumerar usuarios usando la herramienta [**Kerbrute**](https://github.com/ropnop/kerbrute):

![](../../../Imágenes/Attachments/image-4.png)

Depuremos la lista:

![](../../../Imágenes/Attachments/image.png)

Ahora es momento de realizar una pulverización de contraseñas con cuidado de no vulnerar la política de contraseñas mediante la herramienta **Crackmapexec**:

![](../../../Imágenes/Attachments/Selection_017.png)

Por suerte nos encontramos que el usuario **operator** tiene acceso a SMB con la contraseña **operator**. Inspeccionemos SMB y averigüemos que podemos encontrar allí:

![](../../../Imágenes/Attachments/Selection_016.png)

Nada interesante…

### **Acceso como Raven**

No obstante, descubrimos que el usuario **operator** también puede acceder a **MSSQL**:

![](../../../Imágenes/Attachments/Selection_015.png)

Después de enumerar el servicio **MSSQL** y probar diferentes técnicas de forma fallida, descubrimos que podemos leer archivos y en especial vemos una copia de seguridad del sitio del cual podemos acceder a través del protocolo HTTP:

![](../../../Imágenes/Attachments/Selection_013.png)

![](../../../Imágenes/Attachments/Selection_012.png)

Descomprimimos el fichero descargado y visualizamos el fichero **.old-conf.xml** que nos arroja las credenciales del usuario **raven**:

![](../../../Imágenes/Attachments/Selection_014.png)

![](../../../Imágenes/Attachments/Selection_011.png)

Es hora de probar estas credenciales en el servicio WINRM:

![](../../../Imágenes/Attachments/Selection_010-1.png)

Una vez dentro nos hacemos con la bandera **user.txt:**

![](../../../Imágenes/Attachments/image-6.png)

### **Escalada de priviliegios**

En ese mismo directorio y en el directorio **Documents,** el cual fue el punto de acceso al sistema, nos encontramos el fichero **certify.exe**:

![](../../../Imágenes/Attachments/image-5.png)

Esta repetición genera curiosidad y nos da una señal de que la escalada de privilegios podía estar relacionada con certificados, por lo tanto, investiguemos un poco.

Ejecutamos el siguiente comando:

![](../../../Imágenes/Attachments/image-8.png)

El resultado nos arroja mucha información, pero lo más importante aquí son los dos derechos principales **`ManageCA`** y **`ManageCertificates`**.

Si un atacante obtiene control sobre `**ManageCA**` (que tiene Raven), puede obtener remotamente el derecho `**ManageCertificates**`, aprobar solicitudes de certificado pendientes, subvirtiendo la protección de «aprobación del administrador de certificados de CA». Esta vulnerabilidad se denomina ESC7. Podemos encontrar más información en [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation)

Asimismo, la plantilla del certificado **`SubCA`** es **vulnerable a ESC1**, pero **solo los administradores** pueden inscribirse en la plantilla. Así, un **usuario** puede **solicitar** registrarse en el **`SubCA`** (lo cual será **denegado**) pero que después será expedida por el gestor.

**Requisitos previos:**

- Solo **derecho** **`ManageCA`**.
- Derecho **`Manage Certificates`** (se puede otorgar desde **`ManageCA`**)
- La plantilla de certificado **`SubCA`** debe estar **habilitado** (se puede habilitar desde **`ManageCA`**)

#### **Abuso**

Para poder abusar de esta vulnerabilidad vamos a usar la utilidad **[certipy](https://github.com/ly4k/Certipy)**.

Concedemos el derecho `**Manage Certificates**` agregando al usuario rave como nuevo oficial:

![](../../../Imágenes/Attachments/image-9.png)

Habilitamos una plantilla de certificado específica y solicitamos un certificado con privilegios elevados:

![](../../../Imágenes/Attachments/image-10.png)

![](../../../Imágenes/Attachments/image-12.png)

Emitimos el certificado solicitado:

![](./Manager%20–%20Marcos%20Jurado_files/image-13.png)

Nos autenticamos con el certificado obtenido y obtenemos el hash del administrador del sistema:

![](../../../Imágenes/Attachments/image-15.png)

Durante el proceso, me encontré un error relacionado con el desfase del reloj. Este error, conocido como ‘KRB_AP_ERR_SKEW’ , ocurre cuando hay una diferencia horaria significativa entre el sistema local y el servidor remoto.

Para sincronizar la hora del sistema con el servidor **manager.htb**, utilice el siguiente comando:

```
ntpdate -u manager.htb
```

Además, la introducción de los comandos debe de hacerse de forma rápida, ya que el servidor se reinicia periódicamente.

Finalmente, iniciamos sesión mediante WINRM y leemos la bandera **root.txt**:

![](../../../Imágenes/Attachments/image-16.png)

![](../../../Imágenes/Attachments/image-17.png)
