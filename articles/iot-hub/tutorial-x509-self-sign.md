---
title: 'Tutorial: Uso de OpenSSL para crear certificados autofirmados para Azure IoT Hub | Microsoft Docs'
description: 'Tutorial: Uso de OpenSSL para crear certificados X.509 autofirmados para Azure IoT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: fe08b1dd87c754254067a9fc2ae4d022b9224d3d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551470"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Tutorial: Uso de OpenSSL para crear certificados autofirmados

Puede autenticar un dispositivo en la instancia de IoT Hub mediante dos certificados de dispositivo autofirmados. Esto se denomina a veces autenticación de huella digital porque los certificados contienen huellas digitales (valores hash) que se envían a IoT Hub. En los pasos siguientes se indica cómo crear dos certificados autofirmados. Este tipo de certificado se usa principalmente para pruebas.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Paso 1: Creación de una clave para el primer certificado

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Paso 2: Creación de un CSR para el primer certificado

Asegúrese de especificar el identificador de dispositivo cuando se le solicite.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Paso 3: Comprobación del puerto del RDP

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Paso 4: Certificado autofirmado 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device1.crt
```

## <a name="step-5---create-a-key-for-the-second-certificate"></a>Paso 5: Creación de una clave para el segundo certificado

```bash
openssl genpkey -out device2.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-6---create-a-csr-for-the-second-certificate"></a>Paso 6: Creación de CSR para el segundo certificado

Cuando se le solicite, especifique el mismo identificador de dispositivo que usó para el certificado 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:
```

## <a name="step-7---self-sign-certificate-2"></a>Paso 7: Certificado autofirmado 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-1"></a>Paso 8: Recuperación de la huella digital del certificado 1

```bash
openssl x509 -in device1.crt -noout -fingerprint
```

## <a name="step-9---retrieve-the-thumbprint-for-certificate-2"></a>Paso 9: Recuperación de la huella digital del certificado 2

```bash
openssl x509 -in device2.crt -noout -fingerprint
```

## <a name="step-10---create-a-new-iot-device"></a>Paso 10: Creación de un nuevo dispositivo IoT

Vaya a la instancia de IoT Hub en Azure Portal y cree una identidad del dispositivo IoT con las características siguientes:

* Proporcione el **identificador de dispositivo** que coincida con el nombre de sujeto de los certificados de dispositivo.
* Seleccione el tipo de autenticación **X.509 autofirmado**.
* Pegue las huellas digitales hexadecimales que ha copiado de los certificados principal y secundario del dispositivo. Asegúrese de que las cadenas hexadecimales no tengan ningún delimitador de dos puntos.


## <a name="next-steps"></a>Pasos siguientes

Vaya a la [prueba de la autenticación de certificados](tutorial-x509-test-certificate.md) para determinar si el certificado puede autenticar el dispositivo en el IoT Hub. El código de esa página requiere que use un certificado PFX. Use el siguiente comando openSSL para convertir el certificado .crt del dispositivo a formato .pfx.

```bash
openssl pkcs12 -export -in device.crt -inkey device.key -out device.pfx
```
