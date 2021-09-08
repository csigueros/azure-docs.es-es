---
title: Consultas con Azure Data Explorer
titleSuffix: Azure Digital Twins
description: Descripción del complemento de consulta de Azure Digital Twins para Azure Data Explorer
author: baanders
ms.author: baanders
ms.date: 5/19/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8b0c6558be0022d8cb72ede5b665023f2b3f138d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438716"
---
# <a name="azure-digital-twins-query-plugin-for-azure-data-explorer"></a>Complemento de consulta de Azure Digital Twins para Azure Data Explorer

El complemento de Azure Digital Twins para [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) permite ejecutar consultas de Azure Data Explorer que acceden a datos en grafos de Azure Digital Twins y bases de datos de series temporales de Azure Data Explorer y los combinan. Use el complemento para contextualizar datos de series temporales dispares mediante el razonamiento entre los gemelos digitales y sus relaciones para obtener información sobre el comportamiento de los entornos modelados.

Por ejemplo, con este complemento, puede escribir una consulta de Kusto que…
1. seleccione gemelos digitales de interés mediante el complemento de consulta de Azure Digital Twins,
2. combine esos gemelos con la serie temporal correspondiente de Azure Data Explorer y, luego, 
3. realice análisis avanzados de series temporales en esos gemelos.  

La combinación de los datos de un grafo de un gemelo de Azure Digital Twins con datos de series temporales de Azure Data Explorer puede ayudarle a comprender el comportamiento operativo de varios componentes de la solución. 

## <a name="using-the-plugin"></a>Uso del complemento

Puede invocar el complemento en una consulta de Kusto con el siguiente comando. Hay dos marcadores de posición, `<Azure-Digital-Twins-endpoint>` y `<Azure-Digital-Twins-query>`, que son cadenas que representan el punto de conexión de la instancia de Azure Digital Twins y la consulta de Azure Digital Twins, respectivamente. 

```kusto
evaluate azure_digital_twins_query_request(<Azure-Digital-Twins-endpoint>, <Azure-Digital-Twins-query>) 
```

El complemento funciona mediante una llamada a la [API de consulta de Azure Digital Twins](/rest/api/digital-twins/dataplane/query) y la [estructura de lenguaje de la consulta](concepts-query-language.md) es la misma que cuando se usa la API, con dos excepciones: 
* No se admite el carácter comodín `*` en la cláusula `SELECT`. En su lugar, las consultas de Azure Digital Twins que se ejecutan mediante el complemento deben usar alias en la cláusula `SELECT`.

    Por ejemplo, considere la siguiente consulta de Azure Digital Twins que se ejecuta mediante la API:
    
    ```SQL
    SELECT * FROM DIGITALTWINS
    ```
    
    Para ejecutar esa consulta cuando se usa el complemento, debe reescribirse de esta forma:
    
    ```SQL
    SELECT T FROM DIGITALTWINS T
    ```
* Los nombres de columna devueltos por el complemento no pueden empezar por `$`. El uso de alias en la cláusula `SELECT` también ayudará a evitar este escenario.

    Por ejemplo, considere la siguiente consulta de Azure Digital Twins que se ejecuta mediante la API:
    
    ```SQL
    SELECT T.$dtId, T.Temperature FROM DIGITALTWINS T
    ```
    
    Para ejecutar esa consulta cuando se usa el complemento, debe reescribirse de esta forma:
    
    ```SQL
    SELECT T.$dtId as tid, T.Temperature FROM DIGITALTWINS T
    ```


