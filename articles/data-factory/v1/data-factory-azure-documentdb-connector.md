---
title: Movimiento de datos hacia o desde Azure Cosmos DB
description: Aprenda a mover datos hacia y desde una colección de Azure Cosmos DB mediante Azure Data Factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
robots: noindex
ms.openlocfilehash: f7a304bdc1529a2e2878d00d6d25e2fead20e5ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561341"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Movimiento de datos hacia y desde Azure Cosmos DB mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-azure-documentdb-connector.md)
> * [Versión 2 (versión actual)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte el artículo sobre el [conector de Azure Cosmos DB en V2](../connector-azure-cosmos-db.md).

En este artículo se explica cómo usar la actividad de copia en Azure Data Factory para mover datos hacia y desde Azure Cosmos DB (API de SQL). Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos de cualquier almacén de datos de origen admitido hacia Azure Cosmos DB o desde Azure Cosmos DB hacia cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

> [!IMPORTANT]
> El conector de Azure Cosmos DB solo admite la API de SQL.

Para copiar datos tal cual hacia y desde archivos JSON u otra colección de Cosmos DB, consulte [Importación o exportación de documentos JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva datos hacia y desde Cosmos DB mediante diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos.

Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON. Para ver ejemplos con definiciones de JSON para entidades de Data Factory que se usan para copiar datos con Cosmos DB como origen o destino, consulte la sección [Ejemplos de JSON](#json-examples) de este artículo.

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de Cosmos DB:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado Azure Cosmos DB.

| **Propiedad** | **Descripción** | **Obligatorio** |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **DocumentDb** |Sí |
| connectionString |Especifique la información necesaria para conectarse a la base de datos de Azure Cosmos DB. |Sí |

Ejemplo:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **DocumentDbCollection** tiene las propiedades siguientes.

| **Propiedad** | **Descripción** | **Obligatorio** |
| --- | --- | --- |
| collectionName |Nombre de la colección de documentos de Cosmos DB. |Sí |

Ejemplo:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Esquema de Data Factory
En los almacenes de datos sin esquemas como Azure Cosmos DB, el servicio Data Factory deduce el esquema de una de las maneras siguientes:

1. Si especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory respeta esta como la estructura del esquema. En este caso, si una fila no contiene un valor para una columna, se le proporcionará un valor nulo.
2. Si no especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory deduce el esquema utilizando la primera fila en los datos. En este caso, si la primera fila no contiene el esquema completo, algunas columnas se pueden perder en el resultado de la operación de copia.

Por lo tanto, para los orígenes de datos sin esquemas, lo mejor es especificar la estructura de los datos mediante la propiedad **structure** .

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [!NOTE]
> La actividad de copia toma solo una entrada y genera una única salida.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **DocumentDbCollectionSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| **Propiedad** | **Descripción** | **Valores permitidos** | **Obligatorio** |
| --- | --- | --- | --- |
| Query |Especifique la consulta para leer los datos. |Cadena de consulta admitida por Azure Cosmos DB. <br/><br/>Ejemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Si no se especifica, la instrucción SQL que se ejecuta: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Carácter especial para indicar que el documento está anidado |Cualquier carácter. <br/><br/>Azure Cosmos DB es un almacén NoSQL para documentos JSON, en el que se permiten estructuras anidadas. Azure Data Factory permite al usuario indicar la jerarquía a través de nestingSeparator que es "." en los ejemplos anteriores. Con el separador, la actividad de copia generará el objeto "Name" con tres elementos secundarios First, Middle y Last, según "Name.First", "Name.Middle" y "Name.Last" en la definición de tabla. |No |

**DocumentDbCollectionSink** admite las siguientes propiedades:

| **Propiedad** | **Descripción** | **Valores permitidos** | **Obligatorio** |
| --- | --- | --- | --- |
| nestingSeparator |Un carácter especial en el nombre de columna de origen que indica que el documento anidado es necesario. <br/><br/>En el ejemplo anterior: `Name.First` en la tabla de salida produce la siguiente estructura JSON en el documento de Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Carácter que se usa para separar los niveles de anidamiento.<br/><br/>El valor predeterminado es `.` (punto). |Carácter que se usa para separar los niveles de anidamiento. <br/><br/>El valor predeterminado es `.` (punto). |
| writeBatchSize |Número de solicitudes paralelas al servicio de Azure Cosmos DB para crear documentos.<br/><br/>Puede ajustar el rendimiento cuando se copian datos hacia o desde Cosmos DB mediante esta propiedad. Puede esperar un rendimiento mejor al aumentar writeBatchSize porque se envían más solicitudes paralelas a Cosmos DB. Sin embargo, deberá evitar una limitación de solicitudes que puede generar el mensaje de error: "La tasa de solicitudes es grande".<br/><br/>La limitación de solicitudes se decide mediante una serie de factores, incluidos tamaño de los documentos, número de términos en los documentos, directiva de indexación de colección de destino, etc. Para las operaciones de copia, puede usar una colección mejor (por ejemplo, S3) para obtener el máximo rendimiento disponible (2.500 unidades de solicitudes por segundo). |Entero |No (valor predeterminado: 5) |
| writeBatchTimeout |Tiempo de espera para que la operación se complete antes de que se agote el tiempo de espera. |timespan<br/><br/> Ejemplo: "00:30:00" (30 minutos). |No |

## <a name="importexport-json-documents"></a>Importación o exportación de documentos JSON
Con este conector de Cosmos DB, le resultará muy sencillo

* Importar documentos JSON desde varios orígenes en Cosmos DB, incluido Azure Blob, Azure Data Lake, el sistema de archivos local u otros almacenes basados en archivos compatibles con Azure Data Factory.
* Exportar documentos JSON de la colección de Cosmos DB a varios almacenes basados en archivos.
* Migrar datos entre dos colecciones de Cosmos DB como están.

Para lograr dicha copia independiente del esquema,
* Cuando use el Asistente para copia, active la opción **"Export as-is to JSON files or Cosmos DB collection"** (Exportar tal cual a archivos JSON o colección de Cosmos DB).
* Al usar la edición de JSON, no especifique la sección "structure" en los conjuntos de datos de Cosmos DB ni la propiedad "nestingSeparator" en el receptor u origen de Cosmos DB en la actividad de copia. Para importar desde archivos JSON o exportar a ellos, en el conjunto de datos de almacén de archivos, especifique el tipo de formato como "JsonFormat", configure "filePattern" y omita la configuración de formato restante; vea la sección [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) para obtener detalles.

## <a name="json-examples"></a>Ejemplos de JSON
En los siguientes ejemplos, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos hacia y desde Azure Cosmos DB y Azure Blob Storage. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Ejemplo: copia de datos de Azure Cosmos DB a Azure Blob
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [DocumentDb](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [DocumentDbCollection](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [DocumentDbCollectionSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos de Azure Cosmos DB en Azure Blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Servicio vinculado de Azure Blob Storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de datos de entrada de DocumentDB de Azure:**

En el ejemplo se supone que tiene una colección llamada **Person** en una base de datos de Azure Cosmos DB.

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Azure Data Factory de que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Conjunto de datos de salida de blob de Azure:**

Los datos se copian a un blob nuevo cada hora con la ruta de acceso para el blob que refleja la fecha y hora específicas con granularidad de hora.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Documentos JSON de ejemplo en la colección Person de una base de datos de Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB admite la consulta de documentos mediante una sintaxis similar a la de SQL en documentos jerárquicos JSON.

Ejemplo:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

La siguiente canalización copia los datos de la colección Person de la base de datos de Azure Cosmos DB en un blob de Azure. Como parte de la actividad de copia, se han especificado los conjuntos de datos de entrada y de salida.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Ejemplo: copia de datos de Azure Blob a Azure Cosmos DB
El ejemplo siguiente muestra:

1. Un servicio vinculado del tipo DocumentDb.
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida del tipo DocumentDbCollection.
5. Una [canalización](data-factory-create-pipelines.md) con actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) y DocumentDbCollectionSink.

En el ejemplo se copian datos desde Azure Blob hasta Azure Cosmos DB. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de Azure Blob Storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Servicio vinculado Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Conjunto de datos de entrada de blob de Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Conjunto de datos de salida de Azure Cosmos DB:**

El ejemplo copia los datos a una colección denominada "Person".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
La siguiente canalización copia datos de Azure Blob en la colección Person de Cosmos DB. Como parte de la actividad de copia, se han especificado los conjuntos de datos de entrada y de salida.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Si la entrada de blob de ejemplo es como:

```
1,John,,Doe
```
La salida JSON en Cosmos DB será como:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB es un almacén NoSQL para documentos JSON, en el que se permiten estructuras anidadas. Azure Data Factory permite al usuario indicar la jerarquía a través de **nestingSeparator** que es "." en este ejemplo. Con el separador, la actividad de copia generará el objeto "Name" con tres elementos secundarios First, Middle y Last, según "Name.First", "Name.Middle" y "Name.Last" en la definición de tabla.

## <a name="appendix"></a>Apéndice
1. **Pregunta:** ¿Admite la actividad de copia la actualización de los registros existentes?

    **Respuesta:** No.
2. **Pregunta:** ¿Cómo hace frente el reintento de una copia en Azure Cosmos DB a los registros ya copiados?

    **Respuesta:** Si los registros tienen un campo "Id" y la operación de copia intenta insertar un registro con el mismo identificador, la operación de copia genera un error.
3. **Pregunta:** ¿Admite Data Factory el [intervalo o las particiones de datos basadas en hash](../../cosmos-db/partitioning-overview.md)?

    **Respuesta:** No.
4. **Pregunta:** ¿Puedo especificar más de una colección de Azure Cosmos DB para una tabla?

    **Respuesta:** No. Solo se puede especificar una colección cada vez.

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.