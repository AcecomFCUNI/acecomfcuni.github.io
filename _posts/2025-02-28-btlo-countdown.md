---
layout: single
title: BTLO - Countdown
date: 2025-02-28
classes: wide
header:
  teaser: /assets/images/btlo-countdown/image.png
categories:
  - Security
tags:
  - CTF
---

**Autor**: [https://github.com/EnocRosales20](/assets/images/btlo-countdown/https://github.com/EnocRosales20)

# Countdown

**Tema**: Digital Forensics

![image.png](/assets/images/btlo-countdown/image.png)

### **Escenario:**

La policía de Nueva York recibió información de que una banda de atacantes ingresó a la ciudad y planea detonar un artefacto explosivo. Las autoridades han comenzado a investigar todas las pistas para determinar si esto es cierto o un engaño.

Se detuvo a personas de interés y un sospechoso adicional llamado "Zerry" fue detenido mientras los agentes allanaban su casa. Durante la búsqueda encontraron una computadora portátil, recolectaron la evidencia digital y la enviaron a la división forense digital de la ciudad de Nueva York.

La policía cree que Zerry está directamente asociado con la pandilla y está analizando su dispositivo para descubrir cualquier información sobre el posible ataque.

**Descargo de responsabilidad:** la historia, todos los nombres, personajes e incidentes retratados en este desafío son ficticios y cualquier relevancia para los eventos del mundo real es completamente coincidencia.

**En una carrera contra el tiempo, ¿podrás investigar una computadora portátil incautada por las fuerzas del orden para identificar si una amenaza de bomba es real o un engaño?**

![countdown.PNG](/assets/images/btlo-countdown/countdown.png)

# Presentación de investigación

Empezamos:

Damos click en “Start Investigation” y se abrira nuesta maquina virtual donde trabajaremos.

![image.png](/assets/images/btlo-countdown/6c1716b1-2c4c-4cfe-a8c2-53c1d168cd17.png)

![image.png](/assets/images/btlo-countdown/699c7407-d06e-4e5e-8117-179a5c947b9e.png)

### 1.Verifique la imagen del disco. Enviar SectorCount y MD5

💡**Sugerencia:** Lea el archivo Zerry.E01.txt que se generó durante la recopilación de pruebas.

Entrar a la carpeta “Investigation Files” que se encuentra en el escritorio y entramos a la carpeta “Disk Image”.

![image.png](/assets/images/btlo-countdown/1bc2b5b3-8a46-44a6-baf9-87996b1d67c9.png)

Dentro de Disk Image entramos a Zerry

![image.png](/assets/images/btlo-countdown/image%201.png)

Abrimos Zerry.E01.txt y buscamos lo que nos pide:

- Sector Count
- MD5 checksum

![image.png](/assets/images/btlo-countdown/6fb65de8-a1f7-4d65-98a6-11b8653c0f05.png)

![image.png](/assets/images/btlo-countdown/14c29235-2312-4019-aac0-77f0dc80b8a9.png)

**Respuesta:** 25,165,824 , 5c4e94315039f890e839d6992aeb6c58

### 2.¿Cuál es la clave de descifrado de la aplicación de mensajería online utilizada por Zerry?

💡**Sugerencia: V**erifique los listados de Programas instalados + Búsqueda previa de Windows en Autopsy para identificar las aplicaciones instaladas. Busque aplicaciones de mensajería en línea (especialmente aquellas que puedan usarse para una comunicación segura).

Una vez que crea que ha encontrado el programa adecuado, intente buscar en línea la "clave de descifrado del nombre de la aplicación". ¡Autopsy se puede utilizar para extraer archivos de la imagen del disco si necesita profundizar más!

Abrimos Autopsy, que se encuentra en el escritorio

![image.png](/assets/images/btlo-countdown/cc908012-9753-437d-8e5f-f0d41a0a136c.png)

Hacemos click en “Open Recent Case” y selecionamos Countdown y click en Open

![image.png](/assets/images/btlo-countdown/image%202.png)

Una vez ya cargado nuesto caso, lo que haremos sera buscar el archivo que necesitamos, para esto debes seguir esta direccion para encontrar el archivo:

Data Source > Zerry.E01 > vol3(NTFS/exFAT(0x07):104448-24125556) >Users >Zerry🍎 >AppData > Roaming > Signal > config.json

![image.png](/assets/images/btlo-countdown/f737c282-8e23-45fb-983d-dad004f9f705.png)

Al archivo config.json hacemos click dercho y click en Extract File(s)

![image.png](/assets/images/btlo-countdown/ff4a71af-b719-4866-ab11-ffeb1098e865.png)

Y lo extraemos en el escritorio 

![image.png](/assets/images/btlo-countdown/4c331197-4b25-43b2-829d-04dc9a827b68.png)

![image.png](/assets/images/btlo-countdown/69fa1a1d-8966-47ca-a123-6129174e01c9.png)

Abrimos el archivo config.json que esta en el esritorio. Y ahi tenemos la respuesta

![image.png](/assets/images/btlo-countdown/d47cd577-48e7-44a4-940b-92eb5c975b66.png)

**Respuesta**: c2a0e8d6f0853449cfcf4b75176c277535b3677de1bb59186b32f0dc6ed69998

### 3.¿Cuál es el número de teléfono registrado y el nombre de perfil de Zerry en la aplicación de mensajería utilizada?

💡Sugerencia: Puede abrir la base de datos SQL de la aplicación utilizando una de las herramientas instaladas para usted.

Entrar a la carpeta “Investigation Files” que se encuentra en el escritorio luego entramos a la carpeta “Tools” y abrimos SQLiteDatabaseBrowserPortable

![image.png](/assets/images/btlo-countdown/4fa412aa-b8d7-4db1-846b-5f98eb24fca9.png)

En la misma direccion de la pregunta 2, vamos a abrir sql, deberia ser asi:

Data Source > Zerry.E01 > vol3(NTFS/exFAT(0x07):104448-24125556) >Users >Zerry🍎 >AppData > Roaming > Signal > sql >db.sqlite

![image.png](/assets/images/btlo-countdown/f5e96536-3f9c-4256-ac5a-962a58b10ce9.png)

Extraemos el archivo “db.sqlite” al escritorio

![image.png](/assets/images/btlo-countdown/81052fa2-4595-481e-b790-f9127380ddc9.png)

Ahora entramos a SQLiteDatabaseBrowserPortable y click en “Open Database” , buscamos en Desktop y abrimos el archivo db.sqlite

![image.png](/assets/images/btlo-countdown/a4e5003e-5718-4b0e-a6c6-707bfa63b546.png)

Se abrira una ventana donde nos pedira la contraseña, para esto primero cambiamos “Passphrase” por “Raw key”

En Password añadimos: 0x , y al costado pegamos lo que es la llave que es la respuesta de la pregunta 2.

Seria: 0xc2a0e8d6f0853449cfcf4b75176c277535b3677de1bb59186b32f0dc6ed69998

Lo demas lo dejamos por defecto y hacemos click en OK

![image.png](/assets/images/btlo-countdown/754e7356-c06d-4b6e-b980-9c6d5c429d70.png)

Se nos abrira la Base de Datos

![image.png](/assets/images/btlo-countdown/52dc60aa-7f24-4d95-a1d2-ffd9921f1d88.png)

Luego vamos a la tabla “conversations” , damos click derecho y click en Browse Table

![image.png](/assets/images/btlo-countdown/6d21ac33-7e83-4085-96a7-a7ede477efd1.png)

Dentro de la tabla encontramos lo que es el telefono y perfil

![image.png](/assets/images/btlo-countdown/6a588fb8-01a0-49ae-aaf2-dde998b5d9bf.png)

![image.png](/assets/images/btlo-countdown/1561cf39-450b-4718-bd1d-7049930762be.png)

Para el caso del emoji que sale en el nombre, buscaremos en emojipedia. Buscamos Fire y copiamos el emoji

![image.png](/assets/images/btlo-countdown/bdd79bd7-b6df-4e30-aa9f-69cd290f8420.png)

**Respuesta:** 13026482364,ZerryThe🔥 

### 4.¿Cuál es el ID de correo electrónico que se encuentra en el chat?

💡Sugerencia: Mire diferentes tablas dentro de la base de datos.

Click derecho en “messages” y hacemos click en browse table

![image.png](/assets/images/btlo-countdown/image%203.png)

![image.png](/assets/images/btlo-countdown/image%204.png)

Podemos ver la conversacion y saber el contexto. Buscamos el ID del correo

**Respuesta:** eekurk@baybabes.com

### 5.¿Cuál es el nombre del archivo (incluida la extensión) que se recibe como archivo adjunto por correo electrónico?

💡Sugerencia: Los Documentos recientes pueden ayudarle a identificar esto, con el contexto de los registros de chat.

Entramos al Autopsy y vamos: Extracted Content << Recent Documents . Ahi encontramos el nombre del archivo 

![image.png](/assets/images/btlo-countdown/12e10cd5-92c6-42d8-be2b-966bc237cd6e.png)

Nos vamos a Emojipedia y buscamos los emojis que necesitamos

![image.png](/assets/images/btlo-countdown/image%205.png)

![image.png](/assets/images/btlo-countdown/75e06827-add9-4b37-a86f-9df5a49ee238.png)

**Respuesta:** ⌛📅.png

### 6.¿Cuál es la fecha y hora del ataque planeado?

💡Sugerencia: Exportar thumbcache_256.db de /Users/Zerry/AppData/Local/Microsoft/Windows/Explorer via Autopsy. Abra este archivo usando Thumbcache Viewer y observe las imágenes.

-Entramos a Autopsy y seguimos esta secuencia :

/Users/Zerry/AppData/Local/Microsoft/Windows/Explorer

Una vez dentro buscamos el archivo thumbcache_256.db , damos click derecho y Extract File(s) . Lo extraemos en el escritorio

![image.png](/assets/images/btlo-countdown/49801f4e-c261-4bfa-b394-f88b98315e5a.png)

![image.png](/assets/images/btlo-countdown/6cdd40d2-0437-4d48-a972-22415797fe67.png)

Vamos a la carpeta Investigation Files que se encuentra en el escritoio, luego a la carpeta Tools y abrimos el “thumbcache_viewer”

![image.png](/assets/images/btlo-countdown/0e3cf643-e968-4d58-99b9-b1de2b0d54ed.png)

![image.png](/assets/images/btlo-countdown/cf0ea2ee-0ac4-4003-be93-34e6f1a632a4.png)

Una vez abierta damos click en “File” y luego a “Open” , buscamos el archivo que extraimos en el escritorio “thumbcache_256”.

Observamos que la mayoria son de 0KB , pero uno es de 9KB donde se encuentra la fecha.

![image.png](/assets/images/btlo-countdown/8d097cc0-0aa9-4ff5-9d4c-765bd725a3aa.png)

Respuesta: 01-02-2021 09:00 AM

### 7.¿Cuál es la ubicación GPS de la explosión? El formato es el mismo que se encuentra en la evidencia. [Sugerencia: codificar (XX grados, XX minutos, XX segundos)]

💡Sugerencia: Es posible que esta información se haya almacenado en una nota adhesiva. Encuentra esto en /Users/Zerry/AppData/Local/Packages and export it.

Abra el archivo de base de datos plum, sqrlite en el /LocalState/ directory using SQLiteDatabaseBrowser.

Encuentre en qué tabla están almacenadas las notas. Podemos descubrir cómo descifrar el resultado codificado verificando los sitios visitados recientemente a través de Tor en Autopsy mirando /Tor Browser/Browser/TorBrowser/Data/Browser/profile.default/places.sqlite

Entramos a Autposy y buscamos la siguiente direccion : /Users/Zerry/AppData/Local/Packages

Buscamos la carpeta Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe y extraemos al escritorio toda la carpeta

![image.png](/assets/images/btlo-countdown/f667635d-6402-48f1-861a-b314a6a72ed4.png)

Abrimos SQLiteDatabaseBrowserPortable , luego click en Open Database , buscamos la carpeta en escritorio que es Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe , luego abrimos Local State y hacemos click al archivo plum.sqlite

![image.png](/assets/images/btlo-countdown/7ea55ac6-d81f-4248-98ff-3957d3d72bb8.png)

Click derecho a la tabla “Note” y click en Browse Table

![image.png](/assets/images/btlo-countdown/068cc6d6-8105-4c7d-8a0f-2f61a09e26e1.png)

Copiamos lo que se encuentra en la columna “Text”

![image.png](/assets/images/btlo-countdown/7ef49f9a-97be-477e-b2e7-7ecf1a2c4270.png)

Borramos la primera parte y dejamos desde “40 qrterrf …” hasta el ultimo digito 

![image.png](/assets/images/btlo-countdown/5e94ddc9-397c-4b87-b7d6-5dec5a99a651.png)

En Autopsy buscamos esta direccion TorBrowser/Browser/TorBrowser/Data/Browser/profile.default/places.sqlite

![image.png](/assets/images/btlo-countdown/image%206.png)

Y extraemos al escritorio el archivo places.sqlite

![image.png](/assets/images/btlo-countdown/dce8b3c9-d01f-438c-bdb7-f76cef674eb1.png)

Abrimos el archivo con SQLiteDatabaseBrowserPortable, haciendo click en Open Database y buscando el archivo places.sqlite

![image.png](/assets/images/btlo-countdown/da044cfa-4aba-4bc1-9ffc-dcd9cf832742.png)

Abrimos el archivo “moz_places” con clik derecho y en “Browse Table”

![image.png](/assets/images/btlo-countdown/ce6f5a9e-ab54-4239-a3a4-b17629698c77.png)

Observamos que es encirptado en rot13

![image.png](/assets/images/btlo-countdown/05672a5c-6550-4a1e-a19e-fc57d1b40b18.png)

Abrimos el CyberChef que se encuentra en el escritorio

![image.png](/assets/images/btlo-countdown/ce56a659-999d-4903-8bd0-21c5fd4ede4e.png)

En input ponemos lo que teniamos el “40 qrterrf …” y en Recipe el “ROT13”

![image.png](/assets/images/btlo-countdown/image%207.png)

Entonces nos sale la respuesta

### Felicidades, completaste todas las respuestas:

![image.png](/assets/images/btlo-countdown/7eed8c85-6bd9-40b3-bb88-b0b301f6218e.png)