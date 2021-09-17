---
title: Tutorial para evaluar aplicaciones web para su migración a Azure App Service
description: Obtenga información sobre cómo crear una valoración para Azure App Service en Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/28/2021
ms.openlocfilehash: 1bbe267aef27151b3ef70e88e0a8e85091d8a33d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785482"
---
# <a name="tutorial-assess-aspnet-web-apps-for-migration-to-azure-app-service"></a>Tutorial: Evaluar aplicaciones web de ASP.NET para su migración a Azure App Service

Como parte del recorrido de la migración a Azure, puede evaluar las cargas de trabajo locales para medir la preparación de la nube, identificar los riesgos y hacer una estimación de los costos y la complejidad.
En este artículo se muestra cómo evaluar las aplicaciones web de ASP.NET detectadas que se ejecutan en servidores web de IIS como preparación para la migración a Azure App Service mediante la herramienta Azure Migrate: Discovery and Assessment.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Ejecute una valoración basada en los datos de configuración de las aplicaciones web.
> * Revisión de una valoración de Azure App Service

> [!NOTE]
> Los tutoriales muestran la manera más rápida de probar un escenario y utilizar las opciones predeterminadas siempre que sea posible. 

## <a name="prerequisites"></a>Prerrequisitos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.
- Antes de seguir este tutorial para evaluar las aplicaciones web para la migración a Azure App Service, asegúrese de que ha detectado las aplicaciones web que desea evaluar mediante el dispositivo de Azure Migrate; para ello, [siga este tutorial](tutorial-discover-vmware.md).
- Si quiere probar esta característica en un proyecto existente, asegúrese de haber completado los [requisitos previos](how-to-discover-sql-existing-project.md) de este artículo.

## <a name="run-an-assessment"></a>Ejecución de una evaluación

Las evaluaciones se realizan como se indica a continuación:

1. En la página **Información general** > **Servidores, bases de datos y aplicaciones web**, haga clic en **Detectar, evaluar y migrar**.
    :::image type="content" source="./media/tutorial-assess-webapps/discover-assess-migrate.png" alt-text="Página de información general de Azure Migrate":::
2. En **Azure Migrate: Discovery and Assessment**, haga clic en **Evaluar** y elija el tipo de valoración **Azure App Service**.
    :::image type="content" source="./media/tutorial-assess-webapps/assess.png" alt-text="Lista desplegable en la que se puede elegir el tipo de valoración Azure App Service":::
3. En **Crear valoración**, podrá ver que el tipo de valoración preseleccionado es **Azure App Service** y que el origen de detección se ha establecido de manera predeterminada en **Servers discovered from Azure Migrate appliance** (Servidores detectados desde el dispositivo de Azure Migrate).
4. Haga clic en **Editar** para revisar las propiedades de la evaluación.
     :::image type="content" source="./media/tutorial-assess-webapps/assess-webapps.png" alt-text="Botón Editar desde el que se pueden personalizar las propiedades de la evaluación":::
5. En las propiedades de valoración de Azure App Service se incluye lo siguiente:

    **Propiedad** | **Detalles**
    --- | ---
    **Ubicación de destino** | La región de Azure a la que quiere realizar la migración. Las recomendaciones sobre configuración de Azure App Service y sobre costes dependen de la ubicación que especifique.
    **Aislamiento requerido** | Seleccione Sí si quiere que las aplicaciones web se ejecuten en un entorno privado y dedicado en un centro de datos de Azure mediante VM de la serie Dv2 con procesadores más rápidos, almacenamiento SSD y el doble de la proporción de memoria a núcleo en comparación con los planes Estándar.
    **Instancias reservadas** | Especifica las instancias reservadas para que se tengan en cuenta en los cálculos de los costos de la evaluación.<br/><br/> Si selecciona una opción de instancia reservada, no podrá especificar un valor para "Discount (%)" (Descuento [%]).
    **Oferta** | La [oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/) en la que está inscrito. La evaluación calcula el costo de esa oferta.
    **Moneda** | Moneda de facturación de la cuenta.
    **Descuento (%)** | Cualquier descuento específico de la suscripción que recibe además de la oferta de Azure. La configuración predeterminada es 0 %.
    **Suscripción a Contrato Enterprise** | Especifica que se usa una suscripción a Contrato Enterprise (EA) para la estimación de costos. Tiene en cuenta el descuento aplicable a la suscripción. <br/><br/> Deje la configuración de las instancias reservadas y las propiedades de descuento (%) con los valores predeterminados.

    :::image type="content" source="./media/tutorial-assess-webapps/webapps-assessment-properties.png" alt-text="Propiedades de valoración de App Service":::

