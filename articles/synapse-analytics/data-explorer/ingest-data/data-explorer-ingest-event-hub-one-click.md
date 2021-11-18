---
title: Uso de la ingesta con un solo clic para ingerir datos del centro de eventos en el Explorador de datos de Azure Synapse (versión preliminar)
description: Aprenda a usar la ingesta con un solo clic para ingerir (cargar) datos en el Explorador de datos de Azure Synapse desde el centro de eventos.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 6bfb614f3f0263b6ceff3fbf1e00a6c90215c6a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720214"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-synapse-data-explorer-preview"></a>Uso de la ingesta con un clic para crear una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse (versión preliminar)

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [Un solo clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

El Explorador de datos de Azure Synapse ofrece la ingesta (carga de datos) de Event Hubs, una plataforma de streaming de macrodatos y un servicio de ingesta de eventos. [Event Hubs](../../../event-hubs/event-hubs-about.md) puede procesar millones de eventos por segundo prácticamente en tiempo real. En este artículo, conectará un centro de eventos a una tabla del Explorador de datos de Azure Synapse mediante la experiencia de [ingesta con un solo clic](data-explorer-ingest-data-one-click.md).

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Event Hub con datos para la ingesta](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

> [!NOTE]
> La ingesta de datos de un centro de eventos en grupos del Explorador de datos no funcionará si el área de trabajo de Synapse usa una red virtual administrada con la protección de filtración de datos habilitada.

## <a name="ingest-new-data"></a>Ingesta de nuevos datos

1. En Synapse Studio, en el panel izquierdo, seleccione **Datos**.

1. En **Data Explorer Databases** (Bases de datos de Data Explorer), haga clic con el botón derecho en la base de datos pertinente y seleccione **Open in Azure Data Explorer** (Abrir en Azure Data Explorer).

    :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Captura de pantalla Azure Synapse Studio, en la que se muestra Azure Data Explorer en el contexto de un grupo específico.":::

1. En el menú de la izquierda de la interfaz de usuario web, seleccione la pestaña **Datos**. 

    :::image type="content" source="../media/ingest-data-event-hub/one-click-ingestion-event-hub.png" alt-text="Seleccione Datos para la ingesta con un solo clic de datos del centro de eventos en la interfaz de usuario web.":::

1. En **Ingesta de datos del Centro de eventos**, seleccione **Ingerir**. 

Se abrirá la ventana **Ingest new data** (Ingerir nuevos datos) con la pestaña **Destination** (Destino) seleccionada.

### <a name="destination-tab"></a>Pestaña Destination (Destino)

:::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Captura de pantalla de la pestaña Destino. Los campos Clúster, Base de datos y Tabla deben rellenarse antes de continuar para indicar el origen.":::

1. Los campos **Cluster** (Clúster) y **Database** (Base de datos) se rellenan automáticamente. Puede seleccionar otro clúster o base de datos en el menú desplegable.

1. En **Table** (Tabla), seleccione **Create new table** (Crear nueva tabla) y escriba un nombre para la nueva tabla. También puede usar tabla existente. 

    > [!NOTE]
    > Los nombres de las tablas deben tener entre 1 y 1024 caracteres. Puede usar caracteres alfanuméricos, guiones y caracteres de subrayado. No se admiten caracteres especiales.

1. Seleccione **Next: Source** (Siguiente: Origen).

### <a name="source-tab"></a>Pestaña Source (Origen)

1. En **Tipo de origen**, seleccione **Centro de eventos**. 

1. En **Data Connection** (Conexión de datos), rellene los campos siguientes:

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Captura de pantalla de la pestaña Origen con los campos de detalles del proyecto que se van a rellenar para la ingesta de nuevos datos en el Explorador de datos de Azure Synapse con el centro de eventos en la experiencia de un clic.":::

    |**Configuración** | **Valor sugerido** | **Descripción del campo**
    |---|---|---|
    | Nombre de la conexión de datos | *ContosoDataConnection*  | Nombre que identifica la conexión de datos.
    | Suscripción |      | Identificador de la suscripción donde se encuentra el recurso del centro de eventos.  |
    | Espacio de nombres del centro de eventos |  | Nombre que identifica el espacio de nombres. |
    | Centro de eventos |  | Centro de eventos que quiere usar. |
    | Grupo de consumidores |  | Grupo de consumidores definido en el centro de eventos. |
    | Propiedades del sistema de eventos | Seleccione las propiedades pertinentes. | [Propiedades del sistema del centro de eventos](../../../service-bus-messaging/service-bus-amqp-protocol-guide.md#message-annotations). Si hay varios registros por cada mensaje de evento, las propiedades del sistema se agregarán al primero de ellos. Cuando agregue las propiedades del sistema, [cree](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context) o [actualice](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context) el esquema de tabla y la [asignación](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) para incluir las propiedades seleccionadas. |

1. Seleccione **Next: Schema** (Siguiente: Esquema).

## <a name="schema-tab"></a>Pestaña Esquema

Los datos se leen desde el centro de eventos en forma de objetos [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata). Los formatos admitidos son CSV, JSON, PSV, SCsv, SOHsv, TSV, TXT y TSVE.

<!-- For information on schema mapping with JSON-formatted data, see [Edit the schema](one-click-ingestion-existing-table.md#edit-the-schema).
For information on schema mapping with CSV-formatted data, see [Edit the schema](one-click-ingestion-new-table.md#edit-the-schema). -->

:::image type="content" source="../media/ingest-data-event-hub/schema-tab.png" alt-text="Captura de pantalla de la pestaña Esquema para ingerir nuevos datos en el Explorador de datos de Azure Synapse con el centro de eventos en la experiencia de un clic.":::

1. Si los datos que ve en la ventana de vista previa no están completos, puede que necesite más datos para crear una tabla con todos los campos de datos necesarios. Use los siguientes comandos para capturar datos nuevos desde el centro de eventos:
    * **Discard and fetch new data** (Descartar y capturar datos nuevos): descarta los datos presentados y busca nuevos eventos.
    * **Fetch more data** (Capturar más datos): busca más eventos además de los eventos ya encontrados. 
    
    > [!NOTE]
    > Para ver una vista previa de los datos, el centro de eventos debe enviar eventos.
        
1. Seleccione **Siguiente: Resumen**.

## <a name="continuous-ingestion-from-event-hub"></a>Ingesta continua desde el centro de eventos

En la ventana **Ingesta continua desde el centro de eventos** establecida, todos los pasos se marcarán con marcas de verificación verdes cuando el proceso de establecimiento finalice correctamente. Las tarjetas que aparecen debajo de estos pasos le ofrecen opciones para explorar los datos con **consultas rápidas**, deshacer los cambios realizados con las **herramientas** o **supervisar** las conexiones y los datos del centro de eventos.

:::image type="content" source="../media/ingest-data-event-hub/data-ingestion-completed.png" alt-text="Captura de pantalla de la última pantalla de la ingesta en el Explorador de datos de Azure Synapse desde el centro de eventos con la experiencia de un solo clic.":::

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con Data Explorer](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos de Data Explorer](../data-explorer-monitor-pools.md)