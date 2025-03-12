---
layout: single
title: Write-Up KIO
date: 2025-02-28
classes: wide
header:
  teaser: 
categories:
  - Security
tags:
  - CTF
---

# Write-Up Maquina KIO

***By:*** AndrewSthephen23

# Configuracion de la red de las maquinas

Primero debemos tener el Kali y la maquina Kio con el mismo adaptador de red para que se puedan ver.

![image.png](/assets/images/write-up-kio/image.png)

Vemos que nuestra maquina Kali esta con un adaptador de red NAT .

![image.png](/assets/images/write-up-kio/image%201.png)

La maquina Kio tambien esta en NAT

# Conocer las direcciones IP de las maquinas

![image.png](/assets/images/write-up-kio/image%202.png)

El comando `ip a` es una forma abreviada de `ip addr`, que se utiliza en sistemas operativos Linux para mostrar información sobre las interfaces de red.

**Interface `eth0`**:

- **Estado**: `UP` (activa) y con conexión (`LOWER_UP`).
- **Dirección MAC**: `00:0c:29:87:67:f8`.
- **Dirección IPv4**: `192.168.204.128/24`, con una dirección de broadcast `192.168.204.255`.
- **Dirección IPv6**: `fe80::59ab:16f0:e16e:8e2c/64`, que es una dirección link-local.

Ahora usare el siguiente comando:

![image.png](/assets/images/write-up-kio/image%203.png)

El comando `sudo arp-scan -l` se utiliza para escanear la red local en busca de dispositivos activos:

- **`sudo`**: Ejecuta el comando con privilegios de superusuario, necesarios para acceder a ciertas funciones de red.
- **`arp-scan`**: Herramienta que envía solicitudes ARP (Address Resolution Protocol) para descubrir dispositivos en la misma red.
- **`-l`**: Indica que el escaneo debe realizarse en la red local (usualmente, la red donde está conectado el equipo).

### ¿Qué hace?:

- Envía paquetes ARP a todas las direcciones IP en la subred local.
- Recibe respuestas de los dispositivos activos, lo que permite identificar sus direcciones IP y MAC.
- Muestra una lista de dispositivos conectados, facilitando la identificación de otros equipos en la red.

En nuestra salida se muestra que el nombre de los dispositivos es **Desconocido** esto es debido a un problema de ejecutar el codigo en la direccion `/home/kali` pero si nos movemos al Escritorio.

![image.png](/assets/images/write-up-kio/image%204.png)

Vemos que ahora si nos aparece los nombres de los dispositivos.

Ahora analisemos las direcciones IP.

`192.168.204.1`: Es la direccion de la Red

`192.168.204.2`: Es donde se hace el Nateo

`192.168.204.254`: Es la ultima direccion.

El unico que no sabemos que es, seria la direccion `192.168.204.129`.

Ahora usamos el siguiente comando para identificar que es lo que esta en esa direccion.

![image.png](/assets/images/write-up-kio/image%205.png)

El comando `ping 192.168.204.129` se utiliza para verificar la conectividad entre mi equipo y el dispositivo con la dirección IP `192.168.204.129`. Con el `ping` tambien podemos llegar a identicar que sistema operativo es:

### ¿Qué hace?

- **Envía paquetes ICMP (Internet Control Message Protocol)**: Específicamente, envía "echo request" a la dirección IP especificada.
- **Espera una respuesta**: Si el dispositivo está activo y conectado a la red, responderá con "echo reply".

### Información:

- **`icmp_seq`**: Número de secuencia del paquete.
- **`ttl`**: Tiempo de vida del paquete (indica cuántos saltos ha recorrido) el numero nos indica a que SO pertenece.
- **`time`**: Tiempo que tardó en recibir la respuesta.
    - **Windows**: Valor típico de TTL: 128
    - **Linux**: Valor típico de TTL: 64
    - **macOS**: Valor típico de TTL: 64
    - **Router Cisco**: Valor típico de TTL: 255
    - **Otros dispositivos (como algunos dispositivos de red)**: Pueden usar 255 o un valor específico según la configuración.

