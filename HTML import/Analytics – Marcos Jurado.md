  

# Por favor, espera. Copia en progreso...

If you’re making a lot of copies it can take a while  
(up to 5 minutes if you’re on a slow server).

El tiempo promedio es de 8 copias por segundo.

![Revisit consent button](HTML%20import/Attachments/revisit%203.svg)

We value your privacy

We use cookies to enhance your browsing experience, serve personalized ads or content, and analyze our traffic. By clicking "Accept All", you consent to our use of cookies.

Customize Reject All Accept All

Customize Consent Preferences ![Close](HTML%20import/Attachments/close%203.svg)

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
    
- [Copiar esta](https://marcosjurado.com/htb-analytics/#)
- [Editar la entrada](https://marcosjurado.com/wp-admin/post.php?post=352&action=edit)
- [Site Kit](https://marcosjurado.com/htb-analytics/#)
    
    El plugin Site Kit by Google necesita que esté activado JavaScript en tu navegador.
    
- [
    
    ![Estadísticas](HTML%20import/Attachments/admin%203.php "Visitas de 48 horas. Haz clic para ver más estadísticas del sitio.")
    
    ](https://marcosjurado.com/wp-admin/admin.php?page=stats)

- [Avisos](https://wordpress.com/notifications)
    
    Avisos
    
- [Hola, marcosjurado![](./Analytics%20–%20Marcos%20Jurado_files/f2d6c89652d573300d73a9d2c4b82c14.png)](https://marcosjurado.com/wp-admin/profile.php)
    
    - [![](./Analytics%20–%20Marcos%20Jurado_files/f2d6c89652d573300d73a9d2c4b82c14(1).png)marcosjuradoEditar perfil](https://marcosjurado.com/wp-admin/profile.php)
    - [Salir](https://marcosjurado.com/wp-login.php?action=logout&_wpnonce=675479bef5)
    
- Buscar
    

[Saltar al contenido](https://marcosjurado.com/htb-analytics/#wp--skip-link--target)

[![Marcos Jurado](HTML%20import/Attachments/LogotipoH%203.png)](https://marcosjurado.com/)

- [Home](https://marcosjurado.com/)
- [Whoami](https://marcosjurado.com/whoami/)
- [WriteUps](https://marcosjurado.com/category/writeups/)
    - [Hack The Box](https://marcosjurado.com/category/writeups/hack-the-box/)
- [Tutoriales](https://marcosjurado.com/category/tutoriales/)

# Analytics

![](../Imágenes/Analytics.png)

Analytics aloja una instancia de Metabase en un servidor web. A partir de la filtración de un token gracias a una vulnerabilidad descubierta podemos inyectarlo para ejecutar código y acceder en la máquina. Accedemos dentro de un contenedor donde nos encontramos unas variables que nos ayudan a obtener acceso a la máquina host. Desde allí podemos explotar la vulnerabilidad GameOver(Lay) para escalar privilegios hacia root.

### **Enumeración**

#### Nmap

**Nmap** encuentra dos puertos TCP abiertos:

![](../Imágenes/Pasted-image-20240318200812.png)

![](../Imágenes/Pasted-image-20240318200859.png)

Podemos observar un redireccionamiento web hacia **http://analytical.htb**. Por lo tanto, lo incluimos en el archivo **/etc/hosts**.

#### Analytical.htb (HTTP 80)

El sitio web nos descubre una empresa relacionada con el análisis de datos:

![](../Imágenes/Pasted-image-20240318201022.png)

Si nos dirigimos hacia la página de login descubrimos un subdominio **data.analytical.htb**:

![](../Imágenes/Pasted-image-20240318201752.png)

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

![](../Imágenes/Pasted-image-20240318202326-1.png)

#### Explotación

Como hemos conseguido encontrar el token decidimos utilizar este [poc](https://github.com/securezeron/CVE-2023-38646) para intentar penetrar en el sistema objetivo:

![](../Imágenes/Pasted-image-20240318202908.png)

En un primer intento, el exploit nos produce un error. Por tanto, miramos de inspeccionar el código y ver si podemos adaptarlo a nuestras necesidades para que no falle y nos permita el acceso al sistema.

![](../Imágenes/Pasted-image-20240318204311.png)

Decidimos modificar el payload y finalmente conseguimos una shell en el sistema objetivo:

![](../Imágenes/Pasted-image-20240318204340.png)

### **Usuario metalytics**

Después de realizar el reconocimiento en lo que parece ser un contenedor, nos encontramos con unas variables que parecen ser unas credenciales:

![](../Imágenes/Pasted-image-20240318204449.png)

En este caso, intentamos conectarnos vía SSH con estas credenciales encontradas y conseguimos entrar en el host del sistema y la bandera **user.txt**:

![](../Imágenes/Pasted-image-20240318204558.png)

### ****Escalada de privilegios****

Después de realizar una enumeración inicial para intentar escalar privilegios y no encontrar nada útil, decidimos buscar en Google alguna vulnerabilidad relacionada con el kernel del sistema.

Por suerte encontramos que este kernel sufre la vulnerabilidad [GameOver(lay)](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629). Observando el código de uno de los exploits encontrados vemos que simplemente se ejecuta una línea de código. Por tanto, decidimos probar suerte copiando y pegando el código en el terminal:

![](../Imágenes/Pasted-image-20240318204924.png)

Con esto conseguimos escalar privilegios y la bandera **root.txt**.

![](../Imágenes/image-18%201.png)

---

##### ¡Gracias por vuestro apoyo!  
Sígueme para más contenido

---

[Hack The Box](https://marcosjurado.com/category/writeups/hack-the-box/), [Machines](https://marcosjurado.com/category/writeups/hack-the-box/machines/), [WriteUps](https://marcosjurado.com/category/writeups/)

[CVE-2023-2640](https://marcosjurado.com/tag/cve-2023-2640/), [CVE-2023-32629](https://marcosjurado.com/tag/cve-2023-32629/), [CVE-2023-38646](https://marcosjurado.com/tag/cve-2023-38646/), [env](https://marcosjurado.com/tag/env/), [feroxbuster](https://marcosjurado.com/tag/feroxbuster/), [ffuf](https://marcosjurado.com/tag/ffuf/), [gameoverlay](https://marcosjurado.com/tag/gameoverlay/), [nmap](https://marcosjurado.com/tag/nmap/)

[Marcos Jurado](https://marcosjurado.com/)

© 2024 Copyright

- [LinkedIn](https://www.linkedin.com/in/marcosjurado/)
- [X](https://twitter.com/0xRh4ps00dy)
- [GitHub](https://github.com/0xRh4ps00dy)

 

![Imagen copiada correctamente](./Analytics%20–%20Marcos%20Jurado_files/copy.png)

![happy face](HTML%20import/Attachments/smile%203.svg)

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

        

![](HTML%20import/Attachments/htb-analytics.png)

![](data:image/gif;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=)

                                     

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