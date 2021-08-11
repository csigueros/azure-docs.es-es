---
title: Creación de un certificado de cliente con Microsoft Azure Confidential Ledger
description: Creación de un certificado de cliente con Microsoft Azure Confidential Ledger
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 125f56a6c96e37394e4e9e2565093c847e592108
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585940"
---
# <a name="creating-a-client-certificate"></a>Creación de un certificado de cliente

Las API de Confidential Ledger requieren autenticación basada en certificados de cliente. Solo los certificados agregados a una lista de permitidos durante la creación de Ledger o la actualización de Ledger se pueden usar para llamar a las API funcionales de Confidential Ledger.

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

- [Información general de Microsoft Azure Confidential Ledger](overview.md)