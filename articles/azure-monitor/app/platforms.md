---
title: 'Application Insights: lenguajes, plataformas e integraciones| Microsoft Docs'
description: Lenguajes, plataformas e integraciones disponibles para Application Insights
ms.topic: conceptual
ms.date: 10/29/2021
ms.reviewer: olegan
ms.openlocfilehash: 44c08d1008691826299d1fa41507df26064f6274
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462033"
---
# <a name="supported-languages"></a>Idiomas compatibles

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas y marcos compatibles

### <a name="azure-service-integration-portal-enablement-arm-deployments"></a>Integración de servicios de Azure (habilitación del portal, implementaciones de ARM)
* [Conjuntos de escalado de máquinas virtuales de Azure y Azure VM](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [Funciones de Azure](../../azure-functions/functions-monitoring.md)
* [Azure Cloud Services](./cloudservices.md), incluidos los roles web y de trabajo

### <a name="auto-instrumentation-enable-without-code-changes"></a>Instrumentación automática (habilitar sin cambios de código)
* [ASP.NET: para aplicaciones web hospedadas con IIS](./status-monitor-v2-overview.md)
* [Java](./java-in-process-agent.md)

### <a name="manual-instrumentation--sdk-some-code-changes-required"></a>Instrumentación manual/SDK (se requieren algunos cambios de código)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript - Web](./javascript.md)
  * [React](./javascript-react-plugin.md)
  * [React Native](./javascript-react-native-plugin.md)
  * [Angular](./javascript-angular-plugin.md)
* [Aplicaciones, servicios y roles de trabajo del escritorio de Windows](./windows-desktop.md)
* [Aplicación Windows universal](../app/mobile-center-quickstart.md) (App Center)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)

> [!NOTE]
> La instrumentación basada en OpenTelemetry está disponible en estado de versión preliminar para [C#, Node.js y Python](opentelemetry-enable.md). Revise las limitaciones que se han indicado al principio de la documentación oficial de cada idioma. Aquellos que requieran una experiencia completa de características deben usar los SDK de Application Insights existentes.

## <a name="logging-frameworks"></a>Marcos de registro
* [ILogger](./ilogger.md)
* [Log4Net, NLog o System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J o Logback](java-2x-trace-logs.md)
* [Complemento LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exportación y análisis de datos
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>SDK no compatibles
Existen otros SDK de Application Insights con el soporte de la comunidad. Sin embargo, Azure Monitor solo proporciona compatibilidad cuando se usan las opciones de instrumentación admitidas que se enumeran en esta página. Estamos evaluando constantemente las oportunidades de ampliar nuestro soporte técnico para otros idiomas. Siga las [actualizaciones de Azure para Application Insights](https://azure.microsoft.com/updates/?query=application%20insights) para obtener las últimas noticias del SDK.
