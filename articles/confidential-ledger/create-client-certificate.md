---
title: Creación de un certificado de cliente con Microsoft Azure Confidential Ledger
description: Creación de un certificado de cliente con Microsoft Azure Confidential Ledger
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 216a626bfaaf86a8c2237b5d871ab7895d921127
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476402"
---
# <a name="creating-a-client-certificate"></a>Creación de un certificado de cliente

Las API de Azure Confidential Ledger requieren autenticación basada en certificados de cliente. Solo los certificados agregados a una lista de permitidos durante la creación de libros de contabilidad o la actualización de libros de contabilidad se pueden usar para llamar a las API funcionales de Confidential Ledger.

Necesitará un certificado en formato PEM. Puede crear más de un certificado y agregarlos o eliminarlos mediante la API de actualización de Ledger.

## <a name="openssl"></a>OpenSSL

Se recomienda usar OpenSSL para generar certificados. Si tiene GIT instalado, puede ejecutar OpenSSL en el shell de GIT. De lo contrario, puede instalar OpenSSL para el sistema operativo.

- **Windows**: Instale [chocolatey para Windows](https://chocolatey.org/install), abra una ventana de terminal de PowerShell en modo de administrador y ejecute `choco install openssl`. Como alternativa, puede instalar OpenSSL para Windows directamente desde [aquí](http://gnuwin32.sourceforge.net/packages/openssl.htm).
- **Linux**: Ejecute `sudo apt-get install openssl`.

Luego puede generar un certificado ejecutando `openssl` en una ventana de terminal de Bash o PowerShell:

```bash
openssl ecparam -out "privkey_name.pem" -name "secp384r1" -genkey
openssl req -new -key "privkey_name.pem" -x509 -nodes -days 365 -out "cert.pem" -"sha384" -subj=/CN="ACL Client Cert"
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Microsoft Azure Confidential Ledger](overview.md)