1. En **Crear valoración**, haga clic en Siguiente.
1. En **Select servers to assess** > **Assessment name** (Seleccionar servidores que se van a evaluar > Nombre de la evaluación), especifique el nombre de la evaluación.
1. En **Seleccionar o crear un grupo**, seleccione **Crear nuevo** y especifique un nombre de grupo.
1. Seleccione el dispositivo y los servidores que desee agregar al grupo. A continuación, haga clic en Siguiente.
1. En **Revisar y crear valoración**, revise los detalles de la valoración y haga clic en Crear evaluación para crear el grupo y ejecutar la evaluación.
1. Una vez creada la valoración, vaya a **Servidores, bases de datos y aplicaciones web** >  icono de **Azure Migrate: Discovery and Assessment** y actualice los datos del icono. Para ello, haga clic en la opción Actualizar en la parte superior del icono. Espere a que se actualicen los datos.
    :::image type="content" source="./media/tutorial-assess-webapps/tile-refresh.png" alt-text="Actualización de los datos de la herramienta de detección y valoración":::
1. Haga clic en el número situado junto a la valoración de Azure App Service.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-navigation.png" alt-text="Acceso a la evaluación creada":::
1. Haga clic en el nombre de la evaluación que desea ver.

## <a name="review-an-assessment"></a>Revisión de una evaluación

**Para ver una evaluación**:

1. **Servidores, bases de datos y aplicaciones web** > **Azure Migrate: detección y valoración** > Haga clic en el número situado junto a la valoración de Azure App Service.
2. Haga clic en el nombre de la evaluación que desea ver.
:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-summary.png" alt-text="Información general sobre la valoración de App Service":::
3. Revise el resumen de valoraciones. También puede editar las propiedades de la evaluación o volver a calcularla.

#### <a name="azure-app-service-readiness"></a>Preparación de Azure App Service

Indica la distribución de las aplicaciones web evaluadas. Puede profundizar para comprender los detalles de los problemas o advertencias de la migración que puede corregir antes de migrar a Azure App Service. [Más información](concepts-azure-webapps-assessment-calculation.md) También puede ver la SKU de App Service recomendada para planear la migración a Azure App Service.

#### <a name="azure-app-service-cost-details"></a>Detalles de los costes de Azure App Service

Un [plan de App Service](/azure/app-service/overview-hosting-plans) conlleva un [cargo](https://azure.microsoft.com/pricing/details/app-service/windows/) asociado a los recursos de proceso que se utilicen.

### <a name="review-readiness"></a>Revisión de la preparación

1. Haga clic en **Preparación de Azure App Service**.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-readiness.png" alt-text="Detalles de preparación de Azure App Service":::
1. Revise la columna de preparación de Azure App Service en la tabla para las aplicaciones web evaluadas:
    1. Si no se encuentra ningún problema de compatibilidad, la preparación se marca como **Preparado** para el tipo de implementación de destino.
    1. Si hay problemas de compatibilidad que no son críticos, como características degradadas o no compatibles que no bloquean la migración a un tipo de implementación de destino específico, la preparación se marca como **Preparado con condiciones** (con hipervínculo) con los detalles de la **advertencia** y la guía de corrección recomendada.
    1. Si hay algún problema de compatibilidad que puede bloquear la migración a un tipo de implementación de destino específico, la preparación se marca como **No preparado** con los detalles del **problema** y la guía de corrección recomendada.
    1. Si la detección aún está en curso o si hay problemas de detección para una aplicación web, la preparación se marca como **Desconocido**, ya que la valoración no pudo calcular la preparación de esa aplicación web.
1. Consulte la SKU recomendada para las aplicaciones web de acuerdo con la matriz siguiente:

    **Aislamiento requerido** | **Instancia reservada** | **SKU o plan de App Service**
    --- | --- | ---
    Sí  | Sí | I1
    Sí  | No  | I1
    No  | Sí | P1v3
    No  | No | P1v2

    **Preparación de Azure App Service** | **Determinación de la SKU de App Service** | **Determinación de las estimaciones de costos**
    --- | --- | ---
    Ready  | Sí | Sí
    Preparada con condiciones  | Sí  | Sí
    No está listo  | No | No
    Desconocido  | No | No

1. Haga clic en el hipervínculo del plan de App Service de la tabla para ver los detalles del plan, como los recursos de proceso y otras aplicaciones web que forman parte del mismo plan.

### <a name="review-cost-estimates"></a>Revisión de las estimaciones de los costos

El resumen de la valoración muestra los costos mensuales estimados de hospedar aplicaciones web en App Service. En App Service, los cargos se aplican por plan de App Service, no por aplicación web. Pueden configurarse una o varias aplicaciones para que se ejecuten en los mismos recursos informáticos (o en el mismo plan de App Service). Todas las aplicaciones que coloque en este plan de App Service se ejecutan en estos recursos de proceso según lo definido por el plan de App Service.
Para optimizar el costo, la evaluación de Azure Migrate asigna varias aplicaciones web a cada plan de App Service recomendado. El número de aplicaciones web asignadas a cada instancia de plan es el que se indica en la tabla siguiente.

**plan de App Service** | **Aplicaciones web por plan de App Service**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-cost.png" alt-text="Detalles del costo":::

## <a name="next-steps"></a>Pasos siguientes

[Más información](concepts-azure-webapps-assessment-calculation.md) sobre el cálculo de las valoraciones de Azure App Service.