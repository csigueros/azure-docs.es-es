---
title: Métricas de Azure Storage Analytics (clásicas)
description: Obtenga información sobre cómo usar métricas de Storage Analytics en Azure Storage. Aprenda sobre las métricas de transacción y capacidad, cómo se almacenan las métricas, cómo se habilitan, etc.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: ebdac354cd5d490c30ac3eb65f75a71524051517
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553248"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas de Azure Storage Analytics (clásicas)

El **31 de agosto de 2023**, las métricas de Storage Analytics, también denominadas *métricas clásicas*, se retirarán. Para obtener más información, consulte el [anuncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Si usa métricas clásicas, asegúrese de realizar la transición a las métricas de Azure Monitor antes de esa fecha. En este artículo le ayudamos a realizar la transición.

Azure Storage usa la solución Storage Analytics para almacenar métricas, que incluyen estadísticas acumuladas de las transacciones y datos de capacidad sobre las solicitudes realizadas a un servicio de almacenamiento. Las transacciones se informan en el nivel de operación de la API y en el nivel de servicio de almacenamiento. La capacidad se informa en el nivel de servicio de almacenamiento. Los datos de las métricas se pueden usar para lo siguiente:
- Analizar el uso del servicio de almacenamiento.
- Diagnosticar problemas con las solicitudes realizadas al servicio de almacenamiento.
- Mejorar el rendimiento de las aplicaciones que utilizan un servicio.

 Las métricas de Storage Analytics se habilitan de forma predeterminada para las nuevas cuentas de almacenamiento. Puede configurar métricas en [Azure Portal](https://portal.azure.com/), con PowerShell o con la CLI de Azure. Para una guía paso a paso, vea [Habilitación y administración de las métricas de Azure Storage Analytics (clásico)](./manage-storage-analytics-logs.md). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones Set Service Properties para habilitar Storage Analytics en cada servicio.

> [!NOTE]
> Las métricas de Storage Analytics están disponibles para Azure Blob Storage, Azure Queue Storage y Azure Table Storage y Azure Files.
> Ahora, las métricas de Storage Analytics son métricas clásicas. Se recomienda usar [métricas de almacenamiento en Azure Monitor](../blobs/monitor-blob-storage.md) en lugar de métricas de Storage Analytics.

## <a name="transaction-metrics"></a>Métricas de transacciones

 Se registra un conjunto sólido de datos a intervalos de cada hora o de cada minuto para cada servicio de almacenamiento y operación de API que se ha solicitado, lo que incluye entradas y salidas, disponibilidad, errores y porcentajes de solicitudes por categorías. Para obtener una lista completa de los detalles de transacción, consulte [Esquema de las tablas de métricas de Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).

 Los datos de las transacciones se registran a nivel de servicio y a nivel de operación de API. En el nivel de servicio, las estadísticas que resumen todas las operaciones de API solicitadas se escriben en una entidad de la tabla cada hora, aunque no se haya realizado ninguna solicitud al servicio. En el nivel de operación de API, las estadísticas se escriben únicamente en una entidad si la operación se solicitó durante esa hora.

 Por ejemplo, si realiza una operación **GetBlob** en Blob service, las métricas de Storage Analytics registran la solicitud y la incluyen en los datos agregados para Blob service y para la operación **GetBlob**. Si no se solicita ninguna operación **GetBlob** durante esa hora, no escribe ninguna entidad en *$MetricsTransactionsBlob* para dicha operación.

 Las métricas de transacciones se registran para las solicitudes del usuario y para las solicitudes realizadas por Storage Analytics. Por ejemplo, se registran las solicitudes realizadas por Storage Analytics para escribir registros y entidades de tabla.

## <a name="capacity-metrics"></a>Métricas de capacidad

> [!NOTE]
>  Actualmente, las métricas de capacidad solo están disponibles para Blob service.

 Los datos de capacidad se registran diariamente para Blob service de una cuenta de almacenamiento, y se escriben dos entidades de tabla. Una entidad proporciona estadísticas para los datos de usuario y la otra proporciona estadísticas sobre el contenedor de blob `$logs` utilizado por Storage Analytics. La tabla *$MetricsCapacityBlob* incluye las estadísticas siguientes:

- **Capacity**: Cantidad de almacenamiento que ha usado Blob service de la cuenta de almacenamiento, en bytes.
- **ContainerCount**: Número de contenedores de blobs en Blob service de la cuenta de almacenamiento.
- **ObjectCount**: Número de blobs en bloque o en páginas confirmados y sin confirmar de Blob service de la cuenta de almacenamiento.

  Para obtener más información sobre las métricas de capacidad, consulte [Esquema de las tablas de métricas de Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).

## <a name="how-metrics-are-stored"></a>Cómo se almacenan las métricas

 Todos los datos de las métricas de cada uno de los servicios de almacenamiento se almacenan en tres tablas reservadas para ese servicio. Una tabla para la información de transacciones, una tabla para la información de transacciones por minuto y otra tabla para la información de capacidad. La información sobre transacciones y transacciones por minuto consta de datos de solicitudes y respuestas. La información sobre capacidad consta de datos de uso del almacenamiento. Se puede acceder a las métricas por hora, por minuto y de capacidad de Blob service de una cuenta de almacenamiento, en tablas cuyos nombres se describen en la tabla siguiente.

|Nivel de métricas|Nombres de tabla|Versiones compatibles|
|-------------------|-----------------|----------------------------|
|Métricas por horas, ubicación principal|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Versiones anteriores al 15 de agosto de 2013 únicamente. Si bien estos nombres todavía se admiten, se recomienda que cambie para usar las tablas que se muestran a continuación.|
|Métricas por horas, ubicación principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Todas las versiones. La compatibilidad con métricas de File service está disponible únicamente en la versión del 5 de abril de 2015 en adelante.|
|Métricas por minutos, ubicación principal|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Todas las versiones. La compatibilidad con métricas de File service está disponible únicamente en la versión del 5 de abril de 2015 en adelante.|
|Métricas por horas, ubicación secundaria|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Todas las versiones. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura.|
|Métricas por minutos, ubicación secundaria|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Todas las versiones. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura.|
|Capacidad (solo Blob service)|$MetricsCapacityBlob|Todas las versiones.|

 Estas tablas se crean automáticamente cuando Storage Analytics se habilita para un punto de conexión de servicio de almacenamiento. Se tiene acceso a ellas a través del espacio de nombres de la cuenta de almacenamiento, por ejemplo, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Las tablas de métricas no aparecen en una operación de lista, y el acceso a ellas debe efectuarse directamente a través del nombre de la tabla.

## <a name="metrics-alerts"></a>Alertas de métricas

Considere la posibilidad de configurar alertas en [Azure Portal](https://portal.azure.com) para avisarle automáticamente de los cambios importantes en el comportamiento de los servicios de almacenamiento. Para una guía paso a paso, vea [Creación de alertas de métricas](./manage-storage-analytics-logs.md).

Si usa una herramienta de Explorador de Storage para descargar estos datos de métricas en un formato delimitado, puede usar Microsoft Excel para analizar los datos. Para ver una lista de las herramientas del Explorador de Storage disponibles, consulte [Herramientas de cliente de Azure Storage](./storage-explorers.md).

> [!IMPORTANT]
> Puede producirse un retraso entre un evento de almacenamiento y cuándo se registran los datos de métricas por hora o minuto correspondientes. En el caso de las métricas por minuto, es posible que se escriban varios minutos de datos a la vez. Este problema puede conllevar que las transacciones de minutos anteriores se sumen a la transacción del minuto actual. Cuando se presente este problema, es posible que el servicio de alerta carezca de todos los datos de métricas disponibles para el intervalo de alerta configurado, lo que podría provocar que se activaran alertas de forma inesperada.
>

## <a name="billing-on-storage-metrics"></a>Facturación de las métricas de almacenamiento

Las solicitudes por escrito para crear entidades de tabla para métricas se cobran con las tarifas estándar aplicables a todas las operaciones de Azure Storage.

Las solicitudes de lectura de datos de métricas realizadas por un cliente también se facturan con las tarifas estándar.

La capacidad usada por las tablas de métricas también es facturable. Use la información siguiente para calcular la cantidad de capacidad usada para almacenar datos de métricas:

-   Si cada hora un servicio utiliza todas las API en todos los servicios, se almacenan aproximadamente 148 KB de datos cada hora en las tablas de transacciones de métricas si ha habilitado un resumen de nivel de servicio y de nivel de API.
-   Si, dentro de una misma hora, un servicio utiliza todas las API en el servicio, se almacenan aproximadamente 12 KB de datos cada hora en las tablas de transacciones de métricas si ha habilitado solo el resumen de nivel de servicio.
-   A la tabla de capacidad de los blobs se le agregan dos filas cada día, siempre que se haya suscrito a los registros. Este escenario implica que todos los días el tamaño de esta tabla aumenta en 300 bytes aproximadamente.

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión de una cuenta de almacenamiento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)
- [Esquema de las tablas de métricas de Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema)
- [Operaciones y mensajes de estado registrados por Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
- [Registro de Storage Analytics](storage-analytics-logging.md)
