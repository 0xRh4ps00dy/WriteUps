<!-- wp:image {"id":262,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://marcosjurado.com/wp-content/uploads/2024/03/Devvortex-1024x832.png" alt="" class="wp-image-262"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Devvortex es una máquina con un sistema Linux el cual debemos iniciar el ataque realizando fuerza bruta de subdominios para encontrar un subdominio que contiene un CMS vulnerable a un CVE. Esta vulnerabilidad nos permite obtener unas credenciales para acceder al panel de administrador del CMS y obtener una reverse shell que nos dará acceso al sistema objetivo. Una vez dentro debemos hacer un movimiento lateral hacia otro usuario del sistema mediante la recuperación y desencriptación de un hash que encontramos en la base de datos MYSQL que hay en el sistema. Finalmente, para conseguir la escalada de privilegios debemos de abusar de los permisos otorgados a un binario.</p>
<!-- /wp:paragraph -->

<h3><strong>Reconocimiento</strong></h3>
<h4>Nmap</h4>
<p>El escaneo de puertos nos arroja la siguiente información:</p>
<p><img class="alignnone wp-image-506 size-full" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_016-2.png" alt="" width="858" height="557" /></p>
<p>Utilizando esta información, mi evaluación inicial es:</p>
<ul>
<li>
<p>Servicio <strong>SSH</strong> en el puerto 22 TCP. Puede ser útil en el futuro si se encuentran credenciales o se pueden generar claves después de obtener un punto de apoyo.</p>
</li>
<li>
<p>Servicio <strong>HTTP</strong> alojado en el puerto 80 TCP corriendo bajo Nginx 1.18.0 con un redireccionamiento hacia http://devortex.htb. Se puede comprobar el sitio web y si no encontramos ninguna vulnerabilidad podemos enumerar subdirectorios y/o subdominios mediante fuerza bruta.</p>
</li>
</ul>
<h4>Website (80 TCP Port)</h4>
<p>Añadimos devortex.htb en el fichero /etc/hosts y accedemos a la dirección mediante el navegador:</p>
<p><img class="wp-image-508 size-full aligncenter" src="https://marcosjurado.com/wp-content/uploads/2024/04/Screenshot-2024-04-17-at-20-16-01-DevVortex-scaled.jpg" alt="" width="550" height="2560" /></p>
<p>Por el momento no vemos nada interesante.</p>
<h5>Fuerza bruta de directorios</h5>
<p>Ahora es una buena opción intentar encontrar algún subdirectorio. Esto lo podemos hacer mediante alguna herramienta como <strong><code>ffuf</code></strong>:</p>
<pre><code><img class="alignnone wp-image-509 size-full" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_017-2.png" alt="" width="1463" height="448" /><br /></code></pre>
<p>Los resultados tampoco nos muestran nada interesante.</p>
<h5>Fuerza bruta de subdominios</h5>
<p>Ahora podemos realizar una fuerza bruta de subdominios con <strong>wfuzz</strong>:</p>
<p><code></code><img class="alignnone size-full wp-image-511" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_019-2.png" alt="" width="1236" height="415" /></p>
<p>Los resultados nos muestran la existencia de un subdominio: <code><strong>dev.devvortex.htb</strong></code>.</p>
<p>Añadimos el subdominio en el fichero /etc/hosts y navegamos hacia él para ver que nos encontramos:</p>
<p><img class="size-full wp-image-512 aligncenter" src="https://marcosjurado.com/wp-content/uploads/2024/04/Screenshot-2024-04-17-at-20-30-25-Devvortex-scaled.jpg" alt="" width="531" height="2560" /></p>
<p>Tampoco vemos nada interesante, pero sí vemos que es un sitio web desarrollado con Joomla:</p>
<p><img class="alignnone size-full wp-image-516" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_021.png" alt="" width="1257" height="623" /></p>
<h5>Fuerza bruta de directorios en subdominios</h5>
<p>Ahora probemos a realizar fuerza bruta de directorios, pero esta vez en el subdominio descubierto:</p>
<p><img class="alignnone size-full wp-image-517" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_022.png" alt="" width="1237" height="968" /></p>
<p>Encontramos el panel de inicio de sesión en el directorio <strong>administrator</strong>:</p>
<p><img class="alignnone size-full wp-image-518" src="https://marcosjurado.com/wp-content/uploads/2024/04/Screenshot-2024-04-17-at-20-42-49-Development-Administration.png" alt="" width="1265" height="1250" /></p>
<h3><strong>Foothold</strong></h3>
<h4>Joomla Version</h4>
<p>Ahora sería interesante tratar de buscar la versión de Joomla para saber si existe una vulnerabilidad relacionada con dicha versión que podamos usar para ganar acceso al sistema objetivo.</p>
<p>Según este <a href="https://hackertarget.com/attacking-enumerating-joomla/">sitio web</a>, podemos intentar buscar la versión de Joomla en las siguientes direcciones:</p>
<ul>
<li>/administrator/manifests/files/joomla.xml</li>
<li>/language/en-GB/en-GB.xml</li>
</ul>
<pre><code></code><img class="alignnone size-full wp-image-522" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_023.png" alt="" width="1256" height="1313" /></pre>
<p>Con esta información podemos concretar que estamos enfrente de la versión 4.2.6.</p>
<h4>Identificar la vulnerabilidad</h4>
<p>Buscando en Google encontramos que la versión de Joomla tiene una vulnerabilidad, <a href="https://nvd.nist.gov/vuln/detail/CVE-2023-23752">CVE-2023-23752</a>. Según NVD, esta versión de Joomla permite el acceso no autorizado a puntos finales del servicio web gracias a una comprobación de acceso incorrecta.</p>
<p><img class="alignnone size-full wp-image-523" src="https://marcosjurado.com/wp-content/uploads/2024/04/Screenshot-2024-04-18-at-12-16-41-jomla-4.2.6-exploit-Buscar-con-Google.png" alt="" width="1265" height="1250" /></p>
<h4>Prueba de concepto</h4>
<p>Esta vulnerabilidad es fácil de explotar utilizando este <a href="https://www.exploit-db.com/exploits/51334">exploit</a>. Descargamos el exploit y lo ejecutamos:</p>
<pre><code></code><img class="alignnone size-full wp-image-525" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_024.png" alt="" width="575" height="389" /></pre>
<p>Encontramos unas credenciales de la base de datos MYSQL.</p>
<p>Usando estas credenciales en el panel de administrador de Joomla conseguimos tener acceso al CMS.</p>
<h4>Explotación</h4>
<p>Una vez dentro del sistema es muy fácil obtener acceso al sistema, solo tenemos que reemplazar un archivo de alguna plantilla del CMS, preparar un listener de <strong>netcat</strong> y navegar hacia ella:</p>
<pre><code><img class="alignnone size-full wp-image-527" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_026.png" alt="" width="1260" height="1239" /></code></pre>
<p>Una vez conseguimos el punto de apoyo en el sistema objetivo es buen momento para estabilizar la terminal e intentar buscar la bandera user.txt. En este caso hemos accedido al sistema como usuario www-data y no podemos leer la bandera que está en la carpeta del usuario Logan. Vamos a intentar hacer un movimiento lateral hacia el usuario Logan.</p>
<pre><img class="alignnone size-full wp-image-528" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_027.png" alt="" width="704" height="218" /></pre>
<h3><strong> Movimiento lateral<br /></strong></h3>
<p>En primer lugar, intentemos conectarnos a la base de datos MYSQL e intentar recopilar algún hash para desencriptarlo:</p>
<p><img class="alignnone size-full wp-image-529" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_028.png" alt="" width="719" height="277" /></p>
<p> <img class="alignnone size-full wp-image-532" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_029.png" alt="" width="613" height="276" /></p>
<pre><code><img class="alignnone size-full wp-image-531" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_030.png" alt="" width="791" height="1254" /><br /><br /><br /><img class="alignnone size-full wp-image-530" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_031.png" alt="" width="1247" height="481" /><br /><br /></code>Estamos de suerte y encontramos el hash del usuario Logan: <br /><br /><code>$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12</code></pre>
<p>Intentemos desencriptarlo usando <strong>hashcat:</strong></p>
<pre><code><img class="alignnone size-full wp-image-533" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_032.png" alt="" width="1243" height="1133" /></code></pre>
<p>Finalmente, podemos obtener acceso al sistema objetivo mediante <strong>SSH</strong> y como usuario Logan, el cual ya podemos leer la bandera user.txt:</p>
<pre><code><img class="alignnone size-full wp-image-534" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_033.png" alt="" width="1063" height="655" /></code></pre>
<h3><strong>Escalada de privilegios<br /></strong></h3>
<h4>Identificar la vulnerabilidad</h4>
<p>Comprobamos los permisos sudo sobre el usuario <strong>Logan</strong> y encontramos que puede ejecutar el binario <strong>/usr/bin/apport-cli</strong> con privilegios:</p>
<p><img class="alignnone size-full wp-image-536" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_034.png" alt="" width="891" height="151" /></p>
<h4>Proof of Concept</h4>
<p>Investigando en Google encontramos que el binario<strong> apport-cli</strong> tiene una vulnerabilidad llamada <a href="https://nvd.nist.gov/vuln/detail/CVE-2023-1326">CVE-2023-1326</a>:</p>
<p><img class="alignnone size-full wp-image-547" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_046.png" alt="" width="1257" height="911" /></p>
<p>La siguiente prueba de concepto nos puede proporcionar un terminal con privilegios root:</p>
<pre><code>The apport-cli supports view a crash. These features invoke the default
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
Signed-off-by: Benjamin Drung &lt;benjamin.drung@canonical.com&gt;</code></pre>
<h4>Explotación</h4>
<p>En primer lugar, para realizar la explotación de esta vulnerabilidad y obtener un terminal con privilegios debemos ejecutar la aplicación y seleccionar que queremos reportar un problema referente a la pantalla:</p>
<pre><code><img class="alignnone size-full wp-image-541" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_040.png" alt="" width="640" height="440" /></code></pre>
<p>Entonces, indicamos que tipo de problemas hemos observado, por ejemplo, podemos indicar que hemos observado congelaciones de la pantalla durante el arranque o el uso del sistema:</p>
<p><code><img class="alignnone size-full wp-image-540" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_041.png" alt="" width="769" height="374" /></code></p>
<p>Ahora, debemos indicar que queremos ver el informe y una vez el binario nos muestre toda la información del informe nos aparecerán dos puntos (:).</p>
<p><code><img class="alignnone size-full wp-image-543" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_045.png" alt="" width="727" height="276" /></code></p>
<p><img class="alignnone size-full wp-image-539" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_042.png" alt="" width="632" height="462" /></p>
<p>En este momento debemos escribir el signo de exclamación (!) y pulsar la tecla Enter:</p>
<p><code><img class="alignnone size-full wp-image-538" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_043.png" alt="" width="671" height="463" /></code></p>
<p>Con esto, conseguimos obtener el terminal con privilegios y leer la bandera root.txt:</p>
<pre><code><img class="alignnone size-full wp-image-537" src="https://marcosjurado.com/wp-content/uploads/2024/04/Selection_044.png" alt="" width="635" height="91" /></code></pre>

<!-- wp:image {"id":500,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://marcosjurado.com/wp-content/uploads/2024/04/image-12.png" alt="" class="wp-image-500"/></figure>
<!-- /wp:image -->

<!-- wp:separator {"style":{"spacing":{"margin":{"top":"var:preset|spacing|50","bottom":"var:preset|spacing|50"}}},"className":"is-style-wide"} -->
<hr class="wp-block-separator has-alpha-channel-opacity is-style-wide" style="margin-top:var(--wp--preset--spacing--50);margin-bottom:var(--wp--preset--spacing--50)"/>
<!-- /wp:separator -->

<!-- wp:heading {"textAlign":"center","level":5} -->
<h5 class="wp-block-heading has-text-align-center">¡Gracias por vuestro apoyo!<br>Sígueme para más contenido</h5>
<!-- /wp:heading -->