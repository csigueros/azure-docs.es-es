---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 374fcb6470ab22bfd3531d9ef543bf3cc06e17ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576795"
---
## <a name="generate-device-identity-certificates"></a>Generación de los certificados de identidad del dispositivo

El aprovisionamiento manual con certificados X.509 requiere IoT Edge, versión 1.0.10 o posterior.

Al aprovisionar un dispositivo IoT Edge con certificados X.509, se usa lo que se denomina un *certificado de identidad del dispositivo*. Este certificado solo se usa para aprovisionar un dispositivo IoT Edge y autenticarlo en Azure IoT Hub. Es un certificado de hoja que no firma otros certificados. El certificado de identidad del dispositivo es independiente de los certificados de la entidad de certificación (CA) que el dispositivo IoT Edge presenta a los módulos o dispositivos de nivel inferior para la verificación.

En el caso de la autenticación mediante certificados X.509, la información de autenticación de cada dispositivo se proporciona en forma de *huellas digitales* tomadas de los certificados de identidad del dispositivo. Estas huellas digitales se proporcionan a IoT Hub en el momento de registrar el dispositivo para que el servicio pueda reconocer el dispositivo al conectarse.

Para obtener más información sobre cómo se usan los certificados de CA en los dispositivos IoT Edge, consulte [Información sobre los certificados de Azure IoT Edge](../articles/iot-edge/iot-edge-certs.md).

Necesita los siguientes archivos para el aprovisionamiento manual con X.509:

* Dos certificados de identidad del dispositivo con sus certificados de clave privada coincidentes en los formatos .cer o .pem.

  Se proporciona un conjunto de archivos de certificado y clave al entorno de ejecución de IoT Edge. Al crear los certificados de identidad del dispositivo, establezca el nombre común del certificado (CN) en el id. de dispositivo que quiere que tenga el dispositivo en el centro de IoT.

* Huellas digitales tomadas de ambos certificados de identidad del dispositivo.

  Los valores de huella digital son 40 caracteres hexadecimales para hashes de tipo SHA-1 o 64 caracteres hexadecimales para hashes de tipo SHA-256. Ambas huellas digitales se proporcionan a IoT Hub en el momento del registro del dispositivo.

Si no tiene certificados disponibles, puede proceder a la [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](../articles/iot-edge/how-to-create-test-certificates.md). Siga las instrucciones de ese artículo para configurar scripts de creación de certificados, crear un certificado de CA raíz y luego crear dos certificados de identidad del dispositivo IoT Edge.

Una manera de recuperar la huella digital de un certificado es con el siguiente comando openssl:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```
