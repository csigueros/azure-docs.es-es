---
title: Creación de certificados para Azure Stack Edge Pro con GPU desde Azure PowerShell | Microsoft Docs
description: En este artículo se explica cómo crear certificados en un dispositivo Azure Stack Edge Pro con GPU por medio de cmdlets de Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: fc7ec5574e0f0223a66bc4e42ce9029db0ae4fc6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364071"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Uso de certificados con el dispositivo Azure Stack Edge Pro con GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe el procedimiento para crear certificados propios mediante cmdlets de Azure PowerShell. En el artículo se incluyen las instrucciones que debe seguir si tiene previsto traer certificados propios al dispositivo de Azure Stack Edge.

Los certificados garantizan que la comunicación entre el dispositivo y los clientes que acceden a él es de confianza y que se envía información cifrada al servidor correcto. Cuando el dispositivo Azure Stack Edge está configurado desde el principio, se generan automáticamente certificados autofirmados. Como alternativa, puede aportar sus propios certificados. 

Puede usar uno de los métodos siguientes para crear certificados propios para el dispositivo:

 - Use los cmdlets de Azure PowerShell.
 - Use la herramienta Readiness Checker de Azure Stack Hub para crear solicitudes de firma de certificados (CSR) que ayuden a la entidad de certificación a emitirle certificados. 

En este artículo solo se describe el procedimiento para crear certificados propios mediante los cmdlets de Azure PowerShell. 

## <a name="prerequisites"></a>Requisitos previos

Antes de traer los certificados propios, asegúrese de que:

- Conoce los [tipos de certificados que se pueden usar con el dispositivo de Azure Stack Edge](azure-stack-edge-gpu-certificates-overview.md).
- Ha revisado los [requisitos de certificado para cada tipo de certificado](azure-stack-edge-gpu-certificate-requirements.md).


## <a name="create-certificates"></a>Creación de certificados

En la siguiente sección se describe el procedimiento para crear certificados de punto de conexión y de cadena de firma.


### <a name="certificate-workflow"></a>Flujo de trabajo de certificado

Tendrá una manera definida de creación de certificados para los dispositivos que funcionan en su entorno. Puede usar los certificados proporcionados por el administrador de TI. 

**Solo con fines de desarrollo o de prueba, también puede usar Windows PowerShell para crear certificados en el sistema local.** Al crear los certificados para el cliente, siga estas instrucciones:

1. Puede crear cualquiera de los siguientes tipos de certificados:

    - Cree un único certificado válido para su uso con un solo nombre de dominio completo (FQDN). Por ejemplo, *mydomain.com*.
    - Cree un certificado comodín para proteger también el nombre de dominio principal y varios subdominios. Por ejemplo, * *.mydomain.com*.
    - Cree un certificado de nombre alternativo del firmante (SAN) que abarque varios nombres de dominio en un solo certificado. 

2. Si trae su propio certificado, necesitará un certificado raíz para la cadena de firma. Consulte los pasos de [Creación de certificados de cadena de firma](#create-signing-chain-certificate).

3. A continuación, puede crear los certificados de punto de conexión para la interfaz de usuario local del dispositivo, el blob y Azure Resource Manager. Puede crear 3 certificados independientes para el dispositivo, el blob y el Azure Resource Manager, o un certificado para los 3 puntos de conexión. Para pasos detallados, consulte [Creación de certificados de punto de conexión firmados](#create-signed-endpoint-certificates).

4. Tanto si crea 3 certificados independientes como uno solo, especifique los nombres de los firmantes (SN) y los nombres alternativos de los firmantes (SAN) según las instrucciones proporcionadas para cada tipo de certificado. 

### <a name="create-signing-chain-certificate"></a>Creación de certificados de cadena de firma

Cree estos certificados mediante Windows PowerShell en modo de administrador. **Los certificados creados de esta manera deben usarse solo para fines de prueba o desarrollo.**

El certificado de cadena de firma solo debe crearse una vez. Los demás certificados de punto de conexión harán referencia a este certificado para firmar.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Creación de certificados de punto de conexión firmados

Cree estos certificados mediante Windows PowerShell en modo de administrador.

En estos ejemplos, los certificados de punto de conexión se crean para un dispositivo con:
    - Nombre de dispositivo: `DBE-HWDC1T2`
    - Dominio DNS: `microsoftdatabox.com`

Reemplace el valor por el nombre y el dominio DNS del dispositivo para crear certificados para él.
 
**Certificado de punto de conexión de blob**

Cree un certificado para el punto de conexión de blob en el almacén personal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado de punto de conexión de Azure Resource Manager**

Cree un certificado para los puntos de conexión de Azure Resource Manager en su almacén personal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado de interfaz de usuario web local del dispositivo**

Cree un certificado para la interfaz de usuario web local del dispositivo en el almacén personal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado único de varias SAN para todos los puntos de conexión**

Cree un certificado único para todos los puntos de conexión en el almacén personal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Después de crear los certificados, el paso siguiente consiste en cargarlos en el dispositivo Azure Stack Edge Pro con GPU.

## <a name="next-steps"></a>Pasos siguientes

[Carga de certificados en el dispositivo](azure-stack-edge-gpu-manage-certificates.md).
