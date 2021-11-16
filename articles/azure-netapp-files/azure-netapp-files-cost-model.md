---
title: Modo de costo de Azure NetApp Files | Microsoft Docs
description: Describe el modelo de costo de Azure NetApp Files para administrar los gastos del servicio.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: b-juche
ms.openlocfilehash: 56091fb0c6ddfa205fc60f6aef06397a0314369e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053167"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modelo de costo de Azure NetApp Files 

Entender el modelo de costo de Azure NetApp Files ayuda a administrar los gastos del servicio. 

Para el modelo de costos específico de la replicación entre regiones, consulte [Modelo de costos de la replicación entre regiones](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Cálculo del consumo de la capacidad

Azure NetApp Files se factura por capacidad de almacenamiento aprovisionada, que se asigna mediante la creación de grupos de capacidad. Los grupos de capacidad se facturan mensualmente en función de un costo establecido por GiB asignado por hora. La asignación del grupo de capacidad se mide cada hora.  

Los grupos de capacidad deben tener al menos 4 TiB y se pueden aumentar o reducir en intervalos de 1 TiB. Los grupos de capacidad contienen volúmenes cuyo tamaño oscila entre un  100 GiB y 100 TiB. A los volúmenes se les asignan cuotas que se restan del tamaño aprovisionado del grupo de capacidad. En el caso de los volúmenes activos, el consumo de capacidad frente a la cuota se basa en la capacidad lógica (efectiva), sean datos del sistema de archivos activo o datos de instantáneas. Para más información, consulte [Funcionamiento de las instantáneas de Azure NetApp Files](snapshots-introduction.md). 

### <a name="pricing-examples"></a>Ejemplos de precios

En esta sección se muestran ejemplos que le ayudan a conocer el modelo de costos de Azure NetApp Files.

#### <a name="example-1-one-month-cost-with-static-versus-dynamic-capacity-pool-provisioning"></a>Ejemplo 1: Costo de un mes con aprovisionamiento de grupos de capacidad estático, frente a dinámico 

Si los requisitos de tamaño del grupo de capacidad fluctúan (por ejemplo, debido a las variables necesidades de capacidad o rendimiento), considere la posibilidad de [cambiar dinámicamente el tamaño de los volúmenes y grupos de capacidad](azure-netapp-files-resize-capacity-pools-or-volumes.md) para equilibrar el costo con las necesidades de capacidad y rendimiento.

Por ejemplo, usa la capacidad Prémium 24 horas (1 día) a 10 TiB, 96 horas (4 días) a 24 TiB, cuatro veces a 6 horas (1 día) a 5 TiB, 480 horas (20 días) a 6 TiB y las restantes horas del mes a 0 TiB. Un perfil de implementación dinámica de consumo de nube es diferente de un perfil de consumo local estático tradicional: 

[ ![Gráfico de barras que muestra el aprovisionamiento dinámico frente al aprovisionamiento de grupos de capacidad estático.](../media/azure-netapp-files/cost-model-example-one-capacity.png) ](../media/azure-netapp-files/cost-model-example-one-capacity.png#lightbox)

Cuando los costos se facturan a 0,000403 USD por GiB/hora ([los precios dependen de la región](https://azure.microsoft.com/pricing/details/netapp/)), el desglose de costos mensuales es similar a este:

*Aprovisionamiento estático en Prémium (capacidad/rendimiento máximos)*

* 24 TiB x 720 horas x 0,000403 USD por GiB/hora = 7130,97 USD al mes (237,70 USD al día) 

*Aprovisionamiento dinámico mediante el ajuste de tamaño del volumen y del grupo de capacidad* 

* 10 TiB x 24 horas x 0,000403 USD por GiB/hora = 99,04 USD
* 24 TiB x 96 horas x 0,000403 USD por GiB/hora = 950,80 USD
* 6 TiB x 480 horas x 0,000403 USD por GiB/hora = 1188,50 USD
* Total = **2238,33 USD**

[ ![Gráfico de barras que muestra el modelo de costo de nivel de servicio estático, frente al dinámico.](../media/azure-netapp-files/cost-model-example-one-pricing.png) ](../media/azure-netapp-files/cost-model-example-one-pricing.png#lightbox)

Este escenario supone un ahorro mensual de 4892,64 USD, en comparación con el aprovisionamiento estático.

#### <a name="example-2-one-month-cost-with-and-without-dynamic-service-level-change"></a>Ejemplo 2: costo de un mes con y sin cambio de nivel de servicio dinámico

Si los requisitos de tamaño del grupo de capacidad siguen siendo los mismos, pero los requisitos de rendimiento fluctúan, considere la posibilidad de [cambiar dinámicamente el nivel de servicio de un volumen](dynamic-change-volume-service-level.md). Puede aprovisionar y desaprovisionar grupos de capacidad de diferentes tipos a lo largo del mes, lo que proporciona un rendimiento Just-In-Time y reduce los costos durante períodos en los que no se necesita rendimiento. 

Piense en un escenario en el que el requisito de capacidad es de una constante de 24 TiB. Pero las necesidades de rendimiento fluctúan entre las 384 horas (16 días) del nivel de servicio Estándar, las 120 horas (5 días) del nivel de servicio Prémium, las 168 horas (7 días) del nivel de servicio Ultra y, después, hasta 48 horas (2 días) de rendimiento de nivel de servicio estándar. En este escenario, un perfil de implementación dinámica de consumo de nube es diferente, si se compara con un perfil de consumo local estático tradicional: 

[ ![Gráfico de barras que muestra el aprovisionamiento con y sin cambio dinámico en el nivel de servicio.](../media/azure-netapp-files/cost-model-example-two-capacity.png) ](../media/azure-netapp-files/cost-model-example-two-capacity.png#lightbox)

En este caso, cuando los costos se facturan a 0,000202 USD por GiB/hora (Estándar), 0,000403 USD por GiB/hora (Prémium) y 0,000538 USD por GiB/hora (Ultra), respectivamente ([los precios dependen de la región](https://azure.microsoft.com/pricing/details/netapp/)), el desglose de costos mensuales es como este: 

*Aprovisionamiento estático en el nivel de servicio Ultra (rendimiento máximo)*

* 24 TiB x 720 horas x 0,000538 USD por GiB/hora = 9519,76 USD al mes (317,33 USD al día) 
 
*Aprovisionamiento dinámico mediante el cambio de nivel de servicio dinámico*

* 24 TiB x 384 horas x 0,000202 USD por GiB/hora = 1901,31 USD  
* 24 TiB x 120 horas x 0,000403 USD por GiB/hora = 1188,50 USD  
* 24 TiB x 168 horas x 0,000538 USD por GiB/hora = 2221,28 USD  
* 24 TiB x 48 horas x 0,000202 USD por GiB/hora = 238,29 USD 
* Total = **5554,37 USD** 

[ ![Gráfico de barras que muestra el modelo de costo de cambio de nivel de servicio estático, frente al dinámico.](../media/azure-netapp-files/cost-model-example-two-pricing.png) ](../media/azure-netapp-files/cost-model-example-two-pricing.png#lightbox)

Este escenario supone un ahorro mensual de 3965,39 USD, en comparación con el aprovisionamiento estático.

## <a name="capacity-consumption-of-snapshots"></a>Consumo de capacidad de instantáneas 

El consumo de la capacidad de instantáneas de Azure NetApp Files se cobra en función de la cuota del volumen primario.  Como resultado, comparte la misma tarifa de facturación que el grupo de capacidad al que pertenece el volumen.  Pero, a diferencia del volumen activo, el consumo de instantáneas se mide según la capacidad incremental consumida.  Las instantáneas de Azure NetApp Files son diferenciales por naturaleza. Según la velocidad de cambio de los datos, las instantáneas a menudo consumen mucha menos capacidad que la capacidad lógica del volumen activo. Por ejemplo, imagine que tiene una instantánea de un volumen de 500 GiB que solo contiene 10 GiB de datos diferenciales. 

El consumo de capacidad que se cuenta para la cuota de volumen del sistema de archivos activo y la instantánea sería de 510 GiB, no de 1000 GiB. Como norma general, se puede suponer que una capacidad recomendada del 20 % conserva los datos de instantáneas durante una semana (en función de la frecuencia de las instantáneas y las frecuencias de cambio diarias en el nivel de bloque de la aplicación). 

En el siguiente diagrama, se ilustran estos conceptos. 

* Supongamos que hay un grupo de capacidad con 40 TiB de capacidad aprovisionada. El grupo contiene tres volúmenes:    
    * Al volumen 1 se le asigna una cuota de 20 TiB y tiene 13 TiB de consumo (12 TiB activos, 1 TiB de instantáneas).
    * Al volumen 2 se le asigna una cuota de 1 TiB y tiene 450 GiB de consumo.
    * Al volumen 3 se le asigna una cuota de 14 TiB, pero tiene 8,8 TiB de consumo (8 TiB activos, 800 GiB de instantáneas).   
* El grupo de capacidad se mide para 40 TiB de capacidad (la cantidad aprovisionada). Se consumen 22,25 TiB de capacidad (13 TiB, 450 GiB y 8,8 TiB de la cuota de los volúmenes 1, 2 y 3). El grupo de capacidad tiene 17,75 TiB de capacidad restante.   

[ ![Diagrama que muestra el grupo de capacidad con tres volúmenes.](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png) ](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

* [Página de precios de Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Niveles de servicio para Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Modelo de costos de la replicación entre regiones](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [Descripción de la cuota de volumen](volume-quota-introduction.md)
* [Supervisión de la capacidad de un volumen](monitor-volume-capacity.md)
* [Cambio de tamaño de un grupo de capacidad o de un volumen](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Administración de facturación mediante etiquetas](manage-billing-tags.md)
* [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
* [Calculadora de precios de Azure NetApp Files](https://azure.microsoft.com/pricing/calculator/?service=netapp)
