---
title: Escalado basado en eventos en Azure Functions
description: Explica los comportamientos de escalado de las aplicaciones de funciones en el plan de consumo y el plan Prémium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: ff7194b5dfda09253526aa79b52be475c131a411
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752273"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Escalado basado en eventos en Azure Functions

En los planes de consumo y Prémium, Azure Functions escala los recursos de CPU y memoria mediante la incorporación de instancias adicionales del host de Functions. El número de instancias se determina sobre el número de eventos que desencadenan una función. 

Cada instancia del host de Functions del plan de consumo tiene una limitación de 1.5 GB de memoria y una CPU.  Una instancia del host es la aplicación de funciones completa, lo que significa que todas las funciones de una aplicación de funciones comparten recursos al mismo tiempo en una instancia y escala determinadas. Las aplicaciones de funciones que comparten el mismo plan de consumo se escalan de manera independiente.  En el plan Prémium, el tamaño del plan determina la memoria y la CPU disponibles para todas las aplicaciones de ese plan en esa instancia.  

Los archivos de código de función se almacenan en recursos compartidos de Azure Files en la cuenta de almacenamiento principal de la función. Al eliminarse la cuenta de almacenamiento principal de la aplicación de función, los archivos de código de función también se eliminan y no se pueden recuperar.

## <a name="runtime-scaling"></a>Escalado del entorno de tiempo de ejecución

Azure Functions usa un componente denominado *controlador de escala* para supervisar la tasa de eventos y determinar si se debe escalar o reducir horizontalmente. El controlador de escala usa la heurística para cada tipo de desencadenador. Por ejemplo, al usar un desencadenador de Azure Queue Storage, se escala en función de la longitud de la cola y la antigüedad del mensaje más antiguo de la cola.

La unidad de escala de Azure Functions es la aplicación de funciones. Al escalar horizontalmente la aplicación de función, se asignan recursos adicionales para ejecutar varias instancias del host de Azure Functions. Por el contrario, si la demanda se reduce, el controlador de escala elimina instancias del host de la función. El número de instancias se "reduce horizontalmente" hasta cero cuando no se ejecuta ninguna función en la aplicación de funciones.

![Controlador de escala que supervisa los eventos y la creación de instancias](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Arranque en frío

Una vez que la aplicación de funciones ha estado inactiva durante varios minutos, la plataforma puede escalar el número de instancias en las que la aplicación se ejecuta hasta cero. La siguiente solicitud tiene la latencia agregada al escalar de cero a uno. Esta latencia se conoce como _arranque en frío_. El número de dependencias que requiere la aplicación de funciones puede afectar el momento del arranque en frío. El arranque en frío es más problemático para las operaciones sincrónicas, como los desencadenadores HTTP que deben devolver una respuesta. Si los arranques en frío afectan a las funciones, considere la posibilidad de realizar las ejecuciones en un plan Prémium o en un plan dedicado con la opción **AlwaysOn** habilitada.   

## <a name="understanding-scaling-behaviors"></a>Descripción de los comportamientos de escalado

El escalado puede variar en función de varios factores, y realizarse de forma diferente según el desencadenador y el idioma seleccionados. Hay algunas complejidades de los comportamientos del escalado que hay que tener en cuenta:

* **Número máximo de instancias:** Una aplicación de funciones única solo se escala horizontalmente hasta un máximo de 200 instancias. Una única instancia puede procesar más de un mensaje o solicitud a la vez, por lo que no hay un límite establecido en el número de ejecuciones simultáneas.  Puede [especificar un máximo inferior](#limit-scale-out) para limitar la escala según se requiera.
* **Nueva tasa de instancias:** En el caso de los desencadenadores HTTP, solo se asignan nuevas instancias como máximo una vez cada segundo. Para los desencadenadores que no son HTTP, solo se asignan nuevas instancias como máximo una vez cada 30 segundos. El escalado es más rápido cuando se ejecuta en un plan [Premium](functions-premium-plan.md).
* **Eficiencia de escala:** En el caso de los desencadenadores de Service Bus, use los derechos de _Administración_ en los recursos para obtener el escalado más eficaz. Con los derechos de _Escucha_, el escalado no es tan preciso porque la longitud de la cola no se puede utilizar para informar sobre las decisiones de escalado. Para más información sobre cómo establecer derechos en las directivas de acceso de Service Bus, consulte [Directivas de autorización de acceso compartido](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Para los desencadenadores de Event Hubs, consulte la [guía de escalado](functions-bindings-event-hubs-trigger.md#scaling) en el artículo de referencia. 

## <a name="limit-scale-out"></a>Límite de escalabilidad horizontal

Es posible que quiera restringir el número máximo de instancias que una aplicación usa para realizar el escalado horizontal.  Esto es más común en los casos en los que un componente de nivel inferior, como una base de datos, tiene un rendimiento limitado.  De forma predeterminada, las funciones del plan de consumo se escalan horizontalmente hasta un máximo de 200 instancias, mientras que las funciones del plan Prémium se escalarán horizontalmente hasta un máximo de 100 instancias.  Puede especificar un máximo inferior para una aplicación específica modificando el valor `functionAppScaleLimit`.  `functionAppScaleLimit` se puede establecer en `0` o `null` para indicar que no hay restricciones, o en un valor válido entre `1` y el máximo de la aplicación.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

```azurepowershell
$resource = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName <RESOURCE_GROUP> -Name <FUNCTION_APP-NAME>/config/web
$resource.Properties.functionAppScaleLimit = <SCALE_LIMIT>
$resource | Set-AzResource -Force
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedimientos recomendados y patrones para aplicaciones escalables

Hay muchos aspectos de una aplicación de funciones que afectan a cómo se escala, como la configuración del host, la superficie del entorno de tiempo de ejecución y la eficacia de los recursos.  Para obtener más información, consulte la [sección de escalabilidad del artículo sobre consideraciones de rendimiento](functions-best-practices.md#scalability-best-practices). También debe tener en cuenta cómo se comportan las conexiones a medida que la aplicación de función se escala. Para más información, consulte [How to manage connections in Azure Functions](manage-connections.md) (Administración de conexiones en Azure Functions).

Para obtener más información sobre el escalado en Python y Node.js, consulte la [Guía de Azure Functions para desarrolladores de Python: escalado y simultaneidad](functions-reference-python.md#scaling-and-performance) y la [Guía para desarrolladores de Node.js para Azure Functions: escalado y simultaneidad](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Modelo de facturación

La facturación de los diferentes planes se describe en detalle en la [página de precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/). El uso se agrega en el nivel de la aplicación de función, y solo se cuenta el tiempo que el código de la función está en ejecución. Estas son las unidades de facturación:

* **Consumo de recursos en gigabytes-segundo (GB-s)** . Se calcula como una combinación del tamaño de la memoria y el tiempo de ejecución de todas las funciones de una aplicación de función. 
* **Ejecuciones**. Se cuenta cada vez que se ejecuta una función en respuesta a un desencadenador de eventos.

Puede encontrar consultas útiles y obtener información sobre cómo comprender la factura de consumo [en las P+F sobre facturación](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Pasos siguientes

+ [Opciones de hospedaje de Azure Functions](functions-scale.md)

