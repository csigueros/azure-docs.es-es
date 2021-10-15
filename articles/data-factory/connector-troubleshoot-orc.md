---
title: Solución de problemas del conector de formato ORC
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo solucionar problemas con el conector de formato ORC en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 973322c2ec7747fc5233eb897b302bfc910fee1b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390465"
---
# <a name="troubleshoot-the-orc-format-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de formato ORC en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de formato ORC en Azure Data Factory y Azure Synapse.

## <a name="error-code-orcjavainvocationexception"></a>Código de error: OrcJavaInvocationException

- **Mensaje**: `An error occurred when invoking Java, message: %javaException;.`
- **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

    | Análisis de las causas                                               | Recomendación                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Si el mensaje de error contiene las cadenas "java.lang.OutOfMemory", "Java heap space" y "doubleCapacity", suele tratarse de un problema de administración de memoria en la versión anterior del entorno de ejecución de integración. | Si usa el entorno de ejecución de integración autohospedado, se recomienda actualizar a la versión más reciente. |
    | Si el mensaje de error contiene la cadena "java.lang.OutOfMemory", significa que el entorno de ejecución de integración no tiene suficientes recursos para procesar los archivos. | limite las ejecuciones simultáneas en Integration Runtime. En el caso del entorno de ejecución de integración autohospedado, escale verticalmente a una máquina eficaz con una memoria igual o superior a 8 GB. |
    |Cuando el mensaje de error contiene la cadena "NullPointerReference", la causa podría ser un error transitorio. | Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el soporte técnico. |
    | Cuando el mensaje de error contiene la cadena "BufferOverflowException", la causa podría ser un error transitorio. | Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el soporte técnico. |
    | Cuando el mensaje de error contiene la cadena "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable no se puede transmitir a org.apache.hadoop.io.Text", la causa podría ser un problema de conversión de tipos dentro de Java Runtime. Normalmente, significa que los datos de origen no se pueden controlar correctamente en Java Runtime. | Se trata de un problema de datos. Trate de usar una cadena en lugar de los tipos char o varchar en los datos de formato ORC. |

## <a name="error-code-orcdatetimeexceedlimit"></a>Código de error: OrcDateTimeExceedLimit

- **Mensaje**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa**: Si el valor datetime es "0001-01-01 00:00:00", podría deberse a la diferencia entre el [calendario Juliano y el calendario Gregoriano](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Recomendación:**  Compruebe el valor de tics y evite usar el valor datetime "0001-01-01 00:00:00".

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
