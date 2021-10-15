---
title: Solución de problemas del conector de Azure Blob Storage
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector de Azure Blob Storage en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e9a1c085e3d1d88d0897d46b924c5a9a21c747a2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390428"
---
# <a name="troubleshoot-the-azure-blob-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de Azure Blob Storage en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Azure Blob Storage en Azure Data Factory y Azure Synapse.

## <a name="error-code-azurebloboperationfailed"></a>Código de error: AzureBlobOperationFailed

- **Mensaje**: "Error en la operación de blob. ContainerName: %containerName;, ruta de acceso: %path;."

- **Causa**: Problema con la operación de Blob Storage.

- **Recomendación:**  Para consultar los detalles del error, vaya a [Códigos de error de Blob Storage](/rest/api/storageservices/blob-service-error-codes). Si necesita más ayuda, póngase en contacto con el equipo de Blob Storage.


## <a name="invalid-property-during-copy-activity"></a>Propiedad no válida durante la actividad de copia

- **Mensaje**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Causa**: El tipo definido en el conjunto de datos no es coherente con el tipo de origen o de receptor definido en la actividad de copia.

- **Solución:** Edite la definición JSON del conjunto de datos o la canalización para que los tipos sean coherentes y vuelva a ejecutar la implementación.

## <a name="error-code-fipsmodeisnotsupport"></a>Código de error: FIPSModeIsNotSupport

- **Mensaje**: `Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **Causa**: la directiva FIPS está habilitada en la máquina virtual en la que se ha instalado el entorno de ejecución de integración autohospedado.

- **Recomendación**: deshabilite el modo FIPS en la máquina virtual en la que se haya instalado el entorno de ejecución de integración autohospedado. Windows no recomienda el modo FIPS.

## <a name="error-code-azureblobinvalidblocksize"></a>Código de error: AzureBlobInvalidBlockSize

- **Mensaje**: `Block size should between %minSize; MB and 100 MB.`

- **Causa**: el tamaño del bloque supera el límite del blob.

## <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>Código de error: AzureStorageOperationFailedConcurrentWrite

- **Mensaje**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **Causa**: tiene varias ejecuciones simultáneas de actividad de copia o aplicaciones que escriben en el mismo archivo.

## <a name="error-code-azureappendblobconcurrentwriteconflict"></a>Código de error: AzureAppendBlobConcurrentWriteConflict

- **Mensaje**: `Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **Causa**: se producen varias solicitudes de escritura simultáneas, lo que provoca conflictos en el contenido del archivo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
