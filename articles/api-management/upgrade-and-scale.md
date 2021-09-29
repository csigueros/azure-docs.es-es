---
title: Actualización y escalado de una instancia de Azure API Management | Microsoft Docs
description: En este tema, se explica cómo se actualiza y se escala una instancia de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: danlep
ms.openlocfilehash: 9cec5717c6c25faafc7349a4c18b60cc30e97f26
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659975"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Actualización y escalado de una instancia de Azure API Management  

Para escalar una instancia de Azure API Management, los clientes pueden agregar o quitar unidades. Una **unidad** se compone de recursos de Azure dedicados y tiene cierta capacidad de carga, que se expresa mediante el número de llamadas API que se realizan cada mes. Dicho número no representa un límite de llamadas, sino un valor de rendimiento máximo que permite el planeamiento de la capacidad aproximada. El rendimiento y la latencia reales varían considerablemente en función de factores como el número y la tasa de conexiones concurrentes, el tipo y número de directivas configuradas, los tamaños de las solicitudes y respuestas y la latencia del back-end.

La capacidad y el precio de cada unidad dependen del **nivel** en que se encuentra la unidad. Puede elegir entre cuatro niveles: **Desarrollador**, **Básico**, **Estándar**, **Premium**. Si necesita más capacidad para un servicio de un nivel, debe agregar una unidad. Si el nivel que está seleccionado actualmente en la instancia de API Management no permite agregar más unidades, deberá actualizar a un nivel superior.

El precio de cada unidad y las características disponibles (por ejemplo, la implementación en varias regiones) dependen del nivel elegido para la instancia de API Management. En el artículo sobre los [precios](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), se explican el precio por unidad y las características que se obtienen en cada nivel. 

>[!NOTE]
>En este artículo sobre los [precios](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), se indica el número aproximado de unidades que puede tener cada nivel. Para obtener unos datos más exactos, deberá consultar un escenario que se ajuste a la realidad de sus API. Consulte el artículo [Capacidad de una instancia de Azure API Management](api-management-capacity.md).

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos de este artículo, debe:

+ Tener una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Tener una instancia de API Management. Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

+ Comprender el concepto de [Capacidad de una instancia de Azure API Management](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Actualización y escalado  

Puede elegir entre cuatro niveles: **Desarrollador**, **Básico**, **Estándar** y **Premium**. El nivel **Desarrollador** debe utilizarse para evaluar el servicio; no debe emplearse para producción. El nivel **Desarrollador** no dispone de un Acuerdo de Nivel de Servicio y no se puede escalar (no se pueden agregar o quitar unidades). 

Los niveles de producción **Básico**, **Estándar** y **Premium** cuentan con un Acuerdo de Nivel de Servicio y se pueden escalar. El nivel **Básico** es el más económico con un Acuerdo de Nivel de Servicio (SLA) y puede escalarse hasta dos unidades. El nivel **Estándar** puede escalarse hasta cuatro unidades. Puede agregar cualquier número de unidades en el nivel **Premium**.

El nivel **Premium** permite distribuir una instancia de Azure API Management individual entre cualquier número de regiones de Azure. Inicialmente, cuando se crea un servicio Azure API Management, la instancia contiene una sola unidad y reside en una única región de Azure. La región inicial se designa como región **primaria**. Pueden agregarse otras regiones fácilmente. Cuando agregue una región, debe especificar el número de unidades que desea asignar. Por ejemplo, puede tener una unidad en la región **primaria** y cinco unidades en otra región. Puede adaptar el número de unidades al tráfico que tiene en cada región. Para más información, consulte [Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md).

Puede cambiar un nivel por otro superior o inferior. El cambio a un nivel superior o inferior puede quitar algunas características, como ocurre con las redes virtuales o las implementaciones en varias regiones cuando se pasa del nivel Premium al nivel Estándar o Básico.

> [!NOTE]
> El proceso de actualización o escalado puede tardar entre 15 y 45 minutos en aplicarse. Recibirá una notificación cuando se haya completado.

> [!NOTE]
> El servicio API Management en el nivel **Consumo** se escala automáticamente en función del tráfico.

## <a name="scale-your-api-management-service"></a>Escalado del servicio API Management

![Escalado de un servicio API Management en Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Vaya al servicio API Management en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Ubicaciones** en el menú.
3. Haga clic en la fila con la ubicación que desea escalar.
4. Especifique el nuevo número de **unidades**: utilice el control deslizante o escriba directamente el número.
5. Haga clic en **Aplicar**.

## <a name="change-your-api-management-service-tier"></a>Cambio del nivel de servicio de API Management

1. Vaya al servicio API Management en [Azure Portal](https://portal.azure.com/).
2. Haga clic en **Plan de tarifa** del menú.
3. Seleccione el nivel de servicio que desee de la lista desplegable. Use el control deslizante para especificar la escala del servicio API Management después del cambio.
4. Haga clic en **Save**(Guardar).

## <a name="downtime-during-scaling-up-and-down"></a>Tiempo de inactividad durante el escalado y reducción vertical
Si va a escalar desde o hasta el nivel Desarrollador, habrá tiempo de inactividad. De lo contrario, no hay tiempo de inactividad. 

## <a name="compute-isolation"></a>Aislamiento de proceso
Si los requisitos de seguridad incluyen [aislamiento de proceso](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), puede usar el plan de tarifa **Aislado**. Este nivel garantiza que los recursos de proceso de una instancia de servicio de API Management consuman todo el host físico y proporcionen el nivel de aislamiento necesario para admitir, por ejemplo, las cargas de trabajo de nivel de impacto 5 (IL5) del Departamento de Defensa de Estados Unidos. Para obtener acceso al nivel aislado, [cree una incidencia de soporte técnico](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Pasos siguientes

- [Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md)
- [Escalado automático de una instancia de servicio de Azure API Management](api-management-howto-autoscale.md)
- [Planeamiento y administración de costos de API Management](plan-manage-costs.md).
- [Límites de API Management](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)