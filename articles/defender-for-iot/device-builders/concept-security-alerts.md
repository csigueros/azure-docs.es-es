---
title: Lista de alertas integradas y personalizadas
description: Obtenga información sobre las alertas de seguridad y la corrección recomendada mediante las características y el servicio de Defender para IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 89fb77bed6116fcbae847b19b79e90d4dff34a23
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293542"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alertas de seguridad de Defender para IoT Hub

Defender para IoT analiza continuamente la solución de IoT mediante análisis avanzados e inteligencia de amenazas para alertarle de cualquier actividad malintencionada.
Además, puede crear alertas personalizadas según su conocimiento del comportamiento esperado del dispositivo.
Una alerta sirve como indicador de un posible peligro, y debe investigarse y corregirse.

En este artículo encontrará una lista de alertas integradas que pueden activarse en IoT Hub.
Además de las alertas integradas, Defender para IoT le permite definir alertas personalizadas basadas en el comportamiento esperado de IoT Hub o del dispositivo.
Para obtener más información, consulte las [alertas personalizables](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Alertas integradas para IoT Hub

### <a name="medium-severity"></a>Gravedad media

| Nombre | severity | Origen de datos | Descripción | Corrección sugerida | AlertType |
|--|--|--|--|--|--|
| New certificate added to an IoT Hub (Nuevo certificado agregado a una instancia de IoT Hub) | Media | IoT Hub | Se agregó un certificado a una instancia de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | 1. Asegúrese de que el certificado lo agregó una entidad autorizada. <br> 2. Si no lo agregó una entidad autorizada, quite el certificado y escale la alerta al equipo de seguridad de la organización. | IoT_CertificateSuccessfullyAddedToHub |
| Certificate deleted from an IoT Hub (Certificado eliminado de una instancia de IoT Hub) | Media | IoT Hub | Se eliminó un certificado de una instancia de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | 1. Asegúrese de que el certificado lo quitó una entidad autorizada. <br> 2. Si el certificado no lo quitó una entidad autorizada, vuelva a agregarlo y escale la alerta al equipo de seguridad de la organización. | IoT_CertificateSuccessfullyDeletedFromHub |
| Unsuccessful attempt detected to add a certificate to an IoT Hub (Detección de intento fallido de agregar un certificado a una instancia de IoT Hub) | Media | IoT Hub | Se produjo un intento fallido de agregar un certificado a una instancia de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | Asegúrese de que los permisos para cambiar certificados solo se conceden a las entidades autorizadas. | Hub_CertificateFailedToBeAddedToHub |
| Unsuccessful attempt detected to delete a certificate from an IoT Hub (Detección de intento fallido de eliminar un certificado de una instancia de IoT Hub) | Media | IoT Hub | Se produjo un intento fallido de eliminar un certificado de una instancia de IoT Hub. Si una entidad no autorizada realizó esta acción, puede indicar una actividad malintencionada. | Asegúrese de que los permisos para cambiar certificados solo se conceden a una entidad autorizada. | IoT.Hub_CertificateFailedToBeDeletedFromHub |
| x.509 device certificate thumbprint mismatch (Error de concordancia de huella digital de certificado de dispositivo x.509) | Media | IoT Hub | La huella digital del certificado de dispositivo x.509 no coincidió con la configuración. | Revise las alertas de los dispositivos. No se requiere ninguna acción adicional. | IoT_Cert_Print_Mismatch |
| x.509 certificate expired (Certificado x.509 expirado) | Media | IoT Hub | El certificado de dispositivo X.509 ha expirado. | Puede tratarse de un dispositivo legítimo con un certificado expirado o un intento de suplantar un dispositivo legítimo. Si el dispositivo legítimo se comunica actualmente de forma correcta, es probable que se trate de un intento de suplantación. | IoT_Cert_Expired |

### <a name="low-severity"></a>Low severity

| Nombre | severity | Origen de datos | Descripción | Corrección sugerida | AlertType |
|--|--|--|--|--|--|
| Attempt to add or edit a diagnostic setting of an IoT Hub detected (Detección de intento de agregar o editar una configuración de diagnóstico en una instancia de IoT Hub) | Bajo | IoT Hub | Se ha detectado un intento de agregar o editar una configuración de diagnóstico en una instancia de IoT Hub. La configuración de diagnóstico le permite volver a crear seguimientos de actividad con fines de investigación cuando se produce un incidente de seguridad o se pone en peligro la red. Si una entidad autorizada no realizó esta acción, puede indicar una actividad malintencionada. | 1. Asegúrese de que el certificado lo quitó una entidad autorizada. <br> 2. Si el certificado no lo quitó una entidad autorizada, vuelva a agregarlo y escale la alerta al equipo de seguridad de información. | IoT_DiagnosticSettingAddedOrEditedOnHub |
| Attempt to delete a diagnostic setting from an IoT Hub detected (Detección de intento de eliminar una configuración de diagnóstico de una instancia de IoT Hub) | Bajo | IoT Hub | Se ha detectado un intento de agregar o editar una configuración de diagnóstico en una instancia de IoT Hub. La configuración de diagnóstico le permite volver a crear seguimientos de actividad con fines de investigación cuando se produce un incidente de seguridad o se pone en peligro la red. Si una entidad autorizada no realizó esta acción, puede indicar una actividad malintencionada. | Asegúrese de que los permisos para cambiar certificados solo se conceden a una entidad autorizada. | IoT_DiagnosticSettingDeletedFromHub |
| Expired SAS Token (Token de SAS expirado) | Bajo | IoT Hub | Un dispositivo ha usado un token de SAS expirado. | Puede tratarse de un dispositivo legítimo con un token expirado o un intento de suplantar un dispositivo legítimo. Si el dispositivo legítimo se comunica actualmente de forma correcta, es probable que se trate de un intento de suplantación. | IoT_Expired_SAS_Token |
| Invalid SAS token signature (Firma de token de SAS no válida) | Bajo | IoT Hub | Un token de SAS usado por un dispositivo tiene una firma no válida. La firma no coincide con la clave principal o secundaria. | Revise las alertas de los dispositivos. No se requiere ninguna acción adicional. | IoT_Invalid_SAS_Token |

## <a name="next-steps"></a>Pasos siguientes

- [Información general](overview.md) del servicio Defender para IoT
