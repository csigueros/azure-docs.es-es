---
title: Comprobación de la coherencia de los datos en la actividad de copia
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a habilitar la comprobación de la coherencia de datos en una actividad de copia en canalizaciones de Azure Data Factory y Synapse Analytics.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: yexu
ms.openlocfilehash: a79ec65dd9826db51042a8dfde74de6b563355be
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798795"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Comprobación de la coherencia de los datos en la actividad de copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cuando se mueven datos del almacén de origen al de destino, la actividad de copia proporciona una opción para realizar una comprobación adicional de la coherencia de los datos, con el fin de garantizar no solo que estos se copien correctamente del almacén de origen al de destino, sino que sean coherentes entre ambos almacenes. Si se encuentran archivos incoherentes durante el movimiento de datos, puede anular la actividad de copia o habilitar la opción de configuración de tolerancia a errores para seguir copiando el resto y omitir los archivos incoherentes. Para obtener los nombres de los archivos omitidos, habilite la configuración del registro de sesión en la actividad de copia. Puede consultar [Registro de sesión en la actividad de copia](copy-activity-log.md) para más detalles.

## <a name="supported-data-stores-and-scenarios"></a>Almacenes de datos y escenarios que se admiten

-   La comprobación de la coherencia de los datos es compatible con todos los conectores, excepto FTP, sFTP y HTTP. 
-   No se admite la comprobación de la coherencia de los datos en el escenario de copia de almacenamiento provisional.
-   Al copiar archivos binarios, la comprobación de la coherencia de los datos solo está disponible al establecerse el comportamiento de "PreserveHierarchy" en la actividad de copia.
-   Al copiar varios archivos binarios en una actividad de copia con la comprobación de la coherencia de los datos habilitada, tiene la opción de anular la actividad de copia o habilitar la opción de configuración de tolerancia a errores para seguir copiando el resto y omitir los archivos incoherentes. 
-   Al copiar una tabla en una actividad de copia con la comprobación de la coherencia de los datos habilitada, se producirá un error en la actividad de copia si el número de filas leídas desde el origen es distinto del número de filas copiadas en el destino más el número de filas incompatibles omitidas.


