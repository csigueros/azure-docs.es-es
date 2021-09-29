---
title: Traslado de datos de MongoDB
description: Obtenga información acerca de cómo mover los datos de la base de datos de MongoDB mediante Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: c863176b73f4b5a41f0320bf9e59c2286fa22446
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647506"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Movimiento de datos de MongoDB mediante Azure Data Factory

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-on-premises-mongodb-connector.md)
> * [Versión 2 (versión actual)](../connector-mongodb.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo sobre el [conector de MongoDB en V2](../connector-mongodb.md).


En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de una base de datos MongoDB local. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos desde un almacén de datos de MongoDB local a cualquier almacén de datos del receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Data Factory solo admite actualmente el movimiento de datos de un almacén de datos de MongoDB a otros almacenes de datos, pero no el movimiento de datos de otros almacenes de datos a una base de datos de MongoDB.

## <a name="prerequisites"></a>Requisitos previos
Para que el servicio Azure Data Factory pueda conectarse a la base de datos de MongoDB local, debe instalar los siguientes componentes:

- Las versiones admitidas de MongoDB son: 2.4, 2.6, 3.0, 3.2, 3.4 y 3.6.
- Data Management Gateway en el mismo equipo que hospede la base de datos o en un equipo independiente para evitar la competencia por los recursos con la base de datos. Data Management Gateway es un software que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más detalles sobre Data Management Gateway. Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para instrucciones paso a paso sobre cómo configurar la puerta de enlace como canalización de datos para mover datos.

    Cuando instale la puerta de enlace, se instalará automáticamente el controlador ODBC de Microsoft MongoDB que se utiliza para establecer la conexión con MongoDB.

    > [!NOTE]
    > Tiene que usar la puerta de enlace para conectar con MongoDB, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si está intentando conectarse a una instancia de MongoDB hospedada en la nube, también puede instalar la instancia de puerta de enlace en la máquina virtual de IaaS.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un almacén de datos MongoDB local mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos.

Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para obtener un ejemplo con definiciones de JSON para entidades de Data Factory que se utilizan para copiar los datos de un almacén de datos de MongoDB local, consulte la sección [Ejemplo de JSON: Copiar datos de MongoDB a un blob de Azure](#json-example-copy-data-from-mongodb-to-azure-blob) de este artículo.

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de MongoDB:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de **OnPremisesMongoDB** .

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **OnPremisesMongoDb** |Sí |
| server |Dirección IP o nombre de host del servidor de MongoDB. |Sí |
| port |Puerto TCP que el servidor de MongoDB utiliza para escuchar las conexiones del cliente. |Valor predeterminado opcional: 27017 |
| authenticationType |Básica o anónima. |Sí |
| username |Cuenta de usuario para tener acceso a MongoDB. |Sí (si se usa la autenticación básica). |
| password |Contraseña del usuario. |Sí (si se usa la autenticación básica). |
| authSource |Nombre de la base de datos de MongoDB que desea usar para comprobar las credenciales de autenticación. |Opcional (si se usa la autenticación básica). Valor predeterminado: se utiliza la cuenta de administrador y la base de datos especificada mediante la propiedad databaseName. |
| databaseName |Nombre de la base de datos de MongoDB a la que desea acceder. |Sí |
| gatewayName |Nombre de la puerta de enlace que accede al almacén de datos. |Sí |
| encryptedCredential |Credencial cifrada por la puerta de enlace. |Opcional |

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **MongoDbCollection** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| collectionName |Nombre de la colección en la base de datos de MongoDB. |Sí |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen es de tipo **MongoDbSource** , estarán disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| Query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL-92. Por ejemplo: select * from MyTable. |No (si se especifica **collectionName** de **dataset**) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Ejemplo de JSON: Copiar datos de MongoDB a un blob de Azure
En este ejemplo se proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestra cómo copiar datos de una base de datos MongoDB local a Azure Blob Storage. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesMongoDB](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [MongoDbCollection](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [MongoDBSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos de MongoDB en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo [Puerta de enlace de administración de datos](data-factory-data-management-gateway.md) .

**Servicio vinculado de MongoDB:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Servicio vinculado de Azure Storage:**

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

**Conjunto de datos de entrada de MongoDB**: si “external” se establece en “true”, se informa al servicio Data Factory de que la tabla es externa a la factoría de datos y que no la genera ninguna actividad de dicha factoría.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Actividad de copia en una canalización con el origen MongoDB y el receptor de blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **MongoDbSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Esquema de Data Factory
El servicio de Azure Data Factory deduce el esquema de una colección de MongoDB mediante el uso de los últimos 100 documentos de la colección. Si estos 100 documentos no contienen el esquema completo, se pueden omitir algunas columnas durante la operación de copia.

## <a name="type-mapping-for-mongodb"></a>Asignación de tipos para MongoDB
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a MongoDB, se usarán las asignaciones siguientes de tipos MongoDB a tipos .NET.

| Tipo de MongoDB | Tipo de .NET Framework |
| --- | --- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Object |Renormalizado en columnas acopladas con “_” como separador anidado |

> [!NOTE]
> Para más información sobre la compatibilidad para matrices con tablas virtuales, consulte la sección [Compatibilidad para tipos complejos que usan tablas virtuales](#support-for-complex-types-using-virtual-tables) que aparece más adelante.

Actualmente, no se admiten los siguientes tipos de datos de MongoDB: DBPointer, JavaScript, Clave Max y Min, Expresión regular, Símbolo, Marca de tiempo, Sin definir

## <a name="support-for-complex-types-using-virtual-tables"></a>Compatibilidad para tipos complejos que usan tablas virtuales
Azure Data Factory utiliza un controlador ODBC integrado para conectarse a una base de datos de MongoDB y copiar datos de dicha base de datos. Para los tipos complejos como matrices u objetos con diferentes tipos en los documentos, el controlador volverá a normalizar los datos en las tablas virtuales correspondientes. En concreto, si una tabla contiene estas columnas, el controlador generará las siguientes tablas virtuales:

* Una **tabla base**, que contiene los mismos datos que la tabla real, salvo las columnas de tipo complejo. La tabla base utiliza el mismo nombre que la tabla real a la que representa.
* Una **tabla virtual** para cada columna de tipo complejo, que ampliará los datos anidados. Para asignar un nombre a las tablas virtuales, se utiliza el nombre de la tabla real, un separador “_” y el nombre de la matriz u objeto.

Las tablas virtuales hacen referencia a los datos de la tabla real, lo que permite al controlador obtener acceso a los datos no normalizados. Consulte el ejemplo de la siguiente sección para más información. Para acceder al contenido de las matrices de MongoDB, puede crear consultas y combinar las tablas virtuales.

Puede utilizar el [Asistente para copia](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para consultar una vista intuitiva de la lista de tablas de la base de datos de MongoDB (incluidas las tablas virtuales) y una vista previa de los datos incluidos. También puede crear una consulta en el Asistente para copia y validarla para ver el resultado.

### <a name="example"></a>Ejemplo
Por ejemplo, la tabla “ExampleTable” que aparece a continuación es una tabla de MongoDB que tiene una columna con una matriz de objetos en cada celda: Facturas y una columna con una matriz de tipos escalares: Clasificaciones.

| _id | Nombre del cliente | Facturas | Nivel de servicio | Clasificaciones |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, artículo:”tostadora”, precio:”456”, descuento:”0.2”}, {invoice_id:”124”, artículo:”horno”, precio: ”1235”, descuento: ”0.2”}] |Plata |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, artículo:”frigorífico”, precio: ”12543”, descuento: ”0.0”}] |Oro |[1,2] |

El controlador generará varias tablas virtuales que representan a esta tabla. La primera tabla virtual es la tabla base y se denomina “ExampleTable”, tal y como se muestra a continuación. La tabla base contiene todos los datos de la tabla original, pero los datos de las matrices se han omitido y se ampliarán en las tablas virtuales.

| _id | Nombre del cliente | Nivel de servicio |
| --- | --- | --- |
| 1111 |ABC |Plata |
| 2222 |XYZ |Oro |

Las siguientes tablas muestran las tablas virtuales que representan las matrices originales en el ejemplo. Estas tablas contienen lo siguiente:

* Una referencia a la columna de clave principal original correspondiente a la fila de la matriz original (a través del identificador de la columna)
* Una indicación de la posición de los datos dentro de la matriz original
* Los datos ampliados para cada elemento de la matriz

Tabla “ExampleTable_Invoices”:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Descuento |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |tostadora |456 |0,2 |
| 1111 |1 |124 |horno |1235 |0,2 |
| 2222 |0 |135 |frigorífico |12543 |0,0 |

Tabla “ExampleTable_Ratings”:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones paso a paso para crear una canalización de datos que mueva los datos de un almacén de datos local a un almacén de datos de Azure.
