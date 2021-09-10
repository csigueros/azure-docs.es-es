---
title: Recomendaciones automatizadas sobre el rendimiento, el costo y la seguridad para Azure Cosmos DB
description: Obtenga información sobre cómo ver recomendaciones personalizadas sobre el rendimiento, el costo y la seguridad y otro tipo de recomendaciones para Azure Cosmos DB en función de los patrones de carga de trabajo.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 3844e71a1c3c9e823586a428b9ae98a3b804d502
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123034591"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Recomendaciones automatizadas para Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Todos los servicios en la nube, incluido Azure Cosmos DB, obtienen actualizaciones frecuentes con nuevas características, funcionalidades y mejoras. Es importante que la aplicación se mantenga al día con las actualizaciones de rendimiento y seguridad más recientes. Azure Portal ofrece recomendaciones personalizadas que permiten maximizar el rendimiento de la aplicación. El motor de consulta de Azure Cosmos DB analiza continuamente el historial de uso de los recursos de Azure Cosmos DB y proporciona recomendaciones basadas en los patrones de carga de trabajo. Estas recomendaciones se corresponden con áreas como la creación de particiones, la indexación, la red, la seguridad, etc. Estas recomendaciones personalizadas le ayudan a mejorar el rendimiento de la aplicación.

## <a name="view-recommendations"></a>Ver recomendaciones

Puede ver las recomendaciones para Azure Cosmos DB de las siguientes maneras:

