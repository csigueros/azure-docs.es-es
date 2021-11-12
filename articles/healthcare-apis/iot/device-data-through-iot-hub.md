---
title: 'Recepción de datos del dispositivo mediante Azure IoT Hub: API de Azure Healthcare'
description: En este tutorial, aprenderá a habilitar el enrutamiento de datos de dispositivos desde IoT Hub al servicio FHIR a través del conector de IoT.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: f12c5efe25f48ae16e4f59159936627a27b47212
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283474"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Tutorial: Recepción de datos del dispositivo mediante Azure IoT Hub

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El conector de IoT proporciona la capacidad de recopilar e ingerir datos de estado de varios dispositivos médicos o relacionados con el estado de IoT en el servicio Recursos Rápidos de Interoperabilidad en Salud (FHIR®). El conector de IoT es interoperable y responde con dispositivos creados y administrados a través de Azure IoT Hub para mejorar los flujos de trabajo y facilitar su uso. En este tutorial se proporciona el procedimiento para conectar y enrutar los datos del dispositivo desde Azure IoT Hub al conector de IoT.

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción de Azure activa: [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Recurso de servicio de FHIR con al menos un conector de IoT: [implemente el conector de IoT mediante Azure Portal](deploy-iot-connector-in-azure.md)
- Un recurso de Azure IoT Hub conectado a dispositivos reales o simulados: [Creación de un centro de IoT con Azure Portal](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> Si usa una aplicación de dispositivo simulado de Azure IoT Hub, no dude en elegir la aplicación que prefiera entre los distintos lenguajes y sistemas compatibles.

## <a name="get-connection-string-for-iot-connector"></a>Obtener la cadena de conexión para el conector de IoT

Azure IoT El centro requiere una cadena de conexión para conectarse de forma segura con el conector de IoT. Cree una nueva cadena de conexión para el conector de IoT como se describe en [Generación de una cadena de conexión.](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string) Caja fuerte esta cadena de conexión que se usará en el paso siguiente.

El conector de IoT usa una instancia de Azure Event Hubs en segundo plano para recibir los mensajes del dispositivo. La cadena de conexión creada anteriormente es básicamente la cadena de conexión a este centro de eventos subyacente.

## <a name="connect-azure-iot-hub-with-iot-connector"></a>Conectar Azure IoT Hub con el conector de IoT

Azure IoT Hub admite una característica llamada [enrutamiento de mensajes](../../iot-hub/iot-hub-devguide-messages-d2c.md) que proporciona la funcionalidad de enviar datos del dispositivo a varios servicios de Azure, como Event Hubs, una cuenta de Azure Storage y Service Bus. El conector de IoT usa esta característica para conectarse y enviar datos de dispositivo desde Azure IoT Hub a su punto de conexión del centro de eventos.

> [!NOTE] 
> En este momento solo puede usar PowerShell o el comando de la CLI para crear el enrutamiento de mensajes porque el centro de eventos del conector de IoT no está hospedado en la suscripción del cliente, por lo que no será visible para usted a través de la Azure Portal. [](../../iot-hub/tutorial-routing.md) Sin embargo, una vez que se agregan los objetos de ruta de mensaje mediante PowerShell o la CLI, están visibles en Azure Portal y se pueden administrar desde allí.

La configuración de un enrutamiento de mensajes consta de dos pasos.

### <a name="add-an-endpoint"></a>Agregación de un extremo

En este paso se define el punto de conexión al que IoT Hub enrutará los datos. Puede crear este punto de conexión mediante el comando [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIoTHubRoute) de PowerShell o el comando [az iot hub routing-endpoint create](/cli/azure/iot/hub/route#az_iot_hub_route_create) de la CLI, según sus preferencias.

Esta es la lista de parámetros que se usan con el comando para crear un punto de conexión:

|Parámetro de PowerShell|Parámetro de la CLI|Descripción|
|---|---|---|
|ResourceGroupName|resource-group|Nombre del grupo de recursos de su recurso de IoT Hub.|
|Nombre|hub-name|Nombre de su recurso de IoT Hub.|
|EndpointName|endpoint-name|Nombre que le gustaría asignar al punto de conexión que se va a crear.|
|EndpointType|endpoint-type|Tipo de punto de conexión al que necesita conectarse IoT Hub. Use el valor literal "EventHub" para PowerShell y "eventhub" para la CLI.|
|EndpointResourceGroup|endpoint-resource-group|Nombre del grupo de recursos para el recurso del servicio FHIR del conector de IoT. Puede obtener este valor en la página Información general del servicio FHIR.|
|EndpointSubscriptionID|endpoint-subscription-id|Identificador de suscripción del recurso del servicio FHIR del conector de IoT. Puede obtener este valor en la página Información general del servicio FHIR.|
|ConnectionString|connection-string|Cadena de conexión al conector de IoT. Use el valor que obtuvo en el paso anterior.|

### <a name="add-a-message-route"></a>Adición de una ruta de mensajes
En este paso se define una ruta de mensajes mediante el punto de conexión creado anteriormente. Puede crear una ruta mediante el comando [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) de PowerShell o el comando [az iot hub route create](/cli/azure/iot/hub/route) de la CLI, según sus preferencias.

Esta es la lista de parámetros que se usan con el comando para agregar una ruta de mensaje:

|Parámetro de PowerShell|Parámetro de la CLI|Descripción|
|---|---|---|
|ResourceGroupName|g|Nombre del grupo de recursos de su recurso de IoT Hub.|
|Nombre|hub-name|Nombre de su recurso de IoT Hub.|
|EndpointName|endpoint-name|Nombre del punto de conexión que ha creado anteriormente.|
|RouteName|route-name|Nombre que desea asignar a la ruta de mensajes que se va a crear.|
|Source|source-type|Tipo de datos que se van a enviar al punto de conexión. Use el valor literal "DeviceMessages" para PowerShell y "devicemessages" para la CLI.|

## <a name="send-device-message-to-azure-iot-hub"></a>Envío de un mensaje de dispositivo a Azure IoT Hub

Use el dispositivo (real o simulado) para enviar el mensaje de frecuencia cardíaca de ejemplo que se muestra a continuación a Azure IoT Hub. Este mensaje se enruta al conector de IoT, donde el mensaje se transformará en un recurso de observación de FHIR y se almacenará en el servicio FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Asegúrese de enviar el mensaje del dispositivo que se ajusta a las plantillas [de asignación](how-to-use-fhir-mapping-iot.md) configuradas con el conector de IoT.

## <a name="view-device-data-in-fhir-service"></a>Visualización de los datos del dispositivo en el servicio FHIR

Puede ver los recursos de observación de FHIR creados por el conector de IoT en el servicio FHIR mediante Postman. Para obtener información, consulte Acceso al servicio FHIR mediante [Postman](./../use-postman.md)y realice una solicitud para ver los recursos de Observación de FHIR con el valor de frecuencia del corazón enviado en el `GET` mensaje de ejemplo `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` anterior.

> [!TIP]
> Asegúrese de que el usuario tiene acceso adecuado al plano de datos del servicio FHIR. Use el [control de acceso basado en roles de Azure (Azure RBAC)](../azure-api-for-fhir/configure-azure-rbac.md) para asignar los roles del plano de datos requeridos.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configurará Azure IoT Hub para enrutar los datos del dispositivo al conector de IoT. Seleccione los siguientes pasos para obtener más información sobre el conector de IoT:

Comprender las distintas fases del flujo de datos en el conector de IoT.

>[!div class="nextstepaction"]
>[Flujo de datos del conector de IoT](iot-data-flow.md)

(FHIR&#174;) es una marca comercial registrada de HL7 y se usa con el permiso de HL7.
