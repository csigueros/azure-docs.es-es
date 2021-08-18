---
title: 'Configuración de la topología de factoría conectada: Azure | Microsoft Docs'
description: En este artículo se describe cómo configurar el acelerador de la solución Connected Factory, incluida su topología.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 863734596861f3116418cb322a1e4a8ae9c8ea40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746422"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Configuración del acelerador de soluciones de factoría conectada

> [!IMPORTANT]
> Mientras actualizamos este artículo, consulte el artículo sobre [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/), donde encontrará el contenido más actualizado.

El acelerador de soluciones de factoría conectada muestra un panel simulado para una compañía ficticia Contoso. Esta compañía tiene factorías en numerosas ubicaciones globales de todo el mundo.

En este artículo se usa Contoso como ejemplo para describir cómo configurar la topología de una solución de factoría conectada.

## <a name="simulated-factories-configuration"></a>Configuración de factorías simuladas

Cada factoría de Contoso tiene líneas de producción, y cada una de ellas se compone de tres estaciones. Cada estación es un servidor OPC UA real con un rol específico:

* Estación de ensamblado
* Estación de prueba
* Estación de empaquetado

Estos servidores OPC UA tienen nodos OPC UA y el [publicador de OPC](/previous-versions/azure/iot-accelerators/overview-opc-publisher) envía los valores de estos nodos a la factoría conectada. Esto incluye:

* Estado operativo actual, como el consumo de energía actual.
* Información de producción, como el número de productos fabricados.

Puede usar el panel para explorar la topología de la factoría de Contoso desde una vista global hasta una vista a nivel de estación. El panel de factoría conectada permite:

* La visualización de figuras OEE y KPI para cada capa de la topología.
* La visualización de los valores actuales de los nodos OPC UA de las estaciones.
* La agregación de las figuras OEE y KPI desde el nivel de estación al nivel global.
* La visualización de alertas y acciones que se deben realizar si los valores alcanzan umbrales específicos.

## <a name="connected-factory-topology"></a>Topología de la factoría conectada

La topología de las factorías, las líneas de producción y las estaciones es jerárquica:

* El nivel global tiene nodos de factoría como elementos secundarios.
* Las factorías tienen nodos de línea de producción como elementos secundarios.
* Las líneas de producción tienen nodos de estación como elementos secundarios.
* Las estaciones (servidores OPC UA) tienen nodos OPC UA como elementos secundarios.

Todos los nodos de la topología tienen un conjunto común de propiedades que definen:

* Un identificador único para el nodo de la topología.
* Un nombre.
* Una descripción.
* Una imagen.
* Los elementos secundarios del nodo de la topología.
* Los valores mínimos, de destino y máximos de las figuras OEE y KPI y las acciones de alerta que se van a ejecutar.

## <a name="topology-configuration-file"></a>Archivo de configuración de la topología

