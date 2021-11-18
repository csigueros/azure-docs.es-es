---
title: Conexión de datos de Event Grid para el Explorador de datos de Azure Synapse (versión preliminar)
description: En este artículo se proporciona información general sobre cómo ingerir (cargar) datos en el Explorador de datos de Azure Synapse desde Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 14b5129a29e7d09e86ebfd8874654e55be6f1cb6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715841"
---
# <a name="event-grid-data-connection-preview"></a>Conexión de datos de Event Grid (versión preliminar)

La ingesta de Event Grid es una canalización que escucha en Azure Storage y actualiza Azure Data Explorer para extraer información cuando se producen eventos suscritos. El Explorador de datos ofrece la ingesta de datos continua desde Azure Storage (Blob Storage y ADLSv2) con una suscripción de [Azure Event Grid](../../../event-grid/overview.md) para las notificaciones sobre blobs creados o cambios de nombre de blobs y la transmisión de estas notificaciones al Explorador de datos mediante un centro de eventos.

La canalización de ingesta de datos de Event Grid tiene varios pasos. Se crea una tabla de destino en el Explorador de datos en la que se realizará la ingesta de [datos en un formato determinado](#data-format). Luego, se crea una conexión de datos de Event Grid en el Explorador de datos. La conexión de datos de Event Grid debe conocer la información de [enrutamiento de eventos](#events-routing), como la tabla a la que se van a enviar los datos y la asignación de la tabla. También se especifican las [propiedades de la ingesta](#ingestion-properties), que describen los datos que se van a ingerir, la tabla de destino y la asignación. Puede generar datos de ejemplo y [cargar blobs](#upload-blobs) o [cambiar el nombre de los blobs](#rename-blobs) para probar la conexión. [Elimine los blobs](#delete-blobs-using-storage-lifecycle) después de la ingesta. Este proceso se puede administrar a través de [Azure Portal](data-explorer-ingest-event-grid-portal.md). <!-- , using [one-click ingestion](one-click-ingestion-new-table.md), programmatically with [C#](data-connection-event-grid-csharp.md) or [Python](data-connection-event-grid-python.md), or with the [Azure Resource Manager template](data-connection-event-grid-resource-manager.md). -->

<!-- For general information about data ingestion in Data Explorer, see [Data Explorer data ingestion overview](ingest-data-overview.md). -->

## <a name="data-format"></a>Formato de datos

- Consulte los [formatos admitidos](data-explorer-ingest-data-supported-formats.md).
- Consulte los [formatos de compresión admitidos](data-explorer-ingest-data-supported-formats.md#supported-data-compression-formats).
    - El tamaño de datos sin comprimir original debe formar parte de los metadatos del blob o, de lo contrario, el Explorador de datos lo estimará. El límite de tamaño sin comprimir por cada archivo de la ingesta es de 4 GB.

> [!NOTE]
> La suscripción de notificación de Event Grid se puede establecer en cuentas de Azure Storage para `BlobStorage`, `StorageV2` o [Data Lake Storage Gen2](../../../storage/blobs/data-lake-storage-introduction.md).

## <a name="ingestion-properties"></a>Propiedades de la ingesta

Puede especificar las [propiedades de la ingesta](data-explorer-ingest-data-properties.md) de blobs mediante los metadatos del blob.
Puede establecer las siguientes propiedades:

[!INCLUDE [ingestion-properties-event-grid](../includes/data-explorer-event-grid-ingestion-properties.md)]

## <a name="events-routing"></a>Enrutamiento de eventos

Al configurar una conexión de Blob Storage en el clúster del Explorador de datos, especifique las propiedades de la tabla de destino:

- Nombre de la tabla
- Formato de datos
- mapping

Esta configuración es el enrutamiento predeterminado de los datos, en ocasiones conocido como `static routing` (enrutamiento estático).
También puede especificar las propiedades de la tabla de destino para cada blob mediante los metadatos del blob. Los datos se enrutarán dinámicamente, según lo especificado en las [propiedades de la ingesta](#ingestion-properties).

En el ejemplo siguiente se muestra cómo establecer las propiedades de la ingesta en los metadatos del blob antes de la carga. Los blobs se enrutan a tablas diferentes.

Para más información, consulte [Carga de blobs](#upload-blobs).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>Cargar blobs

Puede crear un blob a partir de un archivo local, establecer las propiedades de la ingesta en los metadatos del blob y cargarlo. Para ver ejemplos, consulte [Ingesta de blobs en el Explorador de datos mediante la suscripción a las notificaciones de Event Grid](data-explorer-ingest-event-grid-portal.md#generate-sample-data).

> [!NOTE]
> - Use `BlockBlob` para generar los datos. No se admite `AppendBlob`.
> - El uso del SDK de Azure Data Lake Storage Gen2 requiere el uso de `CreateFile` para cargar los archivos y el elemento `Flush` al final con el parámetro de cierre establecido en "true".
<!-- > For a detailed example of Data Lake Gen2 SDK correct usage, see [upload file using Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk). -->
> - Cuando el punto de conexión del centro de eventos no reconoce la recepción de un evento, Azure Event Grid activa un mecanismo de reintento. Si se produce un error en esta entrega del reintento, Event Grid puede entregar los eventos sin entregar a una cuenta de almacenamiento mediante un proceso de *puesta en cola de mensajes fallidos*. Para más información, vea [Entrega y reintento de entrega de mensajes de Event Grid](../../../event-grid/delivery-and-retry.md).

## <a name="rename-blobs"></a>Cambio del nombre de los blobs

Al usar ADLSv2, puede cambiar el nombre de un blob para desencadenar la ingesta de blobs en el Explorador de datos. Para ver un ejemplo, consulte [Ingesta de blobs en el Explorador de datos mediante la suscripción a las notificaciones de Event Grid](data-explorer-ingest-event-grid-portal.md#generate-sample-data).

> [!NOTE]
> - El cambio de nombre de un directorio es posible en ADLSv2, pero no desencadena eventos de tipo *Blob con el nombre cambiado* ni la ingesta de blobs dentro del directorio. Para ingerir blobs después de cambiar el nombre, cambie directamente el nombre de los blobs deseados.
> - Si definió filtros para realizar un seguimiento de asuntos específicos al [crear la conexión de datos](data-explorer-ingest-event-grid-portal.md#create-an-event-grid-data-connection).<!-- or while creating [Event Grid resources manually](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), these filters are applied on the destination file path. -->

## <a name="delete-blobs-using-storage-lifecycle"></a>Eliminación de blobs mediante el ciclo de vida de almacenamiento

El Explorador de datos no eliminará los blobs después de la ingesta. Use [Administración del ciclo de vida de Azure Blob Storage](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) para administrar la eliminación de blobs. Se recomienda mantener los blobs de tres a cinco días.

## <a name="known-event-grid-issues"></a>Problemas conocidos de Event Grid

- Al usar el Explorador de datos para [exportar](/azure/data-explorer/kusto/management/data-export/export-data-to-storage?context=/azure/synapse-analytics/context/context) los archivos que se usan para la ingesta de datos de Event Grid, tenga en cuenta lo siguiente: 
    - Las notificaciones de Event Grid no se desencadenan si la cadena de conexión proporcionada al comando de exportación o la cadena de conexión proporcionada a una [tabla externa](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table?context=/azure/synapse-analytics/context/context) es una cadena de conexión en el [formato de ADLS Gen2](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-data-lake-storage-gen2) (por ejemplo, `abfss://filesystem@accountname.dfs.core.windows.net`) pero la cuenta de almacenamiento no está habilitada para espacios de nombres jerárquicos.
    - Si la cuenta no está habilitada para espacios de nombres jerárquicos, la cadena de conexión debe usar el formato de [Blob Storage](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-blob-storage) (por ejemplo, `https://accountname.blob.core.windows.net`). La exportación funciona según lo previsto incluso cuando se usa la cadena de conexión de ADLS Gen2, pero las notificaciones no se desencadenarán y ingesta de datos de Event Grid no funcionará.

## <a name="next-steps"></a>Pasos siguientes

- [Ingesta de blobs en el Explorador de datos mediante la suscripción a las notificaciones de Event Grid](data-explorer-ingest-event-grid-portal.md)
<!-- - [Create an Event Grid data connection for Data Explorer by using C#](data-connection-event-grid-csharp.md)
- [Create an Event Grid data connection for Data Explorer by using Python](data-connection-event-grid-python.md)
- [Create an Event Grid data connection for Data Explorer by using Azure Resource Manager template](data-connection-event-grid-resource-manager.md)
- [Use one-click ingestion to ingest CSV data from a container to a new table in Data Explorer](one-click-ingestion-new-table.md) -->
