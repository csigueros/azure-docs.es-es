---
title: 'Inicio rápido: Introducción a la ingesta de datos con canalizaciones (versión preliminar)'
description: En este inicio rápido, aprenderá a ingerir datos en grupos del Explorador de datos mediante canalizaciones de Azure Synapse.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 746f37fae42b117853d2f808d9fc5ff9fa9f980b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478276"
---
# <a name="quickstart-ingest-data-using-azure-synapse-pipelines-preview"></a>Inicio rápido: Ingesta de datos mediante canalizaciones de Azure Synapse (versión preliminar)

En este inicio rápido, aprenderá a cargar datos de un origen de datos en un grupo del Explorador de datos de Azure Synapse.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Cree una tabla [!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)].

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Compruebe que la tabla se creó correctamente. En el panel izquierdo, seleccione **Datos**, elija *contosodataexplorer* en el menú Más y seleccione **Actualizar**. En *contosodataexplorer*, expanda **Tablas** y asegúrese de que la tabla *StormEvents* aparece en la lista.

- Obtenga los puntos de conexión de consulta e ingesta de datos. Necesitará el punto de conexión de consulta para configurar el servicio vinculado.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado

En Azure Synapse Analytics, un servicio vinculado es donde se define la información de conexión a otros servicios. En esta sección, creará un servicio vinculado para Azure Data Explorer.

1. En Synapse Studio, en el panel de la izquierda, seleccione **Administrar** > **Servicios vinculados**.
1. Seleccione **&plus; Nuevo**.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-linked-service.png" alt-text="Captura de pantalla de los servicios vinculados, en la que se muestra la lista de servicios existentes y se resalta el botón Agregar nuevo.":::

1. Seleccione el servicio **Azure Data Explorer** en la galería y seleccione **Continuar**.

    :::image type="content" source="../media/ingest-data-pipeline/select-new-data-explorer-linked-service.png" alt-text="Captura de pantalla del nuevo panel Servicios vinculados, en la que se muestra la lista de servicios disponibles y se resalta la opción para agregar un nuevo servicio de Azure Data Explorer.":::

1. En la página Nuevos servicios vinculados, use la siguiente información:

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Nombre | *contosodataexplorerlinkedservice* | El nombre del nuevo servicio vinculado de Azure Data Explorer. |
    | Método de autenticación | *Identidad administrada* | Método de autenticación del nuevo servicio. |
    | Método de selección de cuentas | *Escribir manualmente* | Método para especificar el punto de conexión de consulta. |
    | Punto de conexión | *https:\/\/contosodataexplorer.contosoanalytics.dev.kusto.windows.net* | Punto de conexión de consulta que [anotó anteriormente](#prerequisites). |
    | Base de datos | *TestDatabase* | Base de datos en la que desea ingerir los datos. |

    :::image type="content" source="../media/ingest-data-pipeline/create-new-data-explorer-linked-service.png" alt-text="Captura de pantalla del nuevo panel de detalles de los servicios vinculados, en el que se muestran los campos que deben completarse para el nuevo servicio.":::

1. Seleccione **Probar conexión** para validar la configuración y, después, seleccione **Crear**.

## <a name="create-a-pipeline-to-ingest-data"></a>Creación de una canalización para ingerir datos

Una canalización contiene el flujo lógico para una ejecución de un conjunto de actividades. En esta sección, creará una canalización que contiene una actividad de copia que ingiere datos desde un origen de su elección en un grupo del Explorador de datos.

1. En Synapse Studio, en el panel izquierdo, seleccione **Integrar**.

1. Seleccione **&plus;**  > **Canalización**. En el panel derecho, puede asignar un nombre a la canalización.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-pipeline.png" alt-text="Captura de pantalla que muestra la selección para crear una canalización.":::

1. En **Actividades** > **Move & transform** (Mover y transformar), arrastre **Copiar datos** en el lienzo de la canalización.
1. Seleccione la actividad de copia y vaya a la pestaña **Origen**. Seleccione o cree un nuevo conjunto de datos de origen como origen desde el que copiar los datos.
1. Haga clic en la pestaña **Receptor**. Seleccione **Nuevo** para crear un conjunto de datos de receptor.

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink.png" alt-text="Captura de pantalla de la actividad de copia de canalización, en la que se muestra la selección para crear un nuevo receptor.":::

1. Seleccione el conjunto de datos **Azure Data Explorer** en la galería y seleccione **Continuar**.
1. En el panel **Establecer propiedades**, use la siguiente información y seleccione **Aceptar**.

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Nombre | *AzureDataExplorerTable* | Nombre de la nueva canalización. |
    | Servicio vinculado | *contosodataexplorerlinkedservice* | El servicio vinculado que creó anteriormente. |
    | Tabla | *StormEvents* | La tabla que creó antes. |

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink-set-properties.png" alt-text="Captura de pantalla del panel de propiedades del conjunto de actividades de copia de la canalización, en el que se muestran los campos que deben completarse para el nuevo receptor.":::

1. Para comprobar la canalización, seleccione **Validate** (Comprobar) en la barra de herramientas. Verá el resultado de la salida de la validación de canalización en el lado derecho de la página.

## <a name="debug-and-publish-the-pipeline"></a>Depuración y publicación de la canalización

Una vez que haya terminado de configurar la canalización, puede ejecutar una depuración antes de publicar los artefactos para verificar que todo esté correcto.

1. Seleccione **Depurar** en la barra de herramientas. Verá el estado de ejecución de la canalización en la pestaña **Output** (Salida) en la parte inferior de la ventana.

1. Una vez que la ejecución de la canalización se realiza correctamente, en la barra de herramientas superior, seleccione **Publish all** (Publicar todo). Esta acción publica las entidades (conjuntos de datos y canalizaciones) que creó en el servicio de Synapse Analytics.
1. Espere a que aparezca el mensaje **Successfully published** (Publicado correctamente). Para ver los mensajes de notificación, seleccione el botón de campana en la esquina superior derecha.

## <a name="trigger-and-monitor-the-pipeline"></a>Activación y supervisión de la canalización

En esta sección, se desencadena manualmente la canalización publicada en el paso anterior.

1. Seleccione **Add Trigger** (Agregar desencadenador) en la barra de herramientas y, después, seleccione **Trigger Now** (Desencadenar ahora). En la página **Pipeline Run** (Ejecución de la canalización), seleccione **OK** (Aceptar).

1. Vaya a la pestaña **Supervisar** que se encuentra en la barra lateral izquierda. Verá una ejecución de canalización que se desencadena de forma manual.
1. Cuando la ejecución de la canalización finaliza correctamente, seleccione el vínculo de la columna **Pipeline name** (Nombre de canalización) para ver los detalles de la ejecución de actividad o para volver a ejecutar la canalización. En este ejemplo, solo hay una actividad, así que solo verá una entrada en la lista.
1. Para más información sobre la operación de copia, seleccione el vínculo **Details** (Detalles) (icono de gafas) en la columna **Activity name** (Nombre de actividad). Puede supervisar detalles como el volumen de datos copiados desde el origen al receptor, el rendimiento de los datos, los pasos de ejecución con su duración correspondiente y las configuraciones que se utilizan.
1. Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **Todas las ejecuciones de la canalización**. Seleccione **Refresh** (Actualizar) para actualizar la lista.
1. Compruebe que los datos se han escrito correctamente en el grupo del Explorador de datos.

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con Data Explorer](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos de Data Explorer](../data-explorer-monitor-pools.md)
