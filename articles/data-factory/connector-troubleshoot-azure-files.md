---
title: Solución de problemas del conector de Azure Files
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector de Azure Files en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 809bf30f1e11eaa7741ac0581881479d854ed4cb
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390439"
---
# <a name="troubleshoot-the-azure-files-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de Azure Files en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Azure Files en Azure Data Factory y Azure Synapse.

## <a name="error-code-azurefileoperationfailed"></a>Código de error: AzureFileOperationFailed

- **Mensaje**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Causa**: Problema con la operación de almacenamiento de Azure Files.

- **Recomendación:**  Para consultar los detalles del error, vea [Ayuda de Azure Files](/rest/api/storageservices/file-service-error-codes). Para recibir ayuda adicional, póngase en contacto con el equipo de Azure Files.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
