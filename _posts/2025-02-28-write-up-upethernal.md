---
layout: single
title: Write-Up UpEthernal
date: 2025-02-28
classes: wide
header:
  teaser: 
categories:
  - Security
tags:
  - CTF
---

# Write-Up Maquina Ethernal


**By : [https://github.com/AndrewSthephen23](https://github.com/AndrewSthephen23)**

# Reconocimiento

Primero iniciamos Nessus para poderlo ejecutar utilizaremos el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image.png)

Creamos nuestro directorio de trabajo:

![image.png](/assets/images/write-up-upethernal/image%201.png)

Hacemos un reconocimiento de la red con el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%202.png)

Vemos la ip de la maquina que queremos analizar es `192.168.204.132`.

Ahora haremos una comprobacion con el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%203.png)

Descubrimos a la maquina Ethernal que tiene un sistema operativo Windows.

> Scripts con ping
> 
1. `ping -c 1 192.168.204.132`
    
    ![image.png](/assets/images/write-up-upethernal/image%204.png)
    
    - **`ping`**: Comando utilizado para verificar la conectividad de red con un host.
    - **`-c 1`**: Opción que indica que solo se debe enviar un paquete (1) de solicitud de eco.
    - **`192.168.204.132`**: Dirección IP del host al que se desea hacer ping.
2. `ping -c 1 192.168.204.132 | grep -oE "ttl=[0-9]{2,3}”`
    
    ![image.png](/assets/images/write-up-upethernal/image%205.png)
    
    - **`ping -c 1 192.168.204.132`**: Envía un solo paquete de ping a la dirección IP 192.168.204.132.
    - **`|`**: Operador de tubería que redirige la salida del comando anterior al siguiente.
    - **`grep -oE "ttl=[0-9]{2,3}"`**:
        - **`grep`**: Comando utilizado para buscar texto en la entrada.
        - `-o`: Opción que muestra solo las partes de la línea que coinciden con la expresión.
        - **`-E`**: Opción que permite usar expresiones regulares extendidas.
        - **`"ttl=[0-9]{2,3}"`**: Expresión regular que busca coincidencias de "ttl=" seguido de 2 o 3 dígitos.
        
        En resumen, este comando envía un ping y extrae la parte que muestra el valor de TTL (Time to Live) del resultado.
        
3. `ping -c 1 192.168.204.132 | grep -oE "ttl=[0-9]{2,3}" | sed s/"ttl="//g`
    
    ![image.png](/assets/images/write-up-upethernal/image%206.png)
    
    - **`|`** (Otro operador de tubería):
        - Redirige la salida del comando `grep` al siguiente comando (`sed`).
    - **`sed s/"ttl="//g`**:
        - **`sed`**: Un editor de texto que permite realizar transformaciones en flujos de texto.
        - **`s/"ttl="//g`**: Este es un comando de sustitución en `sed`:
            - **`s`**: Indica que se va a realizar una sustitución.
            - **`"ttl="//`**: Especifica que se busca el texto "ttl=" y se reemplaza por una cadena vacía.
            - **`g`**: Esta opción indica que la sustitución se debe realizar de manera global en toda la línea, aunque en este caso solo habrá una coincidencia.
4. Script creado por mi:

![image.png](/assets/images/write-up-upethernal/image%207.png)

![image.png](/assets/images/write-up-upethernal/image%208.png)

---

Ahora haremos el scaneo de 2 vias usando el siguiente comando para ver cuantos puertos se han abierto de manera rapida, despues ya ejecutamos los demas scripts:

![image.png](/assets/images/write-up-upethernal/image%209.png)

`sudo nmap -sS -p- -T4 -v -O -A 192.168.204.132`