En este caso identificamos que la maquina Kio es un ***Linux*** y su IP es `192.168.204.129`.

# Creamos nuestra carpeta de trabajo

![image.png](/assets/images/write-up-kio/image%206.png)

Donde esta la carpeta nmap haremos las ejecuciones de la herramienta `nmap` el cual es un analisador de puertos que nos puede ayudar en analisar que es lo que tenemos detras.

Ahora usaremos el siguiente comando:

![image.png](/assets/images/write-up-kio/image%207.png)

- **`-sS`**: Realiza un "SYN scan", que es un método rápido y sigiloso para determinar qué puertos están abiertos en el dispositivo. Este tipo de escaneo envía paquetes SYN (de inicio de conexión) y analiza las respuestas.
- **`-p-`**: Indica que se deben escanear todos los puertos (1-65535).
- **`-T4`**: Establece la velocidad del escaneo en un nivel más agresivo (T4), lo que hace que el escaneo sea más rápido, pero puede ser más detectable.
- **`-O`**: Intenta determinar el sistema operativo del dispositivo escaneado mediante técnicas de fingerprinting.
- **`-A`**: Habilita la detección de versiones de servicios, el sistema operativo y otras características avanzadas, proporcionando información más detallada sobre el dispositivo.

Para ver la lo que va descubriendo la herramienta agregaremos un parametro:

![image.png](/assets/images/write-up-kio/image%208.png)

**`-v`**: Activa el modo verbose, proporcionando más detalles sobre lo que está haciendo Nmap durante el escaneo, lo que te permitirá ver el progreso y los resultados a medida que se obtienen.

## Analisis del Scaneo

Mientras se realizaba el scaneo nos mostro los siguientes puertos abiertos:

- `80`,`111`,`139`,`443`,`22`,`1024`.

Ahora teniendo los puertos ejecutaremos el siguiente comando

![image.png](/assets/images/write-up-kio/image%209.png)

- **`-sV`**: Realiza la detección de versiones de los servicios que están escuchando en los puertos especificados, proporcionando información detallada sobre cada servicio.
- **`-p80,111,139,443,22,1024`**: Especifica los puertos a escanear
- **`-T4`**: Aumenta la velocidad del escaneo, permitiendo un análisis más rápido.
- **`-vv`**: Activa un modo de verbose aún más detallado que `v`, proporcionando una salida más extensa sobre el proceso de escaneo.
- **`-O`**: Intenta identificar el sistema operativo del dispositivo escaneado.
- **`-A`**: Habilita la detección avanzada, que incluye la identificación de versiones de servicios y la ejecución de scripts para obtener información adicional.
- **`192.168.204.129`**: Dirección IP del dispositivo que estás escaneando.
- **`-oA kiopuertos`**: Guarda los resultados del escaneo en tres formatos diferentes (normal, XML y grepable) con el prefijo `kiopuertos`, facilitando su análisis posterior.

Ahora para visualizar el analisis usaremos el siguiente comando:

![image.png](/assets/images/write-up-kio/image%2010.png)

- **`xsltproc`**: Es una herramienta de línea de comandos que se utiliza para procesar archivos XML y aplicarles hojas de estilo XSLT.
- **`kiopuertos.xml`**: Este es el archivo XML de entrada que contiene los datos que deseas transformar.
- **`-o kiopuertos.html`**: Esta opción especifica el archivo de salida. En este caso, se creará un archivo llamado `kiopuertos.html` que contendrá la representación HTML del contenido de `kiopuertos.xml`.

Abrimos el archivo .html y veremos lo siguiente:

![image.png](/assets/images/write-up-kio/image%2011.png)

Ahora con la informacion que vemos diremos lo siguiente:

