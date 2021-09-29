---
title: Movimiento de datos desde HDFS local
description: Obtenga información acerca de cómo mover datos desde HDFS local mediante Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2e514903a3c89790a6b0bb85d37a530e13b07d8c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580915"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Movimiento de datos desde HDFS local mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-hdfs-connector.md)
> * [Versión 2 (versión actual)](../connector-hdfs.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [HDFS connector in V2](../connector-hdfs.md) (Conector HDFS en V2).

En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de HDFS local. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos de HDFS en cualquier almacén de datos de receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. En la actualidad, Factoría de datos solo admite el movimiento de datos desde HDFS local a otros almacenes de datos, pero no el movimiento inverso.

> [!NOTE]
> La actividad de copia no elimina el archivo de origen una vez copiado correctamente en el destino. Si necesita eliminar el archivo de origen tras una copia correcta, cree una actividad personalizada para tal fin y úsela en la canalización. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
El servicio Factoría de datos admite la conexión a HDFS local mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. Use la puerta de enlace para conectar con HDFS, aunque esté hospedado en una máquina virtual de IaaS de Azure.

> [!NOTE]
> Asegúrese de que Data Management Gateway puede obtener acceso a **TODOS** los [servidor de nodo de nombres]:[puerto de nodo de nombres] y [servidores de nodos de datos]:[puerto de nodo de datos] del clúster de Hadoop. El [puerto de nodo de nombres] predeterminado es 50070 y el [puerto de nodo de datos] es 50075.

Aunque puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual de Azure como HDFS, se recomienda que instale la puerta de enlace en un equipo independiente o una máquina virtual de IaaS de Azure. Al tener la puerta de enlace en un equipo independiente, se reduce la contención de recursos y se mejora el rendimiento. Al instalar la puerta de enlace en una máquina independiente, la máquina debería poder acceder a la máquina con HDFS.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un origen de HDFS mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos.

Puede usar las siguientes herramientas para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver un ejemplo con definiciones de JSON para entidades de Data Factory que se emplean para copiar datos de un almacén de datos HDFS, consulte la sección [Ejemplo JSON: Copia de datos de HDFS local a un blob de Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) de este artículo.

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de HDFS:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
Un servicio vinculado vincula un almacén de datos a una factoría de datos. Se crea un servicio vinculado de tipo **Hdfs** para vincular HDFS local a la factoría de datos. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de HDFS.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **Hdfs** |Sí |
| url |Dirección URL a HDFS |Sí |
| authenticationType |Anónima o Windows. <br><br> Para usar la **autenticación Kerberos** para el conector HDFS, consulte [esta sección](#use-kerberos-authentication-for-hdfs-connector) a fin de configurar el entorno local en consecuencia. |Sí |
| userName |Nombre de usuario para la autenticación de Windows Para la autenticación Kerberos, especifique `<username>@<domain>.com`. |Sí (para la autenticación de Windows) |
| password |Contraseña para la autenticación de Windows |Sí (para la autenticación de Windows) |
| gatewayName |Nombre de la puerta de enlace que el servicio Factoría de datos debe usar para conectarse a HDFS. |Sí |
| encryptedCredential |Salida de [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) de la credencial de acceso. |No |

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Con la autenticación de Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **FileShare** (que incluye el conjunto de datos de HDFS) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| folderPath |Ruta de acceso a la carpeta. Ejemplo: `myfolder`<br/><br/>Use el carácter de escape "\" en los caracteres especiales de la cadena. Por ejemplo: para folder\subfolder, especifique la carpeta\\\\subcarpeta y para d:\samplefolder, especifique d:\\\\samplefolder.<br/><br/>Puede combinar esta propiedad con **partitionBy** para que las rutas de acceso de carpeta se basen en las fechas y horas de inicio y finalización del segmento. |Sí |
| fileName |Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo generado estaría en el siguiente formato: <br/><br/>`Data.<Guid>.txt` (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy se puede usar para especificar un valor de folderPath dinámico, un nombre de archivo para datos de series temporales. Por ejemplo, folderPath se parametriza para cada hora de datos. |No |
| format | Se admiten los tipos de formato siguientes: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compression | Especifique el tipo y el nivel de compresión de los datos. Estos son los tipos que se admiten: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**. Estos son los niveles que se admiten: **Optimal** y **Fastest**. Para más información, consulte el artículo sobre [formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> filename y fileFilter no pueden usarse simultáneamente.

### <a name="using-partionedby-property"></a>Uso de la propiedad partitionedBy
Como ya se ha indicado en la sección anterior, se puede especificar un valor dinámico de folderPath y filename para datos de series temporales con la propiedad **partitionedBy**, [funciones de Data Factory y las variables del sistema](data-factory-functions-variables.md).

Para aprender más sobre los conjuntos de datos de series temporales [Creación de conjuntos de datos](data-factory-create-datasets.md), [Programación y ejecución](data-factory-scheduling-and-execution.md) y [Creación de canalizaciones](data-factory-create-pipelines.md) para conocer más detalles sobre los conjuntos de datos de series temporales, la programación y los segmentos.

#### <a name="sample-1"></a>Muestra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

```JSON
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
En este ejemplo, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de actividad de copia, si el origen es del tipo **FileSystemSource** , estarán disponibles las propiedades siguientes en la sección typeProperties:

**FileSystemSource** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True, False (predeterminada) |No |

## <a name="supported-file-and-compression-formats"></a>Formatos de archivo y de compresión admitidos
Consulte los detalles en [Formatos de compresión y de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Ejemplo JSON: Copia de datos de HDFS local a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de un sistema HDFS local a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.  

El ejemplo proporciona definiciones de JSON para las siguientes entidades de Data Factory. Puede utilizar estas definiciones para crear una canalización para copiar datos desde HDFS a Azure Blob Storage mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Un servicio vinculado del tipo [OnPremisesHdfs](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [FileShare](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

El ejemplo copia los datos de un HDFS local a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure la puerta de enlace de administración de datos. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de HDFS:** En este ejemplo se usa la autenticación de Windows. Consulte la sección [Propiedades del servicio vinculado de HDFS](#linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Servicio vinculado de Azure Storage:**

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

**Conjunto de datos de entrada de HDFS:** este conjunto de datos hace referencia a la carpeta DataTransfer/UnitTest/ de HDFS. La canalización copia todos los archivos de su carpeta en el destino.

Si se establece "external": "true", se informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad de la factoría de datos.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Actividad de copia en una canalización con origen del sistema de archivos y receptor blob:**

La canalización contiene una actividad de copia que está configurada para usar estos conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uso de autenticación Kerberos para el conector HDFS
Existen dos opciones para configurar el entorno local para usar la autenticación Kerberos en el conector HDFS. Puede elegir la que mejor se adapte en su caso.
* Opción 1: [Unirse a la máquina de puerta de enlace en el dominio Kerberos](#kerberos-join-realm)
* Opción 2: [Habilitar la confianza mutua entre el dominio de Windows y el dominio Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opción 1: Unirse a la máquina de puerta de enlace en el dominio Kerberos

#### <a name="requirement"></a>Requisito:

* La máquina de puerta de enlace debe unirse al dominio Kerberos y no puede unirse a ningún dominio de Windows.

#### <a name="how-to-configure"></a>Cómo se configura:

**En la máquina de puerta de enlace:**

1. Ejecute la utilidad **Ksetup** para configurar el dominio y el servidor KDC de Kerberos.

   La máquina debe configurarse como miembro de un grupo de trabajo dado que un dominio Kerberos es diferente de un dominio de Windows. Para ello, establezca el dominio Kerberos y agregue un servidor KDC de la manera siguiente: Reemplace *REALM.COM* por su dominio Kerberos propio correspondiente, según sea necesario.

   ```cmd
   Ksetup /setdomain REALM.COM
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ```

   **Reinicie** la máquina después de ejecutar estos 2 comandos.

2. Compruebe la configuración con el comando **Ksetup**. La salida debe ser como la siguiente:

   ```cmd
   Ksetup
   default realm = REALM.COM (external)
   REALM.com:
      kdc = <your_kdc_server_address>
   ```

**En Azure Data Factory:**

* Configure el conector HDFS mediante la **autenticación de Windows** junto con el nombre y la contraseña de la entidad de seguridad de Kerberos para conectarse al origen de datos de HDFS. Compruebe la sección [HDFS Linked Service properties](#linked-service-properties) (Propiedades de servicio vinculado de HDFS) en los detalles de configuración.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opción 2: Habilitar la confianza mutua entre el dominio de Windows y el dominio Kerberos

#### <a name="requirement"></a>Requisito:
*    La máquina de puerta de enlace debe unirse a un dominio de Windows.
*    Necesita permiso para actualizar la configuración del controlador de dominio.

#### <a name="how-to-configure"></a>Cómo se configura:

> [!NOTE]
> Reemplace REALM.COM y AD.COM en el siguiente tutorial por su dominio Kerberos y controlador de dominio propios correspondientes, según sea necesario.

**En el servidor KDC:**

1. Edite la configuración de KDC en el archivo **krb5.conf** para permitir que KDC confíe en el dominio de Windows que hace referencia a la siguiente plantilla de configuración. La configuración se encuentra de forma predeterminada en **/etc/krb5.conf**.

   ```config
   [logging]
   default = FILE:/var/log/krb5libs.log
   kdc = FILE:/var/log/krb5kdc.log
   admin_server = FILE:/var/log/kadmind.log

   [libdefaults]
   default_realm = REALM.COM
   dns_lookup_realm = false
   dns_lookup_kdc = false
   ticket_lifetime = 24h
   renew_lifetime = 7d
   forwardable = true

   [realms]
   REALM.COM = {
       kdc = node.REALM.COM
       admin_server = node.REALM.COM
   }
   AD.COM = {
   kdc = windc.ad.com
   admin_server = windc.ad.com
   }

   [domain_realm]
   .REALM.COM = REALM.COM
   REALM.COM = REALM.COM
   .ad.com = AD.COM
   ad.com = AD.COM

   [capaths]
   AD.COM = {
       REALM.COM = .
   }
   ```

   **Reinicie** el servicio KDC después de la configuración.

2. Prepare una entidad de seguridad llamada **krbtgt/REALM.COM\@AD.COM** en el servidor KDC con el comando siguiente:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. En el archivo de configuración de servicio de HDFS **hadoop.security.auth_to_local**, agregue `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**En el controlador de dominio:**

1. Ejecute los siguientes comandos **Ksetup** para agregar una entrada de dominio Kerberos:

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

2. Establezca la confianza entre el dominio de Windows y el dominio Kerberos. [contraseña] es la contraseña de la entidad de seguridad **krbtgt/REALM.COM\@AD.COM**.

   ```cmd
   netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
   ```

3. Seleccione el algoritmo de cifrado usado en Kerberos.

   1. Vaya a Administrador de servidores > Administración de directivas de grupo > Dominio > Objetos de directiva de grupo > Default or Active Domain Policy (Directiva de dominio predeterminada o activa) y haga clic en Editar.

   2. En la ventana emergente **Editor de administración de directivas de grupo**, vaya a Configuración del equipo > Directivas > Configuración de Windows > Configuración de seguridad > Directivas locales > Opciones de seguridad, y configure **Seguridad de red: Configurar tipos de cifrado permitidos para Kerberos**.

   3. Seleccione el algoritmo de cifrado que quiere usar al conectarse a KDC. Normalmente, puede seleccionar todas las opciones.

      :::image type="content" source="media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png" alt-text="Configuración de tipos de cifrado para Kerberos":::

   4. Use el comando **Ksetup** para especificar el algoritmo de cifrado que se usará en el dominio específico.

      ```cmd
      ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
      ```

4. Cree la asignación entre la cuenta de dominio y la entidad de seguridad de Kerberos, a fin de usar la entidad de seguridad de Kerberos en el dominio de Windows.

   1. Inicie las herramientas administrativas > **Usuarios y equipos de Active Directory**.

   2. Configure características avanzadas; para ello, haga clic en **Ver** > **Características avanzadas**.

   3. Busque la cuenta a la que quiere crear asignaciones y haga clic con el botón derecho para ver **Asignaciones de nombres** > haga clic en la pestaña **Nombres de Kerberos**.

   4. Agregue una entidad de seguridad del dominio Kerberos.

      :::image type="content" source="media/data-factory-hdfs-connector/map-security-identity.png" alt-text="Asignación de la identidad de seguridad":::

**En la máquina de puerta de enlace:**

* Ejecute los siguientes comandos **Ksetup** para agregar una entrada de dominio Kerberos.

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**En Azure Data Factory:**

* Configure el conector de HDFS mediante la **autenticación de Windows** en combinación con la cuenta de dominio o la entidad de seguridad de Kerberos para conectarse al origen de datos de HDFS. Compruebe la sección [HDFS Linked Service properties](#linked-service-properties) (Propiedades de servicio vinculado de HDFS) en los detalles de configuración.

> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
