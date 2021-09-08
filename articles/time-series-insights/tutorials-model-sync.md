---
title: Sincronización de modelos entre Azure Digital Twins y Time Series Insights | Microsoft Docs
description: Procedimientos recomendados y herramientas que se usan para traducir el modelo de recursos de ADT a un modelo de recursos de Azure TSI
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 7e7c38001578caa5e23b6c0458f70171d8dcf630
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564507"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Sincronización de modelos entre Azure Digital Twins y Time Series Insights Gen2

En este artículo se explican los procedimientos recomendados y las herramientas que se usan para traducir el modelo de recursos de Azure Digital Twins (ADT) al modelo de recursos de Azure Time Series Insights (TSI).  Este artículo es la segunda parte de una serie de tutoriales de dos partes que explica la integración de Azure Digital Twins con Azure Time Series Insights. La integración de Azure Digital Twins con Time Series Insights permite archivar y realizar un seguimiento del historial de datos de telemetría y de las propiedades calculadas de Digital Twins. Esta serie de tutoriales está dirigida a los desarrolladores que trabajan para integrar Time Series Insights con Azure Digital Twins. La parte 1 explica el [establecimiento de la canalización de datos que lleva los datos de series temporales reales desde Azure Digital Twins a Time Series Insights](../digital-twins/how-to-integrate-time-series-insights.md), y en esta segunda parte de la serie de tutoriales se explica la sincronización del modelo de recursos entre Azure Digital Twins y Time Series Insights. En este tutorial se explican los procedimientos recomendados para elegir y establecer la convención de nomenclatura para identificadores de series temporales (TS ID) y el establecimiento manual de jerarquías en el modelo de series temporales (TSM).

## <a name="choosing-a-time-series-id"></a>Elección de un identificador de serie temporal

El identificador de serie temporal es un identificador único que se usa para identificar recursos en Time Series Insights. Los datos de series temporales (datos de telemetría del campo, que son pares de valor-tiempo) se representan mediante variables que aparecen en TSID. En Azure Digital Twins, las propiedades y los datos de telemetría del gemelo se usan para representar el estado de un gemelo y las medidas que produce, respectivamente. Con el diseño actual de TSM, los TSID deben ser únicos. El uso de identificadores para los gemelos en Azure Digital Twins o la combinación con el nombre de la propiedad o los datos de telemetría siempre constituirá un TS ID único en TSM. En un caso típico, el **_`<Twin ID>`_** será el TSID y los nombres de propiedad y telemetría serán las variables de TSM. No obstante, hay casos de uso en los que se prefiere que las jerarquías de recursos de Time Series Insights se aplanen mediante un formato de claves compuestas, como **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_**. Veamos un ejemplo para explicar este último caso. Considere una habitación de un edificio modelada como un gemelo cuyo identificador es Room22. Su propiedad de configuración de temperatura se traducirá como **_TSID Room22_TempSetting_** y la medida de temperatura como **_Room22_TempMea_** en TSM.

