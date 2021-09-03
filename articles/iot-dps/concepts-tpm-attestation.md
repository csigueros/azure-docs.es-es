---
title: 'Servicio Azure IoT Hub Device Provisioning: atestación de TPM'
description: En este artículo se proporciona información general y conceptual del flujo de atestación de TPM mediante Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 06/21/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e670b0feabfe785712dc7140bcc594b770783e74
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112457883"
---
# <a name="tpm-attestation"></a>Atestación de TPM

IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que se utiliza para configurar el aprovisionamiento de dispositivos sin interacción de un centro IoT especificado. Con el servicio Device Provisioning puede aprovisionar millones de dispositivos de forma segura.

En este artículo se describe el proceso de atestación de identidad cuando se usa un Módulo de plataforma segura (TPM). Un TPM es un tipo de módulo de seguridad de hardware (HSM). En este artículo se supone que usa un TPM discreto, de firmware o integrado. Los TPM emulados por software son adecuados para prototipos o pruebas, pero no brindan el mismo nivel de seguridad que los TPM discretos, de firmware o integrados. No se recomienda el uso de TPM de software en producción. Para obtener más información acerca de los tipos de TPM, consulte [A Brief Introduction to TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) (Una breve introducción a TPM).

Este artículo solo es pertinente para dispositivos que utilizan TPM 2.0 con compatibilidad con claves de HMAC y sus claves de aprobación. No es para los dispositivos que utilizan certificados X.509 para la autenticación. TPM es un estándar ISO para todo el sector industria de Trusted Computing Group; puede obtener más información sobre TPM en [ la especificación TPM 2.0 completa](https://trustedcomputinggroup.org/tpm-library-specification/) o la [ especificación ISO/IEC 11889 ](https://www.iso.org/standard/66510.html). En este artículo también se da por supuesto que está familiarizado con los pares de claves públicas y privadas, y cómo se utilizan para el cifrado.

Los SDK de dispositivo del servicio Device Provisioning controlan todo lo que se describe en este artículo en su lugar. No es necesario implementar nada adicional si usa los SDK en los dispositivos. Este artículo le ayudará a comprender conceptualmente lo que ocurre con el chip de seguridad de TPM cuando su dispositivo se aprovisiona y por qué es tan seguro.

## <a name="overview"></a>Información general

TPM usa algo denominado clave de aprobación como raíz de confianza segura. La clave de aprobación es única para el TPM, y su cambio básicamente transforma el dispositivo en otro nuevo.

Los TPM tienen otro tipo de clave denominada clave raíz de almacenamiento. Una clave raíz de almacenamiento puede generarse por el propietario del TPM una vez que tome posesión del TPM. Tomar posesión de TPM es la forma específica del TPM de decir "alguien establece una contraseña en el HSM". Si un dispositivo TPM se vende a un nuevo propietario, el nuevo propietario puede tomar posesión de TPM para generar una nueva clave raíz de almacenamiento. La generación de una nueva clave raíz de almacenamiento garantiza que el propietario anterior no puede usar el TPM. Dado que la clave raíz de almacenamiento es única para el propietario del TPM, puede utilizarse para sellar datos en el propio TPM para ese propietario. La clave raíz de almacenamiento ofrece un espacio aislado al propietario para almacenar sus claves y permite revocar el acceso en caso de que el dispositivo o el TPM se venda. Es lo mismo que mudarse a una casa nueva: tomar propiedad implica cambiar las cerraduras y deshacerse de los muebles que hayan dejado los propietarios anteriores (clave raíz de almacenamiento), pero no puede cambiar la dirección de la casa (clave de aprobación).

Una vez que un dispositivo se ha configurado y está listo para usarse, tendrá una clave de aprobación y una clave raíz de almacenamiento listas para su uso.

![Tomar posesión de un TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Una nota sobre la toma de posesión del TPM: la toma de posesión del TPM depende de muchas cosas, como el fabricante del TPM, el conjunto de herramientas de TPM que se utilizan y el SO del dispositivo. Siga las instrucciones pertinentes para el sistema a fin de tomar posesión.

El servicio Device Provisioning usa la parte pública de la clave de aprobación para identificar e inscribir dispositivos. El proveedor del dispositivo puede leer la parte pública de la clave de aprobación durante la fabricación o la prueba final y cargar la parte pública de la clave de aprobación en el servicio de aprovisionamiento de manera que el dispositivo se reconozca cuando se conecte al aprovisionamiento. El servicio Device Provisioning no comprueba la clave raíz de almacenamiento ni el propietario; por tanto, "borrar" el TPM elimina los datos del cliente, pero se mantiene la clave de aprobación (y otros datos del proveedor) y el dispositivo seguirá siendo reconocido por el servicio Device Provisioning cuando se conecte al aprovisionamiento.

## <a name="detailed-attestation-process"></a>Proceso de atestación detallado

Cuando un dispositivo con un TPM se conecta por primera vez al servicio Device Provisioning, el servicio comprueba primero la parte pública de la clave de aprobación con la parte pública de la clave de aprobación almacenada en la lista de inscripción. Si la parte pública de la clave de aprobación no coincide, el dispositivo no se puede aprovisionar. Si la parte pública de la clave de aprobación no coincide, el servicio requiere que el dispositivo demuestre la propiedad de la parte privada de la clave de aprobación por medio de un desafío nonce, que es un desafío seguro que se utiliza para demostrar la identidad. El servicio Device Provisioning genera un nonce y, a continuación, lo cifra con la clave raíz de almacenamiento y la parte pública de la clave de aprobación, que se suministran en ambos casos por parte del dispositivo durante la llamada de registro inicial. El TPM siempre mantiene la parte privada de la clave de aprobación segura. Esto evita la falsificación y garantiza que los tokens SAS se aprovisionen de manera segura en dispositivos autorizados.

Analicemos el proceso de atestación en detalle.

### <a name="device-requests-an-iot-hub-assignment"></a>El dispositivo solicita una asignación de IoT Hub

En primer lugar, el dispositivo se conecta al servicio Device Provisioning y solicita el aprovisionamiento. Al hacerlo, el dispositivo proporciona al servicio un identificador de registro, un ámbito de identificador y la parte pública de la clave de aprobación y la clave raíz de almacenamiento del TPM. El servicio pasa el nonce cifrado de nuevo al dispositivo y le solicita que lo descifre, y lo usa para firmar un token SAS para conectarse de nuevo y finalizar el aprovisionamiento.

![El dispositivo solicita aprovisionamiento](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Desafío nonce

El dispositivo toma el nonce y usa las porciones privadas de la clave privada y la clave raíz de almacenamiento para descifrar el nonce en el TPM; la solicitud de cifrado del nonce delega la confianza desde la clave de aprobación, que es inmutable, a la clave raíz de almacenamiento, que puede cambiar si un nuevo propietario toma posesión del TPM.

![Descifrado del nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validación del nonce y recepción de credenciales

El dispositivo puede firmar un token de SAS utilizando el nonce descifrado y restablecer una conexión con el servicio Device Provisioning utilizando el token SAS firmado. Con el desafío de nonce completado, el servicio permite que el dispositivo se aprovisione.

![El dispositivo restablece la conexión con el servicio Device Provisioning para validar la propiedad de la clave de aprobación](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora el dispositivo se conecta a IoT Hub y se queda con la certeza de que las claves de sus dispositivos están almacenadas de forma segura. Ahora que ya sabe cómo el servicio Device Provisioning verifica de forma segura la identidad de un dispositivo con TPM, consulte los siguientes artículos para obtener más información:

* [Conceptos sobre el aprovisionamiento](about-iot-dps.md#provisioning-process)
* [Introducción al uso del aprovisionamiento automático](./quick-setup-auto-provision.md) 
* [Creación de inscripciones de TPM mediante los SDK](./quick-enroll-device-tpm-java.md)