---
title: Movimiento de datos desde DB2 mediante Azure Data Factory
description: Obtenga información sobre cómo mover los datos desde una base de datos DB2 local mediante la actividad de copia de Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4538939b4b991e181d3b08c7834b201269a7f651
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661210"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Movimiento de datos de DB2 mediante la actividad de copia de Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-onprem-db2-connector.md)
> * [Versión 2 (versión actual)](../connector-db2.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte el artículo sobre el [conector DB2 en V2](../connector-db2.md).


En este artículo se describe cómo se puede usar la actividad de copia en Azure Data Factory para copiar datos desde una base de datos DB2 local a un almacén de datos. Puede copiar los datos a cualquier almacén que aparezca como un receptor compatible en el artículo sobre [actividades de movimiento de datos de Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Este tema se basa en el artículo sobre Data Factory, que presenta información general sobre el movimiento de datos mediante la actividad de copia y enumera las combinaciones de almacenes de datos compatibles. 

Data Factory actualmente solo admite mover datos desde una base de datos DB2 a un [almacén de datos de receptor compatible](data-factory-data-movement-activities.md#supported-data-stores-and-formats). No se admite el movimiento de datos desde otros almacenes de datos a una base de datos DB2.

## <a name="prerequisites"></a>Prerrequisitos
Data Factory admite la conexión a una base de datos DB2 local mediante la [puerta de enlace de administración de datos](data-factory-data-management-gateway.md). Para instrucciones paso a paso sobre cómo configurar la canalización de datos de la puerta de enlace para mover los datos, consulte el artículo [Movimiento de datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

Se requiere una puerta de enlace incluso si DB2 está hospedada en una máquina virtual de IaaS de Azure. Puede instalarla en la misma máquina virtual de IaaS en la que está el almacén de datos. Si la puerta de enlace se puede conectar a la base de datos, es posible instalar la puerta de enlace en otra máquina virtual.

La puerta de enlace de administración de datos proporciona un controlador de DB2 integrado, por lo que no es necesario instalar manualmente un controlador para copiar datos desde DB2.

> [!NOTE]
> Para sugerencias sobre cómo solucionar problemas con la conexión y la puerta de enlace, consulte el artículo [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).


## <a name="supported-versions"></a>Versiones compatibles
Este conector de DB2 de Data Factory admite las plataformas y versiones de IBM DB2 siguientes con las versiones 9, 10 y 11 de SQL Access Manager (SQLAM) de la arquitectura distribuida de bases de datos relacionales (DRDA):

* IBM DB2 para z/OS versión 11.1
* IBM DB2 para z/OS versión 10.1
* IBM DB2 para i (AS400) versión 7.2
* IBM DB2 para i (AS400) versión 7.1
* IBM DB2 para Linux, UNIX y (LUW) versión 11
* IBM DB2 para LUW versión 10.5
* IBM DB2 para LUW versión 10.1

> [!TIP]
> Si recibe el mensaje de error "No se encontró el paquete correspondiente a una solicitud de ejecución de la instrucción SQL. SQLSTATE=51002 SQLCODE=-805", es porque no se creó un paquete necesario para el usuario normal en el SO. Para resolver el problema, siga estas instrucciones según el tipo de servidor de DB2:
> - DB2 para i (AS400): permita que un usuario avanzado cree la colección del usuario normal antes de ejecutar la actividad de copia. Para crear la colección, use el comando: `create collection <username>`
> - DB2 para z/OS o LUW: use una cuenta con privilegios elevados (usuario avanzado o administrador con entidades de paquete y permisos BIND, BINDADD, GRANT EXECUTE TO PUBLIC) para ejecutar una vez la copia. El paquete necesario se creará de forma automáticamente durante la copia. A continuación, puede cambiar al usuario normal para las ejecuciones de copia posteriores.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia para mover datos desde un almacén de datos DB2 local mediante el uso de distintas herramientas o API: 

- La manera más fácil de crear una canalización es usar el Asistente para copia de Azure Data Factory. Para un tutorial rápido sobre cómo crear una canalización con el Asistente para copia, consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md). 
- También puede usar herramientas para crear una canalización, como Visual Studio, Azure PowerShell, una plantilla de Azure Resource Manager, la API de .NET y API REST. Para instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia, consulte el [tutorial de la actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree servicios vinculados para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree conjuntos de datos con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una canalización con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente para copia, se crean automáticamente definiciones de JSON para las entidades de canalizaciones, los conjuntos de datos y los servicios vinculados de Data Factory. Al usar herramientas o API (excepto la API de .NET), se definen las entidades de Data Factory con el formato JSON. El ejemplo de JSON: Copiar datos de DB2 a Azure Blob Storage muestra las definiciones de JSON para las entidades de Data Factory que se usan para copiar datos de un almacén de datos DB2 local.

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de un almacén de datos de DB2.

## <a name="db2-linked-service-properties"></a>Propiedades del servicio vinculado de DB2
En la tabla siguiente se enumeran las propiedades JSON que son específicas de un servicio vinculado de DB2.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| **type** |Esta propiedad se debe establecer en **OnPremisesDB2**. |Sí |
| **servidor** |Nombre del servidor DB2. |Sí |
| **database** |Nombre de la base de datos DB2. |Sí |
| **schema** |Nombre del esquema de la base de datos DB2. Esta propiedad distingue mayúsculas de minúsculas. |No |
| **authenticationType** |Tipo de autenticación que se usa para conectarse a la base de datos DB2. Los valores posibles son: Anonymous, Basic y Windows. |Sí |
| **username** |Nombre de la cuenta de usuario si se usa autenticación Basic o Windows. |No |
| **password** |Contraseña para la cuenta de usuario. |No |
| **gatewayName** |Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la base de datos DB2 local. |Sí |

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [creación de conjuntos de datos](data-factory-create-datasets.md) . Las secciones como **structure**, **availability** y **policy** del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (Azure SQL, Azure Blob Storage, Azure Table Storage, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **RelationalTable**, que incluye el conjunto de datos de DB2, tiene la propiedad siguiente:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| **tableName** |Nombre de la tabla en la instancia de base de datos DB2 a la que hace referencia el servicio vinculado. Esta propiedad distingue mayúsculas de minúsculas. |No (si se especifica la propiedad **query** de una actividad de copia de tipo **RelationalSource**) |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para una lista de las secciones y propiedades disponibles para definir actividades de copia, consulte el artículo sobre [creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades de la actividad de copia, como **nombre**, **descripción**, tabla de **entradas**, tabla de **salidas** y **directiva**, están disponibles para todos los tipos de actividades. Las propiedades que están disponibles en la sección **typeProperties** de la actividad varían según cada tipo de actividad. Para la actividad de copia, las propiedades varían en función de los tipos de orígenes y receptores de datos.

En el caso de la actividad de copia, si el origen es de tipo **RelationalSource** (que incluye DB2), las propiedades siguientes están disponibles en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| **consulta** |Use la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: `"query": "select * from "MySchema"."MyTable""` |No (si se especifica la propiedad **tableName** de un conjunto de datos) |

> [!NOTE]
> Los nombres de esquema y tabla distinguen mayúsculas de minúsculas. En la instrucción de consulta, escriba los nombres de propiedades entre "" (comillas dobles).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Ejemplo de JSON: Copiar datos de DB2 a Azure Blob Storage
Este ejemplo proporciona definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En el ejemplo se muestra cómo copiar datos desde una base de datos DB2 a Blob Storage. Sin embargo, es posible copiar los datos a [cualquier tipo de receptor de almacén de datos compatible](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia de Azure Data Factory.

El ejemplo consta de las siguientes entidades de Data Factory:

- Un servicio vinculado DB2 de tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md).
- Un servicio vinculado de Azure Blob Storage de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
- Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties).
- Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
- Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa las propiedades [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos cada hora de un resultado de consulta de una base de datos DB2 en un blob de Azure. Las propiedades JSON que se usan en el ejemplo se describen en las secciones que aparecen después de las definiciones de entidad.

Como primer paso, instale y configure una puerta de enlace de datos. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Servicio vinculado de almacenamiento de blobs de Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Conjunto de datos de entrada de DB2**

En el ejemplo se supone que creó una tabla en DB2 llamada "MyTable" que tiene una columna etiquetada "timestamp" para los datos de serie temporal.

La propiedad **external** está establecida en "true." Este valor informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y no la produce ninguna actividad de dicha factoría. Tenga en cuenta que la propiedad **type** se establece en **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
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

**Conjunto de datos de salida de blob de Azure**

Para escribir los datos en un blob nuevo cada hora, la propiedad **frequency** se establece en "Hora" y la propiedad **interval**, en 1. La propiedad **folderPath** del blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Canalización de la actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar conjuntos de datos de entrada y de salida especificados y que está programada para ejecutarse cada hora. En la definición JSON de la canalización, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la tabla "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Asignación de tipos para DB2
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas del tipo de origen al tipo de receptor con el enfoque de dos pasos siguiente:

1. Conversión de un tipo de origen nativo a un tipo .NET
2. Conversión de un tipo .NET a un tipo de receptor nativo

Las asignaciones siguientes se usan cuando la actividad de copia convierte los datos de un tipo DB2 a un tipo .NET:

| Tipo de base de datos DB2 | Tipo de .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Entero |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Entero |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para información sobre cómo asignar columnas en el conjunto de datos de origen a columnas en el conjunto de datos de receptor, consulte [Asignación de columnas de conjunto de datos en Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Lecturas repetibles desde orígenes relacionales
Cuando se copian datos desde un almacén de datos relacionales, se debe tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la propiedad **policy** de reintentos para un conjunto de datos a fin de que se vuelva a ejecutar un segmento cuando se produce un error. Asegúrese de que se lean los mismos datos, sin importar cuántas veces se vuelve a ejecutar el segmento e independientemente de cómo se haga. Para más información, consulte [Lecturas repetibles desde orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Obtenga información sobre los factores clave que afectan el rendimiento de la actividad de copia y las formas de optimizar el rendimiento en la [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md).
