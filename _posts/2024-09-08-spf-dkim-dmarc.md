---
layout: single
title: Verificaciones SPF, DKIM y DMARC
date: 2024-09-08
classes: wide
header:
  teaser: /assets/images/spf-dkim-dmarc/teaser.jpg
categories:
  - Security
tags:
  - Email Security
  - SPF
  - DKIM
  - DMARC
---

En este artículo, exploraremos las verificaciones SPF, DKIM y DMARC, que son esenciales para garantizar la seguridad y autenticidad de los correos electrónicos. Estas tecnologías ayudan a prevenir el phishing y el spam al verificar que los correos electrónicos provienen de fuentes legítimas. A continuación, se presentan detalles sobre cada una de estas verificaciones y cómo configurarlas.

# ¿Qué es SPF?

SPF (Sender Policy Framework) es un protocolo que permite a los propietarios de dominios especificar qué servidores de correo están autorizados para enviar correos electrónicos en su nombre. Esto se hace mediante la creación de un registro SPF en el DNS del dominio.

## Configuración de SPF

Para configurar SPF, agrega un registro TXT en el DNS de tu dominio con el siguiente formato:

```bash
v=spf1 include:spf.proveedor.com -all
```

<details>
  <summary>Explicación</summary>
  <p>
    - `v=spf1`: Indica la versión de SPF.
    - `include:spf.proveedor.com`: Autoriza a los servidores de correo del proveedor especificado.
    - `-all`: Indica que solo los servidores especificados están autorizados para enviar correos electrónicos.
  </p>
</details>

# ¿Qué es DKIM?

DKIM (DomainKeys Identified Mail) es un método de autenticación de correo electrónico que permite a los remitentes firmar digitalmente sus correos electrónicos para que los destinatarios puedan verificar su autenticidad.

## Configuración de DKIM

Para configurar DKIM, debes generar un par de claves pública y privada. La clave pública se agrega al DNS del dominio como un registro TXT, y la clave privada se utiliza para firmar los correos electrónicos.

### Generación de claves DKIM

```bash
openssl genrsa -out dkim_private.pem 2048
openssl rsa -in dkim_private.pem -pubout -out dkim_public.pem
```

### Registro DNS DKIM

Agrega un registro TXT en el DNS de tu dominio con el siguiente formato:

```bash
default._domainkey IN TXT "v=DKIM1; k=rsa; p=MIIBIjANBgkq..."
```

<details>
  <summary>Explicación</summary>
  <p>
    - `v=DKIM1`: Indica la versión de DKIM.
    - `k=rsa`: Especifica el algoritmo de cifrado.
    - `p=MIIBIjANBgkq...`: La clave pública generada.
  </p>
</details>

# ¿Qué es DMARC?

DMARC (Domain-based Message Authentication, Reporting, and Conformance) es un protocolo que permite a los propietarios de dominios especificar cómo manejar los correos electrónicos que fallan las verificaciones SPF y DKIM. También proporciona informes sobre el rendimiento de las políticas DMARC.

## Configuración de DMARC

Para configurar DMARC, agrega un registro TXT en el DNS de tu dominio con el siguiente formato:

```bash
v=DMARC1; p=none; rua=mailto:reportes@tu-dominio.com; ruf=mailto:fallos@tu-dominio.com; pct=100
```

<details>
  <summary>Explicación</summary>
  <p>
    - `v=DMARC1`: Indica la versión de DMARC.
    - `p=none`: Especifica la política a aplicar (none, quarantine, reject).
    - `rua=mailto:reportes@tu-dominio.com`: Dirección de correo para recibir informes agregados.
    - `ruf=mailto:fallos@tu-dominio.com`: Dirección de correo para recibir informes forenses.
    - `pct=100`: Porcentaje de correos electrónicos a los que se aplica la política.
  </p>
</details>

# Ejemplos de Verificaciones

A continuación, se presentan ejemplos de cómo se configuran y verifican SPF, DKIM y DMARC en la práctica.

## Ejemplo de Registro SPF

```bash
v=spf1 include:spf.proveedor.com -all
```

## Ejemplo de Registro DKIM

```bash
default._domainkey IN TXT "v=DKIM1; k=rsa; p=MIIBIjANBgkq..."
```

## Ejemplo de Registro DMARC

```bash
v=DMARC1; p=none; rua=mailto:reportes@tu-dominio.com; ruf=mailto:fallos@tu-dominio.com; pct=100
```

# Verificación de Configuraciones

Para verificar que las configuraciones de SPF, DKIM y DMARC están correctas, puedes utilizar herramientas en línea como:

- [MXToolbox](https://mxtoolbox.com)
- [DMARC Analyzer](https://dmarcian.com/dmarc-inspector/)
- [DKIM Core](http://dkimcore.org/tools/)

Estas herramientas te ayudarán a asegurarte de que tus registros DNS están configurados correctamente y que tus correos electrónicos son auténticos y seguros.
