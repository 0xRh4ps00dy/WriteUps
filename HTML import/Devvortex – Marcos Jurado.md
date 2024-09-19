  

# Por favor, espera. Copia en progreso...

If you’re making a lot of copies it can take a while  
(up to 5 minutes if you’re on a slow server).

El tiempo promedio es de 8 copias por segundo.

![Revisit consent button](HTML%20import/Attachments/revisit%202.svg)

We value your privacy

We use cookies to enhance your browsing experience, serve personalized ads or content, and analyze our traffic. By clicking "Accept All", you consent to our use of cookies.

Customize Reject All Accept All

Customize Consent Preferences ![Close](HTML%20import/Attachments/close%202.svg)

We use cookies to help you navigate efficiently and perform certain functions. You will find detailed information about all cookies under each consent category below.

The cookies that are categorized as "Necessary" are stored on your browser as they are essential for enabling the basic functionalities of the site. ... Show more

NecessaryAlways Active

Necessary cookies are required to enable the basic features of this site, such as providing secure log-in or adjusting your consent preferences. These cookies do not store any personally identifiable data.

No cookies to display.

Functional

Functional cookies help perform certain functionalities like sharing the content of the website on social media platforms, collecting feedback, and other third-party features.

No cookies to display.

Analytics

Analytical cookies are used to understand how visitors interact with the website. These cookies help provide information on metrics such as the number of visitors, bounce rate, traffic source, etc.

No cookies to display.

Performance

Performance cookies are used to understand and analyze the key performance indexes of the website which helps in delivering a better user experience for the visitors.

No cookies to display.

Advertisement

Advertisement cookies are used to provide visitors with customized advertisements based on the pages you visited previously and to analyze the effectiveness of the ad campaigns.

No cookies to display.

Reject All Save My Preferences Accept All

