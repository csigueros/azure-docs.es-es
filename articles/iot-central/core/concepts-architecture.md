---
title: Conceptos de arquitectura en Azure IoT Central | Microsoft Docs
description: En este artículo se presentan conceptos clave relacionados con la arquitectura de Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 37e4224ae1347647d15959a55d19d2c6487935d7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273220"
---
# <a name="azure-iot-central-architecture"></a>Arquitectura de Azure IoT Central

En este artículo se proporciona información general sobre los conceptos clave de la arquitectura de Azure IoT Central.

## <a name="devices"></a>Dispositivos

Los dispositivos intercambian datos con la aplicación Azure IoT Central. Un dispositivo puede:

- Enviar las mediciones como telemetría.
- Sincronizar la configuración con la aplicación.

En Azure IoT Central, los datos que un dispositivo puede intercambiar con la aplicación se especifican en una plantilla de dispositivo. Para más información acerca de las plantillas de dispositivo, consulte [Plantillas de dispositivo](concepts-device-templates.md).

Para obtener más información sobre cómo se conectan los dispositivos a la aplicación de Azure IoT Central, consulte [Device connectivity](concepts-get-connected.md) (Conectividad de dispositivos).

### <a name="azure-iot-edge-devices"></a>Dispositivos de Azure IoT Edge

Además de los dispositivos creados con el [SDK de IoT de Azure](https://github.com/Azure/azure-iot-sdks), también puede conectar [dispositivos Azure IoT Edge](../../iot-edge/about-iot-edge.md) a una aplicación IoT Central. IoT Edge permite ejecutar la inteligencia de nube y la lógica personalizada directamente en dispositivos IoT administrados por IoT Central. También puede usar IoT Edge como puerta de enlace para permitir que otros dispositivos de nivel inferior se conecten a IoT Central.

Para obtener más información, consulte [Conexión de dispositivos de Azure IoT Edge a una aplicación de Azure IoT Central](concepts-iot-edge.md).

## <a name="cloud-gateway"></a>Puerta de enlace en la nube

Azure IoT Central usa Azure IoT Hub como una puerta de enlace en la nube que permite la conectividad del dispositivo. IoT Hub permite lo siguiente:

- Ingesta de datos a escala en la nube.
- Administración de dispositivos.
- Conectividad de dispositivos segura.

Para más información acerca de IoT Hub, consulte [Azure IoT Hub](../../iot-hub/index.yml).

Para obtener más información sobre la conectividad de dispositivos en Azure IoT Central, consulte [Device connectivity](concepts-get-connected.md) (Conectividad de dispositivos).

## <a name="data-stores"></a>Almacenes de datos

Azure IoT Central almacena los datos de la aplicación en la nube. Entre los datos de aplicación almacenados están los siguientes:

- Plantillas del dispositivo.
- Identidades del dispositivo.
- Metadatos del dispositivo.
- Datos de usuario y rol.

Azure IoT Central utiliza un almacén en serie temporal para los datos de medición enviados desde los dispositivos. Los datos de serie temporal de dispositivos usados por el servicio de análisis.

## <a name="data-export"></a>Exportación de datos

En una aplicación de Azure IoT Central, puede [exportar continuamente los datos](howto-export-data.md) a sus propias instancias de Azure Event Hubs y de Azure Service Bus. También puede exportar los datos periódicamente a la cuenta de Azure Blob Storage. IoT Central puede exportar medidas, dispositivos y plantillas de dispositivo.

## <a name="batch-device-updates"></a>Actualizaciones de dispositivos en lote

En una aplicación de Azure IoT Central, puede [crear y ejecutar trabajos](howto-manage-devices-in-bulk.md) para administrar los dispositivos conectados. Estos trabajos le permiten realizar actualizaciones masivas de los comandos, las configuraciones y propiedades de los dispositivos. Por ejemplo, puede crear un trabajo para aumentar la velocidad del ventilador de varias máquinas expendedoras refrigeradas.

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)

Cada aplicación de IoT Central tiene su propio sistema RBAC integrado. Un [administrador puede definir reglas de acceso](howto-manage-users-roles.md) para una aplicación de Azure IoT Central con uno de los roles predefinidos o creando un rol personalizado. Los roles determinan las áreas de la aplicación a las que los usuarios tienen acceso y qué pueden hacer.

## <a name="security"></a>Seguridad

Entre las características de seguridad de Azure IoT Central están las siguientes:

- Los datos se cifran en tránsito y en reposo.
- La autenticación se proporciona por Azure Active Directory o la cuenta de Microsoft. Se admite la autenticación de dos factores.
- Aislamiento de inquilino completo.
- Seguridad a nivel de dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre la arquitectura de Azure IoT Central, el siguiente paso sugerido es obtener información sobre [la conectividad de dispositivos](concepts-get-connected.md) en Azure IoT Central.