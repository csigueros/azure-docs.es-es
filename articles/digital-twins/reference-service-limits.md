---
title: Límites de servicio
titleSuffix: Azure Digital Twins
description: Gráfico que muestra los límites del servicio Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: cb86fe0d4a99897821b389682c5a131ce0b9118c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472890"
---
# <a name="azure-digital-twins-service-limits"></a>Límites del servicio Azure Digital Twins

En las secciones siguientes se describen los límites de servicio de Azure Digital Twins.

> [!NOTE]
> Los límites de algunas áreas de este servicio son ajustables. Esto se representa en las tablas siguientes con la columna *¿Ajustable?* . Cuando se puede ajustar el límite, el valor de *¿Ajustable?* es *Sí*.
>
> Si su empresa requiere el aumentar una cuota o límite ajustable por encima del límite predeterminado, puede [abrir una incidencia de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para solicitar más recursos.

## <a name="limits-by-type"></a>Límites por tipo

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Trabajo con límites

Cuando se alcanza un límite, el servicio limita las solicitudes más allá de él, lo que generará una respuesta de error 429 de estas solicitudes.

Para solucionarlo, estas son algunas recomendaciones para trabajar con límites.
* **Use la lógica de reintento.** Los [SDK de Azure Digital Twins](concepts-apis-sdks.md) implementan la lógica de reintento para las solicitudes con error, por lo que si está trabajando con un SDK proporcionado, esta funcionalidad ya está integrada. De lo contrario, considere la posibilidad de implementar la lógica de reintento en su propia aplicación. El servicio devuelve un encabezado `Retry-After` en la respuesta del error, que puede usar para determinar cuánto tiempo se debe esperar antes de volver a intentarlo.
* **Use umbrales y notificaciones para advertir sobre la proximidad de los límites.** Algunos de los límites de servicio de Azure Digital Twins tienen [métricas](troubleshoot-metrics.md) correspondientes que se pueden usar para hacer un seguimiento del uso en estas áreas. Para configurar umbrales y una alerta sobre cualquier métrica cuando se aproxime a un umbral, consulte las instrucciones de [Solución de problemas: Configuración de alertas](troubleshoot-alerts.md). Para configurar notificaciones para otros límites en los que no se proporcionan métricas, considere la posibilidad de implementar esta lógica en su propio código de aplicación.
* **Implementación a escala en varias instancias.** Evite tener un único punto de error. En lugar de un grafo grande para toda la implementación, considere la posibilidad de separar los subconjuntos de gemelos de manera lógica (por ejemplo, por región o inquilino) en varias instancias. 

## <a name="next-steps"></a>Pasos siguientes

Encuentre más información sobre la versión actual de Azure Digital Twins en la información general del servicio:
* [¿Qué es Azure Digital Twins?](overview.md)
