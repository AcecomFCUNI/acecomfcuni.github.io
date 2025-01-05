---
layout: single
title: Write-Up Game Zone
date: 2024-12-31
classes: wide
header:
  teaser: /assets/images/write-up-game-zone/teaser.jpg
categories:
  - Security
tags:
  - CTF
---

**Autor**: [https://github.com/AndrewSthephen23](https://github.com/AndrewSthephen23)

# Reconocimiento

Primero nos conectaremos por medio de una VPN a la maquina de Try Hack Me para lo cual usamos el siguiente comando.

![image.png](/assets/images/write-up-game-zone/image.png)

Hacemos una ejecución de escaneo de puertos inicial para lo cual usamos el siguiente comando.

![Imagen 1: Reconocimiento con Nmap y obtencion de los puertos abiertos](/assets/images/write-up-game-zone/image%201.png)

Imagen 1: Reconocimiento con Nmap y obtencion de los puertos abiertos

Al reconocer los puertos abiertos que se encuentran en la máquina, se va a realizar un escaneo especifico a los puertos para obtener más información acerca de ellos como por ejemplo las tecnologías usadas y las versiones de los servicios que están levantados en dichos puertos.
Para lo cual usaremos el siguiente comando.

![Imagen 2: Reconocimiento con Nmap de puertos especificos.](/assets/images/write-up-game-zone/image%202.png)

Imagen 2: Reconocimiento con Nmap de puertos especificos.

![Imagem 3: Informe de resultados de Nmap de la maquina](/assets/images/write-up-game-zone/image%203.png)

Imagem 3: Informe de resultados de Nmap de la maquina

Datos obtenidos en la fase de reconocimiento.

| Maquina | Game Zone |
| --- | --- |
| IP | 10.10.67.229  |
| Sistema Operativo | Ubuntu |
| Puertos/Servicios  | 22: ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 80: http Apache httpd 2.4.18  |

# Analisis de Vulnerabilidades

Ahora haremos un escaneo de vulnerabilidades de los puertos abiertos con el siguiente comando.

![Imagen 4: Analisis de vulnerabilidades con Nmap](/assets/images/write-up-game-zone/image%204.png)

Imagen 4: Analisis de vulnerabilidades con Nmap

![Imagen 5: Informe de resultados de vulnerabilidades](/assets/images/write-up-game-zone/image%205.png)

Imagen 5: Informe de resultados de vulnerabilidades

- En los resultados obtenemos informacion que tiene una direccion IP interna.

# Explotacion

Ahora por medio de un navegador vemos lo que esta montado en el puerto 80.

![image.png](/assets/images/write-up-game-zone/image%206.png)

Hicimos un bypass por medio de SQL Inyection en el login con el cual entramos sin la contraseña.

![image.png](/assets/images/write-up-game-zone/image%207.png)

![image.png](/assets/images/write-up-game-zone/image%208.png)

Ahora haremos unas consultas sql para saber el numero de columnas que tiene, obtuvimos que son 3 columnas.

![Imagen 6: Resultado de la consulta](/assets/images/write-up-game-zone/image%209.png)

Imagen 6: Resultado de la consulta

Ahora hacemos la siguiente consulta SQL para que nos devuelva lo que contiene la columna 1, 2 y 3.

![image.png](/assets/images/write-up-game-zone/image%2010.png)

- Resultado de la consulta.

Ahora usaremos las siguientes consultas para saber las columnas tipo texto.

![image.png](/assets/images/write-up-game-zone/image%2011.png)

- Respuesta de la consulta `'UNION SELECT NULL, 'a',NULL#`

![image.png](/assets/images/write-up-game-zone/image%2012.png)

- Respuesta de la consulta `'UNION SELECT NULL,NULL,'a'#`

Ahora hicimos la siguiente consulta para obtener el nombre de la base de datos y la version que se tiene.

![image.png](/assets/images/write-up-game-zone/image%2013.png)

- Respuesta de la consulta `'UNION SELECT NULL, @@HOSTNAME, @@VERSION#`

Ahora hicimos la siguiente consulta para obtener el usuario y la base de datos.

![image.png](/assets/images/write-up-game-zone/image%2014.png)

- Respuesta de la consulta `'UNION SELECT NULL, user(), database()#`

Ahora hare una consulta para obtener informacion sobre esquema.

![image.png](/assets/images/write-up-game-zone/image%2015.png)

- Respuesta de la consulta `'UNION SELECT NULL,NULL,SCHEMA_NAME FROM information_schema.SCHEMATA#`

Ahora haremos una consulta para ver la informacion de las tablas de **db.**

![image.png](/assets/images/write-up-game-zone/image%2016.png)

- Respuesta de la consulta `'UNION SELECT NULL,NULL,TABLE_NAME FROM
information_schema.TABLES WHERE TABLE_SCHEMA='db'#`

Ahora sacamos la informacion de las columnas de cada tabla con la siguiente consulta.

![image.png](/assets/images/write-up-game-zone/image%2017.png)

- Respuesta de la consulta `'UNION SELECT NULL,TABLE_NAME, COLUMN_NAME FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='db'#`

Ahora vemos que hay una que puede contener contraseñas para lo cual hacemos la siguiente consulta para ver el contenido.

![image.png](/assets/images/write-up-game-zone/image%2018.png)

- Respuesta de la consulta `'UNION SELECT 1, username, pwd FROM users#`
- ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14

Ahora para crakear el hash primero lo ponemos en un archivo txt para luego usar la siguiente herramienta con el siguiente comando para ver que clase de hash tiene.

![image.png](/assets/images/write-up-game-zone/image%2019.png)

- Obtenemos que es un hash 256.

Ahora usamos el siguiente comando para crakear donde obtuvimos la contraseña.

![image.png](/assets/images/write-up-game-zone/image%2020.png)

- Obtencion de la constraseña `videogamer124` .

Ahora nos podemos conectar con estas credenciales por ssh con el siguiente comando.

![image.png](/assets/images/write-up-game-zone/image%2021.png)

Explorando obtenemos la primera bandera.

![image.png](/assets/images/write-up-game-zone/image%2022.png)

**bandera 1: 649ac17b1480ac13ef1e4fa579dac95c**

# Escalacion de Privilegios

Para escalar privilegios se utilizó **linpeas** que montando un servidor local se descargó en la máquina.

![Imagen 6: Archivo linpeas pasado a la maquina por medio de un server local](/assets/images/write-up-game-zone/image%2023.png)

Imagen 6: Archivo linpeas pasado a la maquina por medio de un server local

Ejecutando linpeas se encontró un servicio interno corriendo en el puerto 10000. 

![image.png](/assets/images/write-up-game-zone/image%2024.png)

Ahora usaremos el siguiente comando para hacer un **SSH Tunel.**

![image.png](/assets/images/write-up-game-zone/image%2025.png)

Ahora con el navegador veremos que es lo que esta en ese puerto.

![image.png](/assets/images/write-up-game-zone/image%2026.png)

Ahora usamos las mismas credenciales con la que nos conectamos por medio de ssh con las cuales logramos ingresar.

![image.png](/assets/images/write-up-game-zone/image%2027.png)

Ahora buscaremos informacion de un exploit para la version de webmin en la cual encontramos un pdf con informacion para hacer un path transversal.

![image.png](/assets/images/write-up-game-zone/image%2028.png)

![Imagen 7: Informacion del pdf](/assets/images/write-up-game-zone/image%2029.png)

Imagen 7: Informacion del pdf

Ahora completamos lo que encontramos en la url que teniamos y vemos que podemos inyectar en la url.

![Imagen 8: Resultados de la inyeccion ](/assets/images/write-up-game-zone/image%2030.png)

Imagen 8: Resultados de la inyeccion 

Ahora usaremos la siguiente inyeccion para ver quien esta ejecutando donde en el resultado vemos que es el root.

![image.png](/assets/images/write-up-game-zone/image%2031.png)

Ahora usaremos lo siguiente para saber donde esta la ruta de python.

![image.png](/assets/images/write-up-game-zone/image%2032.png)

Ahora sabiendo esto haremos una reverse shell usando python usando la direccion IP de la vpn.

![image.png](/assets/images/write-up-game-zone/image%2033.png)

Ahora lo pegaremos en la url pero primero le hacemos un URL Encode para su ejecucion pero antes de eso ejecutamos un listener para recepcionar la conexion.

![image.png](/assets/images/write-up-game-zone/image%2034.png)

![image.png](/assets/images/write-up-game-zone/image%2035.png)

Lo pegamos en la URL y obtuvimos root por el listener. 

![Imagen 9: Obtencion de root](/assets/images/write-up-game-zone/image%2036.png)

Imagen 9: Obtencion de root

Moviendonos por los directorios obtuvimos la bandera 2.

![image.png](/assets/images/write-up-game-zone/image%2037.png)

**bandera 2: a4b945830144bdd71908d12d902adeee**

# Extra

Ahora para esta parte usaremos burp suite con el cual intersectamos la busqueda de un juego.

![image.png](/assets/images/write-up-game-zone/image%2038.png)

Ahora copiaremos la salida en el siguiente archivo.

![image.png](/assets/images/write-up-game-zone/image%2039.png)

Ahora utilizaremos sqlmap para poder visualizar lo que necesitamos ver y lo haremos con el siguiente comando.

![image.png](/assets/images/write-up-game-zone/image%2040.png)

![image.png](/assets/images/write-up-game-zone/image%2041.png)

- En los resultados encontramos las credenciales del agent47.

Ahora usaremos la herramienta de metasploit para lo cual primero buscaremos el exploit que usaremos.

![image.png](/assets/images/write-up-game-zone/image%2042.png)

Ahora haremos las siguientes configuraciones para usar el exploit.

![image.png](/assets/images/write-up-game-zone/image%2043.png)

![image.png](/assets/images/write-up-game-zone/image%2044.png)

Ahora ejecutamos el exploit y conseguimos ser root.

![image.png](/assets/images/write-up-game-zone/image%2045.png)