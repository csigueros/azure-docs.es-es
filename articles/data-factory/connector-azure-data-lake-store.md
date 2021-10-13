---
title: Copia de datos hacia o desde Azure Data Lake Storage Gen1
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos desde almacenes de datos de origen compatibles en Azure Data Lake Store, o bien desde esta plataforma en canalizaciones de Azure Data Factory o Azure Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/11/2021
ms.openlocfilehash: 68f3bb388f003162786a6ce4f50e0f2dd00d48d0
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856664"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory-or-azure-synapse-analytics"></a>Copia de datos con Azure Data Lake Storage Gen1 como origen o destino mediante Azure Data Factory o Azure Synapse Analytics

> [!div class="op_single_selector" title1="Seleccione la versión de Azure Data Factory que usa:"]
>
> * [Versión 1](v1/data-factory-azure-datalake-connector.md)
> * [Versión actual](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se explica cómo copiar datos con Azure Data Lake Storage Gen1 como origen o destino. Para obtener más información, consulte el artículo de introducción de [Azure Data Factory](introduction.md) o [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Azure Data Lake Storage Gen1 es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md) 
- [Asignación de Data Flow](concepts-data-flow-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Actividad de eliminación](delete-activity.md)

En concreto, con este conector, puede hacer lo siguiente:

- Copiar archivos mediante uno de los siguientes métodos de autenticación: entidad de servicio o identidades administradas para recursos de Azure.
- Copiar los archivos tal cual, o bien analizarlos o generarlos con los [códecs de compresión y los formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md).
- [Conservar ACL](#preserve-acls-to-data-lake-storage-gen2) al copiar en Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Si copia datos con el entorno de ejecución de integración autohospedado, configure el firewall corporativo para que permita el tráfico saliente a `<ADLS account name>.azuredatalakestore.net` y a `login.microsoftonline.com/<tenant>/oauth2/token` en el puerto 443. El último es Azure Security Token Service, con el que el entorno de ejecución de integración necesita comunicarse para obtener el token de acceso.

## <a name="get-started"></a>Introducción

> [!TIP]
> Para ver un tutorial sobre cómo usar el conector de Azure Data Lake Store, consulte [Carga de datos en Azure Data Lake Storage Gen1 mediante Azure Data Factory](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-data-lake-storage-gen1-using-ui"></a>Creación de un servicio vinculado a Azure Data Lake Storage Gen1 mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado a Azure Data Lake Storage Gen1 en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña "Administrar" de su área de trabajo de Azure Data Factory o Synapse y seleccione "Servicios vinculados"; a continuación, haga clic en "Nuevo":

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Captura de pantalla de la creación de un servicio vinculado con la interfaz de usuario de Azure Synapse":::

2. Busque y seleccione el conector de Azure Data Lake Storage Gen1.

    :::image type="content" source="media/connector-azure-data-lake-store/azure-data-lake-store-connector.png" alt-text="Captura de pantalla del conector de Azure Data Lake Storage Gen1":::    

1. Configure los detalles del servicio, pruebe la conexión y cree el servicio vinculado.

    :::image type="content" source="media/connector-azure-data-lake-store/configure-azure-data-lake-store-linked-service.png" alt-text="Captura de pantalla de la configuración del servicio vinculado de Azure Data Lake Storage Gen1":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

En las siguientes secciones se proporciona información sobre las propiedades que se usan para definir entidades específicas de Azure Data Lake Store Gen 1.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Azure Data Lake Store:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` debe establecerse en **AzureDataLakeStore**. | Sí |
| dataLakeStoreUri | Información sobre la cuenta de Azure Data Lake Store. Esta información adopta uno de los siguientes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sí |
| subscriptionId | El identificador de la suscripción de Azure al que pertenece la cuenta de Data Lake Store. | Necesario para el receptor |
| resourceGroupName | El nombre del grupo de recursos de Azure al que pertenece la cuenta de Data Lake Store. | Necesario para el receptor |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado, si el almacén de datos se encuentra en una red privada. Si no se especifica esta propiedad, se usa el valor predeterminado de Azure Integration Runtime. |No |

### <a name="use-service-principal-authentication"></a>Uso de la autenticación de entidad de servicio

Antes de usar la autenticación de entidad de servicio, siga estos pasos.

1. Registre una entidad de aplicación en Azure Active Directory y concédale acceso a Data Lake Store. Consulte [Autenticación entre servicios](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md) para ver los pasos detallados. Anote los siguientes valores; los usará para definir el servicio vinculado:

    - Identificador de aplicación
    - Clave de la aplicación
    - Id. de inquilino

2. Conceda a la entidad de servicio el permiso adecuado. Consulte ejemplos sobre el funcionamiento del permiso en Data Lake Storage Gen1 en [Control de acceso en Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como origen**: en el **Explorador de datos** > **Acceso**, conceda al menos permiso de **ejecución** para TODAS las carpetas de nivel superior incluida la raíz, junto con el permiso de **lectura** para los archivos que se van a copiar. Puede elegir agregarlo a **Esta carpeta y todos los elementos secundarios** si desea que sea recursivo y agregarlo como **un permiso de acceso y una entrada de permiso predeterminada**. No hay ningún requisito en el control de acceso de nivel de cuenta (IAM).
    - **Como receptor**: en el **Explorador de datos** > **Acceso**, conceda al menos permiso de **ejecución** para TODAS las carpetas de nivel superior incluida la raíz, junto con el permiso de **escritura** para la carpeta del receptor. Puede elegir agregarlo a **Esta carpeta y todos los elementos secundarios** si desea que sea recursivo y agregarlo como **un permiso de acceso y una entrada de permiso predeterminada**.

Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique el id. de cliente de la aplicación. | Sí |
| servicePrincipalKey | Especifique la clave de la aplicación. Marque este campo como `SecureString` para almacenarlo de forma segura, o bien [haga referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| tenant | Especifique la información del inquilino, como el nombre de dominio o identificador de inquilino, en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí |
| azureCloudType | Para la autenticación de la entidad de servicio, especifique el tipo de entorno de nube de Azure en el que está registrada la aplicación de Azure Active Directory. <br/> Los valores permitidos son **AzurePublic**, **AzureChina**, **AzureUsGovernment** y **AzureGermany**. De manera predeterminada, se usa el entorno de nube del servicio. | No |

**Ejemplo**:

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> Uso de la autenticación de identidad administrada asignada por el sistema

Un área de trabajo de Data Factory o de Synapse se puede asociar con una [identidad administrada asignada por el sistema](data-factory-service-identity.md), que representa el servicio para la autenticación. Puede usar directamente esta identidad administrada asignada por el sistema para la autenticación en Data Lake Store, de forma similar a como usa su propia entidad de servicio. Permite que este recurso designado acceda a datos y los con Data Lake Store como origen o destino.

Para usar la autenticación de identidad administrada asignada por el sistema, siga estos pasos.

1. [Recupere la información de identidad administrada asignada por el sistema](data-factory-service-identity.md#retrieve-managed-identity); para ello, copie el valor del Id. de aplicación de identidad de servicio que se ha generado junto con su área de trabajo de Data Factory o Synapse.

2. Conceda a la identidad administrada asignada por el sistema acceso a Data Lake Store. Consulte ejemplos sobre el funcionamiento del permiso en Data Lake Storage Gen1 en [Control de acceso en Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como origen**: en el **Explorador de datos** > **Acceso**, conceda al menos permiso de **ejecución** para TODAS las carpetas de nivel superior incluida la raíz, junto con el permiso de **lectura** para los archivos que se van a copiar. Puede elegir agregarlo a **Esta carpeta y todos los elementos secundarios** si desea que sea recursivo y agregarlo como **un permiso de acceso y una entrada de permiso predeterminada**. No hay ningún requisito en el control de acceso de nivel de cuenta (IAM).
    - **Como receptor**: en el **Explorador de datos** > **Acceso**, conceda al menos permiso de **ejecución** para TODAS las carpetas de nivel superior incluida la raíz, junto con el permiso de **escritura** para la carpeta del receptor. Puede elegir agregarlo a **Esta carpeta y todos los elementos secundarios** si desea que sea recursivo y agregarlo como **un permiso de acceso y una entrada de permiso predeterminada**.

No es necesario que especifique ninguna propiedad, más allá de la información general de Data Lake Store en el servicio vinculado.

**Ejemplo**:

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-user-assigned-managed-identity-authentication"></a>Uso de la autenticación de identidad administrada asignada por el usuario

Una factoría de datos se puede asignar con una o varias [identidades administradas asignadas por el usuario](data-factory-service-identity.md). Puede usar esta identidad administrada asignada por el usuario para la autenticación de Blob Storage, lo que permite acceder a datos y copiarlos con Data Lake Storage como origen o destino. Para obtener más información sobre las identidades administradas para recursos de Azure, consulte [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md)

Para usar la autenticación de identidad administrada asignada por el usuario, siga estos pasos:

1. [Cree una o varias identidades administradas asignadas por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) y conceda acceso a Azure Data Lake Storage. Consulte ejemplos sobre el funcionamiento del permiso en Data Lake Storage Gen1 en [Control de acceso en Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como origen**: en el **Explorador de datos** > **Acceso**, conceda al menos permiso de **ejecución** para TODAS las carpetas de nivel superior incluida la raíz, junto con el permiso de **lectura** para los archivos que se van a copiar. Puede elegir agregarlo a **Esta carpeta y todos los elementos secundarios** si desea que sea recursivo y agregarlo como **un permiso de acceso y una entrada de permiso predeterminada**. No hay ningún requisito en el control de acceso de nivel de cuenta (IAM).
    - **Como receptor**: en el **Explorador de datos** > **Acceso**, conceda al menos permiso de **ejecución** para TODAS las carpetas de nivel superior incluida la raíz, junto con el permiso de **escritura** para la carpeta del receptor. Puede elegir agregarlo a **Esta carpeta y todos los elementos secundarios** si desea que sea recursivo y agregarlo como **un permiso de acceso y una entrada de permiso predeterminada**.
    
2. Asigne una o varias identidades administradas asignadas por el usuario a la factoría de datos y [cree credenciales](data-factory-service-identity.md#credentials) para cada identidad. 

Se admite la siguiente propiedad:

| Propiedad | Descripción | Requerido |
|:--- |:--- |:--- |
| credentials | Especifique la identidad administrada asignada por el usuario como objeto de credencial. | Sí |

**Ejemplo**:

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Las propiedades siguientes se admiten para Azure Data Lake Store Gen1 en la configuración `location` del conjunto de datos basado en formato:

| Propiedad   | Descripción                                                  | Obligatorio |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propiedad type de `location` del conjunto de datos debe establecerse en **AzureDataLakeStoreLocation**. | Sí      |
| folderPath | Ruta de acceso a una carpeta. Si quiere usar un carácter comodín para filtrar carpetas, omita este valor y especifíquelo en la configuración del origen de actividad. | No       |
| fileName   | Nombre de archivo en la propiedad folderPath indicada. Si quiere usar un carácter comodín para filtrar archivos, omita este valor y especifíquelo en la configuración del origen de actividad. | No       |

**Ejemplo**:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte [Canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el receptor y el origen de Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como origen

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Las propiedades siguientes se admiten para Azure Data Lake Store Gen1 en la configuración `storeSettings` del origen de copia basado en formato:

| Propiedad                 | Descripción                                                  | Obligatorio                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| type                     | La propiedad type de `storeSettings` se debe establecer en **AzureDataLakeStoreReadSettings**. | Sí                                          |
| ***Buscar los archivos que se van a copiar:*** |  |  |
| OPCIÓN 1: ruta de acceso estática<br> | Copia de la ruta de acceso de archivo o carpeta especificada en el conjunto de datos. Si quiere copiar todos los archivos de una carpeta, especifique también `wildcardFileName` como `*`. |  |
| OPCIÓN 2: intervalo de nombres<br>- listAfter | Recupera las carpetas o los archivos cuyo nombre aparece después de este valor por orden alfabético (exclusivo). Usa el filtro del lado de servicio para ADLS Gen1, que proporciona un mejor rendimiento que un filtro de caracteres comodín. <br/>El servicio aplica este filtro a la ruta de acceso definida en el conjunto de datos, y solo se admite un nivel de entidad. Consulte más ejemplos en [Ejemplos de filtros de intervalos de nombres](#name-range-filter-examples). | No |
| OPCIÓN 2: intervalo de nombres<br/>- listBefore | Recupera las carpetas o los archivos cuyo nombre aparece antes de este valor por orden alfabético (inclusivo). Usa el filtro del lado de servicio para ADLS Gen1, que proporciona un mejor rendimiento que un filtro de caracteres comodín.<br>El servicio aplica este filtro a la ruta de acceso definida en el conjunto de datos, y solo se admite un nivel de entidad. Consulte más ejemplos en [Ejemplos de filtros de intervalos de nombres](#name-range-filter-examples). | No |
| OPCIÓN 3: carácter comodín<br>- wildcardFolderPath | Ruta de acceso de carpeta con caracteres comodín para filtrar las carpetas de origen. <br>Los caracteres comodín permitidos son: `*` (coincide con cero o más caracteres) y `?` (coincide con cero o carácter individual); use `^` para el escape si el nombre real de la carpeta tiene un carácter comodín o este carácter de escape dentro. <br>Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | No                                            |
| OPCIÓN 3: carácter comodín<br>- wildcardFileName | Nombre de archivo con caracteres comodín en la propiedad folderPath o wildcardFolderPath indicada para filtrar los archivos de origen. <br>Los caracteres comodín permitidos son: `*` (coincide con cero o más caracteres) y `?` (coincide con cero o carácter individual); use `^` para el escape si el nombre real del archivo tiene un carácter comodín o este carácter de escape dentro.  Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | Sí |
| OPCIÓN 4: una lista de archivos<br>- fileListPath | Indica que se copie un conjunto de archivos determinado. Apunte a un archivo de texto que incluya una lista de los archivos que quiere copiar, con un archivo por línea, que sea la ruta de acceso relativa a la ruta de acceso configurada en el conjunto de datos.<br/>Al utilizar esta opción, no especifique el nombre de archivo en el conjunto de datos. Ver más ejemplos en [Ejemplos de lista de archivos](#file-list-examples). |No |
| ***Configuración adicional:*** |  | |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Tenga en cuenta que cuando recursive se establece en true y el receptor es un almacén basado en archivos, no se crea una carpeta o una subcarpeta vacía en el receptor. <br>Los valores permitidos son: **True** (valor predeterminado) y **False**.<br>Esta propiedad no se aplica al configurar `fileListPath`. |No |
| deleteFilesAfterCompletion | Indica si los archivos binarios se eliminarán del almacén de origen después de moverse correctamente al almacén de destino. Cada archivo se elimina individualmente, de modo que cuando se produzca un error en la actividad de copia, algunos archivos ya se habrán copiado al destino y se habrán eliminado del origen, mientras que otros seguirán aún en el almacén de origen. <br/>Esta propiedad solo es válida en el escenario de copia de archivos binarios. El valor predeterminado es false. |No |
| modifiedDatetimeStart    | Filtro de archivos basado en el atributo: Última modificación. <br>Los archivos se seleccionarán si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br> Las propiedades pueden ser NULL, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos.  Cuando `modifiedDatetimeStart` tiene el valor de fecha y hora, pero `modifiedDatetimeEnd` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora.  Cuando `modifiedDatetimeEnd` tiene el valor de fecha y hora, pero `modifiedDatetimeStart` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea inferior al valor de fecha y hora.<br/>Esta propiedad no se aplica al configurar `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Igual que el anterior.                                               | No                                           |
| enablePartitionDiscovery | En el caso de archivos con particiones, especifique si quiere analizar las particiones de la ruta de acceso del archivo y agregarlas como columnas de origen adicionales.<br/>Los valores permitidos son **false** (valor predeterminado) y **true**. | No                                            |
| partitionRootPath | Cuando esté habilitada la detección de particiones, especifique la ruta de acceso raíz absoluta para poder leer las carpetas con particiones como columnas de datos.<br/><br/>Si no se especifica, de forma predeterminada,<br/>- Cuando se usa la ruta de acceso de archivo en un conjunto de datos o una lista de archivos del origen, la ruta de acceso raíz de la partición es la ruta de acceso configurada en el conjunto de datos.<br/>- Cuando se usa el filtro de carpeta con caracteres comodín, la ruta de acceso raíz de la partición es la subruta antes del primer carácter comodín.<br/><br/>Por ejemplo, supongamos que configura la ruta de acceso en el conjunto de datos como "root/folder/year=2020/month=08/day=27":<br/>- Si especifica la ruta de acceso raíz de la partición como "root/folder/year=2020", la actividad de copia generará dos columnas más, `month` y `day`, con el valor "08" y "27", respectivamente, además de las columnas de los archivos.<br/>- Si no se especifica la ruta de acceso raíz de la partición, no se generará ninguna columna adicional. | No                                            |
| maxConcurrentConnections | Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No                                           |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como receptor

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)]

Las propiedades siguientes se admiten para Azure Data Lake Store Gen1 en la configuración `storeSettings` del receptor de copia basado en formato:

| Propiedad                 | Descripción                                                  | Obligatorio |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propiedad type de `storeSettings` se debe establecer en **AzureDataLakeStoreWriteSettings**. | Sí      |
| copyBehavior             | Define el comportamiento de copia cuando el origen son archivos de un almacén de datos basados en archivos.<br/><br/>Los valores permitidos son:<br/><b>- PreserveHierarchy (valor predeterminado)</b>: conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica que la ruta de acceso relativa del archivo de destino a la carpeta de destino.<br/><b>- FlattenHierarchy</b>: todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombres generados automáticamente. <br/><b>- MergeFiles</b>: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre del archivo, el nombre de archivo combinado es el nombre especificado. De lo contrario, es un nombre de archivo generado automáticamente. | No       |
| expiryDateTime | Especifica la hora de expiración de los archivos escritos. La hora se aplica a la hora UTC en el formato "2020-03-01T08:00:00Z". Es NULL de forma predeterminada, lo que significa que los archivos escritos nunca expiran. | No |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No       |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```
### <a name="name-range-filter-examples"></a>Ejemplos de filtros de intervalos de nombres

En esta sección se describe el comportamiento resultante de los filtros de intervalos de nombres.

| Estructura de origen de ejemplo | Configuración | Resultado |
|:--- |:--- |:--- |
|root<br/>&nbsp;&nbsp;&nbsp;&nbsp;a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;c<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| **En el conjunto de datos:**<br>- Ruta de acceso a la carpeta: `root`<br><br>**En origen de la actividad de copia:**<br>- Enumerar después de: `a`<br>- Enumerar antes de: `b`| A continuación, se copiarán los siguientes archivos:<br><br>root<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

### <a name="folder-and-file-filter-examples"></a>Ejemplos de filtros de carpetas y archivos

Esta sección describe el comportamiento resultante de la ruta de acceso de la carpeta y el nombre de archivo con los filtros de carácter comodín.

| folderPath | fileName | recursive | Resultado de estructura de carpeta de origen y filtro (se recuperan los archivos en **negrita**)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vacío, usar el valor predeterminado) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vacío, usar el valor predeterminado) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Ejemplos de lista de archivos

En esta sección se describe el comportamiento resultante de usar la ruta de acceso de la lista de archivos en el origen de la actividad de copia.

Suponga que tiene la siguiente estructura de carpetas de origen y quiere copiar los archivos en negrita:

| Estructura de origen de ejemplo                                      | Contenido de FileListToCopy.txt                             | Configuración |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatos<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **En el conjunto de datos:**<br>- Ruta de acceso a la carpeta: `root/FolderA`<br><br>**En origen de la actividad de copia:**<br>- Ruta de acceso de la lista de archivos: `root/Metadata/FileListToCopy.txt` <br><br>La ruta de acceso de la lista de archivos apunta a un archivo de texto en el mismo almacén de datos que incluye una lista de archivos que se quiere copiar, un archivo por línea con la ruta de acceso relativa a la ruta de acceso configurada en el conjunto de datos. |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Ejemplos de comportamiento de la operación de copia

En esta sección se describe el comportamiento resultante de la operación de copia para diferentes combinaciones de los valores `recursive` y `copyBehavior`.

| recursive | copyBehavior | Estructura de carpetas de origen | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | El destino Folder1 se crea con la misma estructura que el origen:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 + File3 + File4 + File5 se combina en un archivo con un nombre de archivo generado automáticamente. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>No se selecciona la subcarpeta Subfolder1, que contiene los archivos File3, File4 y File5. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/><br/>No se selecciona la subcarpeta Subfolder1, que contiene los archivos File3, File4 y File5. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 se combina en un archivo con un nombre de archivo generado automáticamente. nombre de archivo generado automáticamente para File1<br/><br/>No se selecciona la subcarpeta Subfolder1, que contiene los archivos File3, File4 y File5. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Conservación de ACL en Data Lake Storage Gen2

>[!TIP]
>Si desea copiar datos desde Azure Data Lake Storage Gen1 en Gen2 en general, consulte [Copia de datos desde Azure Data Lake Storage Gen1 a Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) para ver un tutorial y los procedimientos recomendados.

Si quiere replicar las listas de control de acceso (ACL) junto con los archivos de datos cuando se actualiza desde Data Lake Storage Gen1 a Data Lake Storage Gen2, consulte [Conservar las ACL de Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Propiedades de Asignación de instancias de Data Flow

Al transformar datos en los flujos de datos de asignación, puede leer y escribir archivos de Azure Data Lake Storage Gen1 en los siguientes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Formato ORC](format-orc.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

La configuración específica de formato se encuentra en la documentación de ese formato. Para obtener más información, consulte los artículos sobre la [transformación de origen en un flujo de datos de asignación](data-flow-source.md) y la [transformación de receptor en un flujo de datos de asignación](data-flow-sink.md).

### <a name="source-transformation"></a>Transformación de origen

En la transformación de origen, puede leer de un contenedor, una carpeta o un archivo individual en Azure Data Lake Storage Gen1. La pestaña **Opciones de origen** permite administrar cómo se leen los archivos. 

:::image type="content" source="media/data-flow/sourceOptions1.png" alt-text="Opciones de origen":::

**Ruta de acceso con carácter comodín:** el uso de un patrón de caracteres comodín indicará al servicio que recorra todos los archivos y carpetas que coincidan en una única transformación de origen. Se trata de una manera eficaz de procesar varios archivos en un único flujo. Agregue varios patrones de coincidencia de caracteres comodín con el signo + que aparece al desplazar el puntero sobre el patrón de caracteres comodín existente.

En el contenedor de origen, elija una serie de archivos que coincidan con un patrón. Solo se puede especificar un contenedor en el conjunto de datos. La ruta de acceso con carácter comodín, por tanto, también debe incluir la ruta de acceso de la carpeta de la carpeta raíz.

Ejemplos de caracteres comodín:

* ```*``` Representa cualquier conjunto de caracteres.
* ```**``` Representa el anidamiento recursivo de directorios.
* ```?``` Reemplaza un carácter.
* ```[]``` Coincide con uno de los caracteres entre corchetes.

* ```/data/sales/**/*.csv``` Obtiene todos los archivos csv que se encuentra en /data/sales.
* ```/data/sales/20??/**/``` Obtiene todos los archivos del siglo XX.
* ```/data/sales/*/*/*.csv``` Obtiene los archivos CSV en dos niveles bajo /data/sales.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtiene todos los archivos csv de diciembre de 2004 que comienzan con X o Y precedido por un número de dos dígitos.

**Partition Root Path:** (Ruta de acceso de la raíz de la partición) Si tiene carpetas con particiones en el origen de archivo con un formato ```key=value``` (por ejemplo, year=2019), puede asignar el nivel superior del árbol de carpetas de la partición a un nombre de columna del flujo de datos.

En primer lugar, establezca un comodín que incluya todas las rutas de acceso que sean carpetas con particiones y, además, los archivos de hoja que desee leer.

:::image type="content" source="media/data-flow/partfile2.png" alt-text="Configuración del archivo de origen de partición":::

Use el valor de Partition Root Path (Ruta de acceso de la raíz de la partición) para definir cuál es el nivel superior de la estructura de carpetas. Cuando consulte el contenido de los datos mediante una vista previa, verá que el servicio agrega las particiones resueltas encontradas en cada uno de los niveles de carpeta.

:::image type="content" source="media/data-flow/partfile1.png" alt-text="Ruta de acceso raíz de la partición":::

**Lista de archivos**: Se trata de un conjunto de archivos. Cree un archivo de texto que incluya una lista de archivos de ruta de acceso relativa para procesar. Apunte a este archivo de texto.

**Column to store file name:** (Columna para almacenar el nombre de archivo) Almacene el nombre del archivo de origen en una columna de los datos. Escriba aquí el nombre de una nueva columna para almacenar la cadena de nombre de archivo.

**After completion:** (Tras finalizar) Elija no hacer nada con el archivo de origen después de que se ejecute el flujo de datos o bien elimine o mueva el archivo de origen. Las rutas de acceso para mover los archivos de origen son relativas.

Para mover archivos de origen a otra ubicación posterior al procesamiento, primero seleccione "Mover" para la operación de archivo. A continuación, establezca el directorio "from". Si no usa ningún carácter comodín para la ruta de acceso, la configuración de "from" será la misma carpeta que la carpeta de origen.

Si tiene una ruta de acceso de origen con un comodín, su sintaxis será como esta:

`/data/sales/20??/**/*.csv`

puede especificar "from" como

`/data/sales`

y "to" como

`/backup/priorSales`

En este caso, todos los subdirectorios cuyo origen se encuentra en /data/sales se mueven a /backup/priorSales.

> [!NOTE]
> Las operaciones de archivo solo se ejecutan cuando el flujo de datos se inicia desde una ejecución de canalización (depuración o ejecución de canalización) que usa la actividad de ejecución de Data Flow de una canalización. Las operaciones de archivo *no* se ejecutan en modo de depuración de Data Flow.

**Filter by last modified:** (Filtrar últimos modificados) Puede filtrar los archivos que desea procesar especificando un intervalo de fechas de la última vez que se modificaron. Todas las horas están en formato UTC. 

### <a name="sink-properties"></a>Propiedades del receptor

En la transformación de receptor, puede escribir en un contenedor o una carpeta en Azure Data Lake Storage Gen1. La pestaña **Configuración** permite administrar cómo se escriben los archivos.

:::image type="content" source="media/data-flow/file-sink-settings.png" alt-text="Opciones del receptor":::

**Clear the folder** (Borrar la carpeta): determina si se borra o no la carpeta de destino antes de escribir los datos.

**File name option** (Opción de nombre de archivo): determina cómo se denominan los archivos de destino en la carpeta de destino. Las opciones de nombre de archivo son:
   * **Valor predeterminado**: permita que Spark nombre los archivos según los valores predeterminados de PART.
   * **Patrón**: escriba un patrón que enumere los archivos de salida por partición. Por ejemplo, **loans[n].csv** creará loans1.csv, loans2.csv, etc.
   * **Por partición**: escriba un nombre de archivo por partición.
   * **Como datos de columna**: establezca el archivo de salida en el valor de una columna. La ruta de acceso es relativa al contenedor del conjunto de datos, no a la carpeta de destino. Si tiene una ruta de acceso de carpeta en el conjunto de datos, se invalidará.
   * **Salida en un solo archivo**: combine los archivos de salida con particiones en un solo archivo con nombre. La ruta de acceso es relativa a la carpeta del conjunto de datos. Tenga en cuenta que la operación de combinación probablemente produzca un error en función del tamaño del nodo. Esta opción no se recomienda para conjuntos de datos de gran tamaño.

**Quote all** (Entrecomillar todo): determina si se deben entrecomillar todos los valores.

## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propiedades de la actividad GetMetadata

Para información detallada sobre las propiedades, consulte [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propiedades de la actividad de eliminación

Para información detallada sobre las propiedades, consulte [Actividad de eliminación](delete-activity.md).

## <a name="legacy-models"></a>Modelos heredados

>[!NOTE]
>Estos modelos siguen siendo compatibles con versiones anteriores. Se recomienda usar de ahora en adelante el nuevo modelo que se menciona en las secciones anteriores; además, la interfaz de usuario de creación ha pasado a generar el nuevo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de datos heredado

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **AzureDataLakeStoreFile**. |Sí |
| folderPath | Ruta de acceso a la carpeta de Data Lake Store. Si no se especifica, apunta a la raíz. <br/><br/>Se admiten filtros con caracteres comodín. Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre real de la carpeta contiene un carácter comodín o este carácter de escape. <br/><br/>Por ejemplo: rootfolder/subfolder/. Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). |No |
| fileName | Filtro de nombre o de comodín para los archivos de la ruta "folderPath" especificada. Si no especifica ningún valor para esta propiedad, el conjunto de datos apunta a todos los archivos de la carpeta. <br/><br/>Para filtrar, los caracteres comodín permitidos son `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter).<br/>- Ejemplo 1: `"fileName": "*.csv"`<br/>- Ejemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape si el nombre de archivo real contiene un comodín o este carácter de escape.<br/><br/>Cuando fileName no se especifica para un conjunto de datos de salida y **preserveHierarchy** no se determina en el receptor de la actividad, la actividad de copia generará automáticamente el nombre de archivo con el siguiente patrón: "*Data.[GUID de Id. de ejecución de actividad].[GUID si FlattenHierarchy].[formato si se ha configurado].[compresión si se ha configurado]* ", por ejemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Si realiza una copia desde el origen tabular usando el nombre de una tabla en lugar de una consulta, el patrón del nombre es " *[nombre de tabla].[formato].[compresión, si está configurada]* ", por ejemplo, "MyTable.csv". |No |
| modifiedDatetimeStart | Filtro de archivos basado en el atributo Last Modified. Los archivos se seleccionan si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br/><br/> El rendimiento general del movimiento de datos se ve afectado si habilita esta configuración cuando quiere filtrar archivos con grandes cantidades de archivos. <br/><br/> Las propiedades pueden ser NULL, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos. Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora. Cuando `modifiedDatetimeEnd` tiene el valor de fecha y hora, pero `modifiedDatetimeStart` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea inferior al valor de fecha y hora.| No |
| modifiedDatetimeEnd | Filtro de archivos basado en el atributo Last Modified. Los archivos se seleccionan si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br/><br/> El rendimiento general del movimiento de datos se ve afectado si habilita esta configuración cuando quiere filtrar archivos con grandes cantidades de archivos. <br/><br/> Las propiedades pueden ser NULL, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos. Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora. Cuando `modifiedDatetimeEnd` tiene el valor de fecha y hora, pero `modifiedDatetimeStart` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea inferior al valor de fecha y hora.| No |
| format | Si quiere copiar los archivos tal cual entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida.<br/><br/>Si quiere analizar o generar archivos con un formato concreto, se admiten los siguientes tipos de formato de archivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** en **format** en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format) y [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo para el escenario de copia binaria) |
| compression | Especifique el tipo y el nivel de compresión de los datos. Para más información, consulte el artículo sobre [códecs de compresión y formatos de archivo compatibles](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**.<br/>Niveles admitidos son **Optimal** y **Fastest**. |No |

>[!TIP]
>Para copiar todos los archivos en una carpeta, especifique solo **folderPath**.<br>Para copiar un único archivo con un nombre concreto, especifique **folderPath** con una parte de la carpeta y **fileName** con un nombre de archivo.<br>Para copiar un subconjunto de archivos en una carpeta, especifique **folderPath** con un elemento de carpeta y **fileName** con un filtro de comodín. 

**Ejemplo**:

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modelo de origen de actividad de copia heredada

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` del origen de la actividad de copia debe establecerse en **AzureDataLakeStoreSource**. |Sí |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Cuando `recursive` se establece en true y el receptor es un almacén basado en archivos, no se copia ni crea ninguna carpeta o subcarpeta vacías en el receptor. Los valores permitidos son: **True** (valor predeterminado) y **False**. | No |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Modelo de receptor de actividad de copia heredada

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` del receptor de la actividad de copia debe establecerse en **AzureDataLakeStoreSink**. |Sí |
| copyBehavior | Define el comportamiento de copia cuando el origen son archivos de un almacén de datos basados en archivos.<br/><br/>Los valores permitidos son:<br/><b>- PreserveHierarchy (valor predeterminado)</b>: conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica que la ruta de acceso relativa del archivo de destino a la carpeta de destino.<br/><b>- FlattenHierarchy</b>: todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombres generados automáticamente. <br/><b>- MergeFiles</b>: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre del archivo, el nombre de archivo combinado es el nombre especificado. En caso contrario, el nombre de archivo se genera automáticamente. | No |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, vea [Almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
