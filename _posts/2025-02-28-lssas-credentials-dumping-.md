---
layout: single
title: Extraccion de Credenciales LSASS
date: 2025-02-28
classes: wide
header:
  teaser: 
categories:
  - Security
tags:
  - Research
---

**Autor**: [https://github.com/HugoRivas2101](https://github.com/HugoRivas2101)

# Extracción de Credenciales en LSASS

Si alguna vez te has preguntado cómo es que se produce la autenticación en windows y cómo los atacantes logran extraer credenciales en este sistema operativo (Yo también tuve esa duda y por eso me dieron ganas de investigar 😉), debes de leer este post. Vamos a entender uno de los procesos más críticos del sistema operativo: **LSASS (Local Security Authority Subsystem Service)**.

# Introducción a la Autenticación en Windows 👋

Cuando inicias sesión en Windows, no es solo un simple “verificación y acceso”. Detrás de escena, Windows sigue un flujo para validar quién eres y otorgarte acceso a los recursos del sistema. Para entenderlo mejor, lo separamos en 3 secciones:

## Captura de Credenciales - Winlogon y Credential Providers

1. El proceso WinLogon.exe gestiona el acceso interactivo del usuario y controla la autenticación en windows. Aunque es el proceso central, no maneja directamente la captura de credenciales. En su lugar, delega esta tarea a otros componentes.
2. LogonUI.exe es el proceso encargado de renderizar la pantalla de autenticación, es decir, se encarga de mostrar las opciones de inicio de sesión disponibles para el usuario.
3. Los Credential Providers son DLLs especializadas que capturan credenciales según el método de autenticación configurado en el sistema. Existen distintos tipos:
    - msv1_0.dll → Autenticación basada en usuario y contraseña.
    - Smart Card Providers → Autenticación mediante tarjetas inteligentes.
    - Windows Hello → Métodos biométricos como reconocimiento facial y huella digital.
    - Custom Providers → Métodos personalizados definidos por desarrolladores

![image.png](/assets/images/lssas-credentials-dumping/image.png)

## Validación de Credenciales - LSASS y Authentication Package

Una vez que el usuario ingresa las credenciales, es necesario que el sistema las valide. Este proceso ocurre dentro de LSASS, que se encarga de gestionar la autenticación.

1. LogonUI envía las credenciales capturadas a LSASS para su procesamiento y validación
2. LSASS determina qué Authentication Package debe utilizar en función del tipo de autenticación
    - NTLM (msv1_0.dll) → Utiliza hashes NTLM.
    - Kerberos (kerberos.dll) → Método predeterminado en Active Directory.
    - Smartcards (certcredprov.dll) → Autenticación con tarjetas inteligentes.
3. LSASS valida las credenciales contra la base de datos correspondiente:
    - Si el usuario es **local, LSASS** consulta SAM (Security Account Manager), que almacena los hashes de contrase;as de cuentas locales.
    - Si el usuario pertenece a un **dominio,** LSASS consulta NTDS (Active Directory) para autenticar al usuario dentro de la infraestructura empresarial.
    

![image.png](/assets/images/lssas-credentials-dumping/image%201.png)

## Generación del Token de Seguridad - Interacción con SRM

Una vez autenticado el usuario, el sistema debe otorgarle un token de seguridad que le permitir[a acceder a los recursos adecuados dentro del sistema operativo

1. LSASS llama a una función en el Security Reference Monitor (un ejemplo es NtCreateToken) para generar un objeto Token de Acceso.
2. Este token contiene la informacion clave sobre la identidad y privilegios del usuario, incluyendo
    - SID (Security Identifier) → Identificador único del usuario.
    - Grupos a los que pertenece
    - Privilegios asignados
3. SRM se encarga de aplicar reglas de seguridad y los permisos del usuario en cada acceso a archivos, servicios y otros recursos del sistema.

Nota: Este token permanecerá activo durante toda la sesión del usuario y será utilizado para verificar qué acciones puede realizar dentro del sistema.

![image.png](/assets/images/lssas-credentials-dumping/image%202.png)

En resumen, el flujo de autenticación sería el siguiente:

![image.png](/assets/images/lssas-credentials-dumping/image%203.png)

# Conociendo un poco más a LSASS 🔍

El Local Security Authority Subsystem Service (LSASS) es uno de los procesos más críticos de Windows. No solo gestiona la autenticación de usuarios, sino que también administra tokens de seguridad, aplica políticas de acceso y protege credenciales almacenadas en memoria.

A lo largo del tiempo, este proceso ha sido un objetivo clave en ataques de extracción de credenciales, ya que almacena información valiosa sobre los usuarios autenticados en el sistema.

Para entender cómo funciona, vamos a desglosar sus principales componentes.

## Authentication Packages

LSASS no realiza la autenticación por sí solo; en su lugar, delega esta tarea a distintos paquetes de autenticación, que son DLLs especializadas en procesar diferentes métodos de login.

Cada vez que un usuario intenta autenticarse, LSASS carga dinámicamente los paquetes necesarios y les delega la validación de las credenciales.

A continuación se muestran los paquetes de autenticación más importantes:

- msv1_0.dll: Maneja autenticación NTLM y almacena los hashes NTLM de usuarios autenticados.
- kerberos.dll: Implementa Kerberos el cual es usado en entornos de Active Directory.
- wdigest.dll: Almacena contraseñas en texto claro en memoria. Actualmente está deshabilitado por defecto.
- samsrv.dll: Permite a LSASS comunicarse con la SAM (Security Account Manager), la base de datos de usuarios locales.
- tspkg.dll: Gestiona la autenticación en sesiones RDP (Remote Desktop Protocol).
- livessp.dll: Maneja la autenticación de cuentas Microsoft (Office 365, Azure AD).

**Nota** ⚠️ **: Cuando un atacante extrae las credenciales de LSASS, se obtienen directamente desde estos paquetes de autenticación.**

## LSA Policy Database

Además de manejar autenticaciones, LSASS también almacena y aplica políticas de seguridad dentro del sistema. Algunas funciones clave:

- Aplicación de restricciones como expiración de contraseñas y bloqueos de cuentas.
- Monitoreo de eventos de seguridad relacionados con autenticadores

Este módulo garantiza que las autenticaciones no solo sean válidas, sino que también cumplan con las políticas de seguridad definidas en el sistema.

## Secure Subsystems

LSASS no trabaja solo. Se comunica con varios subsistemas de seguridad en Windows para gestionar credenciales y accesos.

1. **SAM (Security Account Manager)**
    
    Base de datos que almacena usuarios y hashes de contraseñas en sistemas locales.
    
2. **Active Directory (NTDS.dit)**
    
    Base de datos utilizada en entornos de dominio que almacena todas las credenciales y políticas de seguridad de la organización
    
3. **Credential Manager (VaultSVC)**
    
    Administrador de credenciales en Windows que almacena contraseñas de red, aplicaciones y sesiones RDP. Usa DPAPI (Data Protection API) para cifrar las credenciales almacenadas.
    

# Dumpeo del proceso LSASS: Extracción de Credenciales en Memoria 💀

Uno de los métodos más utilizados en ataques de post-explotación en Windows es la extracción de credenciales de LSASS, ya que este proceso almacena hashes NTLM, tickets Kerberos, claves DPAPI y, en algunos casos, contraseñas en texto claro.

Windows no permite que cualquier usuario acceda a la memoria de LSASS, ya que esto pondría en riesgo la seguridad del sistema. Para extraer credenciales, necesitamos tener el privilegio SeDebugPrivilege habilitado (privilegio que solo tienen los administradores locales por defecto).

## Extracción a través del Task Manager

Si tenemos acceso a una sesión RDP o interactiva, podemos usar el Task Manager (taskmgr.exe) para generar un dump de LSASS:

![image.png](/assets/images/lssas-credentials-dumping/image%204.png)

![image.png](/assets/images/lssas-credentials-dumping/image%205.png)

**Nota** ⚠️ **: La extracción del archivo lsass.dmp tiene como ventaja que se puede analizar de forma offline para extraer las credenciales sin dejar rastros en el sistema en ejecución.**

## Extracción con Mimikatz

Si tenemos permisos suficientes, podemos extraer credenciales directamente desde la memoria de LSASS usando Mimikatz:

```jsx
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords
```

Observación: Si sekurlsa::logonPasswords deuvelve null en algunos campos, es posible que Credential Guard esté habilitado.

Si ya tenemos un volcado de LSASS (lsass.dmp), podemos analizarlo con Mimikatz sin ejecutar código en la máquina comprometida:

```jsx
sekurlsa::minidump C:\ruta\lsass.dmp
sekurlsa::logonPasswords
```

## Análisis del Output de Mimikatz

Cuando ejecutamos sekurlsa::logonPasswords, obtenemos una estructura detallada con información sobre las sesiones activas y las credenciales almacenadas en LSASS.

Vamos a desglosar cada campo para entender qué información podemos extraer.

![image.png](/assets/images/lssas-credentials-dumping/image%206.png)

### Identificadores Generales

- Authentication ID: Es el identificador único de la sesión.
- Session: Indica el tipo de sesión activa. En este caso, se observa que es una sesión remota de tipo 5. Podemos ver más acerca de las sesiones visitando este enlace [https://eventlogxp.com/blog/logon-type-what-does-it-mean/](https://eventlogxp.com/blog/logon-type-what-does-it-mean/).
- Username: Nombre de usuario autenticado.
- Domain: Nombre del dominio en el que se autenticó el usuario.
- Logon Server: Controlador de dominio o servidor que manejó la autenticación.
- Logon Time: Fecha y hora en que se estableció la sesión de autenticación
- SID (Security Identifier): Identificador de seguridad único del usuario. Este identificador se usa para validar permisos y accesos dentro del sistema.

### Paquete de Authenticación MSV (NTLM)

- MSV (MSV1_0.dll): Este es el paquete de autenticación de NTLM usado por windows.
- NTLM: Hash NTLM del usuario el cual puede ser usado para realizar ataques de tipo pass the hash y autenticarse sin conocer la contraseña real.
- SHA1: Versión en SHA1 del hash NTLM
- DPAPI: Clave utilizada para proteger credenciales y certificados almacenados en el sistema. Lo podemos usar para desencriptar credenciales almacenadas en Windows Credential Manager.

### Paquete de autenticación TSPKG (RDP)

- tspkg.dll: Maneja credenciales de autenticación en sesiones RDP.

### Paquete de autenticación WDigest (Texto Claro)

- wdigest.dll: En versiones antiguas de Windows, almacenaba contraseñas en texto claro. En nuestro ejemplo, el campo está en null debido a que está deshabilitado por defecto a partir de Windows 8.1 y de Windows Server 2012.
- Podemos habilitar el almacenamiento de credenciales en texto claro editando un registro:

```jsx
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 1 /f
```

Verificamos que se ejecuta usando el siguiente comando.

```jsx
reg query HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential
```

![image.png](/assets/images/lssas-credentials-dumping/image%207.png)

Luego de agregar dicho registro, debemos reiniciar o solo salir de la sesion actual. Veremos las credenciales de wdigest en texto claro.

![image.png](/assets/images/lssas-credentials-dumping/image%208.png)

Podemos deshabilitar nuevamente el registro usando el siguiente comnado

```jsx
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 0 /f
```

### Paquete de autenticación Kerberos

- kerberos.dll: Maneja autenticaciones en Active Directory. Aquí aparecerían tickets de autenticación Kerberos (TGT, TGS).

### Otros paquetes de autenticación

- ssp.dll: Maneja autenticación para procesos del sistema y autenticación Single Sign On (SSO).
- credman.dll: Almacena credenciales en cache usadas por aplicaciones en caché.
- cloudap.dll: Maneja autenticación en la nube (Azure AD, Office 365).

# Conclusiones

- LSASS es el núcleo de la autenticación en Windows y un objetivo clave en ataques post-explotación. Mientras una sesión esté activa, las credenciales permanecen en memoria dentro de los paquetes de autenticación, lo que permite su extracción mediante herramientas como Mimikatz.
- El dumpeo de LSASS puede revelar hashes NTLM, tickets Kerberos y claves DPAPI, facilitando ataques como Pass-the-Hash y Pass-the-Ticket. Sin embargo, Windows ofrece protecciones como Credential Guard y RunAsPPL, que pueden mitigar estos ataques si se configuran correctamente.
- Para proteger un sistema contra la extracción de credenciales, es crucial habilitar estas defensas, restringir accesos privilegiados y monitorear la actividad de LSASS. En entornos sin estas medidas, cualquier atacante con acceso a LSASS puede comprometer por completo el sistema.

# Bibliografía

https://attack.mitre.org/techniques/T1003/

[https://attack.mitre.org/techniques/T1003/001/](https://attack.mitre.org/techniques/T1003/001/)

[https://books.spartan-cybersec.com/cpad/persistencia-en-windows-local/que-es-mimikatz/lsass](https://books.spartan-cybersec.com/cpad/persistencia-en-windows-local/que-es-mimikatz/lsass)

[https://blog.cyberadvisors.com/technical-blog/attacks-defenses-dumping-lsass-no-mimikatz/](https://blog.cyberadvisors.com/technical-blog/attacks-defenses-dumping-lsass-no-mimikatz/)

[https://techcommunity.microsoft.com/blog/itopstalkblog/ops108-windows-authentication-internals-in-a-hybrid-world/2109557](https://techcommunity.microsoft.com/blog/itopstalkblog/ops108-windows-authentication-internals-in-a-hybrid-world/2109557)

[https://github.com/lucky-luk3/Windows_Internals](https://github.com/lucky-luk3/Windows_Internals)

[https://eventlogxp.com/blog/logon-type-what-does-it-mean/](https://eventlogxp.com/blog/logon-type-what-does-it-mean/)

[https://www.microsoftpressstore.com/articles/article.aspx?p=2228450&seqNum=8](https://www.microsoftpressstore.com/articles/article.aspx?p=2228450&seqNum=8)

[https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-azod/d28d536d-3973-4c8d-b2c9-989e3a8ba3c5](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-azod/d28d536d-3973-4c8d-b2c9-989e3a8ba3c5)