- [Acerca de WordPress](https://marcosjurado.com/wp-admin/about.php)
    
    - [Acerca de WordPress](https://marcosjurado.com/wp-admin/about.php)
    - [Únete](https://marcosjurado.com/wp-admin/contribute.php)
    
    - [WordPress.org](https://es.wordpress.org/)
    - [Documentación](https://wordpress.org/documentation/)
    - [Aprende WordPress](https://learn.wordpress.org/)
    - [Soporte](https://es.wordpress.org/support/)
    - [Sugerencias](https://es.wordpress.org/support/forum/comunidad/peticiones-y-feedback/)
    
- [Marcos Jurado](https://marcosjurado.com/wp-admin/)
    
    - [Escritorio](https://marcosjurado.com/wp-admin/)
    - [Plugins](https://marcosjurado.com/wp-admin/plugins.php)
    
    - [Temas](https://marcosjurado.com/wp-admin/themes.php)
    
- [Editar sitio](https://marcosjurado.com/wp-admin/site-editor.php?postType=wp_template&postId=twentytwentythree//single&canvas=edit)
- [33 actualizaciones disponibles](https://marcosjurado.com/wp-admin/update-core.php)
- [00 comentarios en moderación](https://marcosjurado.com/wp-admin/edit-comments.php)
- [Añadir](https://marcosjurado.com/wp-admin/post-new.php)
    
    - [Entrada](https://marcosjurado.com/wp-admin/post-new.php)
    - [Medio](https://marcosjurado.com/wp-admin/media-new.php)
    - [Página](https://marcosjurado.com/wp-admin/post-new.php?post_type=page)
    - [Página de destino](https://marcosjurado.com/wp-admin/edit.php?action=elementor_new_post&post_type=e-landing-page&template_type=landing-page&_wpnonce=34713479b4#library)
    - [Plantilla](https://marcosjurado.com/wp-admin/post-new.php?post_type=elementor_library)
    - [Usuario](https://marcosjurado.com/wp-admin/user-new.php)
    
- [Copiar esta](https://marcosjurado.com/devvortex/#)
- [Editar la entrada](https://marcosjurado.com/wp-admin/post.php?post=233&action=edit)
- [Site Kit](https://marcosjurado.com/devvortex/#)
    
    El plugin Site Kit by Google necesita que esté activado JavaScript en tu navegador.
    
- [
    
    ![Estadísticas](HTML%20import/Attachments/admin%202.php "Visitas de 48 horas. Haz clic para ver más estadísticas del sitio.")
    
    ](https://marcosjurado.com/wp-admin/admin.php?page=stats)

- [Avisos](https://wordpress.com/notifications)
    
    Avisos
    
- [Hola, marcosjurado![](./Devvortex%20–%20Marcos%20Jurado_files/f2d6c89652d573300d73a9d2c4b82c14.png)](https://marcosjurado.com/wp-admin/profile.php)
    
    - [![](./Devvortex%20–%20Marcos%20Jurado_files/f2d6c89652d573300d73a9d2c4b82c14(1).png)marcosjuradoEditar perfil](https://marcosjurado.com/wp-admin/profile.php)
    - [Salir](https://marcosjurado.com/wp-login.php?action=logout&_wpnonce=675479bef5)
    
- Buscar
    

[Saltar al contenido](https://marcosjurado.com/devvortex/#wp--skip-link--target)

[![Marcos Jurado](HTML%20import/Attachments/LogotipoH%202.png)](https://marcosjurado.com/)

- [Home](https://marcosjurado.com/)
- [Whoami](https://marcosjurado.com/whoami/)
- [WriteUps](https://marcosjurado.com/category/writeups/)
    - [Hack The Box](https://marcosjurado.com/category/writeups/hack-the-box/)
- [Tutoriales](https://marcosjurado.com/category/tutoriales/)

# Devvortex

![](HTML%20import/Attachments/Devvortex.png)

Devvortex es una máquina con un sistema Linux el cual debemos iniciar el ataque realizando fuerza bruta de subdominios para encontrar un subdominio que contiene un CMS vulnerable a un CVE. Esta vulnerabilidad nos permite obtener unas credenciales para acceder al panel de administrador del CMS y obtener una reverse shell que nos dará acceso al sistema objetivo. Una vez dentro debemos hacer un movimiento lateral hacia otro usuario del sistema mediante la recuperación y desencriptación de un hash que encontramos en la base de datos MYSQL que hay en el sistema. Finalmente, para conseguir la escalada de privilegios debemos de abusar de los permisos otorgados a un binario.

### **Reconocimiento**

#### Nmap

El escaneo de puertos nos arroja la siguiente información:

![](HTML%20import/Attachments/Selection_016-2.png)

Utilizando esta información, mi evaluación inicial es:

- Servicio **SSH** en el puerto 22 TCP. Puede ser útil en el futuro si se encuentran credenciales o se pueden generar claves después de obtener un punto de apoyo.
    
- Servicio **HTTP** alojado en el puerto 80 TCP corriendo bajo Nginx 1.18.0 con un redireccionamiento hacia http://devortex.htb. Se puede comprobar el sitio web y si no encontramos ninguna vulnerabilidad podemos enumerar subdirectorios y/o subdominios mediante fuerza bruta.
    

#### Website (80 TCP Port)

Añadimos devortex.htb en el fichero /etc/hosts y accedemos a la dirección mediante el navegador:

![](HTML%20import/Attachments/Screenshot-2024-04-17-at-20-16-01-DevVortex-scaled.jpg)

Por el momento no vemos nada interesante.

##### Fuerza bruta de directorios

Ahora es una buena opción intentar encontrar algún subdirectorio. Esto lo podemos hacer mediante alguna herramienta como **`ffuf`**:

```

```

Los resultados tampoco nos muestran nada interesante.

##### Fuerza bruta de subdominios

Ahora podemos realizar una fuerza bruta de subdominios con **wfuzz**:

![](HTML%20import/Attachments/Selection_019-2.png)

Los resultados nos muestran la existencia de un subdominio: `**dev.devvortex.htb**`.

Añadimos el subdominio en el fichero /etc/hosts y navegamos hacia él para ver que nos encontramos:

![](HTML%20import/Attachments/Screenshot-2024-04-17-at-20-30-25-Devvortex-scaled.jpg)

Tampoco vemos nada interesante, pero sí vemos que es un sitio web desarrollado con Joomla:

![](HTML%20import/Attachments/Selection_021.png)

##### Fuerza bruta de directorios en subdominios

Ahora probemos a realizar fuerza bruta de directorios, pero esta vez en el subdominio descubierto:

![](HTML%20import/Attachments/Selection_022.png)

Encontramos el panel de inicio de sesión en el directorio **administrator**:

![](HTML%20import/Attachments/Screenshot-2024-04-17-at-20-42-49-Development-Administration.png)

### **Foothold**

#### Joomla Version

Ahora sería interesante tratar de buscar la versión de Joomla para saber si existe una vulnerabilidad relacionada con dicha versión que podamos usar para ganar acceso al sistema objetivo.

Según este [sitio web](https://hackertarget.com/attacking-enumerating-joomla/), podemos intentar buscar la versión de Joomla en las siguientes direcciones:

- /administrator/manifests/files/joomla.xml
- /language/en-GB/en-GB.xml

```

```

Con esta información podemos concretar que estamos enfrente de la versión 4.2.6.

#### Identificar la vulnerabilidad

Buscando en Google encontramos que la versión de Joomla tiene una vulnerabilidad, [CVE-2023-23752](https://nvd.nist.gov/vuln/detail/CVE-2023-23752). Según NVD, esta versión de Joomla permite el acceso no autorizado a puntos finales del servicio web gracias a una comprobación de acceso incorrecta.

![](HTML%20import/Attachments/Screenshot-2024-04-18-at-12-16-41-jomla-4.2.6-exploit-Buscar-con-Google.png)

#### Prueba de concepto

Esta vulnerabilidad es fácil de explotar utilizando este [exploit](https://www.exploit-db.com/exploits/51334). Descargamos el exploit y lo ejecutamos:

```

```

Encontramos unas credenciales de la base de datos MYSQL.

Usando estas credenciales en el panel de administrador de Joomla conseguimos tener acceso al CMS.

#### Explotación

Una vez dentro del sistema es muy fácil obtener acceso al sistema, solo tenemos que reemplazar un archivo de alguna plantilla del CMS, preparar un listener de **netcat** y navegar hacia ella:

```

```

Una vez conseguimos el punto de apoyo en el sistema objetivo es buen momento para estabilizar la terminal e intentar buscar la bandera user.txt. En este caso hemos accedido al sistema como usuario www-data y no podemos leer la bandera que está en la carpeta del usuario Logan. Vamos a intentar hacer un movimiento lateral hacia el usuario Logan.

![](HTML%20import/Attachments/Selection_027.png)

###  **Movimiento lateral**

En primer lugar, intentemos conectarnos a la base de datos MYSQL e intentar recopilar algún hash para desencriptarlo:

![](HTML%20import/Attachments/Selection_028.png)

 ![](HTML%20import/Attachments/Selection_029.png)

```

```

Intentemos desencriptarlo usando **hashcat:**

```

```

Finalmente, podemos obtener acceso al sistema objetivo mediante **SSH** y como usuario Logan, el cual ya podemos leer la bandera user.txt:

```

```

### **Escalada de privilegios  
**

#### Identificar la vulnerabilidad

Comprobamos los permisos sudo sobre el usuario **Logan** y encontramos que puede ejecutar el binario **/usr/bin/apport-cli** con privilegios:

![](HTML%20import/Attachments/Selection_034.png)

#### Proof of Concept

Investigando en Google encontramos que el binario **apport-cli** tiene una vulnerabilidad llamada [CVE-2023-1326](https://nvd.nist.gov/vuln/detail/CVE-2023-1326):

![](HTML%20import/Attachments/Selection_046.png)

La siguiente prueba de concepto nos puede proporcionar un terminal con privilegios root:

```
The apport-cli supports view a crash. These features invoke the default
pager, which is likely to be less, other functions may apply.

It can be used to break out from restricted environments by spawning an
interactive system shell. If the binary is allowed to run as superuser
by sudo, it does not drop the elevated privileges and may be used to
access the file system, escalate or maintain privileged access.

apport-cli should normally not be called with sudo or pkexec. In case it
is called via sudo or pkexec execute `sensible-pager` as the original
user to avoid privilege elevation.

Proof of concept:

$ sudo apport-cli -c /var/crash/xxx.crash
[...]
Please choose (S/E/V/K/I/C): v
!id
uid=0(root) gid=0(root) groups=0(root)
!done  (press RETURN)

This fixes [CVE-2023-1326](https://github.com/advisories/GHSA-qgrc-7333-5cgx "CVE-2023-1326").

Bug: [https://launchpad.net/bugs/2016023](https://launchpad.net/bugs/2016023)
Signed-off-by: Benjamin Drung <benjamin.drung@canonical.com>
```

#### Explotación

En primer lugar, para realizar la explotación de esta vulnerabilidad y obtener un terminal con privilegios debemos ejecutar la aplicación y seleccionar que queremos reportar un problema referente a la pantalla:

```

```

Entonces, indicamos que tipo de problemas hemos observado, por ejemplo, podemos indicar que hemos observado congelaciones de la pantalla durante el arranque o el uso del sistema:

`![](HTML%20import/Attachments/Selection_041.png)`

Ahora, debemos indicar que queremos ver el informe y una vez el binario nos muestre toda la información del informe nos aparecerán dos puntos (:).

`![](HTML%20import/Attachments/Selection_045.png)`

![](HTML%20import/Attachments/Selection_042.png)

En este momento debemos escribir el signo de exclamación (!) y pulsar la tecla Enter:

`![](HTML%20import/Attachments/Selection_043.png)`

Con esto, conseguimos obtener el terminal con privilegios y leer la bandera root.txt:

```

```

![](HTML%20import/Attachments/image-12.png)

---

##### ¡Gracias por vuestro apoyo!  
Sígueme para más contenido

---

[Hack The Box](https://marcosjurado.com/category/writeups/hack-the-box/), [Machines](https://marcosjurado.com/category/writeups/hack-the-box/machines/), [WriteUps](https://marcosjurado.com/category/writeups/)

[abusing sudo](https://marcosjurado.com/tag/abusing-sudo/), [apport-cli](https://marcosjurado.com/tag/apport-cli/), [cms](https://marcosjurado.com/tag/cms/), [CVE-2023-1326](https://marcosjurado.com/tag/cve-2023-1326/), [CVE-2023-23752](https://marcosjurado.com/tag/cve-2023-23752/), [ffuf](https://marcosjurado.com/tag/ffuf/), [hashcat](https://marcosjurado.com/tag/hashcat/), [joomla](https://marcosjurado.com/tag/joomla/), [mysql](https://marcosjurado.com/tag/mysql/), [netcat](https://marcosjurado.com/tag/netcat/), [nmap](https://marcosjurado.com/tag/nmap/), [wfuzz](https://marcosjurado.com/tag/wfuzz/)

[Marcos Jurado](https://marcosjurado.com/)

© 2024 Copyright

- [LinkedIn](https://www.linkedin.com/in/marcosjurado/)
- [X](https://twitter.com/0xRh4ps00dy)
- [GitHub](https://github.com/0xRh4ps00dy)

 

![Imagen copiada correctamente](./Devvortex%20–%20Marcos%20Jurado_files/copy.png)

![happy face](HTML%20import/Attachments/smile%202.svg)

¡La copia ha funcionado!

Sin embargo, notamos cierto potencial de optimización en tu servidor.

**Por favor**, copia los siguientes registros [en el foro](https://wordpress.org/support/plugin/copy-delete-posts/#new-topic-0) para que podamos hacer el plugin aún mejor (¡gratis!)

The OS: Linux PHP Version: 7.4.28 WP Version: 6.6.2 MySQL Version: 8.0.39 Directory Separator: / Copy logs: 21-04-2024 08:00:22 - 1x, [total: 0.15213584899902, avg: 0.15213584899902] (mem: 11.64 MB - 12201648, peak: 2 MB - 2097152) 21-04-2024 07:56:31 - 1x, [total: 0.21652412414551, avg: 0.21652412414551] (mem: 14.55 MB - 15259640, peak: 8 MB - 8388608) 20-04-2024 07:51:41 - 1x, [total: 0.08299708366394, avg: 0.08299708366394] (mem: 11.61 MB - 12176984, peak: 4 MB - 4194304) 08-04-2024 08:04:41 - 1x, [total: 0.096487998962402, avg: 0.096487998962402] (mem: 11.53 MB - 12088496, peak: 4 MB - 4194304)

Copiar los registros

[

Ir al foro

](https://wordpress.org/support/plugin/copy-delete-posts/#new-topic-0)

¿Problemas para acceder aquí?

No, no quiero ayudarte a mejorar el plugin.

De acuerdo, ¡hecho!

                                              

## Elementos a copiar:

Usar como ajustes base

–– Seleccionar –– Seleccionar todo Iniciar de nuevo Personalizado Por defecto

Seleccionar todo

- –– Seleccionar ––
- Seleccionar todo
- Iniciar de nuevo
- Personalizado
- Por defecto

 Título Fecha  Estado  Slug

 Extracto Contenido  Imagen destacada  Plantilla

 Formato Autor  Contraseña  Secundarias

 Comentarios Orden del menú  Adjuntos  Categorías

 Etiquetas Taxonomías  Menús de navegación  Categorías de enlaces

**Opciones del plugin:**  
 Todos los meta de las entradas

Copiar 

 veces

 to

este sitio

este sitio

- este sitio

Hacer más de 50 copias tardará un tiempo. Dependiendo de tu servidor.

¡Copiarlo!