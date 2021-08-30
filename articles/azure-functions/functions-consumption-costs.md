---
title: Estimación de los costos del plan de consumo en Azure Functions
description: Obtenga información sobre cómo estimar mejor los costos que se pueden producir al ejecutar la aplicación de funciones en un plan de consumo de Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 9544dded7516b07ad7d919d08b0b9cd4e12d0607
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567360"
---
# <a name="estimating-consumption-plan-costs"></a>Estimación de los costos según el plan de consumo

Actualmente hay tres tipos de planes de hospedaje para una aplicación que se ejecuta en Azure Functions, donde cada plan tiene su propio modelo de precios: 

| Plan | Descripción |
| ---- | ----------- |
| [**Consumo**](consumption-plan.md) | Solo se le cobrará por el tiempo que se ejecute la aplicación de funciones. Este plan incluye una [concesión gratuita][página de precios] por suscripción.|
| [**Premium**](functions-premium-plan.md) | Proporciona las mismas características y mecanismo de escalado que el plan de consumo, pero con un rendimiento mejorado y acceso a redes virtuales. El costo se basa en el plan de tarifa elegido. Para más información, consulte [Plan Premium de Azure Functions](functions-premium-plan.md). |
| [**Dedicado (App Service)**](dedicated-plan.md) <br/>(nivel básico o superior) | Cuando necesite ejecutar en máquinas virtuales dedicadas o en aislamiento, utilice imágenes personalizadas o quiera usar el exceso de capacidad del plan de App Service. Usa la [facturación del plan de App Service regular](https://azure.microsoft.com/pricing/details/app-service/). El costo se basa en el plan de tarifa elegido.|

Elija el plan que mejor se adapte a sus requisitos de rendimiento y costo de sus funciones. Para más información, consulte [Escalado y hospedaje de Azure Functions](functions-scale.md).

En este artículo solo se trata el plan de consumo, ya que este plan da como resultado costos variables. Este artículo reemplaza al artículo [Preguntas más frecuentes sobre la facturación de costos del plan de consumo](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

Durable Functions también puede ejecutarse en un plan de consumo. Para más información sobre las consideraciones de costo al usar Durable Functions, consulte [Facturación de Durable Functions](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Costos según el plan de consumo

El *costo* de ejecución de una única ejecución de función se mide en *GB por segundo*. El costo de ejecución se calcula mediante la combinación del uso de memoria con el tiempo de ejecución. Una función que se ejecuta durante más tiempo cuesta más, al igual que una función que consume más memoria. 

Considere un caso en el que la cantidad de memoria que usa la función permanece constante. En este caso, el cálculo del costo es una multiplicación simple. Por ejemplo, suponga que la función ha consumido 0,5 GB durante tres segundos. Después, el costo de ejecución es `0.5GB * 3s = 1.5 GB-seconds`. 

Dado que el uso de la memoria cambia con el tiempo, el cálculo es esencialmente la parte integral del uso de la memoria a lo largo del tiempo.  El sistema realiza este cálculo mediante el muestreo del uso de memoria del proceso (junto con procesos secundarios) a intervalos regulares. Como se mencionó en la [página de precios], el uso de memoria se redondea al cubo de 128 MB más próximo. Cuando el proceso use 160 MB, se le cobrará por 256 MB. El cálculo tiene en cuenta la simultaneidad, que son varias ejecuciones de funciones simultáneas en el mismo proceso.

> [!NOTE]
> Aunque el uso de CPU no se considera directamente en el costo de la ejecución, puede tener un impacto en el costo cuando afecta al tiempo de ejecución de la función.

En el caso de una función desencadenada por HTTP, cuando se produce un error antes de que el código de la función empiece a ejecutarse, no se cobra por una ejecución. Esto significa que las respuestas 401 de la plataforma debidas a la validación de la clave de API o a la característica de autenticación o autorización de App Service no cuentan en el costo de ejecución. Del mismo modo, las respuestas de código de estado 5xx no cuentan cuando se producen en la plataforma antes de que una función procese la solicitud. Una respuesta 5xx generada por la plataforma después de que el código de función se haya empezado a ejecutar se cuenta como una ejecución, aunque el código de la función no genere el error.

## <a name="other-related-costs"></a>Otros costos relacionados

Al calcular el costo general de la ejecución de las funciones en cualquier plan, recuerde que el entorno en tiempo de ejecución de Functions usa otros servicios de Azure, que se facturan por separado. Al calcular los precios de las aplicaciones de funciones, los desencadenadores y enlaces que tenga que se integren con otros servicios de Azure requieren que cree y pague por estos servicios adicionales. 

En el caso de las funciones que se ejecutan en un plan de consumo, el costo total es el costo de ejecución de las funciones, más el costo del ancho de banda y servicios adicionales. 

Al calcular los costos generales de la aplicación de funciones y los servicios relacionados, use la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Costo relacionado | Descripción |
| ------------ | ----------- |
| **Cuenta de almacenamiento** | Cada aplicación de funciones requiere que tenga una [cuenta de almacenamiento de Azure](../storage/common/storage-introduction.md#types-of-storage-accounts) de uso general asociada, que se [factura por separado](https://azure.microsoft.com/pricing/details/storage/). El tiempo de ejecución de Functions usa esta cuenta internamente, pero también se puede usar para los desencadenadores y enlaces de almacenamiento. Si no tiene una cuenta de almacenamiento, se crea una automáticamente cuando se crea la aplicación de funciones. Para más información, consulte [Requisitos de la cuenta de almacenamiento](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Functions se basa en [Application Insights](../azure-monitor/app/app-insights-overview.md) para proporcionar una experiencia de supervisión de alto rendimiento para las aplicaciones de funciones. Aunque no es necesario, debe [habilitar la integración de Application Insights](configure-monitoring.md#enable-application-insights-integration). Cada mes se incluye una concesión gratuita de datos de telemetría. Para más información, consulte la [página de precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Ancho de banda de la red** | No tiene que pagar por las transferencias de datos entre los servicios de Azure de la misma región. Sin embargo, puede incurrir en costos de transferencia de datos de salida a otra región o fuera de Azure. Para más información, consulte [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamientos que afectan al tiempo de ejecución

Los siguientes comportamientos de las funciones pueden afectar al tiempo de ejecución:

+ **Desencadenadores y enlaces**: El tiempo necesario para leer la entrada y escribir la salida en los [enlaces de funciones](functions-triggers-bindings.md) se cuenta como tiempo de ejecución. Por ejemplo, cuando la función usa un enlace de salida para escribir un mensaje en una cola de almacenamiento de Azure, el tiempo de ejecución incluye el tiempo necesario para escribir el mensaje en la cola, que se incluye en el cálculo del costo de la función. 

+ **Ejecución asincrónica**: El tiempo que la función espera los resultados de una solicitud asincrónica (`await` en C#) se cuenta como tiempo de ejecución. El cálculo de GB por segundo se basa en la hora de inicio y finalización de la función y el uso de memoria durante ese período. Lo que sucede durante ese tiempo en términos de actividad de la CPU no se factoriza en el cálculo. Es posible que pueda reducir los costos durante las operaciones asincrónicas mediante el uso de [Durable Functions](durable/durable-functions-overview.md). No se le facturará por el tiempo de espera en las funciones del orquestador.

## <a name="viewing-cost-related-data"></a>Visualización de datos relacionados con los costos

En la [factura](../cost-management-billing/understand/download-azure-invoice.md), puede ver los datos relacionados con los costos del **total de ejecuciones: Funciones** y el **tiempo de ejecución: Funciones**, junto con los costos facturados reales. Sin embargo, estos datos de factura son un agregado mensual para un período de facturación anterior. 

### <a name="function-app-level-metrics"></a>Métricas del nivel de la aplicación de funciones

Para comprender mejor el impacto en el costo de las funciones, puede usar Azure Monitor para ver las métricas relacionadas con los costos que se están generando actualmente en las aplicaciones de funciones. 

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]

### <a name="function-level-metrics"></a>Métricas de nivel de función

Las unidades de ejecución de la función son una combinación de tiempo de ejecución y de uso de memoria, lo que hace que sea una métrica difícil de entender. Los datos de memoria no son una métrica disponible actualmente en Azure Monitor. Sin embargo, si desea optimizar el uso de memoria de la aplicación, puede utilizar los datos del contador de rendimiento recopilados por Application Insights.  

Si aún no lo ha hecho, [habilite Application Insights en la aplicación de funciones](configure-monitoring.md#enable-application-insights-integration). Con esta integración habilitada, puede [consultar estos datos de telemetría en el portal](analyze-telemetry-data.md#query-telemetry-data). 

Puede usar el [explorador de métricas de Azure Monitor](../azure-monitor/essentials/metrics-getting-started.md) en [Azure Portal] o las API REST para obtener los datos de métricas de Monitor.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la supervisión de las aplicaciones de funciones](functions-monitoring.md)

[Página de precios]:https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
