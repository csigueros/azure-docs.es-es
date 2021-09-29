---
title: Movimiento de datos de SAP Business Warehouse mediante Azure Data Factory
description: Obtenga información acerca de cómo mover los datos de SAP Business Warehouse mediante Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: c4d1b2eb431008f62eca5f048b325d1d08aae14d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657602"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Movimiento de datos de SAP Business Warehouse mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-sap-business-warehouse-connector.md)
> * [Versión 2 (versión actual)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [Conector SAP Business Warehouse en V2](../connector-sap-business-warehouse.md).


En este artículo, se explica el uso de la actividad de copia en Azure Data Factory para mover datos desde un almacén de datos de SAP Business Warehouse (BW) local. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos de un almacén de datos de SAP Business Warehouse local a cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Por el momento, Data Factory solo admite el movimiento de datos de un almacén de datos de SAP Business Warehouse a otros almacenes de datos, pero no viceversa. 

## <a name="supported-versions-and-installation"></a>Versiones compatibles e instalación
Este conector es compatible con la versión 7.x de SAP Business Warehouse. Admite la copia de datos de InfoCubes y QueryCubes (incluidas las consultas BEx) mediante consultas MDX.

Para habilitar la conectividad en la instancia de SAP BW, instale los componentes siguientes:
- **Data Management Gateway**: el servicio Data Factory admite la conexión a almacenes de datos locales (incluido SAP Business Warehouse) mediante un componente denominado Data Management Gateway. Para obtener información acerca de Data Management Gateway e instrucciones detalladas acerca de cómo configurar la puerta de enlace, consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). La puerta de enlace es necesaria aunque SAP Business Warehouse esté hospedado en una máquina virtual (VM) de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.
- **Biblioteca SAP NetWeaver** en el equipo de la puerta de enlace. Puede obtener la biblioteca SAP Netweaver desde el administrador de SAP o directamente desde el [centro de descarga de software de SAP](https://support.sap.com/swdc). Busque la **nota 1025361 de SAP** para obtener la ubicación de descarga de la versión más reciente. Asegúrese de que la arquitectura de la biblioteca de SAP NetWeaver (32 bits o 64 bits) coincide con la instalación de la puerta de enlace. A continuación, instale todos los archivos incluidos en SAP NetWeaver RFC SDK según la nota de SAP. La biblioteca SAP NetWeaver también se incluye en la instalación de las herramientas de cliente de SAP.

> [!TIP]
> Coloque los archivos DLL extraídos de NetWeaver RFC SDK en la carpeta system32.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva los datos desde un almacén de datos Cassandra local mediante el uso de diferentes herramientas o API. 

- La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos. 
- Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver un ejemplo con definiciones de JSON para entidades de Data Factory que se emplean para copiar datos de un almacén de datos de SAP Business Warehouse local, consulte la sección [Ejemplo con definiciones de JSON: copia de datos de SAP Business Warehouse a un blob de Azure](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) de este artículo. 

En las secciones siguientes, se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de un almacén de datos de SAP BW:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de SAP Business Warehouse (BW).

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
server | Nombre del servidor en el que reside la instancia de SAP BW. | string | Sí
systemNumber | Número del sistema de SAP BW. | Número decimal de dos dígitos que se representa en forma de cadena. | Sí
clientId | Identificador del cliente en el sistema SAP W. | Número decimal de tres dígitos que se representa en forma de cadena. | Sí
username | Nombre del usuario que tiene acceso al servidor SAP | string | Sí
password | Contraseña del usuario. | string | Sí
gatewayName | Nombre de la puerta de enlace que debe usar el servicio Data Factory para conectarse a la instancia de SAP BW local. | string | Sí
encryptedCredential | La cadena de credenciales cifrada. | string | No

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. No hay ninguna propiedad específica del tipo compatible con el conjunto de datos de SAP BW de tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen es del tipo **RelationalSource** (que incluye SAP BW), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| Query | Especifica la consulta MDX para leer datos de la instancia de SAP BW. | Consulta MDX. | Sí |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Ejemplo con definiciones de JSON: copia de datos de SAP Business Warehouse a un blob de Azure
En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En este ejemplo, se muestra cómo copiar datos de una instancia de SAP Business Warehouse local a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.  

> [!IMPORTANT]
> Este ejemplo proporciona fragmentos JSON. No incluye instrucciones paso a paso para crear la factoría de datos. Las instrucciones paso a paso se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado del tipo [SapBw](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian los datos de una instancia de SAP Business Warehouse a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure Data Management Gateway. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Servicio vinculado de SAP Business Warehouse
Este servicio vincula una instancia de SAP Business Warehouse a la factoría de datos. La propiedad type se establece en **SapBw**. La sección typeProperties proporciona información de conexión para la instancia de SAP Business Warehouse. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage
Este servicio vinculado vincula una cuenta de Azure Storage a la factoría de datos. La propiedad type se establece en **AzureStorage**. La sección typeProperties proporciona información de conexión para la cuenta de Azure Storage.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-bw-input-dataset"></a>Conjunto de datos de entrada de SAP BW
Este conjunto de datos define el conjunto de datos de SAP Business Warehouse. Establezca el tipo del conjunto de datos de Data Factory en **RelationalTable**. Actualmente, no se especifica ninguna propiedad concreta de tipo para un conjunto de datos de SAP BW. La consulta de la definición de la actividad de copia especifica qué datos se deben de la instancia de SAP BW. 

Si se establece la propiedad external en true, se informa al servicio Data Factory de que la tabla es externa a la factoría de datos y que no la produce ninguna actividad de dicha factoría.

Las propiedades de frecuencia e intervalo definen la programación. En este caso, los datos se leen de la instancia de SAP BW cada hora. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida de blob de Azure
Este conjunto de datos define el conjunto de datos de salida del blob de Azure. La propiedad type se establece en AzureBlob. La sección typeProperties proporciona el lugar donde se almacenan los datos que se copian desde la instancia de SAP BW. Los datos se escriben en un blob nuevo cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Canalización con actividad de copia
La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** (para el origen de SAP BW), mientras que el tipo **sink** se establece en **BlobSink**. La consulta especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```

### <a name="type-mapping-for-sap-bw"></a>Asignación de tipos para SAP BW
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos desde SAP BW, se usan las asignaciones siguientes de tipos de SAP BW a tipos de .NET.

Tipo de datos en el diccionario de ABAP | Tipo de datos de .NET
-------------------------------- | --------------
ACCP |    Int
CHAR | String
CLNT | String
CURR | Decimal
CUKY | String
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNIDAD | String
DATS | String
NUMC | String
TIMS | String

> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).


## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
