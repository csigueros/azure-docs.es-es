---
title: Supervisar el rendimiento de Azure App Services | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones de Azure App Services. Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 03ee74851f82fd8ff7833fc5d27c901cf0b2317a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788750"
---
# <a name="application-monitoring-for-azure-app-service-overview"></a>Información general sobre la supervisión de aplicaciones para Azure App Service

Ahora, habilitar la supervisión en las aplicaciones web basadas en ASP.NET, ASP.NET Core, Java y Node.js que se ejecutan en [Azure App Services](../../app-service/index.yml) es más fácil que nunca. Mientras que antes era necesario instrumentar manualmente la aplicación, ahora el agente o la extensión más reciente están integrados en la imagen de App Service de manera predeterminada. 

## <a name="enable-application-insights"></a>Habilitación de Application Insights

Hay dos maneras de habilitar la supervisión de aplicaciones para las aplicaciones hospedadas de Azure App Service:

- **Supervisión de aplicaciones basadas en agentes** (ApplicationInsightsAgent). 
 
    - Este método es el más fácil para habilitar y no se requiere ningún cambio de código ni configuraciones avanzadas. A menudo se conoce como supervisión de "entorno en tiempo de ejecución". Para Azure App Services se recomienda la habilitación de este nivel de supervisión como mínimo y, a continuación, en función del escenario específico, puede evaluar si es necesaria una supervisión más avanzada a través de instrumentación manual.

    - A continuación se muestra la compatibilidad para la supervisión basada en agentes:
        - [.NET Core](./azure-web-apps-net-core.md)
        - [.NET](./azure-web-apps-net.md)
        - [Java](./azure-web-apps-java.md)
        - [Nodejs](./azure-web-apps-nodejs.md)
    
* **Instrumentación manual de la aplicación mediante código** mediante la instalación del SDK de Application Insights.

    * Este enfoque es más fácil de personalizar, pero exige los siguientes métodos: SDK de [.NET Core](./asp-net-core.md), [.NET](./asp-net.md), [Node.js](./nodejs.md), [Python](./opencensus-python.md) y un agente independiente para [Java](./java-in-process-agent.md). Este método también implica que el usuario tiene que administrar las actualizaciones a la versión más reciente de los paquetes.

    * Si necesita realizar llamadas de API personalizadas para supervisar eventos o dependencias no capturados de manera predeterminada con la supervisión basada en agentes, deberá usar este método. Consulte el [artículo API de Application Insights para eventos y métricas personalizados](./api-custom-events-metrics.md) para obtener más información. 

> [!NOTE]
> Si se detecta supervisión basada en agentes e instrumentación manual basada en SDK, en .NET solo se respeta la configuración de instrumentación manual, mientras que en Java solo la instrumentación basada en agentes emite la telemetría. Esto es para evitar que se envíen datos duplicados.

> [!NOTE]
> El depurador de instantáneas y el generador de perfiles solo están disponibles en .NET y .Net Core.

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre cómo habilitar la supervisión de aplicaciones basada en agentes para la aplicación [.NET Core](./azure-web-apps-net-core.md), [.NET](./azure-web-apps-net.md), [Java](./azure-web-apps-java.md) o [Nodejs](./azure-web-apps-nodejs.md) que se ejecuta en App Service. 
