---
title: Inserción de datos en un índice de Azure Cognitive Search mediante Data Factory
description: Obtenga información sobre cómo insertar datos en un índice de Azure Cognitive Search mediante el uso de Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ab7ed3435cd49e6671e5a05623db964ce551e86
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676853"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Inserción de datos en un índice de Azure Cognitive Search mediante el uso de Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-azure-search-connector.md)
> * [Versión 2 (versión actual)](../connector-azure-search.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte el artículo sobre el [conector de Azure Cognitive Search en V2](../connector-azure-search.md).

Este artículo describe cómo usar la actividad de copia para insertar datos de un almacén de datos de origen admitido en un índice de Azure Cognitive Search. Los almacenes de datos de origen compatibles se muestran en la columna Se admite como origen de la tabla de [almacenes de datos y receptores que se admiten](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Este artículo se basa en el artículo sobre [movimiento de datos y actividad de copia](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones de almacén de datos admitidas.

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
Para que el servicio Data Factory pueda conectarse con un almacén de datos local, instale la puerta de enlace de administración de datos en el entorno local. No podrá instalar la puerta de enlace en la misma máquina que hospeda el almacén de datos de origen ni en una independiente para evitar la competencia por los recursos con el almacén de datos.

La puerta de enlace de administración de datos conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que inserte datos de un almacén de datos de origen en un índice de búsqueda mediante el uso de distintas herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos.

Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver un ejemplo con definiciones de JSON para entidades de Data Factory que se emplean para copiar datos al índice de búsqueda, consulte la sección [Ejemplo JSON: Copia de datos de SQL Server a un índice de Azure Cognitive Search](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) de este artículo.

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de índices de búsqueda:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

En la tabla siguiente se proporcionan descripciones de los elementos JSON específicos del servicio vinculado de Azure Cognitive Search.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type debe establecerse en: **AzureSearch**. | Sí |
| url | URL del servicio de búsqueda. | Sí |
| key | Clave de administración del servicio de búsqueda. | Sí |

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo sobre [creación de conjuntos de datos](data-factory-create-datasets.md) . Las secciones como estructura, disponibilidad y directiva de un JSON de conjunto de datos son similares para todos los tipos de conjunto de datos. La sección **typeProperties** es diferente para cada tipo de conjunto de datos. La sección typeProperties de un conjunto de datos del tipo **AzureSearchIndex** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type debe establecerse en **AzureSearchIndex**.| Sí |
| indexName | Nombre del índice de búsqueda. Data Factory no crea el índice. El índice debe existir en Azure Cognitive Search. | Sí |


## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [creación de canalizaciones](data-factory-create-pipelines.md) . Las propiedades (como el nombre, la descripción, las tablas de entrada y salida, y las distintas directivas) están disponibles en todos los tipos de actividades. Por otra parte, las propiedades disponibles en la sección typeProperties varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En la actividad de copia, si el receptor es de tipo **AzureSearchIndexSink**, estarán disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica si, cuando ya haya un documento en el índice, se realizará una operación de combinación o de reemplazo. Consulte la propiedad [WriteBehavior](#writebehavior-property).| Combinar (predeterminado)<br/>Cargar| No |
| WriteBatchSize | Carga datos en el índice de búsqueda cuando el tamaño del búfer alcanza el valor de writeBatchSize. Consulte la propiedad [WriteBatchSize](#writebatchsize-property) para obtener más información. | De 1 a 1000. El valor predeterminado es 1000. | No |

### <a name="writebehavior-property"></a>Propiedad WriteBehavior
AzureSearchSink realiza una operación upsert al escribir los datos. Es decir, al crear un documento, si la clave de este ya se encuentra en el índice de búsqueda, Azure Cognitive Search actualiza el documento existente en lugar de generar una excepción de conflicto.

AzureSearchSink proporciona los siguientes dos comportamientos de upsert (mediante el SDK de Azure Search):

- **Combinar**: combina todas las columnas del nuevo documento con el existente. En las columnas con valor null del nuevo documento, se conserva el valor del existente.
- **Cargar**: el nuevo documento reemplaza al existente. En cuanto a las columnas no especificadas en el nuevo documento, el valor se establece en null con independencia de que haya un valor distinto de null en el documento existente.

El comportamiento predeterminado es **Combinar**.

### <a name="writebatchsize-property"></a>Propiedad WriteBatchSize
El servicio Azure Cognitive Search permite la creación de documentos como lotes. Un lote puede contener entre 1 y 1000 acciones. Una acción controla un documento para llevar a cabo la operación de combinación o de carga.

### <a name="data-type-support"></a>Compatibilidad con tipos de datos
En la tabla siguiente se especifica si se admite o no un tipo de datos de Azure Cognitive Search.

| Tipo de datos de Azure Cognitive Search | Compatible con el receptor de Azure Cognitive Search |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| Matriz de cadenas | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>Ejemplo JSON: Copia de datos de SQL Server a un índice de Azure Cognitive Search

El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureSearch](#linked-service-properties).
2. Un servicio vinculado de tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSearchIndex](#dataset-properties).
4. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) y [AzureSearchIndexSink](#copy-activity-properties).

En el ejemplo se copian datos de serie temporal de una base de datos de SQL Server a un índice de búsqueda cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de ellos.

En primer lugar, configure la puerta de enlace de administración de datos en la máquina local. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de Azure Cognitive Search:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Servicio vinculado de SQL Server**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Conjunto de datos de entrada de SQL Server**

El ejemplo supone que ha creado una tabla "MyTable" en SQL Server y que contiene una columna denominada "timestampcolumn" para los datos de serie temporal. Puede consultar en varias tablas dentro de la misma base de datos mediante un único conjunto de datos, pero se debe utilizar una única tabla para typeProperty tableName del conjunto de datos.

Si se establece "external": "true", se informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y no lo produce ninguna actividad de dicha factoría.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de datos de salida Azure Cognitive Search:**

En el ejemplo se copian los datos a un índice de Azure Cognitive Search denominado "**products**". Data Factory no crea el índice. Para probar el ejemplo, cree un índice con este nombre. Cree el índice de búsqueda con el mismo número de columnas que el conjunto de datos de entrada. Las nuevas entradas se agregan al índice de búsqueda cada hora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Actividad de copia en una canalización con origen SQL y receptor de índice de Azure Cognitive Search:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición JSON de la canalización, el tipo **source** se establece en **SqlSource**, y el tipo **sink**, en **AzureSearchIndexSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Si va a copiar datos desde un almacén de datos en la nube a Azure Cognitive Search, la propiedad `executionLocation` es obligatoria. El siguiente fragmento de código JSON muestra el cambio necesario en la actividad de copia `typeProperties` como ejemplo. Comprobar [copiar datos entre almacenes de datos en la nube](data-factory-data-movement-activities.md#global) para obtener más detalles y los valores admitidos.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copia desde un origen en la nube
Si va a copiar datos desde un almacén de datos en la nube a Azure Cognitive Search, la propiedad `executionLocation` es obligatoria. El siguiente fragmento de código JSON muestra el cambio necesario en la actividad de copia `typeProperties` como ejemplo. Comprobar [copiar datos entre almacenes de datos en la nube](data-factory-data-movement-activities.md#global) para obtener más detalles y los valores admitidos.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

También puede asignar columnas del conjunto de datos de origen a las del conjunto de datos receptor en la definición de actividad de copia. Para más información, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos de Azure Data Factory).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

* [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia.
