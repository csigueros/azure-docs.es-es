---
title: Solución de problemas del conector de Azure Table Storage
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector de Azure Table Storage en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e99a31f4ca0380d8bbc12f941248d745e0c7f74c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390424"
---
# <a name="troubleshoot-the-azure-table-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de Azure Table Storage en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Azure Table Storage en Azure Data Factory y Azure Synapse.

## <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Código de error: AzureTableDuplicateColumnsFromSource

- **Mensaje**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Causa**: Se pueden producir columnas de origen duplicadas por uno de los siguientes motivos:
   * Usa la base de datos como una combinación de tablas de origen y tablas aplicadas.
   * Tiene archivos CSV no estructurados con nombres de columna duplicados en la fila de encabezado.

- **Recomendación:**  Vuelva a comprobar y corrija las columnas de origen, según sea necesario.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
