---
title: Movimiento de datos de SAP HANA mediante Azure Data Factory
description: Aprenda a mover datos de SAP HANA mediante Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 788d773d4bf686d10fa017a5421773f79e82e020
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631084"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Movimiento de datos de SAP HANA mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-sap-hana-connector.md)
> * [Versión 2 (versión actual)](../connector-sap-hana.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [Conector SAP HANA en V2](../connector-sap-business-warehouse.md).

En este artículo, se explica el uso de la actividad de copia en Azure Data Factory para mover datos desde un almacén de datos de SAP HANA local. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos de un almacén de datos de SAP HANA local a cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Por el momento, Data Factory solo admite el movimiento de datos de un almacén de datos de SAP HANA a otros almacenes de datos, pero no viceversa.

## <a name="supported-versions-and-installation"></a>Versiones compatibles e instalación
Este conector es compatible con cualquier versión de base de datos de SAP HANA. Admite la copia de datos de modelos de información de HANA (como las vistas de análisis y cálculo) y las tablas de fila o columna mediante consultas SQL.

Para habilitar la conectividad a la instancia de SAP HANA, instale los componentes siguientes:
- **Data Management Gateway**: el servicio Data Factory admite la conexión a almacenes de datos locales (incluido SAP HANA) mediante un componente denominado Data Management Gateway. Para obtener información acerca de Data Management Gateway e instrucciones detalladas acerca de cómo configurar la puerta de enlace, consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). La puerta de enlace es necesaria incluso si SAP HANA se hospeda en una máquina virtual (VM) de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.
- **Controlador ODBC de SAP HANA** en la máquina de puerta de enlace. Puede descargar el controlador ODBC de SAP HANA desde el [centro de descarga de software de SAP](https://support.sap.com/swdc). Busque con la palabra clave **SAP HANA CLIENT for Windows** (Cliente SAP HANA para Windows). 

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un almacén de datos SAP HANA local mediante el uso de diferentes herramientas o API. 

- La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos. 
- Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver un ejemplo con definiciones de JSON para entidades de Data Factory que se emplean para copiar datos de un almacén de datos de SAP HANA local, consulte la sección [Ejemplo con definiciones de JSON: copia de datos de SAP HANA a un blob de Azure](#json-example-copy-data-from-sap-hana-to-azure-blob) de este artículo. 

En las secciones siguientes, se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de un almacén de datos de SAP HANA:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de SAP HANA.

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
server | Nombre del servidor en el que reside la instancia de SAP HANA. Si el servidor usa un puerto personalizado, especifique `server:port`. | string | Sí
authenticationType | Tipo de autenticación. | cadena. "Basic" o "Windows" | Sí 
username | Nombre del usuario que tiene acceso al servidor SAP | string | Sí
password | Contraseña del usuario. | string | Sí
gatewayName | Nombre de la puerta de enlace que debe usar el servicio Data Factory para conectarse a la instancia de SAP HANA local. | string | Sí
encryptedCredential | La cadena de credenciales cifrada. | string | No

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. No hay ninguna propiedad específica del tipo compatible con el conjunto de datos de SAP HANA de tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen es de tipo **RelationalSource** (que incluye SAP HANA), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| Query | Especifica la consulta SQL para leer datos de la instancia de SAP HANA. | Consulta SQL. | Sí |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Ejemplo con definiciones de JSON: copia de datos de SAP HANA a un blob de Azure
En el siguiente ejemplo se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En este ejemplo, se muestra cómo copiar datos de una instancia de SAP HANA local a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.  

> [!IMPORTANT]
> Este ejemplo proporciona fragmentos JSON. No incluye instrucciones paso a paso para crear la factoría de datos. Las instrucciones paso a paso se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [SapHana](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian los datos de una instancia de SAP HANA a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure Data Management Gateway. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-hana-linked-service"></a>Servicio vinculado de SAP HANA
Este servicio vinculado vincula su instancia de SAP HANA a la factoría de datos. La propiedad type se establece en **SapHana**. La sección typeProperties proporciona información de conexión para la instancia de SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
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

### <a name="sap-hana-input-dataset"></a>Conjunto de datos de entrada de SAP HANA

Este conjunto de datos define el conjunto de datos de SAP HANA. Establezca el tipo del conjunto de datos de Data Factory en **RelationalTable**. Actualmente, no establece ninguna propiedad específica de tipo para un conjunto de datos de SAP HANA. La consulta en la definición de actividad de copia especifica qué datos leer de la instancia de SAP HANA. 

Si se establece la propiedad external en true, se informa al servicio Data Factory de que la tabla es externa a la factoría de datos y que no la produce ninguna actividad de dicha factoría.

Las propiedades de frecuencia e intervalo definen la programación. En este caso, los datos se leen de la instancia de SAP HANA cada hora. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
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
Este conjunto de datos define el conjunto de datos de salida del blob de Azure. La propiedad type se establece en AzureBlob. La sección typeProperties proporciona el lugar donde se almacenan los datos copiados desde la instancia de SAP HANA. Los datos se escriben en un blob nuevo cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** (para el origen de SAP HANA) y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Asignación de tipos para SAP HANA
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos desde SAP HANA, se usarán las asignaciones siguientes desde tipos de SAP HANA a tipos de .NET.

Tipo de SAP HANA | Tipo basado en .NET
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
bigint | Int64
real | Single
DOUBLE | Single
DECIMAL | Decimal
BOOLEAN | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TIME | TimeSpan
timestamp | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Restricciones conocidas
Cuando se copian datos de SAP HANA, hay algunas limitaciones conocidas:

- Las cadenas NVARCHAR se truncan al llegar a la longitud máxima de 4000 caracteres Unicode
- SMALLDECIMAL no se admite
- VARBINARY no se admite
- Las fechas válidas son las del intervalo entre 30/12/1899 y 31/12/9999

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