- **`sudo`**: Ejecuta el comando con privilegios de superusuario. Esto es necesario para realizar ciertos tipos de exploraciones de red.
- **`nmap`**: Herramienta de escaneo de red utilizada para descubrir hosts y servicios en una red, así como para realizar auditorías de seguridad.
- **`-sS`**: Realiza un escaneo de tipo SYN (escaneo sigiloso). Envía paquetes SYN para iniciar una conexión TCP, pero no completa el handshake, lo que puede ayudar a evitar la detección.
- **`-p-`**: Indica que se deben escanear todos los puertos, del 1 al 65535. El guion `` especifica que se incluyan todos los puertos.
- **`-T4`**: Configura el tiempo de espera del escaneo a "agresivo". T4 es un valor que permite realizar el escaneo más rápido, ideal en redes que no tienen una latencia alta.
- **`-v`**: Habilita el modo verbose, que proporciona más información sobre lo que está haciendo Nmap durante el escaneo.
- **`-O`**: Intenta identificar el sistema operativo del host objetivo a partir de la información obtenida durante el escaneo.
- **`-A`**: Activa la detección avanzada, que incluye la detección de versiones de servicios, sistemas operativos, y otros detalles útiles.
- **`192.168.204.132`**: Dirección IP del objetivo que se está escaneando.

Usamos una variacion:

![image.png](/assets/images/write-up-upethernal/image%2010.png)

`sudo nmap --min-rate 5000 -p- --open -n -v -Pn 192.168.204.132 -oG allports` 

- **`sudo`**: Ejecuta el comando con privilegios de superusuario, necesario para realizar escaneos de red más avanzados.
- **`nmap`**: Herramienta de escaneo de red que se usa para descubrir dispositivos y servicios en una red.
- **`--min-rate 5000`**: Establece una tasa mínima de 5000 paquetes por segundo. Esto hace que el escaneo sea más rápido, aunque puede ser más detectable.
- **`-p-`**: Indica que se deben escanear todos los puertos (del 1 al 65535).
- **`--open`**: Solo muestra los puertos que están abiertos en el resultado.
- **`-n`**: Desactiva la resolución de nombres DNS, lo que acelera el escaneo.
- **`-v`**: Modo verbose, que proporciona más información durante la ejecución del escaneo.
- **`-Pn`**: Desactiva el ping de verificación, lo que significa que nmap asumirá que el host está activo sin hacer un ping previo. Esto es útil si el host no responde a pings.
- **`192.168.204.132`**: Es la dirección IP del objetivo que se va a escanear.
- **`-oG allports`**: Guarda la salida del escaneo en un archivo en formato "grepable" llamado `allports`, lo que permite una fácil búsqueda y procesamiento posterior.

> Scripts con nmap
> 

![image.png](/assets/images/write-up-upethernal/image%2011.png)

![image.png](/assets/images/write-up-upethernal/image%2012.png)

**Escaneo de puertos**:

- Utiliza `nmap` para escanear todos los puertos de la dirección IP dada y filtra para encontrar solo los puertos abiertos.
- La salida se procesa usando varios comandos:
    - `awk` extrae la línea que contiene los puertos.
    - `tr -d '()'` elimina paréntesis.
    - `tr -d ' '` elimina espacios.
    - `tr ',' '\n'` convierte las comas en nuevas líneas.
    - Otro `awk` obtiene solo el número del puerto.
    - `tr '\n' ','` convierte nuevamente las nuevas líneas en comas.
    - `sed 's/,$//'` elimina la última coma.

---

Ahora vamos a guardar la salida del comando en un archivo .xml:

`sudo nmap -sS -p- -T4 -vv 192.168.204.132 -oG puertos.xml`

![image.png](/assets/images/write-up-upethernal/image%2013.png)

- **`-sS`**: Realiza un escaneo de tipo "SYN" (también conocido como "stealth scan"). Este método envía paquetes SYN y no completa la conexión, lo que puede ayudar a evitar la detección.
- **`-p-`**: Escanea todos los puertos desde el 1 hasta el 65535.
- **`-T4`**: Establece un modo de tiempo más agresivo, lo que acelera el escaneo. Esto es útil en redes donde no hay restricciones de velocidad.
- **`-vv`**: Modo muy verbose, que proporciona una salida detallada del proceso de escaneo.
- **`192.168.204.132`**: La dirección IP del objetivo que se va a escanear.
- **`-oG puertos.xml`**: Guarda la salida del escaneo en un archivo en formato "grepable" llamado `puertos.xml`, lo que facilita la búsqueda y el análisis posterior.

