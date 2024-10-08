#tag

![](../../../Images/Pasted%20image%2020241008071813.png)
# Enumeración

## Nmap

El escaneo de **nmap** nos enseña la existencia de un servicio SSH i un servicio HTTP funcionando.

```
> nmap --open --min-rate 10000 -Pn -n -oN scans/allTCPPorts $target


```

```
> nmap -oN scans/targetedTCPPorts -sCV $target -p 22,80                       


```

## Website (80 TCP Port)

En el sitio web nos encontramos con un ...




# Usuario XXX


# Escalada de privilegios

Haciendo una investigación a nivel local del sistema ...