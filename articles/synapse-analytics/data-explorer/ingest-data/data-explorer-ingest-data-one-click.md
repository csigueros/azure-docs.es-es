---
title: 'Inicio rápido: Introducción a la ingesta de datos con un solo clic (versión preliminar)'
description: En este inicio rápido, aprenderá a ingerir datos en grupos de Data Explorer con un solo clic.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d5fef80a72e210e71eea0331b0d6098d5414d78a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478278"
---
# <a name="quickstart-ingest-data-using-one-click-preview"></a>Inicio rápido: Ingesta de datos con un solo clic (versión preliminar)

La ingesta con un solo clic hace que el proceso de ingesta de datos sea sencillo, rápido e intuitivo. La ingesta con un solo clic le ayuda a comenzar rápidamente para empezar a ingerir datos, crear tablas de base de datos y asignar estructuras. Seleccione datos de diferentes tipos de orígenes y en diferentes formatos, ya sea en un proceso de ingesta único o continuo.

Las siguientes características hacen que la ingesta con un solo clic sea tan útil:

* Experiencia intuitiva guiada por el Asistente para ingesta
* Ingesta de datos en cuestión de minutos
* Ingesta de datos de diferentes tipos de orígenes: archivos locales, blobs y contenedores (hasta 10 000 blobs)
* Ingesta de datos en diversos [formatos](#file-formats)
* Ingesta de datos en tablas nuevas o existentes
* La asignación de tablas y esquemas es recomendable y fácil de cambiar
<!-- * Continue ingestion easily and quickly from a container with [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion) -->

La ingesta con un clic es especialmente útil cuando se ingieren datos por primera vez, o cuando el esquema de los datos no resulta familiar.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Cree una tabla [!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)].

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Compruebe que la tabla se creó correctamente. En el panel izquierdo, seleccione **Datos**, elija *contosodataexplorer* en el menú Más y seleccione **Actualizar**. En *contosodataexplorer*, expanda **Tablas** y asegúrese de que la tabla *StormEvents* aparece en la lista.

## <a name="access-the-one-click-wizard"></a>Acceso al asistente de ingesta con un clic

El Asistente para ingesta con un clic le guía por el proceso de ingesta con un clic.

* Para acceder al asistente desde Azure Synapse:

    1. En Synapse Studio, en el panel izquierdo, seleccione **Datos**.
    1. En **Data Explorer Databases** (Bases de datos de Data Explorer), haga clic con el botón derecho en la base de datos pertinente y seleccione **Open in Azure Data Explorer** (Abrir en Azure Data Explorer).

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Captura de pantalla de Azure Synapse Studio, en la que se muestra la apertura de Azure Data Explorer en el contexto de un grupo específico.":::

    1. Haga clic con el botón derecho en el grupo pertinente y seleccione **Ingest new data** (Ingerir nuevos datos).

* Para acceder al asistente desde Azure Portal:

    1. En Azure Portal, busque y seleccione el área de trabajo de Synapse pertinente.
    1. En **Data Explorer pools** (Grupos de Data Explorer), seleccione el grupo pertinente.
    1. En la página principal **Welcome to Data Explorer pool** (Le damos la bienvenida al grupo de Data Explorer), seleccione **Ingest new data** (Ingerir nuevos datos).

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-portal.png" alt-text="Captura de pantalla de Azure Portal, en la que se muestra la apertura de Azure Data Explorer en el contexto de un grupo específico.":::


* Para acceder al asistente desde la interfaz de usuario web de Azure Data Explorer:

    1. Antes de empezar, siga estos pasos para obtener los puntos de conexión de ingesta de datos y consulta.
        [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]
    1. En la interfaz de usuario web de Azure Data Explorer, agregue una conexión al *punto de conexión de consulta*.
    1. Seleccione **Consulta** en el menú izquierdo, haga clic con el botón derecho en la **base de datos** o la **tabla** y, luego, seleccione **Ingest new data** (Ingerir nuevos datos).

## <a name="one-click-ingestion-wizard"></a>Asistente para ingesta con un clic

> [!NOTE]
> En esta sección se describe el asistente que usa el centro de eventos como origen de datos. También puede usar estos pasos para ingerir datos de un blob, un archivo, un contenedor de blobs y un contenedor de ADLS Gen2.
>

1. En la pestaña **Destino**, elija la base de datos y la tabla para los datos ingeridos.

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Captura de pantalla del asistente para la ingesta con un solo clic de Azure Data Explorer, en la que se muestra la selección de una base de datos y una tabla.":::

