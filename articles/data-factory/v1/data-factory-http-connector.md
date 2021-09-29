---
title: 'Movimiento de datos desde un origen HTTP: Azure'
description: Aprenda a mover datos desde un origen HTTP local o en la nube con Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 05/22/2018
robots: noindex
ms.openlocfilehash: 316b8cbcf056b5f26d0043d5a09d688215f1ae9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651206"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Movimiento de datos desde un origen HTTP mediante Azure Data Factory

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-http-connector.md)
> * [Versión 2 (versión actual)](../connector-http.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Azure Data Factory, consulte [HTTP connector in V2](../connector-http.md) (Conector HTTP en V2).

En este artículo se describe el uso de la actividad de copia en Azure Data Factory para mover datos de un punto de conexión HTTP local o en la nube a un almacén de datos receptor compatible. Este artículo se basa en [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md), que presenta una introducción general del movimiento de datos con la actividad de copia. En el artículo también se enumeran los almacenes de datos que la actividad de copia admite como orígenes y receptores.

Data Factory solo admite actualmente el movimiento de datos desde un origen HTTP a otros almacenes de datos. No admite mover datos desde otros almacenes de datos a un destino HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Escenarios admitidos y tipos de autenticación

Puede usar este conector HTTP para recuperar datos de *un punto de conexión HTTP/s local o en la nube* mediante los métodos HTTP **GET** o **POST**. Se admiten los siguientes tipos de autenticación: **Anonymous**, **Basic**, **Digest**, **Windows** y **ClientCertificate**. Tenga en cuenta la diferencia entre este conector y el [conector de tabla web](data-factory-web-table-connector.md). El conector de tabla web extrae contenido de la tabla de una página web HTML.

Al copiar datos de un punto de conexión HTTP local, debe instalar una instancia de Data Management Gateway en el entorno local o en una VM de Azure. Para conocer acerca de Data Management Gateway y obtener instrucciones paso a paso sobre cómo configurar la puerta de enlace, consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Introducción

Puede crear una canalización que tenga una actividad de copia para mover datos desde un origen HTTP mediante diferentes herramientas o API:

- La manera más fácil de crear una canalización es usar el Asistente para copiar datos. Para ver un tutorial rápido sobre cómo crear una canalización con el Asistente para copiar datos, consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md).

