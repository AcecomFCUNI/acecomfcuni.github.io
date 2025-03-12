---
layout: single
title: BTLO - Deep Blue
date: 2025-02-28
classes: wide
header:
  teaser: /assets/images/btlo-deepblue/image.png
categories:
  - Security
tags:
  - CTF
---

**Autor**: [https://github.com/EnocRosales20](https://github.com/EnocRosales20)

# Deep Blue 🐋

**Tema:** Incident Response

![image.png](/assets/images/btlo-deepblue/image.png)

**Escenario:**

Una estación de trabajo Windows se vio comprometida recientemente y la evidencia sugiere que fue un ataque contra RDP orientado a Internet, luego se implementó Meterpreter para realizar "Acciones sobre objetivos". ¿Puedes verificar estos hallazgos?

Se le han proporcionado las exportaciones de registros Security.evtx y System.evtx del sistema comprometido; debe analizarlos, NO los registros de Windows generados por la máquina del laboratorio (cuando utilice DeepBlueCLI, asegúrese de proporcionar la ruta a estos archivos, almacenados dentro de \Desktop\Investigation\.

**¿Puedes verificar estos hallazgos?**

![image.png](/assets/images/btlo-deepblue/image%201.png)

# Presentación de investigación

Empezamos:

Damos click en “Start Investigation” y se abrira nuesta maquina virtual donde trabajaremos.

![image.png](/assets/images/btlo-deepblue/def98322-a1e5-468f-b45e-144b0a58ea5e.png)

### 1. Usando DeepBlueCLI, investigue el registro de seguridad recuperado (Security.evtx). ¿Qué cuenta de usuario ejecutó GoogleUpdate.exe?

Entramos a Poweshell y ejecutamos:

```bash
cd desktop
ls
```

Ya viendo los archivos entramos a la carpeta “Investigation”:

```bash
cd investigation
ls  #para ver los archivos dentro
```

![image.png](/assets/images/btlo-deepblue/28105aa8-fedc-47a8-b0b5-2c07d791b661.png)

![image.png](/assets/images/btlo-deepblue/d8958eb5-0d70-4c99-b9d1-56ae64fd3e14.png)

Entramos al archivo DeepBlueCLI-master:

```bash
cd  DeepBlueCLI-master
ls
.\DeepBlue.ps1 ..\security.evtx
```

![image.png](/assets/images/btlo-deepblue/591e69d6-a69a-467c-8f5b-9927621b278f.png)

 Buscamos el usuario que ejecuto GoogleUpdate.exe

![image.png](/assets/images/btlo-deepblue/a26312dc-6925-4f3f-833e-66e9c4dd69b0.png)

**Respuesta:** Mike Smith

### 2. Usando DeepBlueCLI investigue el registro Security.evtx recuperado. ¿A qué hora hay evidencia probable de actividad de Meterpreter?

**¿Qué es Meterpreter?**

Meterpreter es una carga útil de ataque Metasploit que proporciona un shell interactivo desde el cual un atacante puede explorar la máquina objetivo y ejecutar código. Meterpreter se implementa mediante inyección de DLL en memoria. Como resultado, Meterpreter reside completamente en la memoria y no escribe nada en el disco. No se crean nuevos procesos ya que Meterpreter se inyecta en el proceso comprometido, desde el cual puede migrar a otros procesos en ejecución. Como resultado, la huella forense de un ataque es muy limitada.

Buscamos Meterpreter y encontramos la hora:

![image.png](/assets/images/btlo-deepblue/d53bc011-4154-45c1-9b4b-529bd4474bd4.png)

**Respuesta:** 4/10/2021 10:48:14 AM

### 3. Usando DeepBlueCLI investigue el registro System.evtx recuperado. ¿Cuál es el nombre del servicio sospechoso creado?

![image.png](/assets/images/btlo-deepblue/image%202.png)

Como nos pide el nombre del archivo sospechoso lo encontramos despues de “echo”:

![image.png](/assets/images/btlo-deepblue/6e34d883-f460-420d-9e10-a1698e9cbc72.png)

**Respuesta:** rztbzn

### 4.Investigue el registro de Security.evtx en el Visor de eventos. Se está auditando la creación del proceso (ID de evento 4688). Identifique el ejecutable malicioso descargado que se utilizó para obtener un shell inverso de Meterpreter, entre las 10:30 y las 10:50 a. m. del 10 de abril de 2021.

Abrimos el Visor de Eventod que se encuentra en la carpeta Investigation:

![image.png](/assets/images/btlo-deepblue/4d2fc278-9c85-44e3-a017-3e0694cef2c9.png)

Hacemos click el Open Saved log para poder abrir el archivo “Security” y ver su contenido

![image.png](/assets/images/btlo-deepblue/14da3262-5a20-4a54-b56b-3101aa8f1511.png)

Ya abierto …

![image.png](/assets/images/btlo-deepblue/b2e1e4c9-72fe-4939-8cef-df10aeebdf46.png)

Hacemos click en “Filter Current Log ..” para poder filtrarlo por el dia y hora que nos dice

![image.png](/assets/images/btlo-deepblue/2892022f-7593-4934-ae71-56a81845ca98.png)

Ya filtrado , buscamos el ejecutable y el usuario que nos pide

![image.png](/assets/images/btlo-deepblue/731d3202-3202-4b03-973a-0f90ae597875.png)

**Respuesta:** Mike Smith ,  serviceupdate.exe

### 5.También se cree que se creó una cuenta adicional para garantizar la persistencia entre las 11:25 a. m. y las 11:40 a. m. del 10 de abril de 2021. ¿Cuál se utilizó la línea de comando para crear esta cuenta? (¡Asegúrate de haber encontrado la cuenta correcta!)

Filtramos el dia y las horas que nos dice el enunciado .. 

![image.png](/assets/images/btlo-deepblue/c0af51ce-3229-4829-9e42-cc3b55f61eba.png)

 Buscamos y encontramos Process Command Line , entonces obtenemos la respuesta.

![image.png](/assets/images/btlo-deepblue/0e9cb702-6c23-41cc-a9be-4b59be443405.png)

**Respuesta:** net user ServiceAct/add

### 6. ¿A qué dos grupos locales se agregó esta nueva cuenta?

Buscamos los grupos locales que nos piden. Estas se encunatran en el mismo intervalo de horas que en la pregunta 5.

![image.png](/assets/images/btlo-deepblue/b0f71b57-456e-4854-9f66-0a237f3b32af.png)

Buscamos y encontramos los localgroup …

![image.png](/assets/images/btlo-deepblue/3a51e862-3d9d-413b-9293-c9b5a2cb3f63.png)

Respuesta: administrators, remote desktop users

## Felicidades, completaste todas las respuestas