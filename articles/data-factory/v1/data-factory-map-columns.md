---
title: Asignación de columnas de conjuntos de datos en Azure Data Factory
description: Obtenga información acerca de cómo asignar columnas de origen a columnas de destino.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: ac8da7dcfcc95a57606b9590e897a66bfab1675e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602961"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Asignación de columnas de conjunto de datos de origen a columnas del conjunto de datos de destino
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. 

La asignación de columnas puede usarse para especificar cómo se asignan las columnas especificadas en "structure" de la tabla de origen a las columnas especificadas en "structure" de la tabla receptora. La propiedad **columnMapping** está disponible en la sección **typeProperties** de la actividad de copia.

La asignación de columnas admite los siguientes escenarios:

* Todas las columnas del conjunto de datos "structure" están asignadas a todas las columnas del conjunto de datos receptor "structure".
* Un subconjunto de columnas del conjunto de datos de origen "structure" está asignado a todas las columnas del conjunto de datos receptor "structure".

Las siguientes son las condiciones de error que tienen como resultado una excepción:

* O bien menos columnas o más columnas en "structure" de la tabla receptora de las que se especifican en la asignación.
* Asignación duplicada.
* El resultado de la consulta SQL no tiene un nombre de columna que esté especificado en la asignación.

> [!NOTE]
> Los ejemplos siguientes son para Azure SQL y Azure Blob, pero resultan aplicables a cualquier almacén de datos que admita conjuntos de datos rectangulares. Ajuste el conjunto de datos y las definiciones de servicios vinculados en los ejemplos para que apunten a datos del origen de datos pertinente.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Ejemplo 1: asignación de columnas de SQL Server a un blob de Azure
En este ejemplo la tabla de entrada tiene una estructura y apunta a una tabla SQL en Azure SQL Database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

En este ejemplo, la tabla de salida tiene una estructura y apunta a un blob en un almacenamiento de blobs de Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

El siguiente fragmento JSON define una actividad de copia en una canalización. Las columnas del origen se asignan a columnas del receptor (**columnMappings**) usando la propiedad **Translator**.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Flujo de asignación de columnas:**

:::image type="content" source="./media/data-factory-map-columns/column-mapping-flow.png" alt-text="Flujo de asignación de columnas":::

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Ejemplo 2: asignación de columnas con una consulta SQL de SQL de Azure a un blob de Azure
En este ejemplo, se usa una consulta SQL para extraer datos de SQL de Azure en lugar de especificar simplemente el nombre de tabla y los nombres de columna en la sección "structure". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
En este caso, los resultados de consulta se asignan primero a las columnas especificadas en "structure" del origen. A continuación, las columnas de "structure" del origen se asignan a las columnas "structure" del receptor con las reglas especificadas en columnMappings.  Supongamos que la consulta devuelve cinco columnas, dos columnas más que las especificadas en el elemento "structure" de origen.

**Flujo de asignación de columnas**

:::image type="content" source="./media/data-factory-map-columns/column-mapping-flow-2.png" alt-text="Flujo de asignación de columnas 2":::

## <a name="next-steps"></a>Pasos siguientes
Vea el artículo para acceder a un tutorial sobre el uso de la actividad de copia: 

- [Copia de datos de Blob Storage en SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