- También puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Resource Manager**, la **API de .NET** o **API REST**. Para instrucciones paso a paso sobre cómo crear una canalización que tiene una actividad de copia, consulte el [tutorial de la actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Para obtener ejemplos de JSON para copiar datos desde un origen HTTP a Azure Blob Storage, consulte la sección [Ejemplos de JSON](#json-examples).

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

En la tabla siguiente se describen los elementos JSON específicos del servicio vinculado de HTTP:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type | La propiedad **type** debe establecerse en **Http**. | Sí |
| url | La dirección URL base para el servidor web. | Sí |
| authenticationType | Especifica el tipo de autenticación. Los valores permitidos son: **Anonymous**, **Basic**, **Digest**, **Windows** y **ClientCertificate**. <br><br> Consulte las secciones posteriores de este artículo para obtener más propiedades y ejemplos de JSON para estos tipos de autenticación. | Sí |
| enableServerCertificateValidation | Especifique si desea habilitar la validación de certificados TLS/SSL de servidor si el origen es un servidor web HTTPS. Si el servidor HTTPS usa un certificado autofirmado, establezca esta propiedad en **false**. | No<br /> (El valor predeterminado es: **true**) |
| gatewayName | El nombre de la instancia de Data Management Gateway que se usará para conectarse a un origen HTTP local. | Sí, si va a copiar datos de un origen HTTP local. |
| encryptedCredential | Las credenciales cifradas para acceder al punto de conexión HTTP. El valor se genera automáticamente cuando configura la información de autenticación en el Asistente para copia o al usar el cuadro de diálogo **ClickOnce**. | No<br /> (se aplica solo cuando copia datos de un servidor HTTP local) |

Para más detalles sobre cómo establecer las credenciales para un origen de datos de conector HTTP local, consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Uso de la autenticación Basic, Digest o Windows

Establezca **authenticationType** en **Basic**, **Digest** o **Windows**. Además de las propiedades genéricas del conector HTTP descritas en las secciones anteriores, establezca las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| userName | El nombre de usuario para acceder al punto de conexión HTTP. | Sí |
| password | La contraseña para el usuario (**username**). | Sí |

**Ejemplo: Uso de la autenticación Basic, Digest o Windows**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Uso de la autenticación ClientCertificate

Para usar la autenticación básica, establezca **authenticationType** a **ClientCertificate**. Además de las propiedades genéricas del conector HTTP descritas en las secciones anteriores, establezca las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| embeddedCertData | El contenido codificado en base 64 de los datos binarios del archivo PFX. | Especifique **embeddedCertData** o **certThumbprint** |
| certThumbprint | La huella digital del certificado que se instaló en el almacén de certificados de la máquina de la puerta de enlace. Se aplica solo cuando copia datos de un origen HTTP local. | Especifique **embeddedCertData** o **certThumbprint** |
| password | La contraseña asociada con el certificado. | No |

Si utiliza **certThumbprint** para la autenticación y el certificado está instalado en el almacén personal del equipo local, tiene que conceder el permiso de lectura al servicio de puerta de enlace:

1. Abra Microsoft Management Console (MMC). Agregue el complemento **Certificados** que tenga como destino **Equipo local**.
2. Expanda **Certificados** > **Personal** y, luego, seleccione **Certificados**.
3. Haga clic con el botón derecho en el certificado del almacén personal y seleccione **Todas las tareas** >**Administrar claves privadas**.
3. En la pestaña **Seguridad**, agregue la cuenta de usuario en que se ejecuta el servicio de host de Data Management Gateway con acceso de lectura al certificado.  

**Ejemplo: Uso de un certificado de cliente**

Este servicio vinculado vincula su factoría de datos a un servidor de web HTTP local. Usa un certificado de cliente que está instalado en la máquina que tiene Data Management Gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

**Ejemplo: Uso de un certificado de cliente en un archivo**

Este servicio vinculado vincula su factoría de datos a un servidor de web HTTP local. Usa un certificado de cliente en la máquina que tiene Data Management Gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Algunas de las secciones del archivo JSON de un conjunto de datos, como structure, availability y policy, son similares para todos los tipos de datos (Azure SQL Database, Azure Blob Storage y almacenamiento de tablas de Azure).

Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Creación de conjuntos de datos](data-factory-create-datasets.md).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos. La sección **typeProperties** es proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **Http** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del conjunto de datos debe establecerse en **Http**. | Sí |
| relativeUrl | Dirección URL relativa al recurso que contiene los datos. Cuando no se especifica la ruta de acceso, se solo se usa la dirección URL especificada en la definición del servicio vinculado. <br><br> Para construir la dirección URL dinámica, puede usar [funciones de Data Factory y variables del sistema](data-factory-functions-variables.md). Ejemplo: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)** . | No |
| requestMethod | Método HTTP. Los valores permitidos son **GET** y **POST**. | No <br />(el valor predeterminado es **GET**) |
| additionalHeaders | Encabezados de solicitud HTTP adicionales. | No |
| requestBody | Cuerpo de la solicitud HTTP. | No |
| format | Si quiere *recuperar los datos de un punto de conexión HTTP tal cual* sin analizarlos, omita la opción **format**. <br><br> Si desea analizar el contenido de la respuesta HTTP durante la copia, se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| compression | Especifique el tipo y el nivel de compresión de los datos. Tipos que se admiten: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**. Niveles que se admiten: **Optimal** y **Fastest**. Para más información, consulte el artículo sobre [formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Ejemplo: Uso del método GET (predeterminado)**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
          "relativeUrl": "XXX/test.xml",
          "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Ejemplo: Uso del método POST**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Las propiedades como nombre, descripción, tablas de entrada y salida, y directivas están disponibles para todos los tipos de actividades.

Para ver una lista completa de las secciones y propiedades que hay disponibles para definir actividades, consulte [Creación de canalizaciones](data-factory-create-pipelines.md). 

Las propiedades que están disponibles en la sección **typeProperties** de la actividad varían según cada tipo de actividad. Para una actividad de copia, las propiedades varían en función de los tipos de orígenes y receptores.

En este momento, cuando el origen de la actividad de copia es de tipo **HttpSource**, se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| httpRequestTimeout | El tiempo de espera (el valor **TimeSpan**) para que la solicitud HTTP obtenga una respuesta. Es el tiempo de espera para obtener una respuesta, no para leer los datos de la respuesta. | No<br />(valor predeterminado: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formatos de archivo y de compresión admitidos

Para más información, consulte [Formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Ejemplos de JSON

En los siguientes ejemplos, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En los ejemplos se muestra cómo copiar datos de un origen HTTP a Azure Blob Storage. Sin embargo, los datos se pueden copiar *directamente* de cualquiera de los orígenes a cualquiera de los receptores [admitidos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.

**Ejemplo: Copia de datos de un origen HTTP a Azure Blob Storage**

La solución Data Factory de este ejemplo contiene las siguientes entidades de Data Factory:

*   Un servicio vinculado de tipo [HTTP](#linked-service-properties).
*   Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
*   Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [Http](#dataset-properties).
*   Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Una [canalización](data-factory-create-pipelines.md) que tiene una actividad de copia que usa [HttpSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos de un origen HTTP a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

### <a name="http-linked-service"></a>Servicio vinculado HTTP

En este ejemplo se usa el servicio vinculado HTTP con autenticación anónima. Para los diferentes tipos de autenticación que se pueden usar, consulte [Propiedades del servicio vinculado](#linked-service-properties).

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servicio vinculado de almacenamiento de Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Conjunto de datos de entrada HTTP

Si se establece **external** en **true**, se informa al servicio de Data Factory de que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad en la factoría de datos.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida del blob de Azure

Los datos se escriben en un nuevo blob cada hora (**frecuencia**: **hora**, **intervalo**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Canalización que usa una actividad de copia

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida. La actividad de copia está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **HttpSource**, y el tipo **sink**, en **BlobSink**.

Para una lista de las propiedades admitidas por **HttpSource**, consulte [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
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

> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a otras del conjunto de datos receptor, consulte [Asignación de columnas de conjuntos de datos en Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Rendimiento y optimización

Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
