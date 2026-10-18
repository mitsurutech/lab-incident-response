# Laboratorio de respuesta a incidentes y análisis forense: investiga un servidor Linux comprometido

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml)

Un **laboratorio de respuesta a incidentes y análisis forense digital** listo para el aula, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde escuelas y centros dan a cada alumno su propia máquina y su propio terminal en el navegador. Cada alumno recibe su propia máquina virtual Ubuntu con **los registros y los archivos de un servidor web comprometido**: un registro de SSH con un ataque de fuerza bruta, un registro de accesos del servidor web lleno de escáneres, el archivo de cuentas, los crontabs y una lista de fechas de modificación de archivos. Encuentra **la dirección IP del atacante, la cuenta que fue vulnerada, la hora del primer inicio de sesión y la persistencia que dejó el intruso**, y luego escribe lo que ha hallado en `answers.txt`. No se instala nada en el ordenador del alumno.

**No hay malware ni código de explotación.** Todo es texto, que se lee con las herramientas que tiene cualquier máquina Linux: `grep`, `awk`, `sort`, `uniq`, `cut`, `less`. El caso tiene señuelos a propósito: la dirección con más inicios de sesión fallidos nunca llega a entrar, un administrador se equivoca al escribir la contraseña y después acierta, y un usuario legítimo y una clave SSH antigua conviven con los cambios del intruso, así que un `grep` perezoso no lo resuelve y una lectura atenta de la cronología sí.

## Qué hace el alumno

1. Lee el README que el laboratorio deja en la carpeta personal: nombra las pruebas y lo que hay que encontrar, y no da ninguna de las respuestas.
2. Repasa `case/var/log/auth.log` y `case/var/log/nginx/access.log` para distinguir el ataque del ruido y encontrar la dirección y la cuenta.
3. Averigua la hora del primer inicio de sesión correcto desde esa dirección.
4. Mira los crontabs, las cuentas, las claves y `case/mtimes.txt` para encontrar lo que dejó el intruso, en qué archivo está y qué programa sigue ejecutando.
5. Escribe las seis líneas con etiqueta en `answers.txt`.

## Cómo se califica

El laboratorio tiene una **comprobación**, un script que califica una máquina virtual entregada sin que el profesor tenga que leerla: lee `answers.txt` (las mayúsculas y los espacios no importan) y compara cada respuesta con el caso, nombrando **el primer campo que está mal y sin decir nunca cuál es la respuesta**. También rechaza un caso cuyos archivos de pruebas se hayan cambiado para encajar con una respuesta. El caso es el mismo para todos los alumnos, generado a partir de una semilla fija cuando la máquina virtual arranca por primera vez.

Un alumno tiene `sudo` en su propia máquina virtual, así que quien lea los archivos de root puede encontrar la clave. Se acepta: el laboratorio es práctica, y lo que se califica es la respuesta escrita.

## Qué hay en el archivo

`incident-response.lab.yaml` es una **definición de laboratorio de máquina virtual** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la máquina virtual de cada alumno: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es el terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la máquina virtual de cada alumno: crea el caso, escribe el README y un `answers.txt` vacío, e instala Python desde el archivo de Ubuntu solo si la imagen no lo tiene |
| `check` | el script de calificación, ejecutado como root sobre una copia del disco guardado tras un arranque en limpio; su estado de salida es el resultado y `0` significa aprobado |

## Usarlo en la plataforma

**Para ponerlo como trabajo a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio de máquina virtual Ubuntu**.
2. Pega el contenido de `incident-response.lab.yaml`, o pulsa el ejemplo si tu plataforma lo ofrece. Usar un ejemplo sustituye el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada alumno recibe una máquina virtual propia, que se conserva entre sesiones.
4. Asígnalo como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un alumno en la lista de la clase: arranca una copia de su máquina guardada que no se vuelve a guardar, y muestra si aprobó y qué imprimió.

La [página de este laboratorio](https://mitsurutech.co.uk/es/help/lab-incident-response) del manual de ayuda, su [guía de los laboratorios de máquina virtual Ubuntu](https://mitsurutech.co.uk/es/help/labs) y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) cuentan el resto. Para crear tu propio caso, cambia el generador en `setup`: la semilla, las direcciones, la cuenta y los archivos que escribe.

¿Aún no tienes una cuenta de centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) para hasta 50 alumnos, y [registrarse](https://mitsurutech.co.uk/es/signup) solo pide una dirección de correo electrónico.

## Comprobación del archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es YAML válido con las cinco claves de arriba, que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma y que el script de calificación es Python válido. No arranca ninguna máquina virtual: eso se hace en las máquinas de la propia plataforma.

## Qué no funciona en un laboratorio

La máquina virtual llega al archivo de paquetes de Ubuntu, a PyPI, a npm y a GitHub, y a nada más, así que un laboratorio no puede apuntar a un servidor de internet. Es una sola máquina virtual con un conjunto de registros que leer: no hay un atacante en vivo que observar ni una segunda máquina que atacar.

## Acerca de

Hecho por [Mitsuru Technologies](https://mitsurutech.co.uk/es) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para escuelas y centros, con laboratorios de contenedores, laboratorios de máquina virtual, aplicaciones web desplegadas desde GitHub y cuestionarios que se califican solos. Prueba código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) y [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`.

Todas las direcciones del caso pertenecen a un rango reservado para documentación o a un rango privado, y nada en él son datos reales.
