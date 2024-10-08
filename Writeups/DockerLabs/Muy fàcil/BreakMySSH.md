#tag

![](../../../Images/Pasted%20image%2020241008071626.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH i un servicio HTTP funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 12:47 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000013s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:AC:11:00:02 (Unknown)
```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 12:47 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000049s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.7 (protocol 2.0)
| ssh-hostkey:
|   2048 1a:cb:5e:a3:3d:d1:da:c0:ed:2a:61:7f:73:79:46:ce (RSA)
|   256 54:9e:53:23:57:fc:60:1e:c0:41:cb:f3:85:32:01:fc (ECDSA)
|_  256 4b:15:7e:7b:b3:07:54:3d:74:ad:e0:94:78:0c:94:93 (ED25519)
MAC Address: 02:42:AC:11:00:02 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.93 seconds
```

## SSH (22 TCP Port)


https://github.com/Sait-Nuri/CVE-2018-15473

# Usuario Borazuwarah

Hacemos un stego con `steghide` y obtenemos algo de información no importante.

```

```

Luego examinamos los metadatos de la imagen y conseguimos dar con un nombre de usuario.

```

```

Ahora, con el nombre de usuario podemos hacer fuerza bruta contra el servicio SSH con `hydra`.

```
> hydra 
```

Conseguimos acceder al sistema mediante SSH.

# Foothold

Una vez dentro investigamos los comandos que podemos ejecutar con permisos `sudo`.

```
borazuwarah@7dd5484e294a:~$ sudo -l
Matching Defaults entries for borazuwarah on 7dd5484e294a:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

User borazuwarah may run the following commands on 7dd5484e294a:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: /bin/bash
```

Nos encontramos que podemos ejecutar el binario`/bin/bash` con permisos ``sudo`` y sin contraseña.

```
borazuwarah@7dd5484e294a:~$ sudo /bin/bash
root@7dd5484e294a:/home/borazuwarah#
```