Para configurar las propiedades enumeradas en la sección anterior, la solución de factoría conectada utiliza un archivo de configuración denominado [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Puede encontrar este archivo en el código fuente de la solución en la carpeta `WebApp/Contoso/Topology`.

El siguiente fragmento de código muestra un esquema del archivo de configuración `ContosoTopologyDescription.json`:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Las propiedades comunes de `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>` y `<station_configuration>` son:

* **Name** (tipo cadena)

  Define un nombre descriptivo, que debería ser solo una palabra para el nodo de la topología que se va a mostrar en el panel.

* **Description** (tipo cadena)

  Describe el nodo de la topología con más detalle.

* **Image** (tipo cadena)

  La ruta de acceso a una imagen de la solución WebApp para ilustrar cuándo se muestra información sobre el nodo de la topología en el panel.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1** y **Kpi2** (tipo `<performance_definition>`)

  Estas propiedades definen los valores mínimos, de destino y máximos de la figura operativa utilizada para generar alertas. Estas propiedades también definen las acciones que se ejecutarán si se detecta una alerta.

Los elementos `<factory_configuration>` y `<production_line_configuration>` tienen una propiedad:

* **Guid** (tipo cadena)

  Identifica de forma única el nodo de la topología.

`<factory_configuration>` tiene una propiedad:

* **Location** (tipo `<location_definition>`)

  Especifica dónde se encuentra la factoría.

`<station_configuration>` tiene propiedades:

* **OpcUri** (tipo cadena)

  Esta propiedad debe establecerse en el URI de aplicación OPC UA del servidor OPC UA.
  Dado que debe ser único globalmente mediante la especificación de OPC UA, esta propiedad se utiliza para identificar el nodo de la topología de la estación.

* **OpcNodes**, que son una matriz de nodos OPC UA (tipo `<opc_node_description>`)

`<location_definition>` tiene propiedades:

* **City** (tipo de cadena)

  Nombre de la ciudad más cercana a la ubicación

* **Country** (tipo cadena)

  País de la ubicación

* **Latitude** (tipo doble)

  Latitud de la ubicación

* **Longitude** (tipo doble)

  Longitud de la ubicación

`<performance_definition>` tiene propiedades:

* **Minimum** (tipo doble)

  El umbral más bajo que el valor puede alcanzar. Si el valor actual está por debajo de este umbral, se genera una alerta.

* **Target** (tipo doble)

  Valor de destino ideal

* **Maximum** (tipo doble)

  El umbral más alto que el valor puede alcanzar. Si el valor actual está por encima de este umbral, se genera una alerta.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define el conjunto de acciones, que puede considerarse como respuesta a una alerta mínima.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define el conjunto de acciones, que puede considerarse como respuesta a una alerta máxima.

`<alert_action`> tiene propiedades:

* **Type** (tipo cadena)

  Tipo de la acción de alerta. Se conocen los siguientes tipos:

  * **AcknowledgeAlert**: el estado de la alerta debería cambiar a confirmado.
  * **CloseAlert**: todas las alertas anteriores del mismo tipo ya no deben mostrarse en el panel.
  * **CallOpcMethod**: se debe llamar a un método OPC UA.
  * **OpenWebPage**: se debe abrir una ventana del explorador que muestre información contextual adicional.

* **Description** (tipo cadena)

  Descripción de la acción que se muestra en el panel.

* **Parameter** (tipo cadena)

  Parámetros necesarios para ejecutar la acción. El valor depende del tipo de acción.

  * **AcknowledgeAlert**: ningún parámetro requerido.
  * **CloseAlert**: ningún parámetro requerido.
  * **CallOpcMethod**: la información del nodo y los parámetros del método OPC UA para llamar en el formato "NodeId del nodo principal, NodeId del método al que se debe llamar y URI del servidor OPC UA".
  * **OpenWebPage**: la dirección URL que se mostrará en la ventana del explorador.

`<opc_node_description>` contiene información sobre los nodos OPC UA de una estación (servidor OPC UA). Los nodos que representan los nodos OPC UA no existentes, pero que se usan como almacenamiento en la lógica de cálculo de la factoría conectada, también son válidos. Tiene las siguientes propiedades:

* **NodeId** (tipo cadena)

  Dirección del nodo OPC UA del espacio de direcciones de la estación (servidor OPC UA). La sintaxis debe ser como se especifica en la especificación OPC UA de NodeId.

* **SymbolicName** (tipo cadena)

  Nombre que aparece en el panel cuando se muestra el valor de este nodo OPC UA.

* **Relevance** (matriz de tipo cadena)

  Indica para qué cálculo de OEE o KPI es relevante el valor del nodo OPC UA. Cada elemento de la matriz puede ser uno de los siguientes valores:

  * **OeeAvailability_Running**: el valor es relevante para el cálculo de disponibilidad de OEE.
  * **OeeAvailability_Fault**: el valor es relevante para el cálculo de disponibilidad de OEE.
  * **OeePerformance_Ideal**: el valor es relevante para el cálculo del rendimiento de OEE y normalmente es un valor constante.
  * **OeePerformance_Actual**: el valor es relevante para el cálculo del rendimiento de OEE.
  * **OeeQuality_Good**: el valor es relevante para el cálculo de calidad de OEE.
  * **OeeQuality_Bad**: el valor es relevante para el cálculo de calidad de OEE.
  * **Kpi1**: el valor es relevante para el cálculo de KPI1.
  * **Kpi2**: el valor es relevante para el cálculo de KPI2.

* **OpCode** (tipo cadena)

  Indica cómo se controla el valor del nodo OPC UA en consultas de Time Series Insights y cálculos de OEE/KPI. Cada consulta de Time Series Insights abarca un intervalo de tiempo específico, que es un parámetro de la consulta y proporciona un resultado. OpCode controla cómo se calcula el resultado y puede ser alguno de los siguientes valores:

  * **Diff**: diferencia entre el último y el primer valor del intervalo de tiempo.
  * **Avg**: la media de todos los valores del intervalo de tiempo.
  * **Sum**: la suma de todos los valores del intervalo de tiempo.
  * **Last**: no se utiliza actualmente.
  * **Count**: el número de valores del intervalo de tiempo.
  * **Max**: el valor máximo del intervalo de tiempo.
  * **Min**: el valor mínimo del intervalo de tiempo.
  * **Const**: el resultado es el valor especificado por la propiedad ConstValue.
  * **SubMaxMin**: la diferencia entre los valores máximo y mínimo.
  * **Timespan**: el intervalo de tiempo.

* **Units** (tipo cadena)

  Define una unidad del valor para su presentación en el panel.

* **Visible** (tipo booleano)

  Controla si el valor debe mostrarse en el panel.

* **ConstValue** (tipo doble)

  Si **OpCode** es **Const**, significa que esta propiedad es el valor del nodo.

* **Minimum** (tipo doble)

  Si el valor actual desciende por debajo de este valor, se genera una alerta mínima.

* **Maximum** (tipo doble)

  Si el valor actual aumenta por encima de este valor, se genera una alerta máxima.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define el conjunto de acciones, que puede considerarse como respuesta a una alerta mínima.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define el conjunto de acciones, que puede considerarse como respuesta a una alerta máxima.

Al nivel de estación, también verá objetos de **simulación**. Estos objetos solo se usan para configurar la simulación de factoría conectada y no deben usarse para configurar una topología real.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Uso de los datos de configuración en tiempo de ejecución

Todas las propiedades utilizadas en el archivo de configuración pueden agruparse en distintas categorías en función de cómo se usen. Dichas categorías son:

### <a name="visual-appearance"></a>Apariencia visual

Las propiedades de esta categoría definen la apariencia visual del panel de la factoría conectada. Entre los ejemplos se incluyen:

* Nombre
* Descripción
* Imagen
* Ubicación
* Unidades
* Visible

### <a name="internal-topology-tree-addressing"></a>Direccionamiento en árbol de la topología interna

La aplicación WebApp conserva un diccionario de datos interno que contiene información de todos los nodos de la topología. Las propiedades **Guid** y **OpcUri** se usan como claves para acceder a este diccionario y deben ser únicas.

### <a name="oeekpi-computation"></a>Cálculo de OEE/KPI

Las cifras de OEE/KPI para la simulación de factoría conectada están parametrizadas según lo siguiente:

* Los valores del nodo OPC UA que se incluirán en el cálculo.
* Cómo se calcula la cifra a partir de los valores de telemetría.

La solución de factoría conectada usa fórmulas OEE como se publica en [http://www.oeefoundation.org](http://www.oeefoundation.org).

Los objetos del nodo OPC UA de las estaciones permiten el etiquetado para su uso en el cálculo de OEE/KPI. La propiedad **Relevance** indica para qué cifra de OEE/KPI debe usarse el valor del nodo OPC UA. La propiedad **OpCode** define cómo se incluye el valor en el cálculo.

### <a name="alert-handling"></a>Control de alertas

La factoría conectada admite un mecanismo sencillo de generación de alertas basado en umbrales mínimo y máximo. Hay una serie de acciones predefinidas que puede configurar en respuesta a esas alertas. Las siguientes propiedades controlan este mecanismo:

* Máxima
* Mínima
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correlación con los datos de telemetría

Para determinadas operaciones, como la visualización del último valor o la creación de consultas de Time Series Insights, la aplicación WebApp necesita un esquema de direcciones para los datos de telemetría ingeridos. La telemetría enviada a la factoría conectada también debe almacenarse en estructuras de datos internas. Las dos propiedades que habilitan estas operaciones son a nivel de estación (servidor OPC UA) y a nivel de nodo OPC UA:

* **OpcUri**

  Identifica de forma exclusiva a nivel global el servidor OPC UA del que procede la telemetría. En los mensajes ingeridos, esta propiedad se envía como **ApplicationUri**.

* **NodeId**

  Identifica el valor del nodo en el servidor OPC UA. El formato de la propiedad debe ser como se especifica en la especificación OPC UA. En los mensajes ingeridos, esta propiedad se envía como **NodeId**.

Vea [¿Qué es OPC Publisher?](/previous-versions/azure/iot-accelerators/overview-opc-publisher) para obtener más información sobre cómo los datos de telemetría se ingieren en la factoría conectada mediante el publicador de OPC.

## <a name="example-how-kpi1-is-calculated"></a>Ejemplo: Cómo se calcula KPI1

La configuración del archivo `ContosoTopologyDescription.json` controla cómo se calculan las cifras de OEE/KPI. En el ejemplo siguiente se muestra cómo las propiedades de este archivo controlan el cálculo de KPI1.

En la factoría conectada, KPI1 se usa para medir el número de productos fabricados correctamente en la última hora. Cada estación (servidor OPC UA) de la simulación de factoría conectada proporciona un nodo OPC UA (`NodeId: "ns=2;i=385"`), que ofrece la telemetría para calcular este KPI.

La configuración de este nodo OPC UA es similar al siguiente fragmento de código:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Esta configuración permite realizar consultas de los valores de telemetría de este nodo mediante Time Series Insights. La consulta de Time Series Insights obtiene:

* El número de valores.
* El valor mínimo.
* El valor máximo.
* El promedio de todos los valores.
* La suma de todos los valores de todos los valores exclusivos **OpcUri** (**ApplicationUri**), pares de **NodeId** de un intervalo de tiempo determinado.

Una característica del valor del nodo **NumberOfManufactureredProducts** es que solo aumenta. Para calcular el número de productos fabricados en el intervalo de tiempo, la factoría conectada usa **OpCode** **SubMaxMin**. El cálculo recupera el valor mínimo al inicio del intervalo de tiempo y el valor máximo al final del intervalo de tiempo.

**OpCode** en la configuración configura la lógica de cálculo para calcular el resultado de la diferencia entre los valores máximo y mínimo. Después, dichos resultados se acumulan desde la base hasta el nivel raíz (global) y se muestran en el panel.

## <a name="next-steps"></a>Pasos siguientes

El siguiente paso recomendado consiste en aprender a [personalizar la solución de factoría conectada](iot-accelerators-connected-factory-customize.md).