Instalamos lo siguiente para ejecutar el siguiente script: `xargs`

![image.png](/assets/images/write-up-upethernal/image%2014.png)

- **`cat puertos.xml`**: Muestra el contenido del archivo `puertos.xml`.
- **`grep -oE "[0-9]{1,5}/open"`**: Busca y extrae (debido a `-o`) todas las coincidencias de la expresión regular que representan puertos abiertos. La expresión `[0-9]{1,5}/open` coincide con cualquier número de 1 a 5 dígitos seguido de `/open`.
- **`cut -d "/" -f 1`**: Toma la salida anterior y divide cada línea usando `/` como delimitador, y selecciona el primer campo (es decir, el número del puerto).
- **`xargs`**: Toma la lista de puertos extraídos y los convierte en un solo argumento separado por espacios. Esto es útil para procesar la lista en el siguiente paso.
- **`tr " " ","`**: Reemplaza los espacios en blanco por comas, convirtiendo la lista de puertos en un formato separado por comas.

Al descubrir los puertos abiertos que se encuentran abiertos en el objetivo, vamos a realizar un escaneo especifico a los puertos para asi obtener mas informacion como las tecnologias usadas y las versiones.

![image.png](/assets/images/write-up-upethernal/image%2015.png)

- `-sV`: Habilita la detección de versiones de servicios. Nmap intentará identificar la versión exacta de los servicios que están corriendo en los puertos abiertos.
- `-p135,139,445,6646,7680,49671`: Especifica los puertos que se quieren escanear. En este caso, se están escaneando los puertos listados (por ejemplo, 135 es el puerto de DCOM, 445 es SMB).
- `-T4`: Ajusta la velocidad del escaneo. T4 es una configuración que permite un escaneo más rápido y agresivo, pero puede ser más detectable.
- `-v`: Activa el modo detallado, lo que significa que se mostrará más información sobre el escaneo en curso.
- `-O`: Intenta identificar el sistema operativo del host.
- `-A`: Activa la detección avanzada de servicios, que incluye la detección de versiones, sistema operativo, scripts y traceroute.
- `192.168.204.131`: Es la dirección IP del objetivo que se está escaneando.
- `-oA PV`: Indica que se guardará la salida del escaneo en varios formatos (normal, XML y grepable) con el prefijo "PV".

Ahora tenemos los archivos:

![image.png](/assets/images/write-up-upethernal/image%2016.png)

Ahora vamos a cambiar el tipo de archivo para visualizarlo con el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2017.png)

- `xsltproc`: Es una herramienta de línea de comandos para aplicar transformaciones XSLT a archivos XML.
- `PV.xml`: Este es el archivo XML que se va a transformar. En este caso, es el resultado del escaneo de Nmap que se guardó en formato XML.
- `o PV.html`: Esta opción especifica el nombre del archivo de salida, que en este caso será `PV.html`. Es el archivo donde se guardará el resultado de la transformación.

Abrimos el archivo html

![image.png](/assets/images/write-up-upethernal/image%2018.png)

Vemos que el sistema operativo es un **windows 7 Ultimate**

Ahora vemos usaremos los scrips de Nmap que se encuentran en la siguiente direccion:

![image.png](/assets/images/write-up-upethernal/image%2019.png)

Ahora usare el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2020.png)

- `grep`: Es una herramienta de línea de comandos que se utiliza para buscar texto dentro de archivos.
- `"categories"`: Es el patrón que `grep` buscará dentro de los archivos. En este caso, busca líneas que contengan la palabra "categories".
- `/usr/share/nmap/scripts/*.nse`: Especifica la ubicación y los archivos donde se realizará la búsqueda. Aquí, busca en todos los archivos con la extensión `.nse` dentro del directorio `/usr/share/nmap/scripts`, que es donde se encuentran los scripts de Nmap.

Ahora usare otro comando:

![image.png](/assets/images/write-up-upethernal/image%2021.png)

`| grep intrusive`: La barra vertical (`|`) se usa para canalizar la salida del primer comando como entrada al segundo. Este segundo `grep` busca específicamente las líneas que contienen la palabra "intrusive".

