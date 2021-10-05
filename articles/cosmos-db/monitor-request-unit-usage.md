---
title: Supervisión del uso de rendimiento de una operación en Azure Cosmos DB
description: Obtenga información sobre cómo supervisar el rendimiento o el uso de unidades de solicitud de una operación en Azure Cosmos DB. Los propietarios de una cuenta de Azure Cosmos DB pueden conocer qué operaciones toman más unidades de solicitud.
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 09/16/2021
ms.openlocfilehash: 8e592e40b8885158c0b6700b0014a5982fe2d352
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548300"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Cómo supervisar el rendimiento o el uso de unidades de solicitud de una operación en Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor para Azure Cosmos DB proporciona una vista de métricas para supervisar la cuenta y crear paneles. Las métricas de Azure Cosmos DB se recopilan de forma predeterminada, esta característica no requiere que habilite ni configure nada explícitamente. La métrica **Unidades de solicitud totales** se usa para obtener el uso de unidades de solicitud para diferentes tipos de operaciones. Más adelante podrá analizar qué operaciones usaron la mayor parte del rendimiento. De forma predeterminada, los datos de rendimiento se agregan en un intervalo de un minuto. Sin embargo, puede cambiar la unidad de agregación si cambia la opción de granularidad de tiempo.

Hay dos maneras de analizar los datos de uso de la unidad de solicitud:

* Dentro del intervalo de tiempo determinado, qué operaciones usan más unidades de solicitud.
* En general, qué operaciones dominan la carga de trabajo al consumir más unidades de solicitud.
Este análisis le permite centrarse en operaciones como insert, upsert y look en su indexación. Puede averiguar si está por encima o por debajo de la indexación de campos específicos y modificar la [directiva de indexación](index-policy.md#include-exclude-paths) para incluir o excluir las rutas de acceso.

Si observa que algunas consultas están tomando más unidades de solicitud, puede realizar acciones como las siguientes:

* Reconsiderar si está solicitando la cantidad adecuada de datos.
* Modificar la consulta para utilizar el índice con la cláusula de filtro.
* Realizar llamadas a funciones UDF menos costosas.
* Definir las claves de partición para minimizar la dispersión de la consulta en diferentes particiones.
* También puede usar las métricas de consulta que se devuelven en las respuestas a llamadas, los detalles del registro de diagnóstico y consultar el artículo sobre el [ajuste del rendimiento de las consultas](sql-api-query-metrics.md) para obtener más información sobre la ejecución de consultas.
* Puede empezar a partir de SUM y, a continuación, examinar el promedio de uso con la dimensión correcta.

## <a name="view-the-total-request-unit-usage-metric"></a>Visualización de la métrica de uso de unidades de solicitud totales

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **Monitor** en la barra de navegación izquierda y, a continuación, seleccione **Métricas**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Panel Métricas en Azure Monitor":::

1. En el panel **Métricas** panel > **Seleccionar un recurso** > elija la **suscripción**, y el **grupo de recursos** requeridos. En **Tipo de recurso**, seleccione **Cuentas de Azure Cosmos DB**, elija una de las cuentas de Azure Cosmos existentes y seleccione **Aplicar**.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="Selección del ámbito de la cuenta para ver las métricas" border="true":::

1. A continuación, seleccione la métrica **Unidades de solicitud totales** en la lista de métricas disponibles. Para conocer los detalles de todas las métricas disponibles en esta lista, consulte el artículo [Métricas por categoría](monitor-cosmos-db-reference.md). En este ejemplo, vamos a seleccionar **Unidades de solicitud totales** y **Promedio** como valor de agregación. Además de estos detalles, también puede seleccionar los valores de **Intervalo de tiempo** y **Granularidad de tiempo** de las métricas. Como máximo, puede ver las métricas de los últimos 30 días.  Después de aplicar el filtro, se muestra un gráfico basado en dicho filtro. Puede ver el número medio de unidades de solicitud consumidas por minuto durante el período seleccionado.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Elección de una métrica en Azure Portal" border="true":::

## <a name="filters-for-request-unit-usage"></a>Filtros para el uso de unidades de solicitud

También puede filtrar las métricas y hacer que se muestren grafos para una propiedad **CollectionName**, **DatabaseName**, **OperationType**, **Region**, **Status** y **StatusCode** concreta. Las opciones **Agregar filtro** y **Aplicar división** permiten filtrar el uso de unidades de solicitud y agrupar las métricas.

Para obtener el uso de la unidad de solicitud de cada operación en total (suma) o promedio, seleccione **Aplicar división** y elija **Tipo de operación**, y el valor de filtro tal como se muestra en la siguiente imagen:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Unidades de solicitud de Cosmos DB para operaciones en Azure Monitor":::

Si desea ver el uso de unidades de solicitud por colección, seleccione **Aplicar división** y elija el nombre de la colección como filtro. Verá un chat similar al siguiente con una selección de colecciones en el panel. Después, puede seleccionar un nombre de colección específico para ver más detalles:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Unidades de solicitud de Cosmos DB para todas las operaciones de la colección en Azure Monitor" border="true":::

## <a name="next-steps"></a>Pasos siguientes

* Supervise los datos de Azure Cosmos DB mediante la [configuración de diagnóstico](cosmosdb-monitor-resource-logs.md) en Azure.
* [Auditoría de las operaciones del plano de control de Azure Cosmos DB](audit-control-plane-logs.md)
