---
title: Solución de problemas del conector de formato de texto delimitado
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo solucionar problemas con el conector de formato de texto delimitado en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 42346a5922b0f607e467e495455166057248db5e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390457"
---
# <a name="troubleshoot-the-delimited-text-format-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de formato de texto delimitado en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de formato de texto delimitado en Azure Data Factory y Azure Synapse.

## <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Código de error: DelimitedTextColumnNameNotAllowNull

- **Mensaje**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Cuando se establece "firstRowAsHeader" en la actividad, se usa la primera fila como nombre de columna. Este error significa que la primera fila contiene un valor vacío (por ejemplo, "ColumnA, ColumnB").

- **Recomendación:**  Compruebe la primera fila y corrija el valor si está vacío.


## <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Código de error: DelimitedTextMoreColumnsThanDefined

- **Mensaje**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

  | Análisis de las causas                                               | Recomendación                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | el número de columnas de la fila problemática es mayor que el recuento de columnas de la primera fila. La causa podría ser un problema con los datos o con una configuración incorrecta del delimitador de columna o del carácter de comillas. | Obtenga el número de filas del mensaje de error, compruebe la columna de la fila y corrija los datos. |
  | Si el número de columnas esperado es "1" en el mensaje de error, es posible que haya especificado una configuración errónea de compresión o formato, lo que ha causado que se analicen los archivos de manera incorrecta. | Compruebe la configuración de formato para asegurarse de que coincide con la de los archivos de origen. |
  | Si el origen es una carpeta, es posible que los archivos de la carpeta especificada tengan otro esquema. | Asegúrese de que los archivos de la carpeta especificada tengan un esquema idéntico. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
