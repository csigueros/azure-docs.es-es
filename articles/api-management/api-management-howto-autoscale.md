---
title: Configuración del escalado automático de una instancia de Azure API Management | Microsoft Docs
description: En este tema, se explica cómo configurar el comportamiento de escalado automático para una instancia de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: danlep
ms.openlocfilehash: 2684eb2a74f5c7bc5b4913ea2101f1d3febbcab8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628883"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Escalado automático de una instancia de Azure API Management  

Una instancia de servicio de Azure API Management puede escalarse de manera automática en función de un conjunto de reglas. Este comportamiento se puede habilitar y configurar a través de Azure Monitor y solo se admite en los niveles **Estándar** y **Premium** del servicio Azure API Management.

En este artículo se le guía por el proceso de configuración de escalado automático y sugiere una configuración óptima de las reglas de escalado automático.

> [!NOTE]
> El servicio API Management en el nivel **Consumo** se escala automáticamente en función del tráfico, sin necesidad de realizar ninguna configuración adicional.

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos de este artículo, debe:

+ Tener una suscripción de Azure activa.
+ Tener una instancia de Azure API Management. Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Comprender el concepto de [Capacidad de una instancia de Azure API Management](api-management-capacity.md).
+ Comprender el [proceso de escalado manual de una instancia de Azure API Management](upgrade-and-scale.md), incluidas las consecuencias de costos.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitaciones de escalado automático de Azure API Management

Ciertas limitaciones y consecuencias de las decisiones de escalado deben tenerse en cuenta antes de configurar el comportamiento de escalado automático.

+ La escalabilidad automática se puede habilitar solo para los niveles **Estándar** y **Premium** del servicio Azure API Management.
+ Los planes de tarifa también especifican el número máximo de unidades para una instancia de servicio.
+ El proceso de escalado tardará al menos 20 minutos.
+ Si el servicio está bloqueado por otra operación, la solicitud de escalado producirá un error y volverá a intentarse automáticamente.
+ En el caso de un servicio con implementaciones en varias regiones, solo se pueden escalar las unidades en la **Ubicación principal**. No se puede escalar las unidades en otras ubicaciones.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Habilitación y configuración del escalado automático para el servicio Azure API Management

Siga los pasos a continuación para configurar el escalado automático para un servicio Azure API Management:

1. Acceda a la instancia **Monitor** en Azure Portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Seleccione **Escalado automático** del menú de la izquierda.

    ![Recurso del escalado automático de Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Busque el servicio Azure API Management en función de los filtros en los menús desplegables.
4. Seleccione la instancia de servicio deseada de Azure API Management.
5. En la sección recién abierta, haga clic en el botón **Habilitar escalado automático**.

    ![Habilitación del escalado automático de Azure Monitor](media/api-management-howto-autoscale/03.png)

6. En la sección **Reglas**, haga clic en **+ Agregar una regla**.

    ![Adición de una regla para escalado automático de Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Defina una nueva regla de escalado horizontal.

   Por ejemplo, una regla de escalado horizontal podría desencadenar una adición de una unidad de Azure API Management, cuando la métrica de capacidad promedio durante los últimos 30 minutos supere el 80 %. En la tabla siguiente se proporciona la configuración para este tipo de regla.

    | Parámetro             | Value             | Notas                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origen de métricas         | Recurso actual  | Defina la regla según las métricas de recursos de Azure API Management actuales.                                                                                                                                                                                                     |
    | *Criterios*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregación de tiempo      | Average           |                                                                                                                                                                                                                                                                                 |
    | Nombre de métrica           | Capacity          | La métrica de capacidad es una métrica de Azure API Management que refleja el uso de recursos de una instancia de Azure API Management.                                                                                                                                                            |
    | Estadísticas de intervalo de agregación  | Average           |                                                                                                                                                                                                                                                                                 |
    | Operador              | Mayor que      |                                                                                                                                                                                                                                                                                 |
    | Umbral             | 80 %               | El umbral de la métrica de capacidad promedio.                                                                                                                                                                                                                                 |
    | Duración (en minutos) | 30                | El intervalo de tiempo para promediar la métrica de capacidad es específica a los patrones de uso. Cuanto mayor sea el período de tiempo, más suave será la reacción: los picos intermitentes tendrán un efecto menor sobre la decisión de escalado horizontal. Sin embargo, también se demorará el desencadenador de escalado horizontal. |
    | *Acción*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operación             | Aumentar recuento en |                                                                                                                                                                                                                                                                                 |
    | Recuento de instancias        | 1                 | Escale la instancia de Azure API Management horizontalmente en 1 unidad.                                                                                                                                                                                                                          |
    | Tiempo de finalización (minutos)   | 60                | El servicio Azure API Management tarda al menos 20 minutos en escalarse horizontalmente. En la mayoría de los casos, el período de finalización de 60 minutos evita que se desencadenen muchos escalados horizontales.                                                                                                  |

8. Haga clic en **Agregar** para guardar la regla.

    ![Regla de escalado horizontal de Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Haga clic en nuevo en **+ Agregar una regla**.

    Esta vez, debe definirse una regla de reducción horizontal. Esto garantizará que no se malgasten los recursos, cuando se reduzca el uso de las API.

10. Defina una nueva regla de reducción horizontal.

    Por ejemplo, una regla de reducción horizontal podría desencadenar una eliminación de una unidad de Azure API Management, cuando la métrica de capacidad promedio durante los últimos 30 minutos haya sido inferior al 35 %. En la tabla siguiente se proporciona la configuración para este tipo de regla.

    | Parámetro             | Value             | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origen de métricas         | Recurso actual  | Defina la regla según las métricas de recursos de Azure API Management actuales.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Criterios*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregación de tiempo      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nombre de métrica           | Capacity          | La misma métrica que la utilizada para la regla de escalado horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Estadísticas de intervalo de agregación  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operador              | Menor que         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Umbral             | 35 %               | De forma similar a la regla para escalado horizontal, este valor depende en gran medida los patrones de uso de Azure API Management. |
    | Duración (en minutos) | 30                | El mismo valor que el utilizado para la regla de escalado horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Acción*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operación             | Reducir el recuento en | Opuesta a lo que se usó para la regla de escalado horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Recuento de instancias        | 1                 | El mismo valor que el utilizado para la regla de escalado horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Tiempo de finalización (minutos)   | 90                | La reducción horizontal debe ser más conservadora que un escalado horizontal, por lo que el período de finalización debe ser mayor.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Haga clic en **Agregar** para guardar la regla.

    ![Regla de reducción horizontal de Azure Monitor](media/api-management-howto-autoscale/06.png)

12. Establezca el número **máximo** de unidades de Azure API Management.

    > [!NOTE]
    > Azure API Management tiene un límite de unidades a las que se puede escalar horizontalmente una instancia. El límite depende del nivel del servicio.

    ![Captura de pantalla que resalta dónde establecer el número máximo de unidades de Azure API Management.](media/api-management-howto-autoscale/07.png)

13. Haga clic en **Save**(Guardar). Se ha configurado el escalado automático.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md)
- [Optimización y ahorro del gasto en la nube](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)