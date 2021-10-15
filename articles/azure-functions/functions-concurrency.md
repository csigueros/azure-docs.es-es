---
title: Simultaneidad en Azure Functions
description: Información general de la característica de simultaneidad dinámica en Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 9/24/2021
ms.author: cachai
ms.openlocfilehash: 60d6861fbf0f9c02df78674f85633a962fea09a3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367853"
---
# <a name="concurrency-in-azure-functions"></a>Simultaneidad en Azure Functions

En este artículo se describen los comportamientos de simultaneidad de los desencadenadores controlados por eventos en Azure Functions. También se describe un nuevo modelo dinámico para optimizar los comportamientos de simultaneidad. 

>[!NOTE]
>El modelo de simultaneidad dinámica está actualmente en versión preliminar. La compatibilidad con la simultaneidad dinámica se limita a extensiones de enlace específicas, que también están en versión preliminar.  

El modelo de hospedaje de Functions permite que varias invocaciones de función se ejecuten simultáneamente en una única instancia de proceso. Por ejemplo, considere un caso en el que tiene tres funciones diferentes en la aplicación de funciones, que se escala horizontalmente y se ejecuta en varias instancias. En este escenario, cada función procesa invocaciones en cada instancia de máquina virtual en la que se ejecuta la aplicación de funciones. Las invocaciones de función en una sola instancia comparten los mismos recursos de proceso de máquina virtual, como la memoria, la CPU y las conexiones. Cuando la aplicación se hospeda en un plan dinámico (consumo o Premium), la plataforma escala o reduce verticalmente el número de instancias de la aplicación de funciones según el número de eventos entrantes. Para más información, consulte [Escalado impulsado por eventos](./Event-Driven-Scaling.md). Al hospedar las funciones en un plan dedicado (App Service), configure manualmente las instancias o [configure un esquema de escalabilidad automática](dedicated-plan.md#scaling).

Dado que se pueden ejecutar varias invocaciones de función en cada instancia simultáneamente, cada función debe tener una manera de limitar cuántas invocaciones simultáneas procesa en un momento dado.

## <a name="static-concurrency"></a>Simultaneidad estática

Muchos de los desencadenadores admiten un modelo de configuración estático de nivel de host, que se usa para especificar la simultaneidad por instancia para ese tipo de desencadenador. Por ejemplo, el [desencadenador de Service Bus](./functions-bindings-service-bus-trigger.md) proporciona tanto un valor `MaxConcurrentCalls` como `MaxConcurrentSessions` en el [archivo host.json](functions-host-json.md). Estos valores controlan conjuntamente el número máximo de mensajes que cada función procesa simultáneamente en cada instancia. Otros tipos de desencadenador tienen mecanismos integrados para invocar el equilibrio de carga entre instancias. Por ejemplo, Event Hubs y Azure Cosmos DB usan un esquema basado en particiones. 

Para los tipos de desencadenador que admiten la configuración de simultaneidad, hay un comportamiento predeterminado, que puede optar por invalidar en el archivo host.json de la aplicación de funciones. Esta configuración, que se aplica a todas las instancias en ejecución, permite controlar la simultaneidad máxima de las funciones en cada instancia. Por ejemplo, cuando la función consume muchos recursos o CPU, puede optar por limitar la simultaneidad para mantener correctas las instancias. De forma similar, cuando la función realiza solicitudes a un servicio de bajada que se está limitando, también debe considerar la posibilidad de limitar la simultaneidad. 

Aunque estas configuraciones de simultaneidad le permiten controlar determinados comportamientos de desencadenador, como la limitación de las funciones, puede ser difícil determinar los valores óptimos para estos valores. Por lo general, se tiene que llegar a los valores aceptables a través de un proceso de prueba de carga y error. Incluso cuando se determina un conjunto de valores que funcionan para un perfil de carga determinado, el número de eventos que llegan desde los servicios conectados puede cambiar de un día a otro. Esta variabilidad significa que la aplicación puede ejecutarse a menudo con valores que no son los óptimos. Por ejemplo, la aplicación de funciones puede procesar cargas de mensajes especialmente exigentes en el último día de la semana, lo que requiere que se limite la simultaneidad. Sin embargo, durante el resto de la semana, las cargas de mensajes son más simples, lo que significa que puede usar un nivel de simultaneidad mayor el resto de la semana. 

Lo ideal es que el sistema permita que las instancias procesen todo el trabajo que puedan a la vez que mantiene correctas y con latencias bajas todas las instancias, que es para lo que está diseñada la simultaneidad dinámica.

## <a name="dynamic-concurrency-preview"></a>Simultaneidad dinámica (versión preliminar)

Functions ahora proporciona un modelo de simultaneidad dinámica que simplifica la configuración de la simultaneidad para todas las aplicaciones de funciones que se ejecutan en el mismo plan. 

> [!NOTE]
> Actualmente, la simultaneidad dinámica solo se admite para el desencadenador de Service Bus y requiere que use [ versión 5.0.0-beta.6 (o superior)](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.6) de la extensión **Microsoft.Azure.WebJobs.Extensions.ServiceBus**.

### <a name="benefits"></a>Ventajas

El uso de simultaneidad dinámica proporciona las siguientes ventajas: 

- **Configuración simplificada**: ya no tiene que determinar manualmente la configuración de simultaneidad por desencadenador. El sistema aprende los valores óptimos para la carga de trabajo a lo largo del tiempo. 
- **Ajustes dinámicos**: la simultaneidad se ajusta hacia arriba o abajo dinámicamente en tiempo real, lo que permite que el sistema se adapte a los patrones de carga cambiantes a lo largo del tiempo. 
- **Protección del estado de instancia**: el runtime limita la simultaneidad a los niveles que una instancia de aplicación de funciones puede controlar cómodamente. Así se protege a la aplicación frente a la sobrecarga al realizar más trabajo del que debería. 
- **Rendimiento mejorado**: se mejora el rendimiento general porque las instancias individuales no toman más trabajo del que pueden procesar rápidamente. Esto permite equilibrar la carga de trabajo de forma más eficaz entre instancias. En el caso de las funciones que pueden controlar cargas más altas, la simultaneidad se puede aumentar a valores más allá de los valores de configuración predeterminados, lo que produce un mayor rendimiento.

### <a name="dynamic-concurrency-configuration"></a>Configuración de simultaneidad dinámica

La simultaneidad dinámica se puede habilitar en el nivel de host en el archivo host.json. Cuando se habilitan extensiones de enlace usadas por la aplicación de funciones que admiten la simultaneidad dinámica, la simultaneidad se ajustará dinámicamente según sea necesario. La configuración de simultaneidad dinámica invalida cualquier configuración de simultaneidad configurada manualmente para los desencadenadores que admiten la simultaneidad dinámica. 

De forma predeterminada, la simultaneidad dinámica está deshabilitada. Con la simultaneidad dinámica habilitada, la simultaneidad comienza en 1 para cada función y se ajusta hasta un valor óptimo, determinado por el host.

Puede habilitar la simultaneidad dinámica en la aplicación de funciones agregando la siguiente configuración en el archivo host.json: 

```json
    { 
        "version": "2.0", 
        "concurrency": { 
            "dynamicConcurrencyEnabled": true, 
            "snapshotPersistenceEnabled": true 
        } 
    } 
```

 Cuando `SnapshotPersistenceEnabled` es `true`, que es el valor predeterminado, los valores de simultaneidad aprendidos se conservan periódicamente en el almacenamiento para que las nuevas instancias empiecen a partir de esos valores en lugar de empezar a partir de 1 y tener que volver a hacer el aprendizaje. 

### <a name="concurrency-manager"></a>Administrador de la simultaneidad 

Cuando se habilita la simultaneidad dinámica, hay un proceso de administrador de simultaneidad que se ejecuta en segundo plano. Este administrador supervisa constantemente las métricas de estado de la instancia, como el uso de CPU y subprocesos, y los cambios se limitan según sea necesario. Cuando se habilitan una o varias limitaciones, la simultaneidad de la función se ajusta hasta que el host vuelve a tener un estado correcto. Cuando se deshabilitan las limitaciones, la simultaneidad puede aumentar. Se usan varias heurísticas para ajustar o reducir la simultaneidad de forma inteligente según sea necesario en función de estas limitaciones. Con el tiempo, la simultaneidad de cada función se estabiliza en un nivel determinado. 

Los niveles de simultaneidad se administran para cada función individual. Por lo tanto, el sistema equilibra entre las funciones que consumen muchos recursos que requieren un bajo nivel de simultaneidad y funciones más ligeras que pueden controlar una mayor simultaneidad. El equilibrio de simultaneidad de cada función ayuda a mantener el estado general de la instancia de la aplicación de funciones.  

Cuando la simultaneidad dinámica está habilitada, verá decisiones de simultaneidad dinámica en los registros. Por ejemplo, verá registros cuando se habiliten varias limitaciones y cada vez que se ajuste la simultaneidad hacia arriba o hacia abajo para cada función. Estos registros se escriben en la categoría de registro **Host.Concurrency** de la tabla de seguimientos. 

### <a name="extension-support"></a>Compatibilidad con extensiones 

La simultaneidad dinámica se habilita para una aplicación de funciones en el nivel de host y las extensiones que admiten simultaneidad dinámica se ejecutan en ese modo. La simultaneidad dinámica requiere la colaboración entre el host y las extensiones de desencadenador individuales. Para la versión preliminar, solo las versiones más recientes (versión preliminar) de las siguientes extensiones admiten la simultaneidad dinámica.

#### <a name="service-bus"></a>Service Bus 

El desencadenador de Service Bus admite actualmente tres modelos de ejecución. La simultaneidad dinámica afecta a estos modelos de ejecución de la siguiente manera:
 
- **Procesamiento de cola o tema de distribución única**: cada invocación de la función procesa un único mensaje. Cuando se usa la configuración estática, la simultaneidad se rige por la opción de configuración MaxConcurrentCalls. Cuando se usa la simultaneidad dinámica, ese valor de configuración se omite y la simultaneidad se ajusta dinámicamente. 
- **Procesamiento de cola o tema de distribución única basada en sesión**: cada invocación de la función procesa un único mensaje. En función del número de sesiones activas para el tema o la cola, cada instancia concede una o varias sesiones. Los mensajes de cada sesión se procesan en serie, para garantizar el orden en una sesión. Cuando no se usa la simultaneidad dinámica, la simultaneidad se rige por el valor `MaxConcurrentSessions`. Con la simultaneidad dinámica habilitada, `MaxConcurrentSessions` se omite y el número de sesiones que procesa cada instancia se ajusta dinámicamente. 
- **Procesamiento por lotes**: cada invocación de la función procesa un lote de mensajes, regido por el valor `MaxMessageCount`. Dado que las invocaciones por lotes son en serie, la simultaneidad de la función desencadenada por lotes siempre es uno y no se aplica la simultaneidad dinámica. 

Para habilitar que el desencadenador de Service Bus use la simultaneidad dinámica, debe usar la [ versión 5.0.0-beta.6 (o superior)](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.6) de la extensión **Microsoft.Azure.WebJobs.Extensions.ServiceBus**. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
* [Referencia para desarrolladores de Azure Functions](functions-reference.md)
* [Enlaces y desencadenadores de Azure Functions](event-driven-scaling.md)