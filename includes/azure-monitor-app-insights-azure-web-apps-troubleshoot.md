---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.openlocfilehash: 327ab19750615c7912eeffdd520efa896314f1d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699452"
---
### <a name="connection-string-and-instrumentation-key"></a>Cadena de conexión y clave de instrumentación

Al utilizar la supervisión sin código, solo se requiere la cadena de conexión. Sin embargo, todavía se recomienda establecer la clave de instrumentación para mantener la compatibilidad con versiones anteriores de SDK cuando se realiza la instrumentación manual.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>¿Cuál es la diferencia entre las métricas estándar de Application Insights frente a las métricas de Azure App Service?

Application Insights recopila datos de telemetría de las solicitudes que se hicieron a la aplicación. Si el error se produjo en Web Apps o el servidor web, y la solicitud no llegó a la aplicación de usuario, Application Insights no tendrá ningún dato de telemetría sobre él.

La duración de `serverresponsetime` calculada por Application Insights no coincide necesariamente con el tiempo de respuesta del servidor observado por Web Apps. Esto se debe a que Application Insights solo cuenta la duración cuando la solicitud real llega a la aplicación de usuario. Si la solicitud se bloquea o pone en cola en el servidor web, ese tiempo de espera se incluirá en las métricas de Web Apps, pero no en las métricas de Application Insights.


