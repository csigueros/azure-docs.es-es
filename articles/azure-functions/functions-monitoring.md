---
title: Monitor Azure Functions
description: Obtenga información acerca de cómo usar Azure Application Insights con Azure Functions para supervisar la ejecución de funciones.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 0967e440a661d6bf51dc5206e5c21cdda45db073
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113565137"
---
# <a name="monitor-azure-functions"></a>Monitor Azure Functions

[Azure Functions](functions-overview.md) ofrece integración incorporada con [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para supervisar las funciones. En este artículo se proporciona información general sobre las funcionalidades de supervisión que ofrece Azure para supervisar Azure Functions.

Application Insights recopila información del registro, el rendimiento y los errores. Detecta automáticamente anomalías en el rendimiento e incluye herramientas de análisis eficaces, para que le resulte más fácil diagnosticar incidencias y para que comprenda mejor cómo se usan las funciones. Estas herramientas están diseñadas para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso de las funciones. Incluso puede usar Application Insights durante el desarrollo local de proyectos de aplicación de función. Para más información, consulte [¿Qué es Application Insights?](../azure-monitor/app/app-insights-overview.md)

Dado que la instrumentación de Application Insights está integrada en Azure Functions, todo lo que necesita es una clave de instrumentación válida para conectar la aplicación de funciones a un recurso de Application Insights. La clave de instrumentación se agrega a la configuración de la aplicación cuando crea el recurso de la aplicación de funciones en Azure. Si la aplicación de funciones aún no tiene esta clave, puede [establecerla de forma manual](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Precios y límites de Application Insights

Puede probar la integración de Application Insights con Azure Functions sin costo, con un límite diario para la cantidad de datos que se procesan de forma gratuita.

Si habilita Application Insights durante el desarrollo, podría alcanzar este límite durante las pruebas. Azure proporciona notificaciones del portal y por correo electrónico cuando se aproxima al límite diario. Si pasa por alto esas alertas y llega al límite, los nuevos registros no aparecerán en las consultas de Application Insights. Tenga en cuenta el límite para evitar el tiempo que puede perder, de forma innecesaria, solucionando problemas. Para más información, consulte [Administración de precios y volúmenes de datos de Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights tiene una característica de [muestreo](../azure-monitor/app/sampling.md) que le puede ayudar a impedir que se recopilen demasiados datos de telemetría sobre las ejecuciones completadas en los momentos de picos de carga. El muestreo está habilitado de forma predeterminada. Si parece que faltan datos, es posible que simplemente deba ajustar la configuración de muestreo para adaptarla a su escenario de supervisión en particular. Para más información, consulte [Configuración del muestreo](configure-monitoring.md#configure-sampling).

La lista completa de características de Application Insights disponibles para la aplicación de función se detalla en [Características compatibles de Application Insights para Azure Functions](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Integración de Application Insights

Normalmente crea una instancia de Application Insights al crear la aplicación de funciones. En este caso, la clave de instrumentación necesaria para la integración ya se establece como una configuración de aplicación denominada `APPINSIGHTS_INSTRUMENTATIONKEY`. Si, por alguna razón, la aplicación de funciones no tiene establecida la clave de instrumentación, deberá [habilitar la integración de Application Insights](configure-monitoring.md#enable-application-insights-integration).  

> [!IMPORTANT]
> Las nubes soberanas, como Azure Government, requieren el uso de la cadena de conexión de Application Insights (`APPLICATIONINSIGHTS_CONNECTION_STRING`) en lugar de la clave de instrumentación. Para más información, consulte la [referencia de APPLICATIONINSIGHTS_CONNECTION_STRING](functions-app-settings.md#applicationinsights_connection_string).

## <a name="collecting-telemetry-data"></a>Recopilación de datos de telemetría

Con la integración de Application Insights habilitada, los datos de telemetría se envían a la instancia de Application Insights conectada. Estos datos incluyen los registros generados por el host de Functions, los seguimientos que se escriben desde el código de Functions y los datos de rendimiento. 

>[!NOTE]
>Además de los datos de las funciones y del host de Functions, también puede recopilar datos del [controlador de escala de Functions](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Niveles y categorías de registro

Si escribe seguimientos desde el código de la aplicación, debe asignarles un nivel de registro. Estos niveles proporcionan una manera de limitar la cantidad de datos que se recopilan de los seguimientos.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Para más información sobre los niveles de registro, consulte [Configuración de niveles de registro](configure-monitoring.md#configure-log-levels).

Mediante la asignación de elementos registrados a una categoría, tiene más control sobre los datos de telemetría que se generan a partir de orígenes específicos en la aplicación de funciones. Las categorías facilitan la ejecución de análisis en los datos recopilados. Los seguimientos escritos desde el código de la función se asignan a categorías individuales basadas en el nombre de la función. Para más información sobre las categorías, consulte [Configuración de categorías](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Datos de telemetría personalizados

En [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions), [JavaScript](functions-reference-node.md#log-custom-telemetry) y [Python](functions-reference-python.md#log-custom-telemetry), puede usar un SDK de Application Insights para escribir datos de telemetría personalizados.

### <a name="dependencies"></a>Dependencias

A partir de la versión 2.x de Functions, el entorno de ejecución recopila automáticamente los datos de las dependencias de los enlaces que utilizan determinados SDK de cliente. Application Insights recopila datos de las siguientes dependencias:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Servicios de Azure Storage (Blob, Queue y Table)

También se capturan las solicitudes HTTP y las llamadas a la base de datos realizadas con `SqlClient`. Para obtener una lista completa de las dependencias admitidas por Application Insights, consulte [Dependencias con seguimiento automático](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights genera un _mapa de aplicación_ de los datos de dependencia recopilados. A continuación se muestra un ejemplo de un mapa de aplicación de una función de desencadenador HTTP con un enlace de salida de Queue Storage.  

![Mapa de aplicación con dependencia](./media/functions-monitoring/app-map.png)

Las dependencias se escriben en el nivel `Information`. Si filtra por el nivel `Warning` u otro superior, no verá los datos de dependencia. Por otra parte, la recopilación automática de dependencias se produce en un ámbito que no es de usuario. Para capturar datos de dependencia, asegúrese de que el nivel se establece al menos en `Information` fuera del ámbito de usuario (`Function.<YOUR_FUNCTION_NAME>.User`) en el host.

Además de la recopilación automática de datos de dependencia, también puede usar uno de los SDK de Application Insights específicos del lenguaje para escribir información de dependencia personalizada en los registros. Para obtener un ejemplo de cómo escribir dependencias personalizadas, consulte uno de los siguientes ejemplos específicos del lenguaje:

+ [Registrar telemetría personalizada en funciones de C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Registrar telemetría personalizada en funciones de JavaScript](functions-reference-node.md#log-custom-telemetry) 
+ [Registrar telemetría personalizada en funciones de Python](functions-reference-python.md#log-custom-telemetry)

## <a name="writing-to-logs"></a>Escritura en los registros 

La forma en que escribe en los registros y las API que usa dependen del lenguaje del proyecto de la aplicación de funciones.   
Consulte la guía del desarrollador de su lenguaje para obtener más información sobre cómo escribir registros desde las funciones.

+ [C# (biblioteca de clases .NET)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>Análisis de datos

De forma predeterminada, los datos recopilados de la aplicación de funciones se almacenan en Application Insights. En [Azure Portal](https://portal.azure.com), Application Insights proporciona un amplio conjunto de visualizaciones de los datos de telemetría. Puede profundizar en los registros de errores y en los eventos de consulta y las métricas. Para más información, junto con ejemplos básicos de cómo ver y consultar los datos recopilados, consulte [Análisis de la telemetría de Azure Functions en Application Insights](analyze-telemetry-data.md). 

## <a name="streaming-logs"></a>Registros de transmisión

A menudo, cuando desarrolla una aplicación, quiere ver lo que se escribe en los registros casi en tiempo real cuando se ejecuta en Azure.

Hay dos maneras de ver una secuencia de los datos de registro que generan las ejecuciones de la función.

* **Streaming integrado de registros**: la plataforma de App Service le permite ver una secuencia de los archivos de registro de aplicaciones. Esta secuencia equivale a la salida que se ve al depurar las funciones durante el [desarrollo local](functions-develop-local.md) y cuando se usa la pestaña **Prueba** del portal. Se muestra toda la información basada en el registro. Para más información, consulte [Registros de Stream](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Este método de streaming solo admite una instancia y no se puede usar con una aplicación que se ejecuta en Linux en un plan de consumo.

* **Live Metrics Stream**: cuando la aplicación de funciones está [conectada a Application Insights](configure-monitoring.md#enable-application-insights-integration), puede ver los datos de registro y otras métricas casi en tiempo real en Azure Portal mediante [Live Metrics Stream](../azure-monitor/app/live-stream.md). Use este método cuando supervise las funciones que se ejecutan en varias instancias o en Linux en un plan de consumo. En este método se usan [datos muestreados](configure-monitoring.md#configure-sampling).

Las secuencias de registro se pueden ver tanto en el portal como en la mayoría de los entornos de desarrollo locales. Para obtener información sobre cómo habilitar las secuencias de registro, consulte [Habilitación de la transmisión de registros de ejecución en Azure Functions](streaming-logs.md).

## <a name="diagnostic-logs"></a>Registros de diagnóstico

_Esta característica se encuentra en su versión preliminar._ 

Application Insights permite exportar datos de telemetría a un almacenamiento a largo plazo u otros servicios de análisis.  

Dado que Functions también se integra con Azure Monitor, puede usar la configuración de diagnóstico para enviar datos de telemetría a varios destinos, incluidos los registros de Azure Monitor. Para obtener más información, vea [Supervisión de Azure Functions con registros de Azure Monitor](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Registros del controlador de escala

_Esta característica se encuentra en su versión preliminar._ 

El [controlador de escala de Azure Functions](./event-driven-scaling.md#runtime-scaling) supervisa las instancias del host de Azure Functions en el que se ejecuta la aplicación. Este controlador toma decisiones sobre cuándo agregar o quitar instancias en función del rendimiento actual. Puede hacer que el controlador de escala emita registros en Application Insights para comprender mejor las decisiones que este controlador está tomando para la aplicación de funciones. También puede almacenar los registros generados en Blob Storage para analizarlos mediante otro servicio. 

Para habilitar esta característica, agregue una configuración de aplicación denominada `SCALE_CONTROLLER_LOGGING_ENABLED` a la configuración de la aplicación de funciones. Para obtener información sobre cómo hacerlo, consulte [Configuración de los registros del controlador de escala](configure-monitoring.md#configure-scale-controller-logs).

## <a name="azure-monitor-metrics"></a>Métricas de Azure Monitor

Además de los datos de telemetría basados en registro recopilados por Application Insights, también puede obtener datos sobre cómo se ejecuta la aplicación de funciones a partir de las [métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md). Para obtener más información, vea [Uso de métricas de Azure Monitor con Azure Functions](monitor-metrics.md).

## <a name="report-issues"></a>Informar de problemas

Para notificar un problema con la integración de Application Insights en Functions o para realizar una solicitud o sugerencia [cree un problema en GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Application Insights](/azure/application-insights/)
* [Registro de ASP.NET Core](/aspnet/core/fundamentals/logging/)