>[!IMPORTANT]
>Al usuario del complemento se le debe conceder el rol **Lector de datos de Azure Digital Twins** o **Propietario de datos de Azure Digital Twins**, ya que el token de Azure AD del usuario se usa para autenticarse. Encontrará información sobre cómo asignar este rol en [Seguridad para las soluciones de Azure Digital Twins](concepts-security.md#authorization-azure-roles-for-azure-digital-twins).

Para más información sobre el uso del complemento, consulte la [documentación de Kusto para el complemento azure_digital_twins_query_request](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin).

Para ver consultas de ejemplo y completar un tutorial con datos de ejemplo, consulte [Complemento de consulta de Azure Digital Twins para Azure Data Explorer: Consultas y tutorial de ejemplo](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries) en GitHub.

## <a name="using-azure-data-explorer-iot-data-with-azure-digital-twins"></a>Uso de datos de IoT de Azure Data Explorer con Azure Digital Twins

Hay varias maneras de ingerir datos de IoT en Azure Data Explorer. Estas son dos que puede emplear al usar Azure Data Explorer con Azure Digital Twins:
* Historiar los valores de propiedad del gemelo digital en Azure Data Explorer con una función de Azure que controle los eventos de cambio y escriba los datos del gemelo digital en Azure Data Explorer, de forma parecida al proceso que se usó en [Integración con Azure Time Series Insights](how-to-integrate-time-series-insights.md). Esta manera es adecuada para los clientes que usan datos de telemetría para que sus gemelos digitales cobren vida.
* [Ingerir los datos de IoT directamente en el clúster de Azure Data Explorer desde IoT Hub](/azure/data-explorer/ingest-data-iot-hub) o desde otros orígenes. Posteriormente, se usará el grafo de Azure Digital Twins para contextualizar los datos de la serie temporal mediante consultas conjuntas de Azure Digital Twins y Azure Data Explorer. Esta manera puede ser adecuada para cargas de trabajo de ingesta directa. 

### <a name="mapping-data-across-azure-data-explorer-and-azure-digital-twins"></a>Asignación de datos entre Azure Data Explorer y Azure Digital Twins

Si va a ingerir datos de serie temporal directamente en Azure Data Explorer, es probable que tenga que convertir estos datos de serie temporal sin procesar en un esquema adecuado para consultas conjuntas de Azure Digital Twins y Azure Data Explorer.

Una [directiva de actualización](/azure/data-explorer/kusto/management/updatepolicy) en Azure Data Explorer permite transformar y anexar datos automáticamente a una tabla de destino cada vez que se inserten nuevos datos en una tabla de origen. 

Puede usar una directiva de actualización para enriquecer los datos de serie temporal sin procesar con el **identificador del gemelo** correspondiente de Azure Digital Twins y conservarlo en una tabla de destino. Con el identificador del gemelo, la tabla de destino se puede combinar con los gemelos digitales seleccionados por el complemento de Azure Digital Twins. 

Por ejemplo, supongamos que ha creado la tabla siguiente para que contenga los datos de serie temporal sin procesar que se transfieren a la instancia de Azure Data Explorer. 

```kusto
.create-merge table rawData (Timestamp:datetime, someId:string, Value:string, ValueType:string)  
```

Puede crear una tabla de asignación para relacionar los identificadores de series temporales con los identificadores de los gemelos y otros campos opcionales. 

```kusto
.create-merge table mappingTable (someId:string, twinId:string, otherMetadata:string) 
```

A continuación, cree una tabla de destino que contenga los datos de series temporales enriquecidos. 

```kusto
.create-merge table timeseriesSilver (twinId:string, Timestamp:datetime, someId:string, otherMetadata:string, ValueNumeric:real, ValueString:string)  
```

A continuación, cree una función `Update_rawData` para enriquecer los datos sin procesar combinándolos con la tabla de asignación. Esto agregará el identificador del gemelo a la tabla de destino resultante. 

```kusto
.create-or-alter function with (folder = "Update", skipvalidation = "true") Update_rawData() { 
rawData 
| join kind=leftouter mappingTable on someId 
| project 
    Timestamp, ValueNumeric = toreal(Value), ValueString = Value, ... 
} 
```

Por último, cree una directiva de actualización para llamar a la función y actualizar la tabla de destino. 

```kusto
.alter table timeseriesSilver policy update 
@'[{"IsEnabled": true, "Source": "rawData", "Query": "Update_rawData()", "IsTransactional": false, "PropagateIngestionProperties": false}]' 
```

Una vez creada la tabla de destino, puede usar el complemento de Azure Digital Twins para seleccionar los gemelos de interés y, a continuación, combinarlos con los datos de series temporales de la tabla de destino. 

### <a name="example-schema"></a>Esquema de ejemplo

Este es un ejemplo de un esquema que podría usarse para representar datos compartidos.

| timestamp | twinId | modelId | name | value | relationshipTarget | relationshipID |
| --- | --- | --- | --- | --- | --- | --- |
| 01-02-2021 17:24 | ConfRoomTempSensor | dtmi:com:example:TemperatureSensor;1 | temperatura | 301,0 |  |  |

Las propiedades de los gemelos digitales se almacenan como pares clave-valor (`name, value`). `name` y `value` se almacenan como tipos de datos dinámicos. 

El esquema también admite el almacenamiento de propiedades de las relaciones, según los campos `relationshipTarget` y `relationshipID`. El esquema clave-valor evita la necesidad de crear una columna para cada propiedad del gemelo.

### <a name="representing-properties-with-multiple-fields"></a>Representación de propiedades con varios campos 

Es posible que quiera almacenar una propiedad en el esquema con varios campos. Estas propiedades se representan mediante el almacenamiento de un objeto JSON como `value` en el esquema.

Por ejemplo, si desea representar una propiedad con tres campos para roll, pitch y yaw, el objeto de valor tendría este aspecto: `{"roll": 20, "pitch": 15, "yaw": 45}`.

## <a name="next-steps"></a>Pasos siguientes

* Vea la documentación del complemento para el lenguaje de consulta Kusto en Azure Data Explorer: [complemento azure_digital_twins_query_request](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin).

* Vea consultas de ejemplo mediante el complemento, incluido un tutorial que ejecuta las consultas en un escenario de ejemplo: [Complemento de consulta de Azure Digital Twins para Azure Data Explorer: Consultas y tutorial de ejemplo](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries). 

* Obtenga información sobre otra estrategia para analizar datos históricos en Azure Digital Twins: [Integración con Azure Time Series Insights](how-to-integrate-time-series-insights.md).
