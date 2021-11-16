---
title: Introducción a OpenTelemetry con Azure Monitor
description: Proporciona información general sobre cómo usar OpenTelemetry con Azure Monitor.
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: a839437bd572946a6e6c2f62f2658ff4696db4e9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233495"
---
# <a name="opentelemetry-overview"></a>Introducción a OpenTelemetry

Microsoft está encantado de adoptar [OpenTelemetry](https://opentelemetry.io/) como futuro de la instrumentación de telemetría. Nuestros clientes han pedido instrumentación neutral del proveedor y estamos encantados de asociarnos con la comunidad de OpenTelemetry para crear API y SDK coherentes en todos los lenguajes.

Microsoft ha colaborado con las partes interesadas de dos proyecto de telemetría de código abierto ya conocidos, [OpenCensus](https://opencensus.io/) y [OpenTracing](https://opentracing.io/), para ayudar a crear un único proyecto: OpenTelemetry. OpenTelemetry incluye contribuciones de todos los principales proveedores de administración del rendimiento de aplicaciones y de la nube (APM) y reside en la base de software de código abierto [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) de la que Microsoft es miembro Platinum.

## <a name="concepts"></a>Conceptos

La telemetría, los datos recopilados para observar la aplicación, se puede dividir en tres tipos o "pilares":
1.  Seguimiento distribuido
2.  Métricas
3.  Registros

Inicialmente, la comunidad de OpenTelemetry se hizo cargo del seguimiento distribuido. Las métricas y los registros siguen en curso. Una historia de observabilidad completa incluye los tres pilares, pero actualmente nuestras [ofertas de **versión preliminar** del exportador basadas en OpenTelemetry de Azure Monitor para .NET, Python y JavaScript](opentelemetry-enable.md) **solo incluyen el seguimiento distribuido**.

Hay varios orígenes que explican los tres pilares en detalle, incluidos el [sitio web de la comunidad de OpenTelemetry](https://opentelemetry.io/docs/concepts/data-sources/), [Especificaciones de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md) y [Observabilidad de sistemas distribuidos](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html) de Cindy Sridhmen.

En las secciones siguientes, hablaremos de algunos aspectos básicos de la recopilación de telemetría.

### <a name="instrumenting-your-application"></a>Instrumentación de la aplicación

En un nivel básico, la "instrumentación" simplemente permite que una aplicación capture telemetría.

Hay dos métodos para instrumentar la aplicación:
1.  Instrumentación manual
2.  Instrumentación automática (autoinstrumentación)

La instrumentación manual consiste en crear código para la API de OpenTelemetry. En el contexto de un usuario final, normalmente hace referencia a la instalación de un SDK específico del lenguaje en una aplicación. Los paquetes de instrumentación manual constan de nuestras [ofertas en **versión preliminar** del exportador basado en OpenTelemetry para .NET, Python y JavaScript](opentelemetry-enable.md).

> [!IMPORTANT]
> "Manual" **NO** significa que se pida que escriba código complejo para definir intervalos para seguimientos distribuidos (aunque sigue siendo una opción). Un amplio y creciente conjunto de bibliotecas de instrumentación mantenidas por los colaboradores de OpenTelemetry le permitirá capturar sin esfuerzo las señales de telemetría en los marcos y bibliotecas comunes. Un subconjunto de bibliotecas de instrumentación de OpenTelemetry será compatible con Azure Monitor, con la información proporcionada por los clientes. Además, estamos trabajando para [instrumentar los SDK de servicio de Azure más conocidos mediante OpenTelemetry](https://devblogs.microsoft.com/azure-sdk/introducing-experimental-opentelemetry-support-in-the-azure-sdk-for-net/).

Por el otro lado, la instrumentación automática consiste en habilitar la recopilación de datos de telemetría mediante configuración sin tocar el código de la aplicación. Aunque es más conveniente, tiende a ser menos configurable y no está disponible en todos los lenguajes. La oferta de instrumentación automática basada en OpenTelemetry de Azure Monitor consta de la [oferta disponible con carácter general basada en OpenTelemetry de Java 3.X](java-in-process-agent.md) y seguimos invirtiendo en ella con la información proporcionada por los clientes. La comunidad de OpenTelemetry también experimenta con la instrumentación automática de C# y Python, pero Azure Monitor se centra en crear una historia de instrumentación manual sencilla y eficaz a corto plazo.

### <a name="sending-your-telemetry"></a>Envío de la telemetría

También hay dos maneras de enviar los datos a Azure Monitor (o a cualquier proveedor).
1. Exportador directo
2. A través de un agente

Un exportador directo envía datos de telemetría en proceso (desde el código de la aplicación) directamente al punto de conexión de ingesta de Azure Monitor. La principal ventaja de este enfoque es la simplicidad de la incorporación.

**Todas las ofertas basadas en OpenTelemetry actualmente admitidas de Azure Monitor usan un exportador directo**. 

Como alternativa, el envío de telemetría a través de un agente proporcionará una ruta para que cualquier lenguaje compatible con OpenTelemetry la envíe a Azure Monitor a través de [OTLP](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md). La recepción de OTLP permitirá a los clientes observar las aplicaciones escritas en lenguajes distintos de los [lenguajes admitidos](platforms.md). 

> [!NOTE]
> Algunos clientes han empezado a usar [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) como alternativa con agente, aunque Microsoft todavía no admite oficialmente un enfoque basado en agente para la supervisión de aplicaciones. Mientras tanto, la comunidad de código abierto ha contribuido con un exportador de Azure Monitor de OpenTelemetry-Collector que algunos clientes usan para enviar datos a Application Insights de Azure Monitor.

## <a name="terms"></a>Términos

Consulte el [glosario](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md) en las especificaciones de OpenTelemetry.

Algunos términos heredados de Application Insights son confusos dada la convergencia del sector en OpenTelemetry. En la tabla siguiente se resaltan estas diferencias. Finalmente, los términos de Application Insights se reemplazarán por términos de OpenTelemetry.

Application Insights | OpenTelemetry
------ | ------
Recopiladores automáticos | Bibliotecas de instrumentación
Canal | Exportador  
Sin código/basado en agente |  Instrumentación automática
Traces | Registros


## <a name="next-step"></a>Paso siguiente

Las páginas siguientes constan de una guía de cada lenguaje para habilitar y configurar las ofertas basadas en OpenTelemetry de Microsoft. Lo importante es que compartimos la funcionalidad y las limitaciones disponibles de cada oferta para que pueda determinar si OpenTelemetry es adecuado para su proyecto.
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
