---
title: Capacidad de una instancia de Azure API Management | Microsoft Docs
description: En este artículo se explica qué es la métrica de capacidad y cómo tomar decisiones fundadas acerca de si se debe escalar una instancia de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: danlep
ms.custom: fasttrack-edit
ms.openlocfilehash: 88909f2f4c7ed6d0b079af53eeaa1f11416d176c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629756"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacidad de una instancia de Azure API Management

La **capacidad** la [métrica de Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) más importante para tomar decisiones informadas acerca de si se debe escalar una instancia de API Management para acomodar más carga. Su construcción es compleja e impone un comportamiento concreto.

En este artículo se explica lo que es la **capacidad** y su comportamiento. Muestra cómo acceder a la métrica de **capacidad** en Azure Portal y sugiere cuándo hay que considerar la posibilidad de escalar o actualizar una instancia de API Management.

> [!IMPORTANT]
> En este artículo se describe cómo puede supervisar y escalar su instancia de Azure API Management en función de su métrica de capacidad. Sin embargo, es igualmente importante comprender lo que ocurre cuando una instancia individual de API Management ha *alcanzado* realmente su capacidad. Azure API Management no aplicará ninguna limitación de nivel de servicio para evitar una sobrecarga física de las instancias. Cuando una instancia alcanza su capacidad física, se comporta de forma similar a cualquier servidor web sobrecargado que no puede procesar las solicitudes entrantes: la latencia aumentará, se eliminarán las conexiones, se producirán errores de tiempo de espera, etc. Es decir, los clientes de API deben estar preparados para hacer frente a esta posibilidad de manera similar a como ocurre con cualquier otro servicio externo (por ejemplo, mediante la aplicación de directivas de reintento).

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos de este artículo, debe tener:

+ Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Una instancia de APIM. Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>¿Qué es la capacidad?

![Diagrama que explica la métrica de capacidad.](./media/api-management-capacity/capacity-ingredients.png)

La **capacidad** es un indicador de la carga de una instancia de API Management. Refleja el uso de los recursos (CPU y memoria) y las longitudes de cola de la red. El uso de la CPU y la memoria revela el consumo de recursos por parte de:

+ Los servicios de plano de datos de API Management, como el procesamiento de solicitudes, lo que puede incluir el reenvío de solicitudes o la ejecución de una directiva.
+ Los servicios de plano de datos de API Management, como las acciones de administración aplicadas a través de Azure Portal o ARM, o la carga procedente del [portal para desarrolladores](api-management-howto-developer-portal.md).
+ Procesos seleccionados del sistema operativo, entre los que se incluyen los procesos que implican el costo de los protocolos de enlace TLS en las nuevas conexiones.

La **capacidad** total es un promedio de sus propios valores de cada unidad de una instancia de API Management.

Aunque la **métrica de capacidad** está diseñada para solucionar problemas con la instancia de API Management, hay casos en los que los problemas no se reflejarán en los cambios en la **métrica de capacidad**.

## <a name="capacity-metric-behavior"></a>Comportamiento de la métrica de capacidad

Dada su construcción, en la vida real la **capacidad** puede verse afectada por muchas variables, como por ejemplo:

+ patrones de conexión (conexión nueva en una solicitud frente a reutilización de la conexión existente);
+ tamaño de una solicitud y respuesta;
+ directivas configuradas en cada API o número de clientes que envían solicitudes.

Cuanto más complejas sean las operaciones de las solicitudes, más alto será el consumo de la **capacidad**. Por ejemplo, las directivas de transformación compleja consumen mucha más CPU que el reenvío de una solicitud simple. Las respuestas lentas al servicio del back-end también la incrementarán.

> [!IMPORTANT]
> La **capacidad** no es una medida directa del número de solicitudes que se procesan.

![Picos de la métrica de capacidad](./media/api-management-capacity/capacity-spikes.png)

La **capacidad** también puede tener picos intermitentes o ser mayor que cero aunque no se procesen solicitudes. Esto sucede debido a acciones específicas del sistema o de la plataforma, y no debe tenerse en cuenta al decidir si se escala una instancia.

Una **métrica de capacidad** baja no implica necesariamente que la instancia de API Management no experimente ningún problema.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Uso de Azure Portal para examinar la capacidad
  
![Métrica de capacidad](./media/api-management-capacity/capacity-metric.png)  

1. Acceda a la instancia de APIM de [Azure Portal](https://portal.azure.com/).
2. Seleccione **Métricas**.
3. En la sección púrpura, seleccione la métrica **Capacidad** y deje la agregación **Avg** predeterminada.

    > [!TIP]
    > Para evitar interpretaciones incorrectas, siempre debe mirar el desglose de la métrica de **capacidad** por ubicación.

4. En la sección verde, seleccione **Ubicación** para dividir la métrica por dimensión.
5. Elija el período que desee en la barra superior de la sección.

    Puede establecer una alerta de métrica que le avise cuando ocurra algo inesperado. Por ejemplo, obtenga notificaciones cuando la instancia de APIM haya superado su capacidad máxima esperada durante más de 20 minutos.

    >[!TIP]
    > Puede configurar alertas para saber en qué momento se está quedando sin capacidad un servicio o utilizar la funcionalidad de escalado automático de Azure Monitor para agregar automáticamente una unidad de Azure API Management. La operación de escalado puede tardar unos 30 minutos, por lo que debe planear las reglas en consecuencia.  
    > Solo se permite el escalado de la ubicación maestra.

## <a name="use-capacity-for-scaling-decisions"></a>Uso de la capacidad para decidir acerca del escalado

La **capacidad** la métrica de para tomar decisiones acerca de si se debe escalar una instancia de API Management para acomodar más carga. Considere:

+ El examen del promedio y de la tendencia a largo plazo.
+ La omisión de picos repentinos que muy probablemente no estén relacionados con los aumentos en la carga (para obtener una explicación, consulte la sección "Comportamiento de la métrica de capacidad").
+ La actualización o el escalado de la instancia cuando el valor de la **capacidad** supera el 60 % o 70 % durante un período mayor (por ejemplo, 30 minutos). Para su servicio o escenario es posible que sean más apropiados otros valores.

>[!TIP]  
> Si puede calcular el tráfico de antemano, pruebe su instancia de APIM en las cargas de trabajo que espera. Puede aumentar la carga de solicitudes en el inquilino gradualmente y supervisar qué valor de la métrica de capacidad corresponde a la carga máxima. Siga los pasos de la sección anterior para usar Azure Portal para saber la capacidad que se utiliza en un momento dado.

## <a name="next-steps"></a>Pasos siguientes

- [Escalado o actualización de una instancia del servicio Azure API Management](upgrade-and-scale.md)
- [Optimización y ahorro del gasto en la nube](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)