- Una manera de ver las recomendaciones es dentro de la pestaña Notificaciones. Si hay nuevas recomendaciones, verá una barra de mensajes. Inicie sesión en [Azure Portal](https://portal.azure.com) y desplácese hasta la cuenta de Azure Cosmos. Dentro de su cuenta de Azure Cosmos, abra el panel **Notificaciones** y seleccione la pestaña **Recomendaciones**. Puede seleccionar el mensaje y ver las recomendaciones.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Ver recomendaciones desde el panel de Azure Cosmos DB":::

- También puede encontrar las recomendaciones a través de [Azure Advisor](../advisor/advisor-overview.md) categorizadas por cubos diferentes, como el costo, la seguridad, la fiabilidad, el rendimiento y la excelencia operativa. Puede seleccionar suscripciones específicas y filtrar por el tipo de recurso, que es **Cuentas de Azure Cosmos DB**.  Cuando selecciona una recomendación específica, se muestran las acciones que se pueden realizar para beneficiar las cargas de trabajo.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Visualización de recomendaciones desde el panel de Azure Advisor":::

No todas las recomendaciones que se muestran en el panel de Azure Cosmos DB están disponibles en Azure Advisor y viceversa. Esto se debe a que, según el tipo de recomendación, encajan en el panel de Azure Advisor, en el panel de Azure Cosmos DB o en ambos.

Actualmente, Azure Cosmos DB admite recomendaciones sobre las áreas siguientes. Cada una de estas recomendaciones incluye un vínculo a la sección correspondiente de la documentación, por lo que es fácil llevar a cabo los pasos siguientes.

## <a name="sdk-usage-recommendations"></a>Recomendaciones sobre el uso del SDK

En esta categoría, el asesor detecta el uso de una versión anterior de los SDK y recomienda que actualice a una versión más reciente para aprovechar las correcciones de errores y mejoras de rendimiento más recientes. Actualmente están disponibles las siguientes recomendaciones específicas del SDK:

|Nombre  |Descripción  |
|---------|---------|
| Conector de Spark antiguo | Detecta el uso de versiones anteriores del conector de Spark y recomienda actualizar. |
| SDK de .NET antiguo | Detecta el uso de versiones anteriores del SDK de .NET y recomienda actualizar. |
| SDK de Java antiguo | Detecta el uso de versiones anteriores del conector de Java y recomienda actualizar. |

## <a name="indexing-recommendations"></a>Recomendaciones sobre indexación

En esta categoría, el asesor detecta el modo de indexación, la directiva de indexación, las rutas de acceso indexadas y recomienda cambiar si la configuración actual afecta al rendimiento de las consultas. Actualmente están disponibles las siguientes recomendaciones específicas de la indexación:

|Nombre  |Descripción  |
|---------|---------|
| Indexación diferida | Detecta el uso del modo de indexación diferida y, en su lugar, recomienda usar el modo de indexación coherente. La finalidad del modo de indexación diferida de Azure Cosmos DB es limitado y puede afectar a la actualización de los resultados de consulta en algunas situaciones, por lo que se recomienda el modo de indexación coherente. |
| Indexación compuesta| Detecta las cuentas en las que las consultas pueden beneficiarse de los índices compuestos y recomienda su uso. Los índices compuestos pueden mejorar considerablemente el procesamiento y el consumo de rendimiento de algunas consultas.|
| Directiva de indexación predeterminada con muchas rutas de acceso indexadas | Detecta contenedores que se ejecutan en la indexación predeterminada con muchas rutas de acceso indexadas y recomienda personalizar la directiva de indexación.|
| Consultas ORDER BY con una elevada carga de RU/s| Detecta los contenedores que emiten consultas ORDER BY con una elevada carga de RU/s y recomienda explorar índices compuestos.|
| Cuentas de MongoDB 3.6 sin índice y consumo elevado de RU/s| Detecta la API de Azure Cosmos DB para MongoDB con la versión 3.6 de los contenedores que emiten consultas con una elevada carga de RU/s y recomienda agregar índices.|

## <a name="cost-optimization-recommendations"></a>Recomendaciones sobre optimización de costos

En esta categoría, el asesor detecta el uso de RU/s y determina que puede optimizar el precio realizando algunos cambios en los recursos o aprovechando un modelo de precios diferente. Actualmente están disponibles las siguientes recomendaciones específicas de la optimización de costos:

|Nombre  |Descripción  |
|---------|---------|
| Capacidad reservada | Detecta el uso de RU/s y recomienda instancias reservadas para los usuarios que pueden beneficiarse de ellas. |
| Contenedores inactivos | Detecta los contenedores que no se han usado durante más de treinta días y recomienda reducir el rendimiento de dichos contenedores o eliminarlos.|
| Nuevas suscripciones con un alto rendimiento | Detecta nuevas suscripciones con cuentas que gastan una cantidad inusualmente alta de RU/s al día y se lo notifica. Esta notificación es específicamente para dar a conocer a los nuevos clientes que Azure Cosmos DB opera en un modelo basado en rendimiento aprovisionado y no en un modelo basado en consumo. |

## <a name="migration-recommendations"></a>Recomendaciones sobre migración

En esta categoría, el asesor detecta que el uso de características heredadas recomienda la migración para que pueda aprovechar la escalabilidad masiva de Azure Cosmos DB y otras ventajas. Actualmente están disponibles las siguientes recomendaciones específicas de la migración:

|Nombre  |Descripción  |
|---------|---------|
| Contenedores sin particiones | Detecta contenedores de tamaño fijo que se aproximan a su límite de almacenamiento máximo y recomienda migrarlos a contenedores con particiones.|

## <a name="query-usage-recommendations"></a>Recomendaciones sobre el uso de consultas

En esta categoría, el asesor detecta la ejecución de consultas e identifica que el rendimiento de consultas se puede optimizar con algunos cambios. Actualmente están disponibles las siguientes recomendaciones sobre el uso de consultas:

|Nombre  |Descripción  |
|---------|---------|
| Consultas con tamaño de página fijo | Detecta las consultas emitidas con un tamaño de página fijo y recomienda usar -1 (sin límite en el tamaño de página) en lugar de definir un valor específico. Esta opción reduce el número de recorridos de ida y vuelta de red necesarios para recuperar todos los resultados. |

## <a name="next-steps"></a>Pasos siguientes

* [Optimización del rendimiento de consultas en Azure Cosmos DB](sql-api-query-metrics.md)
* [Solución de problemas de consulta](troubleshoot-query-performance.md) al usar Azure Cosmos DB
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
