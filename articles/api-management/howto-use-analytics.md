---
title: Uso de análisis en Azure API Management | Microsoft Docs
description: Use los análisis en Azure API Management para ayudarle a entender y categorizar el uso y el rendimiento de las API.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: da8b462eba7b5f68e345ffe5e73bd4096831935f
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713051"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Obtención de análisis de API en Azure API Management

Azure API Management proporciona análisis integrados para las API. Analice el uso y el rendimiento de las API en la instancia de API Management en varias dimensiones, entre las que se incluyen:

* Hora
* Geography
* API existentes
* Operaciones de la API REST
* Productos
* Suscripciones
* Usuarios
* Requests

> [!NOTE]
> Los valores de geografía son aproximados en función de la asignación de direcciones IP.

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Análisis de escala de tiempo en el portal":::

Use análisis para la supervisión y solución de problemas generales de las API. Para obtener características de supervisión adicionales, incluidas las métricas casi en tiempo real y los registros de recursos para diagnósticos y auditoría, consulte [Tutorial: Supervisión de API publicadas](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Análisis - Portal

Use Azure Portal para revisar los datos de análisis de un vistazo para su instancia de API Management.

1. Vaya a la instancia de API Management en [Azure Portal](https://portal.azure.com). 
1. En el menú de la izquierda, en **Supervisión**, seleccione **Análisis**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Selección de Análisis para instancia de API Management en el portal":::  
1. Seleccione un intervalo de tiempo para los datos o especifique un intervalo de tiempo personalizado.
1. Seleccione una categoría de informe para los datos de análisis, como **Escala de tiempo**, **Información geográfica**, etc.
1. Opcionalmente, filtre el informe por una o varias categorías adicionales.

## <a name="analytics---rest-api"></a>Análisis - API REST

Use las operaciones de [Informes](/rest/api/apimanagement/2020-12-01/reports) de la API REST de API Management para recuperar y filtrar los datos de análisis de la instancia de API Management.

Las operaciones disponibles devuelven registros de informe por API, región geográfica, operaciones de API, producto, solicitud, suscripción, hora o usuario.

## <a name="next-steps"></a>Pasos siguientes

* Para ver una introducción a las características de Azure Monitor en API Management, consulte [Tutorial: Supervisión de API publicadas](api-management-howto-use-azure-monitor.md).
* Para obtener información detallada sobre el registro y la supervisión de HTTP, consulte [Supervisión de las API con Azure API Management, Event Hubs y Moesif](api-management-log-to-eventhub-sample.md).
* Obtenga más información sobre la integración en [Cómo integrar Azure API Management con Azure Application Insights](api-management-howto-app-insights.md).
