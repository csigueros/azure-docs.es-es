---
title: Procedimientos recomendados de Azure Functions
description: Conozca los procedimientos recomendados para diseñar, implementar y mantener la eficacia del código de función que se ejecuta en Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: f2efa490a9788f0e52b4dfb19b4359f247a671e9
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057726"
---
# <a name="best-practices-for-reliable-azure-functions"></a>Procedimientos recomendados para la creación de funciones de Azure confiables

Azure Functions es una experiencia de proceso a petición basada en eventos que amplía la plataforma de aplicaciones Azure App Service existente con funcionalidades para implementar código desencadenado por eventos que tienen lugar en Azure, en servicios de terceros y en sistemas locales. Functions permite crear soluciones mediante la conexión a orígenes de datos o soluciones de mensajería, lo que facilita el procesamiento de los eventos y la reacción a ellos. Functions se ejecuta en centros de datos de Azure, que son complejos con muchos componentes integrados. En un entorno de nube hospedado, se espera que las máquinas virtuales se reinicien o muevan ocasionalmente y que se produzcan actualizaciones de los sistemas. Es probable que las aplicaciones de funciones también dependan de API externas, servicios de Azure y otras bases de datos, que también son propensas a la falta de confiabilidad periódica. 

En este artículo se detallan algunos procedimientos recomendados para diseñar e implementar aplicaciones de funciones eficaces que mantengan un estado correcto y funcionen bien en un entorno basado en la nube.

## <a name="choose-the-correct-hosting-plan"></a>Elección del plan de hospedaje correcto 

Cuando crea una aplicación de funciones en Azure, debe elegir un plan de hospedaje para su aplicación. El plan que elija repercutirá en el rendimiento, la confiabilidad y el costo. Hay tres planes de hospedaje básicos disponibles para Functions: 

+ [Plan de consumo](consumption-plan.md)
+ [Plan Premium](functions-premium-plan.md)
+ [Plan Dedicado (App Service)](dedicated-plan.md)

Todos los planes de hospedaje están disponibles con carácter general (GA) al ejecutar Linux o Windows.

En el contexto de la plataforma App Service, el plan Premium que se usa para hospedar dinámicamente las funciones es el plan Elástico Premium (EP). Hay otros planes dedicados (App Service) denominados Premium. Para más información, consulte el artículo [Plan Premium](functions-premium-plan.md).

El plan de hospedaje que elija determina los comportamientos siguientes:

+   Cómo se escala la aplicación de funciones según la demanda y cómo se administra la asignación de instancias.
+   Los recursos disponibles para cada instancia de aplicación de funciones.
+   Compatibilidad con funcionalidad avanzada, como la conectividad con Azure Virtual Network.

Para más información sobre cómo elegir el plan de hospedaje correcto y para obtener una comparación detallada entre los planes, consulte [Opciones de hospedaje de Azure Functions](functions-scale.md).