## <a name="configuration"></a>Configuración
En el ejemplo siguiente se proporciona una definición de JSON para habilitar la comprobación de la coherencia de datos en la actividad de copia: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Si establece true para esta propiedad, al copiar archivos binarios, la actividad de copia comprobará el tamaño del archivo, el valor de lastModifiedDate y la suma de comprobación de MD5 de cada archivo binario copiado del almacén de origen al de destino para garantizar la coherencia de los datos entre el almacén de origen y el de destino. Al copiar datos tabulares, la actividad de copia comprobará el recuento total de filas después de completarse el trabajo para garantizar que el número total de filas leídas desde el origen sea el mismo que el número de filas copiadas en el destino más el número de filas incompatibles omitidas. Tenga en cuenta que el rendimiento de la copia se verá afectado al habilitar esta opción.  | True<br/>False (valor predeterminado) | No
dataInconsistency | Uno de los pares clave-valor dentro del contenedor de propiedades de skipErrorFile para determinar si desea omitir los archivos incoherentes. <br/> \- True: desea copiar el resto omitiendo los archivos incoherentes.<br/> - False: desea anular la actividad de copia una vez que se encuentre el archivo incoherente.<br/>Tenga en cuenta que esta propiedad solo es válida cuando se copian archivos binarios y se establece validateDataConsistency como True.  | True<br/>False (valor predeterminado) | No
logSettings | Grupo de propiedades que puede especificarse para habilitar el registro de la sesión a fin de registrar los archivos omitidos. | | No
linkedServiceName | Servicio vinculado de [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para almacenar los archivos de registro de sesión. | Nombres de un servicio vinculado a los tipos `AzureBlobStorage` o `AzureBlobFS`, que hace referencia a la instancia que usa para almacenar los archivos de registro. | No
path | Ruta de acceso de los archivos de registro. | Especifique la ruta de acceso donde quiere almacenar los archivos de registro. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para usted. | No

>[!NOTE]
>- Al copiar archivos binarios desde Blob de Azure o Azure Data Lake Storage Gen2 o desde ahí, el servicio realiza la comprobación de la suma de comprobación MD5 de nivel de bloque mediante la [API de Blob de Azure](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) y la [API de Azure Data Lake Storage Gen2](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Si ContentMD5 en los archivos se encuentra en Blob de Azure o en Azure Data Lake Storage Gen2 como orígenes de datos, el servicio realiza la comprobación de la suma de comprobación de MD5 del nivel de archivo también después de leer los archivos. Después de copiar los archivos en Blob de Azure o en Azure Data Lake Storage Gen2 como destino de los datos, el servicio escribe ContentMD5 en Blob de Azure o en Azure Data Lake Storage Gen2, y las aplicaciones de nivel inferior pueden usarlo después para la comprobación de la coherencia de los datos.
>- El servicio realiza la comprobación del tamaño de archivo al copiar archivos binarios entre almacenes.

## <a name="monitoring"></a>Supervisión

### <a name="output-from-copy-activity"></a>Salida de la actividad de copia
Cuando la actividad de copia se ha ejecutado completamente, puede ver el resultado de la comprobación de la coherencia de datos desde la salida de cada ejecución de la actividad de copia:

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
Puede ver los detalles de la comprobación de la coherencia de datos desde la "propiedad dataConsistencyVerification".

Valor de **VerificationResult**: 
-   **Verified**:  se ha comprobado que los datos copiados son coherentes entre el almacén de origen y el de destino. 
-   **NotVerified**: no se ha comprobado que los datos copiados sean coherentes porque no se ha habilitado validateDataConsistency en la actividad de copia. 
-   **No admitidas**: no se ha comprobado que los datos copiados sean coherentes porque la comprobación de la coherencia de datos no se admite para este par de copia concreto. 

Valor de **InconsistentData**: 
-   **Encontrado**: la actividad de copia encontró datos incoherentes. 
-   **Omitido**: la actividad de copia encontró y omitió datos incoherentes. 
-   **Ninguno**: la actividad de copia no encontró datos incoherentes. Puede deberse a que se ha comprobado que los datos son coherentes entre el almacén de origen y el de destino, o bien porque se ha deshabilitado validateDataConsistency en la actividad de copia. 

### <a name="session-log-from-copy-activity"></a>Registro de sesión de la actividad de copia

Si configura el registro del archivo incoherente, puede encontrar el archivo de registro en esta ruta de acceso: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`.  Los archivos de registro serán los archivos CSV. 

El esquema de un archivo de registro es el siguiente:

Columna | Descripción 
-------- | -----------  
Timestamp | Marca de tiempo de cuando el servicio omite los archivos incoherentes.
Nivel | Nivel de registro de este elemento. Estará en el nivel "Warning" para el elemento que muestra la omisión del archivo.
OperationName | Comportamiento operativo de la actividad de copia en cada archivo. Será "FileSkip" para especificar el archivo que se va a omitir.
OperationItem | Nombre del archivo que se va a omitir.
Message | Más información para ilustrar por qué se omiten los archivos.

El ejemplo de un archivo de registro es el siguiente: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
En el archivo de registro anterior, puede ver que sample1.csv se ha omitido porque no se pudo comprobar su coherencia entre el almacén de origen y el de destino. El archivo sample1.csv se vuelve incoherente porque otras aplicaciones lo han modificado mientras la actividad de copia realizaba su función al mismo tiempo. 



## <a name="next-steps"></a>Pasos siguientes
Consulte los otros artículos de la actividad de copia:

- [Información general de la actividad de copia](copy-activity-overview.md)
- [Tolerancia a errores de la actividad de copia](copy-activity-fault-tolerance.md)