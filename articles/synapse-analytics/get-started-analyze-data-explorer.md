---
title: 'Inicio rápido: Introducción al análisis con grupos de Data Explorer (versión preliminar)'
description: En este inicio rápido, aprenderá a analizar datos con Data Explorer.
ms.topic: quickstart
ms.date: 09/30/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 1473b0db71d0ceaa4e7f78d20b10319047427311
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478442"
---
# <a name="quickstart-analyze-with-data-explorer-preview"></a>Inicio rápido: Análisis con Data Explorer (versión preliminar)

En este artículo, aprenderá los pasos básicos para cargar y analizar datos con Data Explorer en Azure Synapse.

## <a name="create-a-data-explorer-pool"></a>Creación de un grupo de Data Explorer

1. En Synapse Studio, en el lado izquierdo, seleccione **Administrar** > **Data Explorer pools** (Grupos de Data Explorer).
1. Seleccione **Nuevo** y, luego, escriba los siguientes detalles en la pestaña **Aspectos básicos**:

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Data Explorer pool name (Nombre del grupo de Data Explorer) | contosodataexplorer | Este es el nombre que tendrá el grupo de Data Explorer. |
    | Carga de trabajo | Proceso optimizado | Esta carga de trabajo proporciona una mayor proporción de almacenamiento de CPU a SSD. |
    | Tamaño del nodo | Pequeña (4 núcleos) | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |

    > [!IMPORTANT]
    > Tenga en cuenta que existen limitaciones específicas para los nombres que los grupos de Data Explorer pueden usar. Los nombres solo deben contener letras minúsculas y números, deben tener entre 4 y 15 caracteres y deben empezar por una letra.

1. Seleccione **Revisar y crear** > **Crear**. El grupo de Data Explorer iniciará el proceso de aprovisionamiento.

## <a name="create-a-data-explorer-database"></a>Creación de una base de datos de Data Explorer

1. En Synapse Studio, en el panel izquierdo, seleccione **Datos**.
1. Seleccione **&plus;** (Agregar un recurso nuevo) > **Data Explorer pool** (Grupo de Data Explorer), y pegue la siguiente información:

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Nombre del grupo | *contosodataexplorer* | Nombre del grupo de Data Explorer que se usará. |
    | Name | *TestDatabase* | El nombre de la base de datos debe ser único dentro del clúster. |
    | Período de retención predeterminado | *365* | El intervalo de tiempo (en días) para el que se garantiza que los datos se mantengan disponibles para consultarlos. El intervalo de tiempo se mide desde el momento en que se ingieren los datos. |
    | Período de caché predeterminado | *31* | El intervalo de tiempo (en días) durante el que los datos consultados con frecuencia se van a mantener disponibles en el almacenamiento SSD o en la RAM, en lugar de en el almacenamiento a largo plazo. |

1. Seleccione **Crear** para crear la base de datos. Normalmente se tarda menos de un minuto.

## <a name="ingest-sample-data-and-analyze-with-a-simple-query"></a>Ingesta de datos de ejemplo y análisis con una consulta simple

1. En Synapse Studio, en el panel izquierdo, seleccione **Desarrollar**.
1. En **KQL scripts** (Scripts de KQL), seleccione **&plus;** (Agregar un recurso nuevo) > **KQL script** (Script de KQL). En el panel derecho, puede asignar un nombre al script.
1. En el menú **Conectarse a**, seleccione *contosodataexplorer*.
1. En el menú **Use database** (Usar base de datos), seleccione *TestDatabase*.
1. Pegue el siguiente comando y seleccione **Ejecutar** para crear una tabla de StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Compruebe que la tabla se creó correctamente. En el panel izquierdo, seleccione **Datos**, elija *contosodataexplorer* en el menú Más y seleccione **Actualizar**. En *contosodataexplorer*, expanda **Tablas** y asegúrese de que la tabla *StormEvents* aparece en la lista.

1. Pegue el siguiente comando y seleccione **Ejecutar** para ingerir datos en la tabla de StormEvents.

    ```Kusto
    .ingest into table StormEvents 'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. Una vez finalizada la ingesta, pegue la siguiente consulta, seleccione la consulta en la ventana y haga clic en **Ejecutar**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

    La consulta devuelve los siguientes resultados de los datos de ejemplo ingeridos.

    :::image type="content" source="data-explorer/media/get-started-analyze-data-explorer/sample-query-results.png" alt-text="Resultados de la ejecución de consultas en datos de ejemplo":::

## <a name="next-steps"></a>Pasos siguientes

- [Tutorial: Uso de consultas KQL](/azure/data-explorer/kusto/query/tutorial?context=/azure/synapse-analytics/context/context&pivots=synapse)
- [Supervisión de grupos de Data Explorer](data-explorer/data-explorer-monitor-pools.md)