1. En la pestaña **Origen de datos**:
    1. Seleccione *Centro de eventos* como **tipo de origen** de la ingesta.

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-source-type.png" alt-text="Captura de pantalla del asistente para la ingesta con un solo clic de Azure Data Explorer, en la que se muestra la selección del tipo de origen.":::

    1. Rellene los detalles de conexión de datos del centro de eventos con la siguiente información:

        | Configuración | Valor sugerido | Descripción |
        |--|--|--|
        | Nombre de la conexión de datos | *ContosoDataConnection* | Nombre de la conexión de datos del centro de eventos. |
        | Subscription | *Contoso_Synapse* | La suscripción donde reside el centro de eventos. |
        | Espacio de nombres del centro de eventos | *contosoeventhubnamespace* | El espacio de nombres del centro de eventos. |
        | Grupo de consumidores | *contosoconsumergroup* | El nombre del grupo de consumidores del centro de eventos. |

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Captura de pantalla del asistente para la ingesta con un solo clic de Azure Data Explorer, en la que se muestran los detalles de conexión del centro de eventos.":::

    1. Seleccione **Next** (Siguiente).

### <a name="schema-mapping"></a>Asignación de esquemas

El servicio genera automáticamente propiedades de esquema y de ingesta que se pueden cambiar. Puede usar una estructura de asignación existente o crear una nueva, en función de si va a realizar la ingesta en una tabla nueva o en una ya existente.

En la pestaña **Esquema**, realice las siguientes acciones:
   1. Confirme el tipo de compresión generado automáticamente.
   1. Elija el [formato de los datos](#file-formats). Los distintos formatos le permitirán realizar más cambios.
   1. Cambie la asignación en la [ventana Editor](#editor-window).

#### <a name="file-formats"></a>Formatos de archivo

La ingesta con un solo clic admite la ingesta de datos de origen en todos los [formatos de datos que admite Data Explorer para la ingesta](data-explorer-ingest-data-supported-formats.md).

### <a name="editor-window"></a>Ventana Editor

En la ventana **Editor** de la pestaña **Esquema**, puede ajustar las columnas de la tabla de datos según sea necesario.

Los cambios que pueda realizar a una tabla dependerán de los siguientes parámetros:

* El tipo de **tabla** es nuevo o existente
* El tipo de **asignación** es nuevo o existente

Tipo de tabla. | Tipo de asignación | Ajustes disponibles|
|---|---|---|
|Tabla nueva   | Asignación nueva |Cambiar el tipo de datos, cambiar el nombre de la columna, nueva columna, eliminar columna, actualizar columna, orden ascendente, orden descendente.  |
|Tabla existente  | Asignación nueva | Nueva columna (en la que puede cambiar el tipo de datos, cambiar el nombre y actualizar).<br> Nueva columna, orden ascendente, orden descendente  |
| | Asignación existente | Orden ascendente, orden descendente

> [!NOTE]
> Al agregar una nueva columna o actualizarla, puede cambiar las transformaciones de asignación. Para más información, consulte [Asignación de transformaciones](#mapping-transformations).

<!-- >[!NOTE]
> At any time, you can open the [command editor](one-click-ingestion-new-table.md#command-editor) above the **Editor** pane. In the command editor, you can view and copy the automatic commands generated from your inputs. -->

#### <a name="mapping-transformations"></a>Asignación de transformaciones

Algunas de las asignaciones de formato de datos (Parquet, JSON y Avro) admiten transformaciones sencillas en el momento de la ingesta. Para aplicar la asignación de transformaciones, cree o actualice una columna en la [ventana Editor](#editor-window).

La asignación de transformaciones se puede realizar en una columna de **tipo** string o datetime y un **origen** con un tipo de datos int o long. Las asignaciones de transformaciones que se admiten son:

* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

### <a name="data-ingestion"></a>Ingesta de datos

Una vez que haya completado la asignación de esquemas y las manipulaciones en las columnas, el Asistente para ingesta iniciará el proceso de ingesta de datos.

* Al ingerir datos de orígenes que **no sean contenedores**, la ingesta tendrá un efecto inmediato.

* Si el origen de datos es un **contenedor**:
    * La [directiva de procesamiento por lotes](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) de Data Explorer agregará los datos.
    * Después de la ingesta, puede descargar el informe de ingesta y revisar el rendimiento de cada blob que se ha utilizado.
    <!-- * You can select **Create continuous ingestion** and set up [continuous ingestion using Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion). -->

### <a name="initial-data-exploration"></a>Exploración inicial de datos

Después de la ingesta, el asistente le ofrece opciones de uso de **comandos rápidos** para la exploración inicial de los datos.

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con Data Explorer](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos de Data Explorer](../data-explorer-monitor-pools.md)
