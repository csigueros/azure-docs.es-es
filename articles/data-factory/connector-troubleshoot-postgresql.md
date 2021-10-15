---
title: Solución de problemas del conector de Azure Database for PostgreSQL
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector de Azure Database for PostgreSQL en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8613c6b17007f51caf437fb2d66d2d8e17965007
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390421"
---
# <a name="troubleshoot-the-azure-database-for-postgresql-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de Azure Database for PostgreSQL en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Azure Database for PostgreSQL en Azure Data Factory y Azure Synapse.

## <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>Código de error: AzurePostgreSqlNpgsqlDataTypeNotSupported

- **Mensaje**: `The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **Recomendación**: elija una columna de partición con int, bigint, smallint, serial, bigserial, smallserial, timestamp con o sin zona horaria y hora sin el tipo de datos de zona horaria o fecha.

## <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>Código de error: AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **Mensaje**: `Partition column name must be specified.`

- **Causa**: no se proporciona ningún nombre de columna de partición y no se ha podido decidir automáticamente.
 
## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
