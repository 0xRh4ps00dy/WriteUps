#tag 

![](../../../Images/Pasted%20image%2020241008071854.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio **SSH** un servicio **HTTP** y un servicio **FTP** funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 16:01 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000012s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.55 seconds
```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-08 16:02 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000039s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0             667 Jun 18 03:20 chat-gonza.txt
|_-rw-r--r--    1 0        0             315 Jun 18 03:21 pendientes.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:172.17.0.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 60:05:bd:a9:97:27:a5:ad:46:53:82:15:dd:d5:7a:dd (ECDSA)
|_  256 0e:07:e6:d4:3b:63:4e:77:62:0f:1a:17:69:91:85:ef (ED25519)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
|_http-server-header: Apache/2.4.58 (Ubuntu)
|_http-title: Russoski Coaching
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.11 seconds
```

## FTP (21 TCP Port)

Tal como **nmap** nos muestra con sus scripts, podemos acceder al servicio usando el nombre de usuario **anonymous** y sin contraseña para descargar dos ficheros.

**chat-gonza.txt**

```
[16:21, 16/6/2024] Gonza: pero en serio es tan guapa esa tal Nágore como dices?
[16:28, 16/6/2024] Russoski: es una auténtica princesa pff, le he hecho hasta un vídeo y todo, lo tengo ya subido y tengo la URL guardada
[16:29, 16/6/2024] Russoski: en mi ordenador en una ruta segura, ahora cuando quedemos te lo muestro si quieres
[21:52, 16/6/2024] Gonza: buah la verdad tenías razón eh, es hermosa esa chica, del 9 no baja
[21:53, 16/6/2024] Gonza: por cierto buen entreno el de hoy en el gym, noto los brazos bastante hinchados, así sí
[22:36, 16/6/2024] Russoski: te lo dije, ya sabes que yo tengo buenos gustos para estas cosas xD, y sí buen training hoy
```

**pendientes.txt**

```
1 Comprar el Voucher de la certificación eJPTv2 cuanto antes!

2 Aumentar el precio de mis asesorías online en la Web!

3 Terminar mi laboratorio vulnerable para la plataforma Dockerlabs!

4 Cambiar algunas configuraciones de mi equipo, creo que tengo ciertos
  permisos habilitados que no son del todo seguros..
```

## Website (80 TCP Port)

En el sitio web nos encontramos con un ...


![](../../../Images/Pasted%20image%2020241008160638.png)

```
dirscan http://172.17.0.2/FUZZ

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://172.17.0.2/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

:: Progress: [1/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 :backup                  [Status: 301, Size: 309, Words: 20, Lines: 10, Duration: 3ms]
:: Progress: [126/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0:: Progress: [1299/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [2970/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: server-status           [Status: 403, Size: 275, Words: 20, Lines: 10, Duration: 1ms]
:: Progress: [4265/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:                         [Status: 200, Size: 5208, Words: 2135, Lines: 119, Duration: 1ms]
:: Progress: [4273/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [4282/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [5773/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [7363/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [8659/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [10102/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors::: Progress: [11765/30000] :: Job [1/1] :: 13333 req/sec :: Duration: [0:00:01] :: Errimportant               [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 0ms]
:: Progress: [11996/30000] :: Job [1/1] :: 8695 req/sec :: Duration: [0:00:01] :: Erro:: Progress: [13168/30000] :: Job [1/1] :: 12500 req/sec :: Duration: [0:00:01] :: Err:: Progress: [14643/30000] :: Job [1/1] :: 11764 req/sec :: Duration: [0:00:01] :: Err:: Progress: [16190/30000] :: Job [1/1] :: 12500 req/sec :: Duration: [0:00:01] :: Err:: Progress: [17596/30000] :: Job [1/1] :: 15384 req/sec :: Duration: [0:00:01] :: Err:: Progress: [19214/30000] :: Job [1/1] :: 15384 req/sec :: Duration: [0:00:01] :: Err:: Progress: [20797/30000] :: Job [1/1] :: 13333 req/sec :: Duration: [0:00:01] :: Err:: Progress: [22309/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:01] :: Err:: Progress: [23927/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:02] :: Err:: Progress: [25439/30000] :: Job [1/1] :: 10526 req/sec :: Duration: [0:00:02] :: Err:: Progress: [26977/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:02] :: Err:: Progress: [28170/30000] :: Job [1/1] :: 6896 req/sec :: Duration: [0:00:02] :: Erro:: Progress: [29556/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:02] :: Err:: Progress: [30000/30000] :: Job [1/1] :: 15384 req/sec :: Duration: [0:00:02] :: Err:: Progress: [30000/30000] :: Job [1/1] :: 15384 req/sec :: Duration: [0:00:02] :: Errors: 2 ::
```

Descubre un directio llamado backup y uno llamado important

![](../../../Images/Pasted%20image%2020241008161739.png)

**backup.txt**

```
Usuario para todos mis servicios: russoski (cambiar pronto!)
```

# Usuario XXX


# Escalada de privilegios

Haciendo una investigación a nivel local del sistema ...