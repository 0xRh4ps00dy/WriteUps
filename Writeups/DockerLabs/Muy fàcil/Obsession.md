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


```

## Website (80 TCP Port)

En el sitio web nos encontramos con un ...




# Usuario XXX


# Escalada de privilegios

Haciendo una investigación a nivel local del sistema ...