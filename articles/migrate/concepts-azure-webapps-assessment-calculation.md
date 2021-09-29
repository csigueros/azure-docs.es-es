---
title: Evaluaciones de Azure App Service en la herramienta de detección y evaluación de Azure Migrate
description: Obtenga información sobre las evaluaciones de Azure App Service en la herramienta de detección y evaluación de Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 3328fb0fb20a9b0e492c3cd4281ee35dcf12dda0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792037"
---
# <a name="assessment-overview-migrate-to-azure-app-service"></a>Información general sobre la evaluación (migración a Azure App Service)

En este artículo se proporciona información general sobre las evaluaciones para migrar las aplicaciones web de ASP.NET desde un entorno de VMware a Azure App Service mediante la [herramienta de detección y evaluación de Azure Migrate](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>¿Qué es una evaluación?
Una evaluación con la herramienta de detección y evaluación es una instantánea en un momento dado de los datos, mide la preparación y proporciona los detalles de los costes a los servidores locales host, bases de datos y aplicaciones web a Azure.

## <a name="types-of-assessments"></a>Tipos de evaluaciones

Existen cuatro tipos de evaluaciones que puede crear con la herramienta de detección y evaluación de Azure Migrate.

**Tipo de evaluación** | **Detalles**
--- | ---
**MV de Azure** | Evaluaciones para la migración de los servidores locales a máquinas virtuales de Azure. <br/><br/> Con este tipo de evaluación, puede evaluar los servidores locales en los entornos de [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) y los [servidores físicos](how-to-set-up-appliance-physical.md) para la migración a máquinas virtuales de Azure.
**SQL de Azure** | Evaluaciones para migrar las instancias locales de SQL Server desde su entorno de VMware a Azure SQL Database o a Azure SQL Managed Instance.
**Azure App Service** | Evaluaciones para migrar las aplicaciones web de ASP.NET locales, que se ejecutan en servidores web IIS, desde el entorno de VMware a Azure App Service.
**Azure VMware Solution (AVS)** | Evaluaciones para la migración de los servidores locales a [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Puede evaluar las [máquinas virtuales de VMware](how-to-set-up-appliance-vmware.md) locales para la migración a Azure VMware Solution (AVS) con este tipo de evaluación. [Más información](concepts-azure-vmware-solution-assessment-calculation.md)

Una evaluación de Azure App Service proporciona un criterio de dimensionamiento:

**Criterio de tamaño** | **Detalles** | **Data**
--- | --- | ---
**Basado en configuración** | Valoraciones que realizan recomendaciones basadas en datos de configuración recopilados | La valoración de Azure App Service solo tiene en cuenta los datos de configuración para el cálculo de la valoración. No se recopilan los datos de rendimiento de las aplicaciones web.

## <a name="how-do-i-assess-my-on-premises-aspnet-web-apps"></a>¿Cómo puedo evaluar mis aplicaciones web de ASP.NET locales?

Puede evaluar las aplicaciones web locales mediante los datos de configuración recopilados por un dispositivo ligero de Azure Migrate. El dispositivo detecta las aplicaciones web locales y envía los datos de configuración a Azure Migrate. [Más información](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>¿Cómo se evalúa con el dispositivo?

Haga lo siguiente si va a implementar un dispositivo de Azure Migrate para detectar servidores locales:

1. Configure Azure y el entorno local para que funcionen con Azure Migrate.
2. Para su primera evaluación, cree un proyecto de Azure Migrate. La herramienta de detección y evaluación de Azure Migrate se agrega automáticamente al proyecto.
3. Implemente un dispositivo de Azure Migrate ligero. El dispositivo detecta los servidores locales de forma continuada y envía los datos de configuración y rendimiento a Azure Migrate. Implemente el dispositivo como una máquina virtual o un servidor físico. No es necesario instalar nada en los servidores que quiera evaluar.

Una vez que el dispositivo inicia la detección, puede reunir los servidores (hospedaje de aplicaciones web) que quiera evaluar en un grupo y ejecutar una evaluación del grupo con el tipo de evaluación de **Azure App Service**.

Siga nuestro tutorial para la evaluación de [aplicaciones web de ASP.NET](tutorial-assess-webapps.md) para probar estos pasos.

## <a name="what-properties-are-used-to-customize-the-assessment"></a>¿Qué propiedades se usan para personalizar la evaluación?

En las propiedades de evaluación de Azure App Service se incluye lo siguiente:

**Propiedad** | **Detalles**
--- | ---
**Ubicación de destino** | La región de Azure a la que quiere realizar la migración. Las recomendaciones sobre configuración de Azure App Service y sobre costos dependen de la ubicación que especifique.
**Aislamiento requerido** | Seleccione Sí si quiere que las aplicaciones web se ejecuten en un entorno privado y dedicado en un centro de datos de Azure mediante VM de la serie Dv2 con procesadores más rápidos, almacenamiento SSD y el doble de la proporción de memoria a núcleo en comparación con los planes Estándar.
**Instancias reservadas** | Especifica las instancias reservadas para que se tengan en cuenta en los cálculos de los costos de la evaluación.<br/><br/> Si selecciona una opción de instancia reservada, no podrá especificar un valor para "Discount (%)" (Descuento [%]).
**Oferta** | La [oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/) en la que está inscrito. La evaluación calcula el costo de esa oferta.
**Moneda** | Moneda de facturación de la cuenta.
**Descuento (%)** | Cualquier descuento específico de la suscripción que recibe además de la oferta de Azure. La configuración predeterminada es 0 %.
**Suscripción a Contrato Enterprise** | Especifica que se usa una suscripción a Contrato Enterprise (EA) para la estimación de costos. Tiene en cuenta el descuento aplicable a la suscripción. <br/><br/> Deje la configuración de instancias reservadas, el descuento (%) y las propiedades de tiempo de actividad de la máquina virtual con su configuración predeterminada.

[Revise los procedimientos recomendados](best-practices-assessment.md) para crear una evaluación con Azure Migrate.

## <a name="calculate-readiness"></a>Cálculo de la preparación

### <a name="azure-app-service-readiness"></a>Preparación de Azure App Service

La preparación de Azure App Service para aplicaciones web se basa en las comprobaciones de compatibilidad de características entre la configuración local de aplicaciones web y Azure App Service:

1. En la evaluación de Azure App Service se tienen en cuenta los datos de configuración de las aplicaciones web para identificar problemas de compatibilidad.
1. Si no se encuentra ningún problema de compatibilidad, la preparación se marca como **Preparado** para el tipo de implementación de destino.
1. Si hay problemas de compatibilidad que no son críticos, como características degradadas o no compatibles que no bloquean la migración a un tipo de implementación de destino específico, la preparación se marca como **Preparado con condiciones** (con hipervínculo) con los detalles de **advertencia** y la guía de corrección recomendada.
1. Si hay algún problema de compatibilidad que puede bloquear la migración a un tipo de implementación de destino específico, la preparación se marca como **No preparado** con los detalles del **problema** y la guía de corrección recomendada.
1. Si la detección aún está en curso o si hay problemas de detección para una aplicación web, la preparación se marca como **Desconocido**, ya que la evaluación no pudo calcular la preparación de esa aplicación web.

## <a name="calculate-sizing"></a>Cálculo del dimensionamiento

### <a name="azure-app-service-sku"></a>SKU de Azure App Service

Una vez que la evaluación determina la preparación en función de los datos de configuración, determina la SKU de Azure App Service adecuada para ejecutar las aplicaciones en Azure App Service.
Los planes Premium están pensados para cargas de trabajo de producción y se ejecutan en instancias de máquinas virtuales dedicadas. Cada instancia puede admitir varias aplicaciones y dominios. Los planes Aislados permiten hospedar las aplicaciones en un entorno privado y dedicado de Azure y resultan ideales para aquellas aplicaciones que requieren conexiones seguras con la red local.

> [!NOTE]
> Actualmente, Azure Migrate solamente recomienda las SKU I1, P1v2 y P1v3. Hay más SKU disponibles en Azure App Service. [Más información](https://azure.microsoft.com/pricing/details/app-service/windows/).

### <a name="azure-app-service-plan"></a>Plan de Azure App Service

En App Service, cada aplicación se ejecuta siempre en un [plan de App Service](../app-service/overview-hosting-plans.md). Un plan de App Service define un conjunto de recursos de proceso para que una aplicación web se ejecute. En un nivel alto, el plan o SKU se determina según la tabla siguiente.

**Aislamiento requerido** | **Instancia reservada** | **SKU o plan de App Service**
--- | --- | ---
Sí  | Sí | I1
Sí  | No  | I1
No  | Sí | P1v3
No  | No | P1v2

### <a name="azure-app-service-cost-details"></a>Detalles de los costes de Azure App Service

Un [plan de App Service](../app-service/overview-hosting-plans.md) conlleva un [cargo](https://azure.microsoft.com/pricing/details/app-service/windows/) asociado a los recursos de proceso que se utilicen. En App Service, se pagan cargos por planes de App Service y no por aplicación web. Pueden configurarse una o varias aplicaciones para que se ejecuten en los mismos recursos informáticos (o en el mismo plan de App Service). Todas las aplicaciones que coloque en este plan de App Service se ejecutan en estos recursos de proceso según lo definido por el plan de App Service.
Para optimizar el costo, la valoración de Azure Migrate asigna varias aplicaciones web a cada plan de App Service recomendado. El número de aplicaciones web asignadas a cada instancia de plan es el que se indica en la tabla siguiente.

**plan de App Service** | **Aplicaciones web por plan de App Service**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

> [!NOTE]
> El plan de App Service se puede escalar o reducir verticalmente en cualquier momento. [Más información](../app-service/overview-hosting-plans.md#what-if-my-app-needs-more-capabilities-or-features).

## <a name="next-steps"></a>Pasos siguientes
- [Revise](best-practices-assessment.md) los procedimientos recomendados para crear evaluaciones. 
- Obtenga información sobre cómo ejecutar una [evaluación de Azure App Service](how-to-create-azure-app-service-assessment.md).