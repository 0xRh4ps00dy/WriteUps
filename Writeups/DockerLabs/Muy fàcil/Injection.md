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

```

En el sitio web nos encontramos con una panel de inicio de sesión. Comprobando los campos del formulario si contienen una posible vulnerabilidad SQLi, comprobamos que el campo de usuario es vulnerable.





# Foothold




# Escalada de privilegios



