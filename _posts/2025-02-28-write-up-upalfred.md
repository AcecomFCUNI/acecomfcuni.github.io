---
layout: single
title: Write-Up UPAlfred
date: 2025-02-28
classes: wide
header:
  teaser: /assets/images/write-up-game-zone/teaser.jpg
categories:
  - Security
tags:
  - CTF
---

# Write-UP Alfred

By: [https://github.com/AndrewSthephen23](https://github.com/AndrewSthephen23)

# 1. Reconocimiento

Primero nos conectamos por medio de una VPN a la maquina de Try Hack Me para lo cual usaremos el siguiente comando.

![image.png](/assets/images/write-up-upalfred/image.png)

Hacemos una ejecucion de escaneo de puertos inicial sin hacer una comprobacion de ping para lo cual usaremos el siguiente comando .

![image.png](/assets/images/write-up-upalfred/image%201.png)

Al reconocer los puertos abiertos vamos a realizar un escaneo especifico con el siguiente comando.

![image.png](/assets/images/write-up-upalfred/image%202.png)

![image.png](/assets/images/write-up-upalfred/image%203.png)

![image.png](/assets/images/write-up-upalfred/image%204.png)

# 2. Análisis de vulnerabilidades/debilidades

Ahora haremos un escaneo de vulnerabilidades de los puertos abiertos con el siguiente comando.

![image.png](/assets/images/write-up-upalfred/image%205.png)

![image.png](/assets/images/write-up-upalfred/image%206.png)

# 3. Explotación

Como tenemos los puertos 80 y 8080 podemos ver las paginas web de ese puerto.

![image.png](/assets/images/write-up-upalfred/image%207.png)

![image.png](/assets/images/write-up-upalfred/image%208.png)

Ahora usando credenciales por defecto como **admin admin** logramos logearnos. 

![image.png](/assets/images/write-up-upalfred/image%209.png)

Encontramos una caracteristica que nos permite ejecutar codigo en el sistema.

![image.png](/assets/images/write-up-upalfred/image%2010.png)

Usando esta caracteristica hacemos una reverse Shell usando Grovvy con la direccion Ip de la VPN.

![image.png](/assets/images/write-up-upalfred/image%2011.png)

Ahora ejecutamos un listener para recepcionar la conexión.

![image.png](/assets/images/write-up-upalfred/image%2012.png)

Ahora pegamos el script en la consola que nos permite ejecutar codigo y le damos a run.

![image.png](/assets/images/write-up-upalfred/image%2013.png)

Accedimos como el usuario Bruce

![image.png](/assets/images/write-up-upalfred/image%2014.png)

Moviendonos por los archivos encontramos la bandera 1.

![image.png](/assets/images/write-up-upalfred/image%2015.png)

- **bandera 1: 79007a09481963edf2e1321abd9ae2a0**

Ahora haremos un cambio de shell para usar meterpreter para lo cual primero ejecutamos el siguiente comando para crear un archivo para la reverse shell.

![image.png](/assets/images/write-up-upalfred/image%2016.png)

Ahora levanto un servidor web para que le objetivo descargue el archivo.

![image.png](/assets/images/write-up-upalfred/image%2017.png)

Ahora pegare el siguiente script en la consola que nos permite ejecutar codigo creando un nuevo proyecto.

![image.png](/assets/images/write-up-upalfred/image%2018.png)

Ahora ejecutamos el proyecto y vemos que se descargo el archivo.

![image.png](/assets/images/write-up-upalfred/image%2019.png)

![image.png](/assets/images/write-up-upalfred/image%2020.png)

Ahora antes de ejecutar el archivo que se descargo en el objetivo configuramos nuestro Metasploit y lo ejecutamos.

![image.png](/assets/images/write-up-upalfred/image%2021.png)

Ahora ejecutamos el archivo con el siguiente comando y vemos que obtuvimos el cambio de shell.

![image.png](/assets/images/write-up-upalfred/image%2022.png)

![image.png](/assets/images/write-up-upalfred/image%2023.png)

# Escalacion de Privilegios

Con el siguiente comando veremos los provilegios que tenemos con el usuario.

![image.png](/assets/images/write-up-upalfred/image%2024.png)

Ahora usamos el siguiente comando para cargar el modo incognito de Metasploit.

![image.png](/assets/images/write-up-upalfred/image%2025.png)

Ahora con el siguiente comando podemos ver que tokens estan disponibles dentro de los que nos aparecen usaremos el primero.

![image.png](/assets/images/write-up-upalfred/image%2026.png)

Ahora usaremos el siguiente comando para suplantar el token de los administradores y tener privilegios de administrador.

![image.png](/assets/images/write-up-upalfred/image%2027.png)

Ahora usamos el siguiente comando para ver todos los procesos y del cual elegimos uno al cual migrar.

![image.png](/assets/images/write-up-upalfred/image%2028.png)

Ahora usaremos el siguiente comando para migrar a ese proceso y logramos escalar privilegios..

![image.png](/assets/images/write-up-upalfred/image%2029.png)

Ahora buscamos entre los directorios y encontramos la segunda bandera.

![image.png](/assets/images/write-up-upalfred/image%2030.png)

**bandera 2 : dff0f748678f280250f25a45b8046b4a**

# Extra

![image.png](/assets/images/write-up-upalfred/image%2031.png)

![image.png](/assets/images/write-up-upalfred/image%2032.png)

![image.png](/assets/images/write-up-upalfred/image%2033.png)

===⇒ PWNED (￣ω￣)

---