- Puerto 22: SSH , openssh 2.9p2
- Puerto 80: Apache 1.3.20
- Puerto 111: rpcbind 2
- Puerto 139: Samba
- Puerto 443: Apache/1.3.20 (Unix) (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b

Ahora con esta informacion entramos a cada puerto empezaremos poniendo la IP de Kio en el navegador:

![image.png](/assets/images/write-up-kio/image%2012.png)

Entonces vemos que el servicio esta levantado y es un Apache y usando Wappalyzer podemos corroborar las versiones y las extensiones del servidor web.

# Exploracion de la pagina web

Ahora usaremos la tecnica de **"fuzzing"** o **"directory brute-forcing"** en el contexto de explorar una página web. Esta técnica implica intentar acceder a diferentes rutas o recursos en un servidor web para ver cuáles están disponibles. En este caso estaríamos probando URLs como `http://192.168.204.129/1`, `http://192.168.204.129/2`, y así sucesivamente, para descubrir contenido que podría no ser fácilmente accesible.

Usaremos ahora la siguiente herramienta:

![image.png](/assets/images/write-up-kio/image%2013.png)

***Disbuster*** es una herramienta especializada, que automatiza el proceso de prueba de muchas rutas en un servidor.
Ahora nos pide que usemos un diccionario usaremos la que ya tenemos en el Kali.

Nos iremos a la siguiente ruta:`/usr/share/dirbuster/wordlists`

Y escogeremos el diccionario que deseamos:

![image.png](/assets/images/write-up-kio/image%2014.png)

Y ahora solo le damos Star y empieza a ejecutar.

![image.png](/assets/images/write-up-kio/image%2015.png)

![image.png](/assets/images/write-up-kio/image%2016.png)

En los resultados veo algo interesantes que viene a ser un directorio `/mrtg` que contiene archivos uno de ellos es el primero `mrtg.html` ingresamos para ver que hay.

![image.png](/assets/images/write-up-kio/image%2017.png)

Vemos que hay algo montado aqui asi que investiquemos con la siguiente herramienta:

![image.png](/assets/images/write-up-kio/image%2018.png)

- **`nikto`**: Es una herramienta de escaneo de vulnerabilidades en servidores web que busca configuraciones incorrectas, archivos peligrosos y posibles vulnerabilidades.
- **`-h`**: Especifica el host que se va a escanear.
- **`http://192.168.204.129/mrtg/mrtg.html`**: Esta es la URL del servidor web que se está escaneando. En este caso, parece que estamos apuntando a un archivo específico en un directorio relacionado con **MRTG** (Multi Router Traffic Grapher), que es una herramienta utilizada para monitorizar el tráfico de red.

### Resumen de la salida:

1. **Información del servidor**:
    - **Servidor**: `Apache/1.3.20 (Unix)` con `mod_ssl/2.8.4` y `OpenSSL/0.9.6b`. Estas versiones son bastante antiguas y pueden ser vulnerables a múltiples ataques.
2. **Cabeceras de seguridad faltantes**:
    - **X-Frame-Options**: Falta esta cabecera, lo que puede permitir ataques de "clickjacking".
    - **X-Content-Type-Options**: No está establecida, lo que podría permitir la ejecución de contenido malicioso.
3. **Vulnerabilidades específicas**:
    - **ETags**: Puede filtrar información sensible a través de inodes.
    - **XSS**: El servidor Apache es vulnerable a XSS a través del encabezado Expect.
    - **Apache y OpenSSL obsoletos**: Las versiones instaladas son vulnerables y no están soportadas.
4. **Métodos HTTP permitidos**:
    - Se observó que el método HTTP `TRACE` está habilitado, lo que puede permitir ataques de Cross-Site Tracing (XST).
5. **Posibles brechas de seguridad**:
    - Permite la lectura de archivos del sistema a través de URLs manipuladas.
    - Se identificaron múltiples posibles **backdoors** de PHP, lo que indica que puede haber scripts maliciosos en el servidor que permiten el acceso no autorizado a archivos del sistema.
6. **Comandos de ejecución remota**:
    - Se encontraron rutas que pueden permitir la ejecución remota de comandos en el servidor, lo que es un riesgo grave de seguridad.

# Descubrir la version de Samba

Ahora analizaremos por otra parte que sera usando el puerto que tiene el servicio de Samba que no sabemos su version para esto usaremos la siguiente herramienta:

![image.png](/assets/images/write-up-kio/image%2019.png)

El comando `msfconsole` inicia la interfaz de línea de comandos del **Metasploit Framework**. Esta herramienta es esencial para realizar pruebas de penetración y análisis de seguridad. Al ejecutar `msfconsole`, obtenemos acceso a una amplia gama de funcionalidades relacionadas con la explotación de vulnerabilidades en sistemas y aplicaciones.

### Funcionalidades principales de `msfconsole`:

1. **Exploración de vulnerabilidades**: Podemos buscar y listar exploits y módulos disponibles que se pueden utilizar para atacar diferentes servicios y sistemas.
2. **Configuración de exploits**: Permite seleccionar un exploit específico y configurar sus opciones, como la dirección IP del objetivo y otros parámetros necesarios para el ataque.
3. **Selección de payloads**: Podemos elegir qué tipo de payload (carga útil) deseas usar una vez que el exploit tiene éxito. Esto puede incluir ejecutar un shell inverso, crear una sesión Meterpreter, entre otros.
4. **Ejecución de ataques**: Una vez configurado, podemos ejecutar el exploit y tratar de comprometer el sistema objetivo.
5. **Gestión de sesiones**: Si el ataque tiene éxito, podemos interactuar con las sesiones abiertas y ejecutar comandos en el sistema comprometido.
6. **Utilidades adicionales**: Metasploit incluye herramientas para realizar escaneos de red, detectar vulnerabilidades y generar informes.

Otra herramienta que podemos usar es la siguiente:

![image.png](/assets/images/write-up-kio/image%2020.png)

El comando `smbclient -L 192.168.204.129` se utiliza para listar los recursos compartidos de un servidor que utiliza el protocolo **SMB** (Server Message Block), comúnmente utilizado en redes Windows.

- **`smbclient`**: Es una herramienta de línea de comandos que permite acceder y manipular archivos en servidores que utilizan SMB/CIFS. Funciona de manera similar a un cliente FTP.
- **`-L`**: Esta opción indica que deseamos listar los recursos compartidos disponibles en el servidor especificado.
- **`192.168.204.129`**: Esta es la dirección IP del servidor SMB que estamos intentando consultar.

Otra herramienta que podemos usar seria:

![image.png](/assets/images/write-up-kio/image%2021.png)

- **`smbmap`**:**Propósito**: Esta es una herramienta de línea de comandos que permite enumerar y acceder a recursos compartidos en servidores SMB (Server Message Block). Es útil para auditar y realizar pruebas de penetración en entornos que utilizan SMB.
- **`-H`**: Esta opción se utiliza para especificar la dirección IP del host al que deseas conectarte. En este caso, es el servidor SMB en `192.168.204.129`.
- **`-P`**: Esta opción permite especificar el puerto que se utilizará para la conexión. El puerto 139 es uno de los puertos utilizados para conexiones SMB (especialmente en versiones más antiguas de SMB que utilizan NetBIOS). El otro puerto comúnmente utilizado para SMB es el 445.
- **`-u`**: Esta opción permite especificar el nombre de usuario que se utilizará para la autenticación. En este caso el campo vacío (`''`), lo que indica que estamos intentando acceder de forma anónima al recurso compartido.

Podemos usar otra herramienta mas:

![image.png](/assets/images/write-up-kio/image%2022.png)

- **`enum4linux`**: Esta es una herramienta de código abierto diseñada para la enumeración de información en sistemas que utilizan el protocolo SMB. Es especialmente útil para auditorías de seguridad y pruebas de penetración.
- **`192.168.204.129`**: Este es el servidor SMB que deseamos escanear. Al proporcionar esta dirección, `enum4linux` intentará conectarse al servidor y recopilar información.

Con el resultado que nos dio no encontramos algo que nos sirva.

Seguiremos usando **Metasploit** 

![image.png](/assets/images/write-up-kio/image%2023.png)

El comando `search smb version` en **Metasploit** busca módulos relacionados con la versión de SMB (Server Message Block). Este comando es útil para identificar exploits o auxiliares que pueden aprovechar vulnerabilidades específicas en las versiones de SMB.

- **Exploits**: Módulos que pueden ser utilizados para explotar vulnerabilidades en implementaciones de SMB.
- **Auxiliares**: Herramientas que pueden ser utilizadas para realizar escaneos, enumeraciones, y otras tareas relacionadas con SMB.
- **Post-exploitation**: Módulos que se utilizan después de comprometer un sistema para realizar tareas adicionales.

![image.png](/assets/images/write-up-kio/image%2024.png)

Encontramos un auxiliar que es un scaner para detectar la version de Samba.

Ahora lo utilizaremos con el siguiente comando

![image.png](/assets/images/write-up-kio/image%2025.png)

Cuando usamos el comando `use 103` en **Metasploit**, seleccionamos el módulo `auxiliary/scanner/smb/smb_version`, que es una herramienta diseñada para escanear y determinar la versión del protocolo SMB en un objetivo. Luego, al usar `show options`, podemos ver las configuraciones necesarias para ejecutar el módulo.

- **`set`**: Este comando se utiliza en Metasploit para establecer opciones específicas para el módulo activo.
- **`rhost`**: Este parámetro representa el **Remote Host**, es decir, el servidor o dispositivo que estás tratando de escanear. Es crucial para dirigir las operaciones del módulo hacia el objetivo correcto.
- **`192.168.204.129`**: Esta es la dirección IP del servidor SMB al que quieres acceder para determinar su versión de protocolo.
- **`exploit`**: para iniciar el escaneo

Como podemos ver nos dio la version del Samba que es **2.2.1a**

Luego usamos el siguiente comando para salir de la herramienta:

![image.png](/assets/images/write-up-kio/image%2026.png)

- **`exit`**: Este comando se usa para salir de la consola de Metasploit. Es la forma estándar de cerrar la sesión del framework.
- **`y`**: Esta opción le dice a Metasploit que salga automáticamente sin solicitar confirmación.

# Descarga y compilacion de Exploits

Ahora con lo que ya tenemos buscaremos un exploit para la informacion que recopilamos con la siguiente herramienta:

![image.png](/assets/images/write-up-kio/image%2027.png)

- **`searchsploit`**: Esta es una herramienta de línea de comandos que permite buscar exploits y vulnerabilidades en la base de datos de Exploit-DB. Es útil para buscan información sobre vulnerabilidades específicas.
- **`apache 1.3.20`**: Al proporcionar estos términos, le indicas a `searchsploit` que busque vulnerabilidades relacionadas con la versión 1.3.20 de Apache. Esto incluye exploits, pruebas de concepto (POC) y detalles sobre las vulnerabilidades.

Vemos que no encontramos nada seguimos con el siguiente servicio:

![image.png](/assets/images/write-up-kio/image%2028.png)

Aqui conseguimos lo siguiente que nos puede ayudar a vulnerar Kio:

- **Apache mod_ssl < 2.8.7**: Esto indica que la vulnerabilidad afecta a versiones de **mod_ssl** de Apache que son anteriores a la 2.8.7. Esta es una extensión de Apache que proporciona soporte para conexiones seguras utilizando SSL/TLS.
- **`OpenFuckV2.c`**:Este es el nombre del archivo de código fuente que contiene el exploit. Normalmente, los exploits en formato C pueden ser compilados y ejecutados para intentar explotar la vulnerabilidad en un sistema objetivo.
- **`Remote Buffer Overflow`**: Este tipo de vulnerabilidad permite a un atacante remoto enviar datos al servidor que superan la capacidad del búfer asignado. Esto puede provocar que el programa se comporte de manera inesperada, lo que a menudo lleva a la ejecución de código malicioso, denegación de servicio, o incluso toma de control del sistema afectado.
- **`(2)`**: El sufijo `(2)` indica que hay múltiples versiones o variantes del mismo exploit. En este caso, es la segunda variante que se ha registrado para este tipo de vulnerabilidad.

Provemos con otro servicio mas:

![image.png](/assets/images/write-up-kio/image%2029.png)

Vemos que con samba tambien hay vulnerabilidades

Ahora nosotros podemos buscar los exploits en la siguiente pagina https://www.exploit-db.com/:

![image.png](/assets/images/write-up-kio/image%2030.png)

Tambien podemos descargar los exploits con la herramienta:

![image.png](/assets/images/write-up-kio/image%2031.png)

El comando `searchsploit -m 47080` se utiliza para copiar el exploit identificado con el ID `47080` desde la base de datos de Exploit-DB a tu mi maquina local.

- **`-m`**: Esta opción se utiliza para "mover" (o copiar) el exploit desde su ubicación en la base de datos a tu sistema local. El comando genera una copia del archivo en el directorio actual de trabajo.
- **`47080`**: Este es el ID del exploit específico que deseas copiar. En este caso, está relacionado con una vulnerabilidad de tipo "Remote Buffer Overflow" en versiones de mod_ssl de Apache.

Ahora usamos el siguiente comando:

![image.png](/assets/images/write-up-kio/image%2032.png)

El comando `head 47080.c` se utiliza para mostrar las primeras líneas del archivo `47080.c`

- **`head`**: Esta es una utilidad de línea de comandos que muestra las primeras líneas de un archivo. Por defecto, muestra las primeras 10 líneas.
- **`47080.c`**: Este es el archivo de exploit que contiene el código fuente en C.

Ahora complilaremos el exploit:

![image.png](/assets/images/write-up-kio/image%2033.png)

- **`gcc`**: Es el compilador de GNU para C y C++. Se utiliza para compilar el código fuente en archivos ejecutables.
- **`-o ExKio`**: Esta opción especifica el nombre del archivo ejecutable que se va a crear. En este caso, el ejecutable se llamará `ExKio`. Si no se especifica esta opción, el compilador generaría un ejecutable llamado `a.out` por defecto.
- **`47080.c`**: Este es el archivo de código fuente que se está compilando. En este caso, es un archivo escrito en C que contiene el código del exploit.
- **`-lcrypto`**: Esta opción indica al compilador que debe vincular la biblioteca `libcrypto`, que forma parte de OpenSSL. Esta biblioteca proporciona funciones criptográficas que pueden ser necesarias para el funcionamiento del exploit. La opción `-l` se usa para vincular bibliotecas. En este caso, `lcrypto` se refiere a `libcrypto.so` (la biblioteca compartida).

Ahora ejecutamos el exploit:

![image.png](/assets/images/write-up-kio/image%2034.png)

Aqui podemos ver que nos indica como hacer la ejecucion entonces ingresamos los datos que obtuvimos:

![image.png](/assets/images/write-up-kio/image%2035.png)

- **`./ExKio`**:Esto indica que estás ejecutando un programa o script llamado `ExKio` que se encuentra en el directorio actual (`.`).
- **`target`**:Este es un argumento que indica la acción o el modo en que el programa debe funcionar. En este caso, se está especificando un "objetivo". en este caso tenemos 2 opciones:
    - **0x6a - RedHat Linux 7.2 (apache-1.3.20-16)1**
    - **0x6b - RedHat Linux 7.2 (apache-1.3.20-16)2**
- **`192.168.204.129`**: Esta es la dirección IP del "box" (máquina o servidor) que estás intentando atacar o analizar.
- **`443`**: Este es el puerto para la conexión SSL (Secure Sockets Layer). El puerto 443 es el puerto estándar para tráfico HTTPS, lo que indica que la conexión será segura.
- **`-c 45`**: Este es el número de conexiones que se desean abrir. En este caso, se está especificando que se abrirán 45 conexiones. Se sugiere un rango de 40 a 50 si no estamos seguro de cuántas conexiones usar.

Con el target **0x6a** no funciono asi que intentaremos con el otro:

![image.png](/assets/images/write-up-kio/image%2036.png)

Pudimos acceder al la maquina 😁 pero hay un problema 😢 no somos **root.**

# Control Remoto de Kio

Para poder ser root tenemos que fijarnos porque no accedimos como tal.

![image.png](/assets/images/write-up-kio/image%2037.png)

Vemos que el archivo que se tenia que descargar en Kio no le pudo realizar debido a que Kio no tiene acceso a internet por lo que esto provoco que no seamos root.

Para solucionar esto hacemos lo siguiente:

![image.png](/assets/images/write-up-kio/image%2038.png)

- **`wget`**: Es una herramienta de línea de comandos utilizada para descargar archivos desde la web.
- **`https://dl.packetstormsecurity.net/0304-exploits/ptrace-kmod.c`**: Esta es la URL del archivo que se está descargando. En este caso, se trata de un archivo de código fuente llamado `ptrace-kmod.c`, es un exploit relacionado con el uso de `ptrace`, una llamada del sistema en Linux que permite a un proceso observar y controlar la ejecución de otro proceso.

Ahora para que este archivo le pueda pasar a Kio levantamos un servidor web local para que kio lo pueda descargar:

![image.png](/assets/images/write-up-kio/image%2039.png)

Al ejecutar este comando, se iniciará un servidor web que servirá archivos desde el directorio actual (`~/Desktop/kio/xploit`) en el que se ejecuta el comando. 
Podemos acceder a este servidor desde un navegador web visitando `http://localhost:8080` o desde la otra máquina en la misma red utilizando la dirección IP de nuestra máquina.

- **`python3`**: Invoca el intérprete de Python 3.
- **`-m http.server`**: La opción `-m` permite ejecutar un módulo como un script. En este caso, estamos ejecutando el módulo `http.server`, que proporciona un servidor web simple.
- **`8080`**: Este es el número de puerto en el que se levantará el servidor HTTP.

![image.png](/assets/images/write-up-kio/image%2040.png)

copiamos el link del archivo que descargamos: `http://192.168.204.128:8080/ptrace-kmod.c` 

Ahora tenemos que modificar el codigo de nuestro archivo 47080.c pasandole esta direccion.

![image.png](/assets/images/write-up-kio/image%2041.png)

Borramos el anterior ejecutable y abrimos un editor de codigo para cambiar la direcion para pegar la nuestra.

![image.png](/assets/images/write-up-kio/image%2042.png)

Ahora guardamos y volvemos a ejecutar:

![image.png](/assets/images/write-up-kio/image%2043.png)

![image.png](/assets/images/write-up-kio/image%2044.png)

Ahora compremos si somos root

![image.png](/assets/images/write-up-kio/image%2045.png)

- **`whoami`**:Este comando se utiliza para mostrar el nombre del usuario actual que está ejecutando la sesión de la terminal. El resultado es `root`, lo que significa que estamos conectado como el superusuario o administrador del sistema.
- **`bash -i`**:Este comando intenta iniciar una nueva instancia de la shell Bash en modo interactivo (`i` significa "interactivo"). Sin embargo, recibo dos mensajes de error:
    - `bash: no job control in this shell`: Esto indica que no se puede usar el control de trabajos en esta instancia de Bash. El control de trabajos permite gestionar procesos en segundo plano y primer plano.
    - `stty: standard input: Invalid argument`: Este error se debe a que la entrada estándar no está configurada correctamente para el modo interactivo. Puede ocurrir si estamos ejecutando Bash en un entorno no interactivo, como dentro de un script o una conexión de red que no proporciona un terminal completo.
- **`[root@kio-kid tmp]#`**:
    - Esto es un indicador de la línea de comandos que muestra que estamos en un terminal de Bash como usuario `root`, en la máquina llamada `kio-kid`, y actualmente en el directorio `tmp`. El símbolo `#` indica que tienes privilegios de superusuario.

## Buscamos las banderas

Usamos el siguiente comando para encontrar las banderas:

![image.png](/assets/images/write-up-kio/image%2046.png)

- **`find`**: Utilizado para buscar archivos y directorios en el sistema de archivos.
- **`/`**: Indica que la búsqueda comenzará desde el directorio raíz.
- **`name "bandera*.txt"`**: Busca archivos cuyos nombres coincidan con el patrón `bandera*.txt`. Esto incluye cualquier archivo que comience con "bandera" y termine con ".txt", con cualquier combinación de caracteres en medio.
- **`2>/dev/null`**: Esta parte redirige los mensajes de error (el descriptor de archivo `2` se refiere a `stderr`) a `/dev/null`, que es un "sumidero" donde se desechan los datos. Esto es útil para ocultar mensajes de error sobre permisos denegados que pueden aparecer durante la búsqueda.

Ahora veamos el contenido de las banderas:

![image.png](/assets/images/write-up-kio/image%2047.png)

Bandera 1: `684d0624c19cac22a44a8413795368b9`

Bandera 2: `c9b2db2dbe3d8e65485c6c348785a760`

Bandera 3: `9699a2a93f0d7eeb172dca2de51d3db2`

# Forma sencilla de vulnerar Kio

Usaremos metasploit pero primero buscamos el exploit

![image.png](/assets/images/write-up-kio/image%2048.png)

Usaremos la vulnerabilidad que se ve

**Samba** es un software que permite la interoperabilidad entre sistemas operativos Unix/Linux y Windows a través del uso de los protocolos SMB/CIFS.

- **`trans2open`**: Es una llamada dentro del protocolo SMB utilizada por Samba para manejar solicitudes. El problema se origina en cómo Samba maneja ciertas solicitudes a través de esta función.
- **Buffer Overflow (desbordamiento de búfer)**: Este tipo de vulnerabilidad ocurre cuando un programa escribe más datos en un búfer de los que puede manejar. Esto puede sobrescribir datos en la memoria adyacente, lo que permite a un atacante ejecutar código arbitrario o causar un comportamiento inesperado en el sistema.

![image.png](/assets/images/write-up-kio/image%2049.png)

Usaremos ese exploit

![image.png](/assets/images/write-up-kio/image%2050.png)

Ahora tenemos que configurar nuestro payload(carga util) tenemos de 2 formas:

- Stage: Envia el payload por etapas, puede ser menos estable
    - `windows/meterpreter/reverce_tcp` → `/meterpreter` [Etapa 1] `/reverse_tcp` [Etapa 2]
- Non-Staged Payloads:Envia el payload todo a la vez, mas grande el tamaño de bytes, no siempre funciona
    - `windows/meterpreter_reverce_tcp` → `/meterpreter_reverce_tcp` [Todo a la vez]

Ahora haremos lo siguiente:

![image.png](/assets/images/write-up-kio/image%2051.png)

El comando `show payloads` dentro de un módulo específico, como `exploit(linux/samba/trans2open)`, nos muestra una lista de los payloads (cargas útiles) que son compatibles con ese módulo de explotación.

Los payloads son el código que se ejecuta en el sistema objetivo una vez que se ha logrado la explotación. Pueden ser de diferentes tipos, como:

- **Shells**: Proporcionan acceso a una línea de comandos en el sistema objetivo.
- **Meterpreter**: Un payload avanzado que ofrece múltiples funcionalidades para el control del sistema, incluyendo la capacidad de ejecutar comandos, capturar teclados, entre otros.
- **VNC**: Permite acceder al escritorio del sistema objetivo.
- **Reverse TCP**: Se conecta de vuelta al atacante para establecer una sesión.

Ahora escogemos que payload usaremos y vamos a ver las opciones:

![image.png](/assets/images/write-up-kio/image%2052.png)

Ahora vamos a añadir el host remoto:

![image.png](/assets/images/write-up-kio/image%2053.png)

Y ahora mandamos el exploit:

![image.png](/assets/images/write-up-kio/image%2054.png)

Vemos que logramos ser root y volvemos a conseguir las banderas.

=====> **PWNED** ദ്ദി •⩊• )