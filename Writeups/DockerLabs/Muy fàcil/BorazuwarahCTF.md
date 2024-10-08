![](../../../Images/Pasted%20image%2020241008070451.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH i un servicio HTTP funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target

Nmap scan report for 172.17.0.2
Host is up (0.000013s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 07:06 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000054s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey:
|   256 3d:fd:d7:c8:17:97:f5:12:b1:f5:11:7d:af:88:06:fe (ECDSA)
|_  256 43:b3:ba:a9:32:c9:01:43:ee:62:d0:11:12:1d:5d:17 (ED25519)
80/tcp open  http    Apache httpd 2.4.59 ((Debian))
|_http-server-header: Apache/2.4.59 (Debian)
|_http-title: Site doesn't have a title (text/html).
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.07 seconds
```

## Website (80 TCP Port)

En el sitio web nos encontramos con solo una imagen.

![](../../../Images/Pasted%20image%2020241008070656.png)

Descargamos la imagen en nuestro sistema de ataque y nos ponemos a investigarla.
# Foothold

Hacemos un stego con `steghide` y obtenemos algo de información no importante.


Luego examinamos los metadatos de la imagen y conseguimos dar con un nombre de usuario.

Ahora, con el nombre de usuario podemos hacer fuerza bruta contra el servicio SSH con `hydra`.


Conseguimos acceder al sistema mediante SSH.

# Escalada de privilegios

Una vez dentro nvestigamos los comandos que podemos ejecutar con perimsos `sudo`

