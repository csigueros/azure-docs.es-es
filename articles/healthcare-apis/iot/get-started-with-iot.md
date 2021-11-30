---
title: 'Introducción al conector de IoT: API de Azure Healthcare'
description: En este documento se describe cómo empezar a trabajar con el conector de IoT en las API de Azure Healthcare.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: 65080a0d884d6388f713ea80f4447f4c8d9fba7b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273241"
---
# <a name="get-started-with-the-iot-connector"></a>Introducción con el conector IOT

En este artículo se describen los pasos básicos para empezar a trabajar con el conector de IoT en las [API de Azure Healthcare.](../healthcare-apis-overview.md) 

Como requisito previo, necesitará una suscripción de Azure y se le han concedido los permisos adecuados para crear grupos de recursos de Azure e implementar recursos de Azure. Puede seguir todos los pasos u omitir algunos si tiene un entorno existente. Además, puede combinar todos los pasos y completarlos en PowerShell, CLI de Azure y scripts de API REST.

[![Introducción con DICOM](media/get-started-with-iot.png)](media/get-started-with-iot.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Creación de un área de trabajo en la suscripción de Azure

Puede crear un área de trabajo desde la [Azure Portal](../healthcare-apis-quickstart.md) o mediante PowerShell, CLI de Azure API rest]. Puede encontrar scripts en los ejemplos [de LAS API de atención sanitaria.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

> [!NOTE]
> Hay límites en el número de áreas de trabajo y el número de instancias del conector de IoT que puede crear en cada suscripción de Azure.

## <a name="create-the-fhir-service-and-an-event-hub"></a>Creación del servicio FHIR y un centro de eventos

El conector de IoT funciona con El centro de eventos de Azure y el servicio FHIR. Puede crear un nuevo servicio [FHIR o](../fhir/get-started-with-fhir.md) usar uno existente en la misma área de trabajo o en otra. De forma similar, puede crear un centro de [eventos](../../event-hubs/event-hubs-create.md) o usar uno existente.


## <a name="create-a-iot-connector-in-the-workspace"></a>Creación de un conector de IoT en el área de trabajo

Puede crear un conector de IoT desde la [Azure Portal](deploy-iot-connector-in-azure.md) o mediante PowerShell, CLI de Azure o la API REST. Puede encontrar scripts en los ejemplos [de LAS API de atención sanitaria.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

Opcionalmente, puede crear un servicio [FHIR y](../fhir/fhir-portal-quickstart.md) [un servicio DICOM](../dicom/deploy-dicom-services-in-azure.md) en el área de trabajo.

## <a name="assign-roles-to-allow-iot-to-access-event-hub"></a>Asignación de roles para permitir que IoT acceda al centro de eventos

Por diseño, el conector de IoT recupera datos del centro de eventos especificado mediante la identidad administrada por el sistema. Para más información sobre cómo asignar el rol al conector de IoT desde [Event Hubs.](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#granting-iot-connector-access)

## <a name="assign-roles-to-allow-iot-to-access-fhir-service"></a>Asignación de roles para permitir que IoT acceda al servicio FHIR

El conector de IoT conserva los datos en el almacén de FHIR mediante la identidad administrada por el sistema. Consulte los detalles sobre cómo asignar el rol al conector de IoT desde el [servicio FHIR](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#accessing-the-iot-connector-from-the-fhir-service).

## <a name="sending-data-to-the-iot-connector"></a>Envío de datos al conector de IoT

Puede enviar datos al centro de eventos, que está asociado al conector de IoT. Si no ve ningún dato en el almacén de FHIR, compruebe las asignaciones y asignaciones de roles para el conector de IoT.

## <a name="iot-connector-mappings-data-flow-ml-power-bi-and-teams-notifications"></a>Asignaciones de conectores de IoT, flujo de datos, ML, Power BI y notificaciones Teams datos

Puede encontrar más detalles sobre las asignaciones de conectores de IoT, el flujo de datos, el servicio de aprendizaje automático, Power BI y Teams en la documentación del conector [de IoT.](iot-connector-overview.md)

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describen los pasos básicos para empezar a usar el conector de IoT. Para obtener información sobre cómo implementar el conector de IoT en el área de trabajo, consulte

>[!div class="nextstepaction"]
>[Implementación del conector de IoT en el Azure Portal](deploy-iot-connector-in-azure.md)