![](../../../Imágenes/Analytics.png)

Analytics aloja una instancia de Metabase en un servidor web. A partir de la filtración de un token gracias a una vulnerabilidad descubierta podemos inyectarlo para ejecutar código y acceder en la máquina. Accedemos dentro de un contenedor donde nos encontramos unas variables que nos ayudan a obtener acceso a la máquina host. Desde allí podemos explotar la vulnerabilidad GameOver(Lay) para escalar privilegios hacia root.

### **Enumeración**

#### Nmap

**Nmap** encuentra dos puertos TCP abiertos:

![](../../../Imágenes/Pasted-image-20240318200812.png)

![](../../../Imágenes/Pasted-image-20240318200859.png)

Podemos observar un redireccionamiento web hacia **http://analytical.htb**. Por lo tanto, lo incluimos en el archivo **/etc/hosts**.

#### Analytical.htb (HTTP 80)

El sitio web nos descubre una empresa relacionada con el análisis de datos:

![](../../../Imágenes/Pasted-image-20240318201022.png)

Si nos dirigimos hacia la página de login descubrimos un subdominio **data.analytical.htb**:

![](../../../Imágenes/Pasted-image-20240318201752.png)

Por lo tanto, también lo añadimos en **/etc/hosts** y visitamos el subdominio para descubrir una página de inicio de sesión de una aplicación llamada Metabase que sirve para el análisis de datos.

### **Usuario metabase**

#### Identificación

Investigando por internet en busca de exploits de Metabase descubrimos que existen muchas referencia al [CVE-2023-38646](https://nvd.nist.gov/vuln/detail/CVE-2023-38646). Por lo cual decidimos investigar un poco sobre la vulnerabilidad.

#### Detalles

[La siguiente publicación](https://www.assetnote.io/resources/research/chaining-our-way-to-pre-auth-rce-in-metabase-cve-2023-38646) nos arroja información sobre esta vulnerabilidad. Básicamente, Metabase utiliza un token llamado **setup-token** para ejecutar la inicialización y configuración de la aplicación. Este token debería ser borrado una vez completada la configuración, aunque después de eso este token aún permanece disponible para los usuarios no autenticados en dos lugares:

1. En el código fuente de la página de inicio de sesión.
2. En la dirección: **/api/session/properties**.

El token nos permite la ejecución de comandos arbitrarios mediante una solicitud en la dirección: **/api/setup/validate**.

Entonces decidimos intentar encontrar el token en la dirección **/api/session/properties**:

![](../../../Imágenes/Pasted-image-20240318202326-1.png)

#### Explotación

Como hemos conseguido encontrar el token decidimos utilizar este [poc](https://github.com/securezeron/CVE-2023-38646) para intentar penetrar en el sistema objetivo:

![](../../../Imágenes/Pasted-image-20240318202908.png)

En un primer intento, el exploit nos produce un error. Por tanto, miramos de inspeccionar el código y ver si podemos adaptarlo a nuestras necesidades para que no falle y nos permita el acceso al sistema.

![](../../../Imágenes/Pasted-image-20240318204311.png)

Decidimos modificar el payload y finalmente conseguimos una shell en el sistema objetivo:

![](../../../Imágenes/Pasted-image-20240318204340.png)

### **Usuario metalytics**

Después de realizar el reconocimiento en lo que parece ser un contenedor, nos encontramos con unas variables que parecen ser unas credenciales:

![](../../../Imágenes/Pasted-image-20240318204449.png)

En este caso, intentamos conectarnos vía SSH con estas credenciales encontradas y conseguimos entrar en el host del sistema y la bandera **user.txt**:

![](../../../Imágenes/Pasted-image-20240318204558.png)

### ****Escalada de privilegios****

Después de realizar una enumeración inicial para intentar escalar privilegios y no encontrar nada útil, decidimos buscar en Google alguna vulnerabilidad relacionada con el kernel del sistema.

Por suerte encontramos que este kernel sufre la vulnerabilidad [GameOver(lay)](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629). Observando el código de uno de los exploits encontrados vemos que simplemente se ejecuta una línea de código. Por tanto, decidimos probar suerte copiando y pegando el código en el terminal:

![](../../../Imágenes/Pasted-image-20240318204924.png)

Con esto conseguimos escalar privilegios y la bandera **root.txt**.

![](../../../Imágenes/image-18%201.png)
