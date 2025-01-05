---
layout: single
title: Write-Up Steel Mountain
date: 2024-12-30
classes: wide
header:
  teaser: /assets/images/write-up-steel-mountain/teaser.png
categories:
  - Security
tags:
  - CTF
---

**Autor**: [https://github.com/AndrewSthephen23](https://github.com/AndrewSthephen23)

# Reconocimiento

Lo primero que haremos sera conectarnos por medio de una VPN a la maquina de Try Hack Me para lo cual usamos el siguiente comando.

![Imagen 1: Conexion via VPN con nuevo IP local](/assets/images/write-up-steel-mountain/image.png)

Imagen 1: Conexion via VPN con nuevo IP local

- Nos conectamos a la maquina con la VPN y tenemos la IP 10.13.73.83.

Ahora haremos un escaneo de puertos inicial para lo cual usamos el siguiente comando.

![Imagen 2: Reconocimiento con nmap](/assets/images/write-up-steel-mountain/image%201.png)

Imagen 2: Reconocimiento con nmap

![Imagen 3: Obtencion de los puertos abiertos](/assets/images/write-up-steel-mountain/image%202.png)

Imagen 3: Obtencion de los puertos abiertos

- Con es escaneo de puertos nos damos cuenta de que se trata de un sistema operativo Windows.

Al reconocer los puertos abiertos que se encuentran en la máquina, se va a realizar un escaneo especifico a los puertos para obtener más información acerca de ellos como por ejemplo las tecnologías usadas y las versiones de los servicios que están levantados en dichos puertos.
Para lo cual usaremos el siguiente comando.

![Imagen 4: Reconocimiento con Nmap de puertos especificos](/assets/images/write-up-steel-mountain/image%203.png)

Imagen 4: Reconocimiento con Nmap de puertos especificos

![Imagen 5: Informe de resultados de Nmap de la maquina.](/assets/images/write-up-steel-mountain/image%204.png)

Imagen 5: Informe de resultados de Nmap de la maquina.

Datos obtenidos en la fase de reconocimiento.

| Maquina | Steel Mountain |
| --- | --- |
| IP | 10.10.252.171  |
| Sistema Operativo | Windows |
| Puertos/Servicios | 80:  http 135: msrpc 139: netbios-ssn 445: Microsoft-ds 3389: ms-wbt-server 8080: http-proxy |

# Analisis de vulnerabilidades/debilidades

Como tenemos un puerto 80 y 8080 podemos ver las paginas web de estos puertos.

![image.png](/assets/images/write-up-steel-mountain/image%205.png)

- Descubrimos en el puerto 80 que el empleado del mes es Bill Harper

![Imagen 6: Pagina web en el puerto 8080](/assets/images/write-up-steel-mountain/image%206.png)

Imagen 6: Pagina web en el puerto 8080

![image.png](/assets/images/write-up-steel-mountain/image%207.png)

- Obtuvimos la informacion de la web del puerto 8080 donde vemos que tiene un rejetto.

Ahora buscaremos vulnerabilidades de rejetto con exploit con el siguiente comando.

![image.png](/assets/images/write-up-steel-mountain/image%208.png)

- Obtenemos los siguientes exploits.

Ahora nos descargaremos el exploit con el siguiente comando.

![Imagen 7:Descarga del exploit](/assets/images/write-up-steel-mountain/image%209.png)

Imagen 7: Descarga del exploit

Al abrir el exploit obtenemos el funcionamiento y a que CVE pertenece esta vulnerabilidad.

![Imagen 8: Contenido del exploit](/assets/images/write-up-steel-mountain/image%2010.png)

Imagen 8: Contenido del exploit

# Explotacion

Ahora para ejecutar el exploit primero copiamos el nc.exe con los siguientes comandos.

![image.png](/assets/images/write-up-steel-mountain/image%2011.png)

Ahora vamos a configurar la IP del exploit por la que tenemos con el VPN.

![Imagen 9: Configuracion del exploit](/assets/images/write-up-steel-mountain/image%2012.png)

Imagen 9: Configuracion del exploit

Ahora levantamos un servidor web en ese puerto con el siguiente comando.

![image.png](/assets/images/write-up-steel-mountain/image%2013.png)

Ahora vamos a ejecutar un listener con el siguiente comando.

![image.png](/assets/images/write-up-steel-mountain/image%2014.png)

Ahora vamos a ejecutar el exploit 2 veces.

![image.png](/assets/images/write-up-steel-mountain/image%2015.png)

- En la primera ejecucion descarga el nc.exe y en segundo lo ejecuta.

![Imagen 10: Conexion con la maquina.](/assets/images/write-up-steel-mountain/image%2016.png)

Imagen 10: Conexion con la maquina.

Ahora moviendonos por los directorios encontramos la primera bandera.

![image.png](/assets/images/write-up-steel-mountain/image%2017.png)

**bandera : b04763b6fcf51fcd7c13abc7db4fd365**

# Escalacion de Privilegios

Para escalar privilegios se utilizó **winPEAS** que montando un servidor local se descargó en la máquina.

![Imagen 11: Archivo winPEAS pasado a la maquina por medio de un server local](/assets/images/write-up-steel-mountain/image%2018.png)

Imagen 11: Archivo winPEAS pasado a la maquina por medio de un server local

Ejecutando winPEAS se encontró la información del equipo , credenciales de acceso y una vulnerabilidad que usaremos para escalar privilegios.

![image.png](/assets/images/write-up-steel-mountain/image%2019.png)

- Obtenemos informacion de la Maquina

![image.png](/assets/images/write-up-steel-mountain/image%2020.png)

- Obtenemos las credenciales de bill

![image.png](/assets/images/write-up-steel-mountain/image%2021.png)

- Obtenemos la vulnerabilidad **Unquoted and Space detected**

Vemos que tiene los permisos de escritura el usuario sobre el directorio con el siguiente comando

![image.png](/assets/images/write-up-steel-mountain/image%2022.png)

- Vemos que bill tiene permisos de escritura.

![image.png](/assets/images/write-up-steel-mountain/image%2023.png)

- Vemos lo que contiene el directorio.

Ahora utilizaremos el siguiente comando para ver cuales tienen comillas y cuales no.

![image.png](/assets/images/write-up-steel-mountain/image%2024.png)

- Usaremos lo que esta marcado para utilizarlo.

Ahora  con la herramienta de msfvenom creamos el siguiente archivo.

![Imagen 12: Creacion del .exe](/assets/images/write-up-steel-mountain/image%2025.png)

Imagen 12: Creacion del .exe

Ahora con el siguiente comando hacemos que la maquina descargue el archivo que creamos.

![image.png](/assets/images/write-up-steel-mountain/image%2026.png)

Ahora para esta parte nos volvemos a poner en escucha con el siguiente comando.

![image.png](/assets/images/write-up-steel-mountain/image%2027.png)

Ahora reiniciaremos el servicio para que ejecute el .exe que creamos con el siguiente comando.

![Imagen 13: Reiniciamos el servicio ](/assets/images/write-up-steel-mountain/image%2028.png)

Imagen 13: Reiniciamos el servicio 

Ahora en la parte del listener vemos que pudimos escalar privilegios
obteniendo authority system.

![Imagen 14: Escalacion de Privilegios](/assets/images/write-up-steel-mountain/image%2029.png)

Imagen 14: Escalacion de Privilegios

Ahora moviendonos entre los directorios encontramos la segunda bandera.

![image.png](/assets/images/write-up-steel-mountain/image%2030.png)

**bandera 2: 9af5f314f57607c00fd09803a587db80**

# Extra

![image.png](/assets/images/write-up-steel-mountain/image%2031.png)

![image.png](/assets/images/write-up-steel-mountain/image%2032.png)

![image.png](/assets/images/write-up-steel-mountain/image%2033.png)

![image.png](/assets/images/write-up-steel-mountain/image%2034.png)

![image.png](/assets/images/write-up-steel-mountain/image%2035.png)