Ahora clasificamos por la categoria que vamos a utilizar este vez:

![image.png](/assets/images/write-up-upethernal/image%2022.png)

Filtra esos resultados para mostrar solo aquellas líneas que también mencionan "vuln". Esto es útil para identificar scripts que están relacionados con vulnerabilidades.

Ahora usaremos el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2023.png)

- `-sVC`: Esta opción combina dos flags:
- `-sV`: Detecta versiones de servicios en los puertos abiertos.
- `-C`: Realiza la ejecución de scripts de Nmap relacionados con el servicio (es una forma abreviada de `-script=default`).

Ejecutamos los siguientes comando para poder ver la informacion:

![image.png](/assets/images/write-up-upethernal/image%2024.png)

![image.png](/assets/images/write-up-upethernal/image%2025.png)

Ahora pruebo el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2026.png)

**`--script vuln`**: Activa la ejecución de scripts de Nmap que están diseñados para detectar vulnerabilidades en los servicios encontrados en los puertos especificados.
Ejecutamos los comandos para ver el archivo:

![image.png](/assets/images/write-up-upethernal/image%2027.png)

![image.png](/assets/images/write-up-upethernal/image%2028.png)

Otra herramienta para ver el analisis de vulnerabilidades es:

![image.png](/assets/images/write-up-upethernal/image%2029.png)

- **`enum4linux`**: Es una herramienta diseñada para obtener información de un sistema Windows a través de SMB (Server Message Block).
- **`192.168.204.131`**: Es la dirección IP del objetivo del que deseas obtener información.

**Funciones de `enum4linux`**

La herramienta puede extraer varios tipos de información, como:

- Usuarios y grupos del sistema.
- Comparticiones de archivos.
- Políticas de seguridad.
- Información de sesiones activas.
- Versiones de Windows y otros detalles del sistema.

# Analisis de vulnerabilidades y debilidades

Abrimos nessus

![image.png](/assets/images/write-up-upethernal/image%2030.png)

![image.png](/assets/images/write-up-upethernal/image%2031.png)

![image.png](/assets/images/write-up-upethernal/image%2032.png)

![image.png](/assets/images/write-up-upethernal/image%2033.png)

![image.png](/assets/images/write-up-upethernal/image%2034.png)

![image.png](/assets/images/write-up-upethernal/image%2035.png)

![image.png](/assets/images/write-up-upethernal/image%2036.png)

Activamos esto para equipos windows:

![image.png](/assets/images/write-up-upethernal/image%2037.png)

![image.png](/assets/images/write-up-upethernal/image%2038.png)

![image.png](/assets/images/write-up-upethernal/image%2039.png)

![image.png](/assets/images/write-up-upethernal/image%2040.png)

![image.png](/assets/images/write-up-upethernal/image%2041.png)

Con el analisis vemos que la vulnerabilidad es ***ms17-010*** mas conocida como el script que se utiliza para aprovecharse de esta exploit **Eternal-Blue.** 

Gracias a un buffer overflow se aprovecha de la conexion y lo que hace es tomar control, lo importante de esta vulnerabilidad es que no se necesita ejecutar nada la otra persona solo se necesita tener la vulnerabilidad ejecutar el control y se tomo el control automaticamente, no se toma ni como admin ni usuario sino NTSYSTEM es mas que un admin.

# Explotacion

## Manual

![image.png](/assets/images/write-up-upethernal/image%2042.png)

![image.png](/assets/images/write-up-upethernal/image%2043.png)

nos ponemos a la escucha

![image.png](/assets/images/write-up-upethernal/image%2044.png)

Ejecutamos el xploit

![image.png](/assets/images/write-up-upethernal/image%2045.png)

Ahora en la escucha ya tenemos acceso

![image.png](/assets/images/write-up-upethernal/image%2046.png)

![image.png](/assets/images/write-up-upethernal/image%2047.png)

Con este usuario podemos movernos entre los directorios del equipo y encontrar la banderas:

![image.png](/assets/images/write-up-upethernal/image%2048.png)

![image.png](/assets/images/write-up-upethernal/image%2049.png)