Al crear la aplicación de funciones, es importante que elija el plan correcto. Functions proporciona una capacidad limitada para cambiar de plan de hospedaje, principalmente entre los planes de Consumo y Elástico Premium. Para más información, consulte [Migración del plan](functions-how-to-use-azure-function-app-settings.md?tabs=portal#plan-migration). 

## <a name="configure-storage-correctly"></a>Configuración correcta del almacenamiento

Functions requiere la asociación de una cuenta de almacenamiento a la aplicación de funciones. El host de Functions usa la conexión de la cuenta de almacenamiento para operaciones como la administración de desencadenadores y el registro de ejecuciones de funciones. Esta conexión también se usa al escalar dinámicamente las aplicaciones de funciones. Para más información, consulte [Consideraciones de almacenamiento de Azure Functions](storage-considerations.md).

Un sistema de archivos o una cuenta de almacenamiento mal configurados en la aplicación de funciones puede afectar al rendimiento y la disponibilidad de las funciones. Para obtener ayuda con la solución de problemas de cuentas de almacenamiento que no se han configurado correctamente, consulte el artículo de [solución de problemas de almacenamiento](functions-recover-storage-account.md). 

### <a name="storage-connection-settings"></a>Configuración de la conexión de almacenamiento

Las aplicaciones de funciones que se escalan dinámicamente se pueden ejecutar desde un punto de conexión de Azure Files de la cuenta de almacenamiento o desde los servidores de archivos asociados a las instancias con escalado horizontal. Este comportamiento se controla mediante la siguiente configuración de la aplicación:

+ [WEBSITE_CONTENTAZUREFILECONNECTIONSTRING](functions-app-settings.md#website_contentazurefileconnectionstring)
+ [WEBSITE_CONTENTSHARE](functions-app-settings.md#website_contentshare)

Esta configuración solo se admite cuando trabaja con un plan Premium o un plan de Consumo en Windows.

Cuando se crea la aplicación de funciones en Azure Portal o mediante la CLI de Azure o Azure PowerShell, esta configuración se crea para la aplicación de funciones cuando es necesario. Al crear los recursos a partir de una plantilla de Azure Resource Manager (plantilla de ARM), también debe incluir `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` en la plantilla. 

En la primera implementación con una plantilla de ARM, no incluya `WEBSITE_CONTENTSHARE`, ya que se genera automáticamente.   

Puede usar los siguientes ejemplos de plantilla de ARM para ayudar a configurar correctamente estas opciones:

+ [Plan de consumo](https://azure.microsoft.com/resources/templates/function-app-create-dynamic/)
+ [Plan dedicado](https://azure.microsoft.com/resources/templates/function-app-create-dedicated/)
+ [Plan Premium con integración de red virtual](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)
+ [Plan de consumo con una ranura de implementación](https://azure.microsoft.com/resources/templates/function-app-create-dynamic-slot/)

### <a name="storage-account-configuration"></a>Configuración de la cuenta de almacenamiento

Al crear una aplicación de funciones, debe crear o vincular una cuenta de Azure Storage de uso general compatible con Blob, Queue y Table Storage. Functions depende de Azure Storage para determinadas operaciones; por ejemplo, para administrar los desencadenadores y registrar las ejecuciones de funciones. La cadena de conexión de la cuenta de almacenamiento de la aplicación de funciones se encuentra en los valores de configuración de la aplicación `AzureWebJobsStorage` y `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`.

Al crear esta cuenta de almacenamiento, tenga en cuenta lo siguiente: 

+ Para reducir la latencia, cree la cuenta de almacenamiento en la misma región que la aplicación de funciones.

+ Para mejorar el rendimiento en producción, use una cuenta de almacenamiento distinta para cada aplicación de funciones. Esto es especialmente cierto con Durable Functions y las funciones desencadenadas por el centro de eventos. 

+ Con las funciones desencadenadas por el centro de eventos, no use una cuenta con [Data Lake Storage habilitado](https://github.com/Azure/azure-functions-eventhubs-extension/issues/81).

### <a name="handling-large-data-sets"></a>Administración de grandes conjuntos de datos

Si trabaja en Linux, puede montar un recurso compartido de archivos para agregar almacenamiento adicional. El montaje de un recurso compartido es una manera cómoda de que una función procese un conjunto de datos de gran tamaño existente. Para más información, consulte [Montaje de recursos compartidos de archivos](storage-considerations.md#mount-file-shares).

## <a name="organize-your-functions"></a>Organización de las funciones 

Como parte de la solución, es probable que desarrolle y publique varias funciones. Estas funciones suelen combinarse en una única aplicación de funciones, pero también se pueden ejecutar en aplicaciones de funciones independientes. En los planes de hospedaje Premium y Dedicado (App Service), varias aplicaciones de funciones también pueden compartir los mismos recursos si se usa el mismo plan. La forma de agrupar las funciones y las aplicaciones de funciones puede afectar al rendimiento, el escalado, la configuración, la implementación y la seguridad de la solución global. 

En los planes de Consumo y Premium, todas las funciones de una aplicación de funciones se escalan de forma dinámica.

Para más información sobre cómo organizar las funciones, consulte [Procedimientos recomendados de la organización de funciones](performance-reliability.md#function-organization-best-practices).

## <a name="optimize-deployments"></a>Optimización de las implementaciones

Al implementar una aplicación de funciones, es importante tener en cuenta que la unidad de implementación de las funciones en Azure es la aplicación de funciones. Todas las funciones de una aplicación de funciones se implementan al mismo tiempo, normalmente desde el mismo paquete de implementación.  

Para que la implementación se realice correctamente, tenga en cuenta lo siguiente:

+  Las funciones deben ejecutarse desde el paquete de implementación. Este [enfoque de ejecución desde paquete](run-functions-from-deployment-package.md) proporciona las siguientes ventajas:

    + Se reduce el riesgo de problemas de bloqueo de copia de archivos. 
    + Se puede implementar directamente en una aplicación de producción, lo que desencadena un reinicio. 
    + Se sabe que todos los archivos del paquete están disponibles para la aplicación. 
    + Mejora el rendimiento de las implementaciones de plantillas de ARM.
    + Puede reducir los tiempos de arranque en frío, especialmente para las funciones de JavaScript con árboles de paquete de npm grandes.

+ Considere la posibilidad de usar la [implementación continua](functions-continuous-deployment.md) para conectar las implementaciones a la solución de control de código fuente. Las implementaciones continuas también permiten ejecutar las funciones desde el paquete de implementación.

+ En el [plan de hospedaje Premium](functions-premium-plan.md), considere la posibilidad de agregar un desencadenador de preparación para reducir la latencia cuando se agreguen nuevas instancias. Para más información, consulte [Desencadenador de preparación de Azure Functions](functions-bindings-warmup.md). 

## <a name="write-robust-functions"></a>Escritura de funciones sólidas

Hay varios principios de diseño que puede seguir al escribir código de función que ayudan con el rendimiento general y la disponibilidad de las funciones. Estos principios incluyen:
 
+ [Evitar funciones de larga duración.](performance-reliability.md#avoid-long-running-functions) 
+ [Planear la comunicación entre funciones.](performance-reliability.md#cross-function-communication) 
+ [Escribir funciones que no tengan estado.](performance-reliability.md#write-functions-to-be-stateless)
+ [Escribir funciones defensivas.](performance-reliability.md#write-defensive-functions)

Dado que en la informática en la nube es común la aparición de errores transitorios, debe usar un [patrón de reintento](/azure/architecture/patterns/retry) al acceder a recursos basados en la nube. Muchos desencadenadores y enlaces ya implementan el reintento. 

## <a name="design-for-security"></a>Diseño para seguridad

La seguridad se debe tener en cuenta durante la fase de planeamiento y no después de que las funciones estén listas para usarse. Para aprender a desarrollar e implementar funciones de forma segura, consulte [Protección de Azure Functions](security-concepts.md).  

## <a name="consider-concurrency"></a>Consideración sobre la simultaneidad

A medida que la demanda aumenta en su aplicación de funciones como resultado de los eventos entrantes, las aplicaciones de funciones que se ejecutan en los planes de Consumo y Premium se escalan horizontalmente. Es importante entender cómo responde la aplicación de funciones a la carga y cómo se pueden configurar los desencadenadores para administrar los eventos entrantes. Puede encontrar información general al respecto en [Escalado basado en eventos en Azure Functions](event-driven-scaling.md).

En los planes Dedicados (App Service), debe proporcionar el escalado horizontal de las aplicaciones de funciones. 

### <a name="worker-process-count"></a>Recuento de procesos de trabajo

En algunos casos, es más eficaz controlar la carga mediante la creación de varios procesos, denominados procesos de trabajo de lenguaje, en la instancia antes del escalado horizontal. El número máximo de procesos de trabajo de lenguaje permitidos se controla mediante el valor [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). El valor predeterminado de esta configuración es `1`, lo que significa que no se usan varios procesos. Una vez alcanzado el número máximo de procesos, la aplicación de funciones se escala horizontalmente a más instancias para controlar la carga. Esta configuración no se aplica a las [funciones de la biblioteca de clases de C#](functions-dotnet-class-library.md), que se ejecutan en el proceso de host.

Al usar `FUNCTIONS_WORKER_PROCESS_COUNT` en un plan Premium o en un plan Dedicado (App Service), tenga en cuenta el número de núcleos que proporciona el plan. Por ejemplo, el plan Premium `EP2` proporciona dos núcleos, por lo que debe empezar con un valor de `2` y aumentar en dos según sea necesario, hasta el máximo.

### <a name="trigger-configuration"></a>Configuración del desencadenador

Al planear el rendimiento y el escalado, es importante comprender cómo los distintos tipos de desencadenadores procesan los eventos. Algunos desencadenadores permiten controlar los comportamientos de procesamiento por lotes y administrar la simultaneidad. Frecuentemente, el ajustar los valores de estas opciones puede hacer que cada instancia se escale adecuadamente para satisfacer la demanda de las funciones que se invocan. Estas opciones de configuración se aplican a todos los desencadenadores de una aplicación de funciones y se mantienen en el archivo host.json de la aplicación. Consulte la sección Configuración de la referencia al desencadenador específico para obtener detalles sobre la configuración.

Para más información sobre cómo Functions procesa los flujos de mensajes, consulte [Procesamiento de eventos confiable de Azure Functions](functions-reliable-event-processing.md).

### <a name="plan-for-connections"></a>Planeamiento de conexiones

Las aplicaciones de funciones que se ejecutan en el [plan de Consumo](consumption-plan.md) están sujetas a límites de conexión. Estos límites se aplican por instancia. Debido a estos límites, y como procedimiento recomendado general, debe optimizar las conexiones salientes en el código de función. Para más información, consulte [Administración de conexiones en Azure Functions](manage-connections.md). 

### <a name="language-specific-considerations"></a>Consideraciones específicas sobre el lenguaje

A la hora de elegir el lenguaje, tenga en cuenta lo siguiente:

# <a name="c"></a>[C#](#tab/csharp)

+ [Use el código asincrónico, pero evite las llamadas de bloqueo](performance-reliability.md#use-async-code-but-avoid-blocking-calls).

+ [Use tokens de cancelación](functions-dotnet-class-library.md?#cancellation-tokens) (solo en proceso).

# <a name="java"></a>[Java](#tab/java)

+ En el caso de aplicaciones que son una combinación de operaciones enlazadas a CPU y a E/S, considere la posibilidad de usar [más procesos de trabajo](functions-app-settings.md#functions_worker_process_count).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

+ [Use `async` y `await`](functions-reference-node.md#use-async-and-await).

+ [Use varios procesos de trabajo para aplicaciones enlazadas a CPU](functions-reference-node.md?tabs=v2#scaling-and-concurrency).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Revise las consideraciones sobre simultaneidad](functions-reference-powershell.md#concurrency).

# <a name="python"></a>[Python](#tab/python)

+ [Mejora del rendimiento de las aplicaciones de Python en Azure Functions](python-scale-performance-reference.md)

---

## <a name="maximize-availability"></a>Maximizar la disponibilidad

El inicio en frío es uno de los aspectos clave que se deben tener en cuenta en las arquitecturas sin servidor. Para más información, consulte [Inicios en frío](event-driven-scaling.md#cold-start). Si el inicio en frío es motivo de preocupación en su escenario, puede encontrar más información en la publicación [Descripción del inicio en frío sin servidor](https://azure.microsoft.com/blog/understanding-serverless-cold-start/). 

El plan Premium es el plan recomendado para reducir los inicios en frío, pero sin perder la escala dinámica. Puede usar las instrucciones siguientes para reducir los inicios en frío y mejorar la disponibilidad en los tres planes de hospedaje. 

| Planear | Instrucciones |
| --- | --- | 
| **Plan Premium** | • [Implementar un desencadenador de preparación en la aplicación de funciones](functions-bindings-warmup.md)<br/>• [Establecer los valores de las instancias Always-Ready y el límite máximo de ráfaga](functions-premium-plan.md#plan-and-sku-settings)<br/>• [Usar la compatibilidad con desencadenadores de red virtual cuando se emplean desencadenadores que no son HTTP en una red virtual](functions-networking-options.md#premium-plan-with-virtual-network-triggers)|
| **Planes dedicados** | • [Trabajar en al menos dos instancias con la comprobación de estado de Azure App Service habilitada](../app-service/monitor-instances-health-check.md)<br/>• [Implementar el escalado automático](/azure/architecture/best-practices/auto-scaling)|
| **Plan de consumo** | • Revisar el uso de patrones singleton y la configuración de simultaneidad de enlaces y desencadenadores para evitar colocar artificialmente límites en el modo en que se escala la aplicación de función<br/>• [Revisar el valor de configuración `functionAppScaleLimit`, que puede limitar el escalado horizontal](event-driven-scaling.md#limit-scale-out)<br/>• Comprobar que existe un límite de cuota de uso diario (GB-s) establecido durante las fases de desarrollo y prueba. Considere la posibilidad de quitar este límite en entornos de producción. |

## <a name="monitor-effectively"></a>Supervisión eficaz

Azure Functions ofrece integración de fábrica con Azure Application Insights para supervisar la ejecución de funciones y los seguimientos escritos con el código. Para más información, consulte [Supervisión de Azure Functions](functions-monitoring.md). Azure Monitor también proporciona utilidades para supervisar el estado de la propia aplicación de funciones. Para obtener más información, vea [Uso de métricas de Azure Monitor con Azure Functions](monitor-metrics.md).

Al usar Application Insights para supervisar las funciones, debe tener en cuenta los siguientes aspectos:

+ Asegúrese de que se ha quitado la configuración de la aplicación [AzureWebJobsDashboard](functions-app-settings.md#azurewebjobsdashboard). Esta configuración se admite en versiones anteriores de Functions. Si existe, al quitar `AzureWebJobsDashboard` se mejora el rendimiento de las funciones. 

+  Revise los [registros de Application Insights](analyze-telemetry-data.md). Si faltan datos que esperaba encontrar, puede ajustar la configuración de muestreo para capturar mejor su escenario de supervisión. Puede usar el valor de configuración `excludedTypes` para excluir determinados tipos del muestreo, como `Request` o `Exception`. Para más información, consulte [Configuración del muestreo](configure-monitoring.md?tabs=v2#configure-sampling).

Azure Functions también permite [enviar registros generados por el sistema y generados por el usuario a los registros de Azure Monitor](functions-monitor-log-analytics.md). La integración con registros de Azure Monitor está actualmente en versión preliminar pública. 

## <a name="build-in-redundancy"></a>Creación de redundancia

Las necesidades empresariales pueden exigir que las funciones estén siempre disponibles, incluso durante una interrupción del centro de datos. Para aprender a usar un enfoque multirregional a fin de mantener siempre en ejecución las funciones críticas, consulte [Recuperación ante desastres geográfica y alta disponibilidad de Azure Functions](functions-geo-disaster-recovery.md).

## <a name="next-steps"></a>Pasos siguientes

[Administración de la aplicación de funciones](functions-how-to-use-azure-function-app-settings.md)
