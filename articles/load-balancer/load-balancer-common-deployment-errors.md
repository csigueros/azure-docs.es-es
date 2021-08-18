---
title: Solución de errores de implementación comunes
titleSuffix: Azure Load Balancer
description: Describe cómo resolver errores comunes al implementar equilibradores de carga de Azure.
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: da7d31d018853b340be4506af48a602e7cb2765b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437943"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Solución de errores comunes de implementación de Azure con Azure Load Balancer

En este artículo se describen algunos errores comunes de implementación de Azure Load Balancer y se proporciona información sobre cómo resolverlos. Si busca información sobre un código de error y esa información no se proporciona en este artículo, háganoslo saber. En la parte inferior de esta página, puede dejar comentarios. Se realiza un seguimiento de los comentarios con problemas de GitHub.

## <a name="error-codes"></a>Códigos de error

| Código de error | Detalles y mitigación |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Las SKU de la IP pública y de Load Balancer deben coincidir. Asegúrese de que las SKU de la IP pública y de Azure Load Balancer coincidan. Se recomienda usar SKU estándar para cargas de trabajo de producción. Más información sobre las [diferencias en las SKU](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Los conjuntos de escalado de máquinas virtuales usan Basic Load Balancer de forma predeterminada cuando no se especifica la SKU o se implementa sin direcciones IP públicas estándar. Vuelva a implementar el conjunto de escalado de máquinas virtuales con direcciones IP públicas estándar en las instancias individuales para asegurarse de que ha seleccionado Standard Load Balancer, o simplemente seleccione un equilibrador de carga estándar al implementar el conjunto de escalado de máquinas virtuales en Azure Portal. |
|MaxAvailabilitySetsInLoadBalancerReached | El grupo de back-end de un equilibrador de carga puede contener como máximo 150 conjuntos de disponibilidad. Si no tiene conjuntos de disponibilidad definidos explícitamente para las máquinas virtuales en el grupo de back-end, cada máquina virtual individual entra en su propio conjunto de disponibilidad. Por lo tanto, la implementación de 150 máquinas virtuales independientes implicaría que tendría 150 conjuntos de disponibilidad, lo que alcanza el límite. Puede implementar un conjunto de disponibilidad y agregarle más máquinas virtuales como solución alternativa. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | En el caso del equilibrador de carga de la SKU básica, la interfaz de red y el equilibrador de carga deben estar en el mismo conjunto de disponibilidad. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Un conjunto de escalado de máquinas virtuales solo puede hacer referencia a una regla en un tipo de equilibrador de carga determinado (interno, público) con el mismo puerto de back-end y protocolo. Actualice la regla para cambiar esta creación de regla duplicada. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Un conjunto de escalado de máquinas virtuales solo puede hacer referencia a una regla en un tipo de equilibrador de carga determinado (interno, público) con el mismo puerto de back-end y protocolo. Actualice los parámetros de la regla para cambiar esta creación de regla duplicada. |
|AnotherInternalLoadBalancerExists| Solo puede tener un equilibrador de carga de tipo interno que haga referencia al mismo conjunto de interfaces de red o máquinas virtuales en el back-end del equilibrador de carga. Actualice la implementación para asegurarse de que está creando solo una instancia de Load Balancer del mismo tipo. |
|CannotUseInactiveHealthProbe| No puede tener un sondeo que no lo use ninguna regla configurada para el mantenimiento del conjunto de escalado de máquinas virtuales. Asegúrese de que el sondeo que se está configurando se use activamente. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| No puede tener varios equilibradores de carga del mismo tipo (interno, público). Puede tener un equilibrador de carga público interno como máximo. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | No se admite Basic Load Balancer para conjuntos de escalado de máquinas virtuales de múltiples ubicaciones o para el conjunto de escalado de máquinas virtuales de varias zonas de disponibilidad. Use Standard Load Balancer en su lugar. |
|MarketplacePurchaseEligibilityFailed | Cambie a la cuenta administrativa correcta para habilitar las compras debido a que la suscripción es un una suscripción EA. Puede leer más [aquí](../marketplace/marketplace-faq-publisher-guide.yml#what-could-block-a-customer-from-completing-a-purchase-). |
|ResourceDeploymentFailure| Si el equilibrador de carga está en un estado de error, siga estos pasos para recuperarlo de dicho estado:<ol><li>Vaya a https://resources.azure.com e inicie sesión con las credenciales de Azure Portal.</li><li>Seleccione **Lectura o escritura**.</li><li>A la izquierda, expanda **Suscripciones** y, a continuación, expanda la suscripción con la instancia de Load Balancer que se va a actualizar.</li><li>Expanda **ResourceGroups** y, a continuación, expanda el grupo de recursos con la instancia de Load Balancer que se va a actualizar.</li><li>Seleccione **Microsoft.Network** > **LoadBalancers** y, a continuación, seleccione la instancia de Load Balancer que se va a actualizar, **LoadBalancer_1**.</li><li>En la página para mostrar de **LoadBalancer_1**, seleccione **GET** > **Editar**.</li><li>Actualice el valor de **ProvisioningState** de **Incorrecto** a **Correcto**.</li><li>Seleccione **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Pasos siguientes

* Consulte la [tabla de comparación de SKU](./skus.md) de Azure Load Balancer
* Más información sobre los [límites de Azure Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).