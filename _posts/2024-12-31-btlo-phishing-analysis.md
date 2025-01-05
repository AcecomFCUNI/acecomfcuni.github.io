---
layout: single
title: BTLO - Phishing Analysis
date: 2024-12-31
classes: wide
header:
  teaser: /assets/images/btlo-phishing-analysis/teaser.jpg
categories:
  - Security
tags:
  - CTF
---

![image.png](/assets/images/btlo-phishing-analysis/teaser.jpg)

**Autor**: Elias

En este laboratorio de Blue Team Labs Online, hemos recibido un correo electrónico de phishing reenviado por un usuario al SOC. El objetivo es investigar el correo y su archivo adjunto para recopilar información útil y detectar posibles amenazas.

Usaremos **Editor de Texto** para revisar los encabezados del correo, el remitente y los enlaces incluidos en el mensaje. Esto nos permitirá verificar si el correo proviene de una fuente confiable o si contiene señales de falsificación. También utilizaremos **URL2PNG** para capturar vistas previas de los enlaces sospechosos y analizar si apuntan a sitios maliciosos. Además, realizaremos una consulta **WHOIS** sobre los dominios relacionados para identificar registros de dominios sospechosos.

En caso de que el archivo adjunto contenga malware, realizaremos un análisis detallado en un entorno seguro. Al final, se proporcionarán recomendaciones sobre cómo prevenir futuros ataques de phishing, incluyendo el bloqueo de direcciones sospechosas y la educación de los usuarios para evitar caer en este tipo de engaños.

![image.png](/assets/images/btlo-phishing-analysis/image%201.png)

### Enunciado:

Un usuario recibió un correo electrónico de phishing y lo reenvió al SOC. ¿Puede investigar el correo electrónico y el archivo adjunto para recopilar información útil?

![image.png](/assets/images/btlo-phishing-analysis/8023badc-497c-4772-be6e-fe2b199a01f3.png)

==============================================================

### **Nota:** Este laboratorio lo resolveremos en Windows, no usaremos comandos , solo analizaremos el phising.

==============================================================

**PRIMEROS PASOS:**

Procedemos a descargar y descompimir el archivo:

![image.png](/assets/images/btlo-phishing-analysis/2d77883a-428f-4e6d-8020-579fdfcb9d2b.png)

Nos pedira la contraseña y esta es : btlo

![image.png](/assets/images/btlo-phishing-analysis/956dfe92-8190-4199-b751-fa0f3c7fcc67.png)

Luego abriremos el archivo con Editor de Texto, tambien lo abriremos con Outlook.

![image.png](/assets/images/btlo-phishing-analysis/53779d57-479c-4954-b103-c6a3d0b8bdc8.png)

## RESPONDAMOS:

### 1.¿Quién es el destinatario principal de este correo electrónico?

Buscamos el destinatario: Podemos buscar mas rapidamente entrando en Edicion y luego en Buscar, o tambien haciendo Ctrl + B :

![image.png](/assets/images/btlo-phishing-analysis/7ed2032e-8fb1-4791-85de-bc67d01940bd.png)

**Respuesta:** kinnar1975@yahoo.co.uk 

### 2.¿Cuál es el asunto de este correo electrónico?

![image.png](/assets/images/btlo-phishing-analysis/377b796e-df68-41a8-8049-a8c814337acd.png)

**Respuesta :** Undeliverable: Website contact form submission

### 3.¿Cuál es la fecha y hora en que se envió el correo electrónico?

![image.png](/assets/images/btlo-phishing-analysis/f6909762-85e4-497a-85bb-79153fddfabd.png)

**Respuesta:** 18 March 2021 04:14

### 4.¿Cuál es la IP de origen?

![image.png](/assets/images/btlo-phishing-analysis/572b7082-bf86-4cf1-8933-767d80ec439c.png)

**Respuesta:** 103.9.171.10

### 5.Realizar DNS inverso en esta dirección IP, ¿cuál es el host resuelto? ([whois.domaintools.com](http://whois.domaintools.com/))

**Domaintools:** es una plataforma que recopila datos de dominios, sitios web y DNS para ofrecer análisis de riesgos y contexto inmediato.

Para responder esta pregunta, entramos a  [whois.domaintools.com](http://whois.domaintools.com/) y pegaremos la IP:

![image.png](/assets/images/btlo-phishing-analysis/6b1375f7-8c0c-41ee-9e45-68c5beae23f6.png)

Tambien lo podemos encontrar en el Editor de Texto

![image.png](/assets/images/btlo-phishing-analysis/68c4735e-00fe-4369-93df-0874ad0fb148.png)

**Respuesta:** c5s2-1e-syd.hosting-services.net.au

### 6.¿Cómo se llama el archivo adjunto?

![image.png](/assets/images/btlo-phishing-analysis/18f7466e-92bd-448d-be72-1b1ba2e2c5b5.png)

**Respuesta:** Website contact form submission.email

### 7.¿Cuál es la URL que se encuentra dentro del archivo adjunto?

![image.png](/assets/images/btlo-phishing-analysis/20ca3998-4e6b-466f-a078-5d2cd97ff6ef.png)

**Respuesta:** 

https://35000usdperwwekpodf.blogspot.sg?p=3D9swghttps://35000usdperww=ekpodf.blogspot.co.il?o=3D0hnd

### 8.¿En qué servicio está alojada esta página web?

De la URL podemos observar el servicio:

![image.png](/assets/images/btlo-phishing-analysis/ef877c2c-422f-4889-b8ec-bf7556158b88.png)

**Respuesta:** blogspot

### 9.Usando URL2PNG, ¿cuál es el texto del encabezado de esta página? (¡No importa si la página ha sido eliminada!)

URL2PNG: Es un servicio que permite a las empresas en línea capturar capturas de pantalla de páginas web y convertirlas en archivos de imagen

![image.png](/assets/images/btlo-phishing-analysis/dd845831-3b82-4353-9b29-f03bab1cc4ab.png)

Respuesta: Blog has been removed

### **Felicidades, completaste todas las respuestas**

![image.png](/assets/images/btlo-phishing-analysis/91e7567a-3b68-434c-8521-093b99400baf.png)