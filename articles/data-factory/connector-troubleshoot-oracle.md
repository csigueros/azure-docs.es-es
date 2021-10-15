---
title: Solución de problemas del conector de Oracle
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector de Oracle en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0450faa94af3c01fccebc618c5f91094447116f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390431"
---
# <a name="troubleshoot-the-oracle-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de Oracle en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de Oracle en Azure Data Factory y Azure Synapse.

## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Código de error: ArgumentOutOfRangeException

- **Mensaje**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Causa**: en las canalizaciones de Azure Data Factory y Synapse, los valores DateTime se admiten en el intervalo comprendido entre 0001-01-01 00:00:00 y 9999-12-31 23:59:59. Pero Oracle admite un intervalo más amplio de valores DateTime (como siglos AC o min/s > 59), lo que da lugar a un error.

- **Recomendación:** 

    Para ver si el valor de Oracle está en el intervalo de fechas admitido, ejecute `select dump(<column name>)`. 

    Para conocer la secuencia de bytes en el resultado, consulte [¿Cómo se almacenan las fechas en Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
