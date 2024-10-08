#tag

![](../../../Images/Pasted%20image%2020241008071742.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH i un servicio HTTP funcionando.

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


```

## Website (80 TCP Port)

En el sitio web nos encontramos con un ...




# Usuario XXX


# Escalada de privilegios

Haciendo una investigación a nivel local del sistema ...