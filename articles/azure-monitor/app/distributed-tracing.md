---
title: Seguimiento distribuido en Azure Application Insights | Microsoft Docs
description: Proporciona información acerca de la compatibilidad de Microsoft con el seguimiento distribuido mediante la asociación en el proyecto OpenCensus
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c4f32c0803bb5815599c81ca6c89335fa6e1ffac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455176"
---
# <a name="what-is-distributed-tracing"></a>¿Qué es el seguimiento distribuido?

La llegada de las modernas arquitecturas de [microservicios](https://azure.com/microservices) y en la nube ha dado lugar a servicios simples que se pueden implementar de forma independiente y que pueden ayudar a reducir los costos al tiempo que aumentan la disponibilidad y el rendimiento. Pero aunque estos movimientos han hecho más fáciles de entender los servicios individuales como un todo, se han creado sistemas que en general son más difíciles de analizar y depurar.

En las arquitecturas monolíticas, nos hemos acostumbrado a depurar con pilas de llamadas. Las pilas de llamadas son excelentes herramientas para mostrar el flujo de ejecución (el método A llama al método B y este al método C), junto con detalles y parámetros sobre cada una de esas llamadas. Esto es fantástico para estructuras monolíticas o servicios que se ejecutan en un único proceso, pero ¿cómo se depura cuando la llamada se produce fuera de los límites de un proceso y no es simplemente una referencia en la pila local? 

Aquí entra en juego el seguimiento distribuido.  

El seguimiento distribuido es el equivalente de las pilas de llamadas para arquitecturas de microservicios y en la nube modernas, junto con un generador de perfiles de rendimiento simple. En Azure Monitor, se proporcionan dos experiencias para consumir datos de seguimiento distribuido. La primera es la vista de [diagnósticos de transacción](./transaction-diagnostics.md), que es como una pila de llamadas con una dimensión de tiempo adicional. La vista de diagnósticos de transacción proporciona visibilidad sobre una sola transacción o solicitud y resulta útil para encontrar la causa raíz de problemas de confiabilidad y cuellos de botella de rendimiento por cada solicitud.

Azure Monitor ofrece también una vista de [mapa de aplicación](./app-map.md) que agrega muchas transacciones para mostrar una vista topológica de cómo interactúan los sistemas y cuáles son las tasas promedio de rendimiento y de errores. 

## <a name="how-to-enable-distributed-tracing"></a>Cómo habilitar el seguimiento distribuido

Habilitar el seguimiento distribuido de los servicios de una aplicación es tan simple como agregar el agente, el SDK o la biblioteca adecuados a cada servicio, en función del lenguaje en el que se implementó el servicio.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Habilitación mediante Application Insights a través de la instrumentación automática o SDK

Los SDK o agentes de Application Insights para .NET, .NET Core, Java, Node.js y JavaScript admiten el seguimiento distribuido de forma nativa. Las instrucciones para instalar y configurar cada SDK de Application Insights están disponibles a continuación:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Con el SDK de Application Insights adecuado instalado y configurado, la información de seguimiento se recopila automáticamente para los marcos de trabajo, bibliotecas y tecnologías más populares mediante los auto-recopiladores de dependencias del SDK. La lista completa de las tecnologías compatibles está disponible en [la documentación de la recopilación automática de dependencias](./auto-collect-dependencies.md).

 Además, se puede realizar el seguimiento manual de cualquier tecnología con una llamada a [TrackDependency](./api-custom-events-metrics.md) en [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opentelemetry"></a>Habilitación mediante OpenTelemetry

Application Insights admite ahora el seguimiento distribuido mediante [OpenTelemetry](https://opentelemetry.io/). OpenTelemetry proporciona una instrumentación neutral del proveedor para enviar seguimientos, métricas y registros a Application Insights. Inicialmente, la comunidad de OpenTelemetry se hizo cargo del seguimiento distribuido. Las métricas y los registros siguen en curso. Una historia de observabilidad completa incluye los tres pilares, pero actualmente nuestras [ofertas de versión preliminar del exportador basadas en OpenTelemetry de Azure Monitor para .NET, Python y JavaScript](opentelemetry-enable.md) solo incluyen el seguimiento distribuido. Sin embargo, nuestra oferta de Azure Monitor basada en Java OpenTelemetry tiene disponibilidad general y es totalmente compatible. 

Las páginas siguientes constan de una guía de cada lenguaje para habilitar y configurar las ofertas basadas en OpenTelemetry de Microsoft. Lo importante es que compartimos la funcionalidad y las limitaciones disponibles de cada oferta para que pueda determinar si OpenTelemetry es adecuado para su proyecto.

* [.NET](opentelemetry-enable.md?tabs=net)
* [Java](java-in-process-agent.md)
* [Node.js](opentelemetry-enable.md?tabs=nodejs)
* [Python](opentelemetry-enable.md?tabs=python)

## <a name="enable-via-opencensus"></a>Habilitación mediante OpenCensus

Además de los SDK de Application Insights, Application Insights también admite el seguimiento distribuido mediante [OpenCensus](https://opencensus.io/). OpenCensus es una distribución de bibliotecas de código abierto e independiente del proveedor que proporciona recopilación de métricas y seguimiento distribuido para los servicios. También permite a la comunidad de código abierto habilitar el seguimiento distribuido con tecnologías conocidas como Redis, Memcached o MongoDB. [Microsoft colabora en OpenCensus junto con varios otros asociados de supervisión y en la nube](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

El sitio web de OpenCensus mantiene la documentación de referencia de la API para [Python](https://opencensus.io/api/python/trace/usage.html) y [Go](https://godoc.org/go.opencensus.io), así como diversas guías para el uso de OpenCensus. 

## <a name="next-steps"></a>Pasos siguientes

* [Guía de uso de Python de OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mapa de aplicación](./app-map.md)
* [Supervisión del rendimiento de un extremo a otro](../app/tutorial-performance.md)

