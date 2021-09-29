---
title: Movimiento de datos de un servidor FTP mediante Azure Data Factory
description: Aprenda a mover datos de un servidor FTP mediante Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6c267c3234e0861a394c75cf47bd198b1209b58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589993"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Movimiento de datos de un servidor FTP mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-ftp-connector.md)
> * [Versión 2 (versión actual)](../connector-ftp.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [FTP connector in V2](../connector-ftp.md) (Conector FTP en V2).

En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos desde un servidor FTP. Se basa en la información general ofrecida por el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos de un servidor FTP a cualquier almacén de datos receptor admitido. Para ver una lista de almacenes de datos admitidos como receptores por la actividad de copia, consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory solo admite actualmente el movimiento de datos de un servidor FTP a otros almacenes de datos, pero no viceversa. Admite servidores FTP tanto locales como en la nube.

> [!NOTE]
> La actividad de copia no elimina el archivo de origen una vez copiado correctamente en el destino. Si necesita eliminar el archivo de origen tras una copia correcta, cree una actividad personalizada para tal fin y úsela en la canalización.

## <a name="enable-connectivity"></a>Habilitación de la conectividad
Si va a mover datos desde un servidor FTP **local** a un almacén de datos de nube (ejemplo, en Azure Blob Storage), instale y utilice la puerta de enlace de administración de datos. La puerta de enlace de administración de datos es un agente de cliente instalado en la máquina local, que permite a Cloud Services conectarse al recurso local. Para más información, vea [Data Management Gateway](data-factory-data-management-gateway.md). Vea [Movimiento de datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones paso a paso sobre cómo configurar y usar la puerta de enlace. Utilice la puerta de enlace para conectarse a un servidor FTP, aunque este se encuentre en una máquina virtual de infraestructura como servicio (IaaS) de Azure.

Puede instalar la puerta de enlace en la misma máquina local o la máquina virtual de IaaS como servidor FTP. Sin embargo, se recomienda que instale la puerta de enlace en un equipo o máquina virtual de IaaS independiente para evitar la contención de recursos y mejorar el rendimiento. Al instalar la puerta de enlace en una máquina independiente, esta última debería poder acceder al servidor FTP.

## <a name="get-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva datos desde un origen FTP mediante diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar de Data Factory**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido.

Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **PowerShell**, **la plantilla de Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON. Para ver un ejemplo con definiciones de JSON para entidades de Data Factory que se emplean para copiar datos de un almacén de datos de FTP, consulte la sección [Ejemplo JSON: Copia de datos de un servidor FTP a un blob de Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) de este artículo.

> [!NOTE]
> Para obtener información sobre los formatos de compresión y archivos compatibles que usar, vea [Formatos de archivo y de compresión admitidos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de FTP.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se describen los elementos JSON específicos del servicio vinculado de FTP.

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- |
| type |Establezca el valor en FtpServer. |Sí |&nbsp; |
| host |Especifique el nombre o dirección IP del servidor FTP. |Sí |&nbsp; |
| authenticationType |Especifique el tipo de autenticación. |Sí |Basic, Anonymous |
| username |Especifique el usuario que tiene acceso al servidor FTP. |No |&nbsp; |
| password |Especifique la contraseña del usuario (nombre de usuario). |No |&nbsp; |
| encryptedCredential |Especifique las credenciales cifradas para obtener acceso al servidor FTP. |No |&nbsp; |
| gatewayName |Especifique el nombre de la puerta de enlace en Data Management Gateway para conectarse a un servidor FTP local. |No |&nbsp; |
| port |Especifique el puerto en el que se está realizando la escucha del servidor FTP. |No |21 |
| enableSsl |Especificar si desea usar FTP a través del canal SSL/TLS. |No |true |
| enableServerCertificateValidation |Especifique si desea habilitar la validación de certificados de servidor TLS/SSL al usar FTP sobre el canal SSL/TLS. |No |true |

>[!NOTE]
>El conector FTP permite el acceso al servidor FTP sin cifrado o con cifrado SSL/TLS explícito, pero no permite el cifrado SSL/TLS implícito.

### <a name="use-anonymous-authentication"></a>Uso de la autenticación anónima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Uso del nombre de usuario y la contraseña en texto sin formato para la autenticación básica

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Uso de puerto, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Uso de encryptedCredential para la autenticación y la puerta de enlace

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como estructura, disponibilidad y directiva de un JSON de conjunto de datos son similares para todos los tipos de conjunto de datos.

La sección **typeProperties** es diferente para cada tipo de conjunto de datos. Proporciona información específica del tipo de conjunto de datos. La sección **typeProperties** del conjunto de datos de tipo **FileShare** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| folderPath |Subtrazado a la carpeta. Use el carácter de escape "\" en los caracteres especiales de la cadena. Consulte los casos que se exponen en Ejemplos de definiciones de servicio vinculado y conjunto de datos.<br/><br/>Puede combinar esta propiedad con **partitionBy** para que las rutas de acceso de carpeta se basen en las fechas y horas de inicio y finalización del segmento. |Sí |
| fileName |Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<br/><br/>Si no se especifica **fileName** para un conjunto de datos de salida, el nombre del archivo generado está en el siguiente formato: <br/><br/>`Data.<Guid>.txt` (Ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Especifique el filtro que se va a usar para seleccionar un subconjunto de archivos de **folderPath**, en lugar de todos los archivos.<br/><br/>Valores permitidos son: `*` (varios caracteres) y `?` (un único individual).<br/><br/>Ejemplo 1: `"fileFilter": "*.log"`<br/>Ejemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** es aplicable a un conjunto de datos FileShare de entrada. Esta propiedad no es compatible con el sistema de archivos distribuido de Hadoop (HDFS). |No |
| partitionedBy |Se usa para especificar una propiedad **folderPath** y **fileName** dinámicos para datos de series temporales. Por ejemplo, puede especificar una propiedad **folderPath** que tenga parámetros para cada hora de datos. |No |
| format | Se admiten los tipos de formato siguientes: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, vea las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea copiar los archivos tal cual entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compression | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**, y los niveles que se admiten son: **Optimal** (Óptimo) y **Fastest** (Más rápido). Para más información, consulte el artículo sobre [formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Especifique si se usar el modo de transferencia binario. Los valores son true para el modo binario (es el valor predeterminado) y false para ASCII. Esta propiedad solo puede usarse cuando el tipo de servicio vinculado asociado es: FtpServer. |No |

> [!NOTE]
> **fileName** y **fileFilter** no pueden usarse simultáneamente.

### <a name="use-the-partionedby-property"></a>Uso de la propiedad partitionedBy
Como ya se ha indicado en la sección anterior, se puede especificar un valor dinámico de **folderPath** y **fileName** para datos de series temporales con la propiedad **partitionedBy**.

Para aprender más sobre los conjuntos de datos, la programación y los segmentos de series temporales, vea [Creación de conjuntos de datos](data-factory-create-datasets.md), [Programación y ejecución](data-factory-scheduling-and-execution.md) y [Creación de canalizaciones](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Ejemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. La ruta de acceso de la carpeta es diferente para cada segmento. (Por ejemplo, wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104).

#### <a name="sample-2"></a>Ejemplo 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
En este ejemplo, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades **folderPath** y **fileName**.

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, las propiedades de tipo varían en función de los tipos de orígenes y receptores.

En la actividad de copia, si el origen es del tipo **FileSystemSource**, estarán disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True, False (predeterminada) |No |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Ejemplo JSON: Copia de datos del servidor FTP a un blob de Azure
En este ejemplo se muestra cómo copiar datos un servidor FTP a Azure Blob Storage. Sin embargo, los datos se pueden copiar directamente en cualquiera de los receptores indicados en [Formatos y almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Un servicio vinculado de tipo [FtpServer](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [FileShare](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian los datos de servidor FTP a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

### <a name="ftp-linked-service"></a>Servicio vinculado FTP

En este ejemplo se usa la autenticación básica con el nombre de usuario y la contraseña en texto sin formato. También se puede hacer de una de las siguientes maneras:

* Autenticación anónima
* Autenticación básica con credenciales cifradas
* FTP sobre SSL/TLS (FTPS)

Para los diferentes tipos de autenticación que se pueden usar, consulte la sección [Servicio vinculado FTP](#linked-service-properties).

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage

```JSON
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
### <a name="ftp-input-dataset"></a>Conjunto de datos de entrada FTP

Este conjunto de datos hace referencia a la carpeta FTP `mysharedfolder` y al archivo `test.csv`. La canalización copia el archivo en el destino.

Si se establece **external** en **true**, se informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad de la factoría de datos.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida de blob de Azure

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Actividad de copia en una canalización con el origen del sistema de archivos y el receptor de blob

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** en **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

* Para aprender sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory y las diversas formas de optimizarlo, consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md).

* Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instrucciones paso a paso para crear una canalización con una actividad de copia.
