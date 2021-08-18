---
title: Lista de alertas integradas y personalizadas
description: Obtenga información sobre las alertas de seguridad y la corrección recomendada mediante las características y el servicio de Defender para IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018225"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alertas de seguridad de Defender para IoT Hub

Defender para IoT analiza continuamente la solución de IoT mediante análisis avanzados e inteligencia de amenazas para alertarle de cualquier actividad malintencionada.
Además, puede crear alertas personalizadas según su conocimiento del comportamiento esperado del dispositivo.
Una alerta sirve como indicador de un posible peligro, y debe investigarse y corregirse.

En este artículo encontrará una lista de alertas integradas que pueden activarse en IoT Hub.
Además de las alertas integradas, Defender para IoT le permite definir alertas personalizadas basadas en el comportamiento esperado de IoT Hub o del dispositivo.
Para obtener más información, consulte las [alertas personalizables](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Alertas integradas para IoT Hub

| severity | Nombre | Descripción | Corrección sugerida |
|--|--|--|--|
| Gravedad **media** |  |  |  |
| New certificate added to an IoT Hub (Nuevo certificado agregado a una instancia de IoT Hub) | Media | Se agregó un certificado con el nombre \'%{DescCertificateName}\' a \'%{DescIoTHubName}\' de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | 1. Asegúrese de que el certificado lo agregó una entidad autorizada. <br> 2. Si no lo agregó una entidad autorizada, quite el certificado y escale la alerta al equipo de seguridad de la organización. |
| Certificate deleted from an IoT Hub (Certificado eliminado de una instancia de IoT Hub) | Media | Un certificado con el nombre \'%{DescCertificateName}\' se eliminó de \'%{DescIoTHubName}\' de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | 1. Asegúrese de que el certificado lo quitó una entidad autorizada. <br> 2. Si el certificado no lo quitó una entidad autorizada, vuelva a agregarlo y escale la alerta al equipo de seguridad de la organización. |
| Unsuccessful attempt detected to add a certificate to an IoT Hub (Detección de intento fallido de agregar un certificado a una instancia de IoT Hub) | Media | Se produjo un intento incorrecto de agregar el certificado \'%{DescCertificateName}\' a \'%{DescIoTHubName}\' de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | Asegúrese de que los permisos para cambiar certificados solo se conceden a las entidades autorizadas. |
| Unsuccessful attempt detected to delete a certificate from an IoT Hub (Detección de intento fallido de eliminar un certificado de una instancia de IoT Hub) | Media | Se produjo un intento incorrecto de eliminar el certificado \'%{DescCertificateName}\' de \'%{DescIoTHubName}\' de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | Asegúrese de que los permisos para cambiar certificados solo se conceden a una entidad autorizada. |
| x.509 device certificate thumbprint mismatch (Error de concordancia de huella digital de certificado de dispositivo x.509) | Media | La huella digital del certificado de dispositivo x.509 no coincidió con la configuración. | Revise las alertas de los dispositivos. No se requiere ninguna acción adicional. |
| x.509 certificate expired (Certificado x.509 expirado) | Media | El certificado de dispositivo X.509 ha expirado. | Puede tratarse de un dispositivo legítimo con un certificado expirado o un intento de suplantar un dispositivo legítimo. Si el dispositivo legítimo se comunica actualmente de forma correcta, es probable que se trate de un intento de suplantación. |
| Gravedad **baja** |  |  |  |
| Attempt to add or edit a diagnostic setting of an IoT Hub detected (Detección de intento de agregar o editar una configuración de diagnóstico en una instancia de IoT Hub) | Bajo | Se ha detectado un intento de agregar o editar una configuración de diagnóstico en una instancia de IoT Hub. La configuración de diagnóstico le permite volver a crear seguimientos de actividad con fines de investigación cuando se produce un incidente de seguridad o se pone en peligro la red. Si una entidad autorizada no realizó esta acción, puede indicar una actividad malintencionada. | 1. Asegúrese de que el certificado lo quitó una entidad autorizada.<br> 2. Si el certificado no lo quitó una entidad autorizada, vuelva a agregarlo y escale la alerta al equipo de seguridad de información. |
| Attempt to delete a diagnostic setting from an IoT Hub detected (Detección de intento de eliminar una configuración de diagnóstico de una instancia de IoT Hub) | Bajo | %{DescAttemptStatusMessage}\' intentó agregar o editar la configuración de diagnóstico \'%{DescDiagnosticSettingName}\' de \'%{DescIoTHubName}\' de IoT Hub. La configuración de diagnóstico le permite volver a crear seguimientos de actividad con fines de investigación cuando se produce un incidente de seguridad o se pone en peligro la red. Si una entidad autorizada no realizó esta acción, puede indicar una actividad malintencionada. | Asegúrese de que los permisos para cambiar certificados solo se conceden a una entidad autorizada. |
| Expired SAS Token (Token de SAS expirado) | Bajo | Un dispositivo ha usado un token de SAS expirado. | Puede tratarse de un dispositivo legítimo con un token expirado o un intento de suplantar un dispositivo legítimo. Si el dispositivo legítimo se comunica actualmente de forma correcta, es probable que se trate de un intento de suplantación. |
| Invalid SAS token signature (Firma de token de SAS no válida) | Bajo | Un token de SAS usado por un dispositivo tiene una firma no válida. La firma no coincide con la clave principal o secundaria. | Revise las alertas de los dispositivos. No se requiere ninguna acción adicional. |

## <a name="next-steps"></a>Pasos siguientes

- [Información general](overview.md) del servicio Defender para IoT
- Aprenda a [acceder a los datos de seguridad](how-to-security-data-access.md)
- Más información sobre cómo [investigar un dispositivo](how-to-investigate-device.md)