![image.png](/assets/images/write-up-upethernal/image%2050.png)

a63c1c39c0c7fd570053343451667939

![image.png](/assets/images/write-up-upethernal/image%2051.png)

0ef3b7d488b11e3e800f547a0765da8e

## Automatizado

Usamos el siguiente comando para buscar nuestro xploit para la vulnerabilidad

![image.png](/assets/images/write-up-upethernal/image%2052.png)

usamos ahora metasploit para hacer la busqueda.

![image.png](/assets/images/write-up-upethernal/image%2053.png)

Buscamos uno para detectar la version de SMB

![image.png](/assets/images/write-up-upethernal/image%2054.png)

Configuramos el paylod y ejecutamos

![image.png](/assets/images/write-up-upethernal/image%2055.png)

Obtuvimos la version de SMB.

Tambien podemos usar el auxiliar con el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2056.png)

Usamos un auxiliar para saber la arquitectura del SMB con el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2057.png)

Otras herramientas para poder visualizar la version de SMB serian:

![image.png](/assets/images/write-up-upethernal/image%2058.png)

- **`smbmap`**: Esta es una herramienta que permite explorar y manipular recursos compartidos de Samba en una red. Puede listar recursos compartidos, verificar permisos y más.
- **`-H 192.168.204.131`**: La opción `H` especifica la dirección IP del host al que deseas conectarte. En este caso, se está intentando conectar a un host con la IP `192.168.204.131`.
- **`-u ''`**: La opción `u` se utiliza para especificar el nombre de usuario. Aquí, las comillas vacías indican que no se está proporcionando un nombre de usuario, lo que implica que se está intentando acceder como un usuario anónimo o sin credenciales.
- **`-u 'guest'`**: La opción `-u` indica el nombre de usuario que se utilizará para la conexión. En este caso, el usuario es `guest` **(invitado)**, que a menudo se usa para acceder a recursos compartidos que permiten conexiones anónimas.

---

Seguimos con la explotacion:

Usaremos el siguiente exploit:

![image.png](/assets/images/write-up-upethernal/image%2059.png)

![image.png](/assets/images/write-up-upethernal/image%2060.png)

![image.png](/assets/images/write-up-upethernal/image%2061.png)

![image.png](/assets/images/write-up-upethernal/image%2062.png)

El comando `sysinfo` en una sesión de Meterpreter se utiliza para obtener información sobre el sistema operativo y la máquina en la que se está ejecutando el payload de Meterpreter. Aquí tienes un desglose de lo que hace:

- **`meterpreter`**: Es una herramienta de explotación dentro del framework Metasploit, que permite a los atacantes interactuar con el sistema comprometido de manera flexible y poderosa.
- **`sysinfo`**: Este comando proporciona información sobre el sistema objetivo, como:
    - Nombre del host
    - Sistema operativo (incluyendo versión y arquitectura)
    - Nombre del dominio
    - Información sobre el usuario que está conectado

![image.png](/assets/images/write-up-upethernal/image%2063.png)

Obtenemos los hashes de las contraseñas.

**`hashdump`**: Este comando extrae los hashes de las contraseñas almacenadas en el sistema. Esto es útil para obtener credenciales que pueden ser utilizadas para la autenticación en el sistema o en otros servicios.

**`netstat`**: Este comando proporciona detalles sobre las conexiones de red actuales, incluyendo:

- Direcciones IP de origen y destino
- Puertos de origen y destino
- Estado de las conexiones (por ejemplo, establecidas, cerradas, escuchando)

Partes de la salida de `hasdump`

- **Usuario**: Nombre del usuario en el sistema.
- **ID de usuario (UID)**: Un identificador único para cada usuario.
- **Hash LM**: En este caso, todos los hashes LM (Lan Manager) son `aad3b435b51404eeaad3b435b51404ee`, que indica que el usuario no tiene una contraseña establecida o que se está utilizando un formato incompatible.
- **Hash NT**: Este es el hash NT (New Technology) de la contraseña del usuario. Este hash es más seguro que el hash LM y se utiliza en sistemas modernos.
- **Campo vacío**: Los campos restantes generalmente se utilizan para información adicional, como el dominio, pero están vacíos en esta salida.
- **Guest**: Nombre de usuario.
- **501**: ID de usuario.
- **aad3b435b51404eeaad3b435b51404ee**: Hash LM.
- **31d6cfe0d16ae931b73c59d7e0c089c0**: Hash NT.