[![Elección de un identificador de serie temporal](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualización de series temporales

La contextualización de los datos (principalmente de naturaleza espacial) de Time Series Insights se logra mediante las jerarquías de recursos y se usa para facilitar la navegación por los datos a través de una vista de árbol en el explorador de Time Series Insights. Los tipos de series temporales y las jerarquías se definen mediante el modelo de series temporales (TSM) en Time Series Insights. Los tipos de TSM ayudan a definir variables, mientras que los niveles de la jerarquía y los valores de campo de la instancia se usan para construir la vista de árbol en el explorador de Time Series Insights. Para más información sobre el TSM, consulte la [documentación en línea de Time Series Insights](./concepts-model-overview.md).

En Azure Digital Twins, la conexión entre los recursos se expresa mediante relaciones gemelas. Las relaciones gemelas son simplemente un grafo de recursos conectados. Sin embargo, en Time Series Insights, las relaciones entre los recursos son jerárquicas por naturaleza. Es decir, los recursos comparten una relación de tipo principal-secundario y se representan mediante una estructura de árbol. Para traducir la información de la relación de Azure Digital Twins a las jerarquías de Time Series Insights, es necesario elegir las relaciones jerárquicas pertinentes de Azure Digital Twins. Azure Digital Twins usa un lenguaje de modelado estándar abierto denominado lenguaje de definición de gemelos digitales (DTDL). Los modelos de DTDL se describen mediante una variante de JSON denominada JSON-LD. Consulte la [documentación sobre DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para obtener una información completa sobre la especificación.

[![Conexión entre recursos](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Traducción de la representación del grafo de Azure Digital Twins en la estructura de árbol de Time Series Insights

En las secciones siguientes del tutorial se capturan algunos escenarios básicos de la traducción manual de la estructura del grafo de Azure Digital Twins a la estructura de árbol de Time Series Insights.

Sistema de ejemplo: En este tutorial, se usa el siguiente ejemplo para explicar los conceptos que se describen a continuación. Es un sistema de administración sencillo y ficticio de un edificio de una sola planta y dos habitaciones. Tiene tres termostatos, uno en cada una de las habitaciones y otro común para toda la planta. Además, también tiene un contador de agua que mide el caudal de agua de Room21 a Room22 a través de una conexión de canalización entre las habitaciones. Observando la relación espacial entre gemelos, presenta ambos tipos de relaciones.

1. Más habitual, una relación jerárquica. Por ejemplo, Building40 -> Floor01 -> FloorTS* -> Temperature
1. No tan habitual, una relación circular. Por ejemplo, a partir de Building40, se puede realizar un seguimiento de FlowMtr a través de dos rutas diferentes.

   1. Building40 -> Floor01 -> Room21 -> FlowMtr* -> Flow
   1. Building40 -> Floor01 -> Room22 -> FlowMtr* -> Flow  
      Donde "Flow" es el dato de telemetría real que mide el caudal de agua entre Room21 y Room22.

> [!Note]
>
> `*` FloorTS significa FloorThermoStat (termostato de la planta) y FlowMtr significa Flow Meter (contador de agua) y normalmente se eligen como TSID. La temperatura y el caudal son los datos de telemetría sin procesar que se denominan variables en Time Series Insights.

Dada la limitación actual en Time Series Insights por la que un recurso no se puede representar en varias ramas, en las secciones siguientes se explica el modelado de las relaciones jerárquicas y las circulares en Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Caso 1: Relación jerárquica (de tipo principal-secundario)

Es el tipo de relación más común entre gemelos. El modelado de la relación de tipo principal-secundario puro se explica en la siguiente ilustración. Los campos de instancia y el TSID se derivan de los identificadores de los gemelos, como se muestra a continuación. Aunque los campos de la instancia se pueden actualizar manualmente mediante el explorador de Time Series Insights, la sección siguiente denominada "Actualización de campos de instancia mediante API" explica la escucha de cambios en el modelo de Azure Digital Twins y la actualización de los campos de la instancia en Time Series Insights mediante funciones de Azure.

[![Asignación de identificadores de gemelos](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Asignación 2 de identificadores de gemelos](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Caso 2: Relación circular

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Relación circular entre Azure Digital Twins y la relación jerárquica simple de Time Series Insights

Dado que el TSID debe ser único y solo puede representarse en una jerarquía, en este caso se representa el _"FlowMtr"_ con una telemetría denominada _"Flow"_ directamente debajo del gemelo _"Room21"_ . En el futuro, cuando Time Series Insights pueda admitir la representación múltiple de series temporales en TSM, la telemetría _"Flow"_ se representará en _"Room 21"_ y _"Room 22"_

En la captura de pantalla siguiente se muestra la asignación manual de los identificadores de gemelos de Azure Digital Twins al campo de la instancia en TSM y a la jerarquía resultante en Time Series Insights.

[![Asignación de identificadores de gemelos en Azure Digital Twins](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Relación circular entre Azure Digital Twins y varias jerarquías de Time Series Insights mediante duplicados

La primera parte del tutorial explica cómo un programa cliente (una función de Azure) ayuda a transferir los datos de telemetría desde IoT Hub u otros orígenes de eventos a Azure Digital Twins. Este enfoque sugiere el uso del mismo programa cliente para hacer actualizaciones de las propiedades pertinentes del gemelo principal. En este ejemplo, al leer la telemetría de FlowMtr desde IoT Hub y actualizar la propiedad "Flow" en el gemelo FlowMtr, el programa también puede actualizar las propiedades correspondientes en todos los gemelos principales posibles del origen. En nuestro ejemplo, sería la propiedad "outflowmea" (identificada mediante la relación "outflow") de Room21 y la propiedad "inflowmea" de Room22.  En la captura de pantalla siguiente se muestra la experiencia del usuario final en el Explorador de Time Series Insights. Se debe tener en cuenta que mediante este enfoque se obtienen datos duplicados.

[![Explorador de Time Series Insights](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

En el fragmento de código siguiente se muestra cómo la aplicación cliente pudo desplazarse por la relación entre los gemelos mediante las API de Azure Digital Twins.

> [!Note]
>
> En este ejemplo de fragmento de código se da por supuesto que los lectores están familiarizados con la [parte 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) del tutorial y este cambio de código se realizó dentro de la función "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Actualización de los campos de la instancia mediante API

En esta sección del tutorial se explica la escucha de los cambios de los modelos de Azure Digital Twins como, por ejemplo, la creación o eliminación de gemelos, o el cambio en las relaciones entre estos, y la actualización de los campos de instancia y de las jerarquías mediante programación con las API de modelos de Time Series Insights. Este método de actualización del modelo de Time Series Insights normalmente se logra mediante funciones de Azure. En Azure Digital Twins, las notificaciones de eventos, como la adición o eliminación de gemelos, se pueden enrutar hacia servicios descendentes como Event Hubs que, a su vez, pueden alimentar a las funciones de Azure. [Aquí](../digital-twins/how-to-manage-routes-portal.md) encontrará más información sobre el enrutamiento y el filtrado de eventos.  En lo que resta de esta sección se explica el uso de las API de modelo de Time Series Insights en funciones de Azure para actualizar el modelo de Time Series Insights en respuesta a la adición de un gemelo (un tipo de cambio de modelo) en Azure Digital Twins.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Recepción e identificación de la notificación de eventos de adición de un gemelo

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Creación de un cliente de Time Series Insights y adición de detalles de instancia

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Aplicación de la información de la jerarquía a la instancia

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Pasos siguientes

En la tercera parte de la serie de tutoriales se mostrará cómo consultar datos históricos de Azure Digital Twins mediante las API de Time Series Insights. Este es un trabajo en curso y la sección se actualizará cuando esté preparado. Mientras tanto, se anima a los lectores a consultar la [documentación sobre las API de consulta de datos de Time Series Insights](./concepts-query-overview.md).
