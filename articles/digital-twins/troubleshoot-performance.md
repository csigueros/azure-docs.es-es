---
title: 'Solución de problemas: rendimiento'
titleSuffix: Azure Digital Twins
description: Sugerencias para solucionar problemas de rendimiento de una instancia de Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 10/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 67f182ffbaf439d2c6b592854156073d6104efa7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131436"
---
# <a name="troubleshooting-azure-digital-twins-performance"></a>Solución de problemas de Azure Digital Twins: rendimiento

Si experimenta retrasos u otros problemas de rendimiento al trabajar con Azure Digital Twins, use las sugerencias de este artículo para ayudarle a solucionar problemas.

## <a name="isolate-the-source-of-the-delay"></a>Determinación del origen del retraso

Determine si el retraso viene de Azure Digital Twins u otro servicio de la solución. Para investigar este retraso, puede usar la métrica **Latencia de API** de [Azure Monitor](../azure-monitor/essentials/quick-monitor-azure-resource.md) desde Azure Portal. Para obtener instrucciones para ver métricas de Azure Monitor de una instancia de Azure Digital Twins, vea [Solución de problemas: métricas](troubleshoot-metrics.md).

## <a name="check-regions"></a>Comprobación de las regiones

Si la solución usa Azure Digital Twins en combinación con otros servicios de Azure (como Azure Functions), compruebe la región de implementación de cada servicio. Los servicios que se implementan en regiones diferentes pueden agregar retrasos a la solución. A menos que esté creando intencionadamente una solución distribuida, considere la posibilidad de implementar todas las instancias de servicio dentro de la misma región para evitar la introducción accidental de retrasos.

## <a name="check-logs"></a>Compruebe los registros

Azure Digital Twins puede recopilar los registros de una instancia de servicio para ayudar a supervisar su rendimiento, entre otros datos. Los registros se pueden enviar a [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) o a un mecanismo de almacenamiento personalizado. Para habilitar el registro en la instancia, siga las instrucciones de [Solución de problemas: registros de diagnóstico](troubleshoot-diagnostics.md). Puede analizar las marcas de tiempo de los registros para medir las latencias, evaluar si son coherentes y comprender su origen.

## <a name="check-api-frequency"></a>Comprobación de la frecuencia de la API

Otro factor que podría afectar al rendimiento es el tiempo necesario para volver a autorizar las llamadas API. Tenga en cuenta la frecuencia de las llamadas de API. Si hay un intervalo de más de 15 minutos entre las llamadas, es posible que el sistema vuelva a realizar la autorización en cada llamada, para lo que se tarda más tiempo. Puede evitarlo si agrega un temporizador o algo similar al código, para asegurarse de que llama a Azure Digital Twins al menos una vez cada 15 minutos.

## <a name="contact-support"></a>Ponerse en contacto con soporte técnico

Si sigue experimentando problemas de rendimiento después de solucionar los problemas con los pasos anteriores, puede crear una solicitud de soporte técnico de Ayuda y soporte técnico de Azure a fin de obtener ayuda adicional para la solución de problemas. 

Siga estos pasos:

1. Recopile las [métricas](troubleshoot-metrics.md) y los [registros](troubleshoot-diagnostics.md) de la instancia.
2. Vaya a [Ayuda y soporte técnico de Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal. Use las indicaciones para proporcionar detalles del problema, consulte las soluciones recomendadas, comparta sus archivos de métricas o registro, y envíe cualquier otra información que el equipo de soporte técnico pueda usar para ayudar a investigar el problema. Para más información sobre la creación de solicitudes de soporte técnico, consulte [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Pasos siguientes

Consulte otras formas de solucionar problemas de la instancia de Azure Digital Twins en los siguientes artículos:
* [Solución de problemas: métricas](troubleshoot-metrics.md)
* [Solución de problemas: registros de diagnóstico](troubleshoot-diagnostics.md)
* [Solución de problemas: alertas](troubleshoot-alerts.md)
* [Solución de problemas: Resource Health](troubleshoot-resource-health.md)
