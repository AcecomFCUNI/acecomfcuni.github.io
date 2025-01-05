---
layout: single
title: Hacking Wi-Fi 101
date: 2024-10-31
classes: wide
header:
  teaser: /assets/images/wifi-hacking/teaser.jpg
categories:
  - Security
tags:
  - Wifi Hacking
---

**Autor**: [https://github.com/AndrewSthephen23](https://github.com/AndrewSthephen23)

# Tabla de Contenidos

1. Introducción
2. Tipos de Seguridad Wi-Fi
    - WEP
    - WPA/WPA2
    - WPA3
3. Proceso de Auditoría Wi-Fi


# Introducción

La seguridad de las redes Wi-Fi es un tema crucial en el mundo digital actual, ya que la mayoría de los dispositivos conectados dependen de una red inalámbrica para acceder a internet y compartir información. Sin embargo, la popularidad de estas redes también las hace vulnerables a una variedad de ataques. El hacking de redes Wi-Fi, también conocido como auditoría de redes, tiene como objetivo identificar y corregir posibles fallas de seguridad para proteger la privacidad de los usuarios.

Este write-up está diseñado para proporcionar una visión general de las técnicas utilizadas en auditorías de seguridad de redes Wi-Fi. A través de herramientas específicas y métodos probados, aprenderás los conceptos básicos para realizar una evaluación de seguridad en una red inalámbrica, siempre bajo la premisa de realizar estos procedimientos de manera ética y legal.

**Nota importante:** Este contenido es únicamente para fines educativos y debe emplearse con el permiso explícito del propietario de la red. El acceso no autorizado a una red Wi-Fi es ilegal y puede tener graves consecuencias legales.


# Tipos de Seguridad Wifi

![image.png](/assets/images/wifi-hacking/02069d15-204c-4536-86f7-31244e1570f6.png)

- WEP: permitía romper contraseñas incluso sin diccionario ya que era un algoritmo mas fácil de romper.
- WPA2: Estando fuera de una red wifi se puede inyectar paquetes y de autenticar a un cliente (sacar de la red a un cliente) y al momento de sacarlo de la red podemos tener la contraseña cuando el cliente se vuelva a conectar a la red.
- WPA3: No es aplicable porque muchos dispositivos no son compatibles con el protocolo.

# Proceso de Auditoría Wi-Fi

![image.png](/assets/images/wifi-hacking/image.png)

- Tenemos a un atacante fuera de la red tiene un dispositivo (tarjeta wifi), va a estar a la escucha de paquetes de tramas de una red en especifico o de varias redes a su alrededor como un ataque man in the middle.
- En el momento en que un cliente se conecte a una red se envia una trama muy en especifico (tramas Eapol) estas tramas contienen la contraseña cifrada de la red.
- Cuando un dispositivo se conecta al Access Point este le manda la contraseña al Access Point donde este verifica si es la contraseña correcta y lo admite en la red.
- Cuando obtiene la contraseña cifrada hace uso de craking de contraseñas para obtener la contraseña y pueda conectarse a la red.

## Herramienta para el crakear

### ¿Qué es Hashcat?

Hashcat es una herramienta avanzada diseñada para descifrar contraseñas utilizando la potencia combinada de CPU y GPU. Es compatible con una amplia variedad de algoritmos de hash y permite realizar auditorías de seguridad de manera eficiente y rápida.

**Características**

- **Descifra contraseñas**: Extrae contraseñas a partir de sus códigos hash.
- **Compatibilidad de plataformas**: Disponible para Linux, Windows y Mac OS X, tanto en línea de comandos como con interfaz gráfica (GUI).
- **Extracción de contraseñas**: Permite obtener la contraseña a partir del hash de dicha contraseña.
- **Diversidad de ataques**: Soporta ocho tipos de ataque distintos para romper un hash.
- **Uso de reglas personalizadas**: Permite crear o utilizar una gran variedad de reglas para personalizar los ataques.
- **Optimización para GPU**: Está optimizado para utilizarlo con GPU, lo que aumenta la velocidad de procesamiento.
- **Código Abierto**: Hashcat es una herramienta de código abierto, disponible para toda la comunidad.

### Ventajas Principales

- **Rapidez**: Soporte para cracking acelerado mediante GPU, permitiendo una mayor velocidad en el descifrado de contraseñas.
- **Compatibilidad**: Compatible con una amplia variedad de algoritmos de hash, haciéndolo útil para múltiples escenarios de seguridad.
- **Distribución**: Capacidad para distribuir el trabajo en múltiples máquinas, facilitando el manejo de tareas complejas de cracking en entornos distribuidos.
- Utiliza la tarjeta grafica para que el crakeo vaya mas rápido.
- Permite descifrar distintos tipos de Hashes (contraseñas cifradas) para este laboratorio se usara el hash WPA2 el protocolo de seguridad wifi.

## 1. Escaneo de redes

- En el Kali linux se conecta a una tarjeta wifi.
    
    Para verificar si la targeta a sido conectada se usa el siguiente comando.
    
    ![image.png](/assets/images/wifi-hacking/image%201.png)
    
    `lsusb` : `ls` comando para listar `usb` para indicar que liste los usb conectados.
    
    ⚠️El adaptador tiene que ser compatible para inyeccion de paquetes.
    
- Ahora usamos el siguiente comando
    
    ![image.png](/assets/images/wifi-hacking/image%202.png)
    
    El comando `iwconfig` permite listar las tarjetas wireless
    
    ❗Nota: 
    
    - RTL8814AU es el chipset.
    - Mode: Managed → Significa que la tarjeta esta en modo de que se conecte a redes.
    - Para el laboratorio necesitamos que este en modo `Monitor`.
    - El monitor permite inyectar paquetes y capturar tramas importantes de la red como **eapol**
- Ahora tenemos que cambiar el modo de manage a monitor
    
    Usaremos el siguiente comando
    
    ![image.png](/assets/images/wifi-hacking/image%203.png)
    
    Podemos ver que la tarjeta cambio a modo monitor `(monitor mode enabled)`.
    
    Nos da una sugerencia para matar 2 procesos que podrían causar problemas y nos da un comando que es el siguiente.
    
    ![image.png](/assets/images/wifi-hacking/image%204.png)
    
    Ahora para comprobar que se cambio el modo vamos a poner otra ves el comando.
    
    ![image.png](/assets/images/wifi-hacking/image%205.png)
    

## 2. Captura de paquetes

- Ahora vamos a crear un directorio
    
    ![image.png](/assets/images/wifi-hacking/image%206.png)
    
    Dentro de la carpeta que se creo vamos a capturar todo el trafico para lo cual vamos a segur usando la herramienta de `aircrack`.
    
    Usaremos el siguiente comando de aircrack para escuchar el trafico (las tramas de la red).
    
    ![image.png](/assets/images/wifi-hacking/image%207.png)
    
    `airodump-ng`: herramienta de aircrack
    
    `wlan0`: nombre de la targeta de red
    
    `--essid`: parametro a poner el nombre de la red Wifi(Cuarto1)
    
    `-w`: parametro para guardar las tramas en un archivo llamado tramasEAPOL
    
- Podemos ver que estamos capturando las tramas de la red
    
    ![image.png](/assets/images/wifi-hacking/image%208.png)
    
    `BSSID:` dirección MAC del router
    
    `CH:` canal
    
    `ENC:` que cifrado utiliza (WPA2)
    
    `ESSID:` nombre de la red (CUARTO1)
    
- Abrimos otra terminal mientras la otra sigue en operación.
    
    Listamos lo que contiene la carpeta que creamos
    
    ![image.png](/assets/images/wifi-hacking/image%209.png)
    
    En el archivo `.cap` es donde esta guardando las tramas escuchadas
    
- Ahora lo único que necesitamos es que un cliente se conecte a la red.
    
    Para hacer esto usamos un teléfono que lo desconectaremos y volveremos a conectar a la red.
    
    ![image.png](/assets/images/wifi-hacking/image%2010.png)
    
    ![image.png](/assets/images/wifi-hacking/image%2011.png)
    
    Tendriamos que ver un aviso que se capturo la contraseña cifrada en caso de que no usamos la herramienta `wireshark` para comprobar.
    
- Con la herramienta abrimos el archivo `.cap`
    
    ![image.png](/assets/images/wifi-hacking/image%2012.png)
    
    aquí podemos ver todas las tramas capturas de la red
    
    ![image.png](/assets/images/wifi-hacking/image%2013.png)
    
    si filtramos en el buscador con la palabra `eapol` deberíamos ver la trama capturada
    
    ![image.png](/assets/images/wifi-hacking/image%2014.png)
    
    por lo que se ve que en el primer escaneo no se obtuvo la contraseña así que volveremos a intentar.
    
    Dado el caso no se pueda obtener la contraseña por la interferencia de varias redes alrededor usaremos una herramienta.
    

## 3. Desautenticación de dispositivos

- Usaremos la herramienta aireplay-ng
    
    ![image.png](/assets/images/wifi-hacking/image%2015.png)
    
    `-0`: parametro para hacer una deautenticacion (enviar un muchas tramas al router para congestionarlo y saque a los clientes de la red).
    
    `0`: es la cantidad de tramas que queremos enviar sea 10 o 100. ❗Nota: si le ponemos 0 enviaremos infitas tramas hasta parar el ataque.
    
    `-a`: paramentro para poner la dirección MAC del router.
    
    `wlan0`: tarjeta wifi que utilizamos para el ataque.
    
    Aqui nos da un error que dice que la tarjeta wifi no esta haciendo un cambio de canal para solucionar esto reiniciamos la tarjeta con los siguientes comandos.
    
    ![image.png](/assets/images/wifi-hacking/image%2016.png)
    
    y volvemos a guardar las tramas en otro archivo
    
    ![image.png](/assets/images/wifi-hacking/image%2017.png)
    
    y si con eso no funciona forzamos a que la tarjeta wifi se quede en el canal 1 aumentando el siguiente parámetro
    
    ![image.png](/assets/images/wifi-hacking/image%2018.png)
    
    `-c`: numero de canal de la escucha 
    
    La tarjeta wifi hace los cambios de canales para probar con cualquier red wifi en caso no cambie forzamos que se quede en el que queremos.
    
- Ahora volvemos a intentar a usar la deautenticacion
    
    ![image.png](/assets/images/wifi-hacking/image%2019.png)
    
    Ahora veamos que pasa cuando se ejecuta el comando
    
    ![image.png](/assets/images/wifi-hacking/image%2020.png)
    
    El telefono se desconecto de la red porque la tarjeta empezo a enviar muchas tramas
    
    ![image.png](/assets/images/wifi-hacking/image%2021.png)
    
    Hasta que no paremos el ataque no se conectara el telefono a la red.
    
- Vemos que cuando se conecto aparecio el mensaje `WPA handshake:`
    
    ![image.png](/assets/images/wifi-hacking/image%2022.png)
    
    Ahora si se guardo la contraseña en el archivo por lo que lo abriremos con wireshark.
    
    ![image.png](/assets/images/wifi-hacking/image%2023.png)
    
    Seleccionamos el archivo .cap donde se guardo y abrimos, filtramos poniendo EAPOL.
    
    ![image.png](/assets/images/wifi-hacking/image%2024.png)
    
    vemos que nos da las tramas recibidad con mensajes de 1 a 4 pero se ve que el mensaje 4 no hay por lo que talvez no se haya enviado la contraseña completamente.
    
    Entre todos los mensajes esta la contraseña cifrada.
    

## 4. Cracking de contraseñas

- Utilizaremos la herramienta aircrack para romper la contraseña.
    
    ![image.png](/assets/images/wifi-hacking/image%2025.png)
    
    `tramasEAPOL2-01.cap:` el archivo que contiene la contraseña cifrada
    
    `rockyou.txt:` es un diccionario donde este tiene una cantidad de palabras que pueden ser la contraseña o no.
    
    Se le da enter al comando
    
    ![image.png](/assets/images/wifi-hacking/image%2026.png)
    
    Acá vemos que prueba cada una de las contraseñas del diccionario donde vemos que va a demorar 40 min el hacer todas las comparaciones.
    
    Pero la contraseña no se va a conseguir porque la contraseña de la red tiene que estar en el diccionario.
    
    Para saber la cantidad de palabras de un diccionario usamos el siguiente comando.
    
    ![image.png](/assets/images/wifi-hacking/image%2027.png)
    
    `wc:` cuenta el numero de lineas
    
    `-l:` la direccion del archivo 
    
    Se ve que se tiene 14344393 palabras
    
- Supongamos que una ves usado aircrack esperamos el tiempo que dice ahi no obtenemos la contraseña.
    
    que nos queda?: usar un mejor diccionario con mas palabras o usar reglas de hashcat.
    
    Que es usar reglas? → de una palabra raiz formar otras añadiendo o modificando caracteres en esta por ejemplo:
    
    ![image.png](/assets/images/wifi-hacking/image%2028.png)
    
    la palabra base `*arbol*` con reglas puedo volverla `*Arbol06!*` y puedo usar esta regla para que se aplique a todas las palabras del diccionario. 
    
    ![image.png](/assets/images/wifi-hacking/image%2029.png)
    
    En la paguina de hashcat podemos obtener todas las reglas que se puede aplicar. 
    
    ![image.png](/assets/images/wifi-hacking/image%2030.png)
    
    si aplicamos esa regla a arbol se volvera → Arbol06
    
    De esta manera para hacer mejores combinaciones para obtener la contraseña se utilizaran muchas combinaciones de reglas haciendo que el directorio crezca mucho mas en palabras.
    
    ![image.png](/assets/images/wifi-hacking/image%2031.png)
    
- Para mejorar el tiempo de espera para que se rompa la contraseña usaremos la targeta grafica y la herramienta hashcat.
    
    Primero convertiremos el archivo .cap a algo compatible con hashcat usaremos la pagina de hashcat y subiremos el archivo.
    
    ![image.png](/assets/images/wifi-hacking/image%2032.png)
    
    le damos a **convert y descargamos**
    
    ![image.png](/assets/images/wifi-hacking/image%2033.png)
    
    El archivo que descargamos lo arrastramos al windows y luego usamos el siguiente comando
    
    ![image.png](/assets/images/wifi-hacking/image%2034.png)
    
    `-m:` (22000) modulo para crakear ya que hashcat puede hacerlo para distintos tipos de protocolos.
    
    `CUARTO1.hc22000:` archivo para crakear
    
    `rockyou.txt:` diccionario a utilizar 
    
    `-d:` (1) dispositivo que se usara para usar hashcat  
    
    ![image.png](/assets/images/wifi-hacking/image%2035.png)
    
    podemos ver que al ejecutarlo vemos que el tiempo es mucho menor solo **1m** para que analice todo.
    
    ![image.png](/assets/images/wifi-hacking/image%2036.png)
    
    al finalizar vemos que no encontro la contraseña porque no esta en el diccionario.
    

Ahora para obtener la contraseña podemos usar reglas 

Al instalar hashcat nos viene con las reglas en la carpeta que se ve.

![image.png](/assets/images/wifi-hacking/image%2037.png)

❗Nota: OneRuleToRuleThemAll.rule es una de las mejores reglas pero esta no viene se obtiene por github.

Que contiene el archivo usaremos el comando `cat` para ver.

![image.png](/assets/images/wifi-hacking/image%2038.png)

- Ahora vamos a hacer la prueba con una palabra para eso usaremos el siguiente comando
    
    ![image.png](/assets/images/wifi-hacking/image%2039.png)
    
    `echo:` imprimir una palabra (casa)
    
    `>:` guardar en un archivo (casa.txt)
    
    Ahora usaremos la herramienta hashcat
    
    ![image.png](/assets/images/wifi-hacking/image%2040.png)
    
    `-r:` parametro para agregar la regla o el archivo que contiene las regla.
    
    `--stdout:` parametro para indicar a que archivo se le va a aplicar la regla.
    
    ![image.png](/assets/images/wifi-hacking/image%2041.png)
    
    Se vera algo asi pero para guardar todas las palabras podemos guardarlas en un archivo por lo que solo al comando anterior le añadimos `> casatransformada.txt` y para ver su contenido ponemos tambien el siguiente comando.
    
    ![image.png](/assets/images/wifi-hacking/image%2042.png)
    
    ![image.png](/assets/images/wifi-hacking/image%2043.png)
    
    vemos esa salida.
    
- Ahora usaremos lo que vimos con el diccionario que se tenia añadiremos lo siguiente al comando que ya teniamos al inicio.
    
    ![image.png](/assets/images/wifi-hacking/image%2044.png)
    
    veremos lo siguiente
    
    ![image.png](/assets/images/wifi-hacking/image%2045.png)
    
    antes se tenia la cantidad que pone `Passwords` de palabras a evaluar ahora la cantidad seria `Keyspace`.
    
    ![image.png](/assets/images/wifi-hacking/image%2046.png)
    
    vemos que nos da que el proceso terminara en 96 dias
    

Como la cantidad de dias es mucho se utilizara un diccionario mas pequeño.

Usaremos el siguiente comando para recortar las palabras del diccionario

![image.png](/assets/images/wifi-hacking/image%2047.png)

`-n:` parametro para indicar la cantidad de primeras palabras a extraer. 

`>:` parametro que indica que va a guardar en un archivo.

![image.png](/assets/images/wifi-hacking/image%2048.png)

ahora usamos el diccionario pequeño.

![image.png](/assets/images/wifi-hacking/image%2049.png)

vemos que el tiempo ahora es menor

![image.png](/assets/images/wifi-hacking/image%2050.png)

Despues de esperar obtenemos las contraseña de la red wifi.

‼️**Nota:** Para diccionarios pequeños con muchas reglas ponemos aumentar el parametro `-S` en el comando.

![image.png](/assets/images/wifi-hacking/image%2051.png)

Y asi acaba el ataque obteniendo la contraseña.

Muchas gracias por llegar hasta aca :D