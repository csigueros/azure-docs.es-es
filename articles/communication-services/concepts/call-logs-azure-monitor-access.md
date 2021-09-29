---
title: 'Azure Communication Services: Habilitación de registros de resumen de llamadas y de diagnóstico de llamadas y acceso a ellos'
titleSuffix: An Azure Communication Services concept document
description: Acceso a los registros de resumen de llamadas y de diagnóstico de llamadas en Azure Monitor
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: b5c2da9842ba21b63c7b36d5b7377f74a25a1e90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672287"
---
# <a name="enable-and-access-call-summary-and-call-diagnostic-logs"></a>Habilitación de registros de resumen de llamadas y de diagnóstico de llamadas y acceso a ellos

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Para acceder a la telemetría de los recursos de voz y vídeo de Azure Communication Services, siga estos pasos.

## <a name="enable-logging"></a>Habilitar registro
1. En primer lugar, deberá crear una cuenta de almacenamiento para los registros. Vaya a [Crear una cuenta de almacenamiento](../../storage/common/storage-account-create.md?tabs=azure-portal) para obtener instrucciones sobre cómo completar este paso. Consulte también [Introducción a las cuentas de almacenamiento](../../storage/common/storage-account-overview.md) para obtener más información sobre los tipos y las características de las distintas opciones de almacenamiento. Si ya tiene una cuenta de almacenamiento de Azure, vaya al paso 2.
 
1. Una vez creada la cuenta de almacenamiento, debe habilitar el registro siguiendo las instrucciones de [Habilitación de los registros de diagnóstico en el recurso](./logging-and-diagnostics.md#enable-diagnostic-logs-in-your-resource). Seleccionará las casillas de los registros "CallSummaryPRIVATEPREVIEW" y "CallDiagnosticPRIVATEPREVIEW". 

1. A continuación, seleccione el cuadro "Archivar en una cuenta de almacenamiento" y, a continuación, seleccione la cuenta de almacenamiento para los registros en el menú desplegable siguiente. La opción "Send to Analytics workspace" (Enviar al área de trabajo de Analytics) no está disponible actualmente para la versión preliminar privada de esta característica, pero estará disponible cuando esta característica se haga pública.

:::image type="content" source="media\call-logs-images\call-logs-access-diagnostic-setting.png" alt-text="Configuración de diagnósticos de Azure Monitor":::



## <a name="access-your-logs"></a>Acceso a los registros

Para acceder a los registros, vaya a la cuenta de almacenamiento que designó en el paso 3 anterior; para ello, vaya a [Cuentas de almacenamiento](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) en Azure Portal. 

:::image type="content" source="media\call-logs-images\call-logs-access-storage.png" alt-text="Almacenamiento en Azure Portal":::

Desde ahí, puede descargar todos los registros o registros individuales.

:::image type="content" source="media\call-logs-images\call-logs-access-storage-resource.png" alt-text="Descarga del almacenamiento en Azure Portal":::

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [Registro y diagnóstico](./logging-and-diagnostics.md)