![image.png](/assets/images/write-up-upethernal/image%2064.png)

![image.png](/assets/images/write-up-upethernal/image%2065.png)

**`screenshot`**: Este comando toma una instantánea de la pantalla actual del sistema objetivo y guarda la imagen en el sistema donde se está ejecutando Meterpreter.

Tambien puedo usar el siguiente comando:

![image.png](/assets/images/write-up-upethernal/image%2066.png)

El comando `bg` en una sesión de Meterpreter se utiliza para enviar la sesión actual al fondo (background), permitiendo que continúes usando Metasploit para ejecutar otros comandos o gestionar otras sesiones sin cerrar la sesión activa de Meterpreter.

![image.png](/assets/images/write-up-upethernal/image%2067.png)

Cuando ejecutas `getuid`, Meterpreter devolverá el nombre del usuario actual, lo que te permitirá saber con qué privilegios estás operando en el sistema comprometido.

`Server user: NT AUTHORITY\SYSTEM:`

Esto indica que el payload de Meterpreter se está ejecutando con privilegios de sistema, lo cual otorga acceso completo al sistema operativo.

El comando `getpid` en una sesión de Meterpreter se utiliza para obtener el ID del proceso (PID) del proceso de Meterpreter en ejecución en el sistema objetivo.

Al ejecutar `ps`, Meterpreter te devolverá una lista de procesos activos en el sistema. La salida incluirá información sobre cada proceso, como su ID de proceso (PID), nombre de usuario que lo ejecuta, y el nombre del proceso.

---

Ahora abrimos las siguientes paginas web y pegamos los hashes [https://hashes.com/en/decrypt/hash](https://hashes.com/en/decrypt/hash), [https://crackstation.net/](https://crackstation.net/):

![image.png](/assets/images/write-up-upethernal/image%2068.png)

![image.png](/assets/images/write-up-upethernal/image%2069.png)

Por medio de mimikatz obtenemos las credenciales en texto claro

![image.png](/assets/images/write-up-upethernal/image%2070.png)

![image.png](/assets/images/write-up-upethernal/image%2071.png)

Si abrimos ahora un block de notas en eternal podemos ver el proceso que esta ejecutando:

![image.png](/assets/images/write-up-upethernal/image%2072.png)

![image.png](/assets/images/write-up-upethernal/image%2073.png)

El comando `migrate` en una sesión de Meterpreter se utiliza para mover el proceso de Meterpreter a otro proceso en el sistema objetivo. Esto es útil para mantener la persistencia y evadir detección.

El proceso de migración puede fallar si no tienes los permisos adecuados en el proceso de destino. Por ejemplo, intentar migrar a un proceso que se está ejecutando bajo un usuario con más privilegios puede no ser posible.

![image.png](/assets/images/write-up-upethernal/image%2074.png)

El comando `keyscan_start` en una sesión de Meterpreter se utiliza para iniciar la captura de las pulsaciones de teclas (keylogging) en el sistema objetivo.

# Persistencia

Primero migramos a un proceso donde esta el usuario Admin

![image.png](/assets/images/write-up-upethernal/image%2075.png)

![image.png](/assets/images/write-up-upethernal/image%2076.png)

![image.png](/assets/images/write-up-upethernal/image%2077.png)

ahora en otra consola configuro lo siguiente:

![image.png](/assets/images/write-up-upethernal/image%2078.png)

Ejecutamos y reiniciamos eternal

![image.png](/assets/images/write-up-upethernal/image%2079.png)

## Borrado de rastros

![image.png](/assets/images/write-up-upethernal/image%2080.png)

![image.png](/assets/images/write-up-upethernal/image%2081.png)

![image.png](/assets/images/write-up-upethernal/image%2082.png)

====⇒ PWNED (￣ω￣)