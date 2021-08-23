---
title: ¿Qué es Azure Lighthouse?
description: Azure Lighthouse permite a los proveedores de servicios ofrecer servicios administrados para sus clientes con mayor automatización y eficacia a escala.
ms.date: 07/13/2021
ms.topic: overview
ms.openlocfilehash: 2da14a65f146a181af39a8c06a8089fee9010467
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666571"
---
# <a name="what-is-azure-lighthouse"></a>¿Qué es Azure Lighthouse?

Azure Lighthouse permite la administración multiinquilino con escalabilidad, mayor automatización y gobernanza mejorada de los recursos.

Con Azure Lighthouse, los proveedores de servicios pueden ofrecer servicios administrados mediante [herramientas completas y potentes integradas en la plataforma Azure](concepts/architecture.md). Los clientes mantienen el control sobre quién tiene acceso a su inquilino, a qué recursos se puede acceder y qué acciones se pueden realizar. Esta oferta también puede ayudar a las [organizaciones de TI empresariales](concepts/enterprise.md) a administrar recursos en varios inquilinos.

Las [experiencias de administración entre inquilinos](concepts/cross-tenant-management-experience.md) le permiten trabajar de forma más eficaz con servicios de Azure como [Azure Policy](how-to/policy-at-scale.md), [Azure Sentinel](how-to/manage-sentinel-workspaces.md), [Azure Arc](how-to/manage-hybrid-infrastructure-arc.md), etc. Los usuarios pueden ver qué cambios se han realizado y quién los ha realizado [en el registro de actividad](how-to/view-service-provider-activity.md), el cual se almacena en el inquilino del cliente (y los usuarios del inquilino de administración pueden verlo).

![Diagrama de información general de Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Ventajas

Azure Lighthouse ayuda a los proveedores de servicios a compilar y ofrecer servicios administrados de forma eficaz. Dicha integración aporta las siguientes ventajas:

- **Administración a escala**: la involucración del cliente y las operaciones de ciclo de vida para administrar los recursos del cliente son más fáciles y escalables. Las API, las herramientas de administración y los flujos de trabajo existentes se pueden usar con recursos delegados, incluidas las máquinas hospedadas fuera de Azure, con independencia de las regiones en las que se encuentren.
- **Mayor visibilidad y control para los clientes**: los clientes tienen un control preciso sobre los ámbitos en los que delegan la administración y sobre los permisos admitidos. Pueden [auditar las acciones del proveedor de servicios](how-to/view-service-provider-activity.md) y retirar el acceso por completo si así lo desean.
- [Herramientas de plataforma completas y unificadas](concepts/cloud-solution-provider.md): Azure Lighthouse funciona con herramientas y API existentes, **aplicaciones administradas de Azure** y programas de asociados, como el [programa del proveedor de soluciones en la nube (CSP)](concepts/managed-applications.md). Esta flexibilidad admite escenarios clave del proveedor de servicios, incluidos varios modelos de licencia como EA, CSP y pago por uso. Azure Lighthouse se puede integrar en los flujos de trabajo y aplicaciones existentes, y puede realizar un seguimiento de su efecto sobre las interacciones de los clientes [vinculando su identificador de asociado](how-to/partner-earned-credit.md).

## <a name="capabilities"></a>Capacidades

Azure Lighthouse incluye varias formas de ayudar a simplificar la administración y la involucración:

- **Administración de recursos delegados de Azure**: [administre los recursos de Azure de sus clientes de forma segura desde su propio inquilino](concepts/architecture.md) sin tener que cambiar los planos de contexto y control. Las suscripciones y los grupos de recursos de los clientes se pueden delegar a usuarios y roles especificados en el inquilino de administración, con la posibilidad de quitar el acceso según sea necesario.
- **Nuevas experiencias de Azure Portal**: Vea la información de distintos inquilinos en la página [**Mis clientes** de](how-to/view-manage-customers.md) Azure Portal. Una página de [**proveedores de servicios** correspondiente](how-to/view-manage-service-providers.md) permite a los clientes ver y administrar el acceso de los proveedores de servicios.
- **Plantillas de Azure Resource Manager**: use plantillas de Resource Manager para [incorporar recursos de clientes delegados](how-to/onboard-customer.md) y [realizar tareas de administración entre inquilinos](samples/index.md).
- **Ofertas de servicio administrado en Azure Marketplace**: [ofrezca sus servicios a los clientes](concepts/managed-services-offers.md) mediante ofertas públicas o privadas e incorpórelos automáticamente a Azure Lighthouse.

> [!TIP]
> Una oferta similar, [Microsoft 365 Lighthouse](/microsoft-365/lighthouse/m365-lighthouse-overview), ayuda a los proveedores de servicios a incorporar, supervisar y administrar sus clientes de Microsoft 365 a escala. Microsoft 365 Lighthouse se encuentra actualmente en versión preliminar.

## <a name="pricing-and-availability"></a>Precios y disponibilidad

No hay costos adicionales asociados con el uso de Azure Lighthouse para administrar los recursos de Azure. Cualquier cliente o asociado de Azure puede usar Azure Lighthouse.

## <a name="cross-region-and-cloud-considerations"></a>Consideraciones sobre el entorno entre regiones y la nube

Azure Lighthouse no es un servicio regional. Puede administrar recursos delegados que estén ubicados en diferentes [regiones](../availability-zones/az-overview.md#regions). Sin embargo, no se admite la delegación de suscripciones entre una [nube nacional](../active-directory/develop/authentication-national-cloud.md) y la nube pública de Azure o entre dos nubes nacionales independientes.

## <a name="support-for-azure-lighthouse"></a>Compatibilidad con Azure Lighthouse

Para obtener ayuda con el uso de Azure Lighthouse, [abra una solicitud de soporte técnico](..//azure-portal/supportability/how-to-create-azure-support-request.md) en Azure Portal. En **Tipo de problema**, elija **Técnico**. Seleccione una suscripción y luego **Lighthouse** (en **Supervisión y administración**).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [cómo funciona Azure Lighthouse en un nivel técnico](concepts/architecture.md).
- Explore las [experiencias de administración entre inquilinos](concepts/cross-tenant-management-experience.md).
- Vea cómo [usar Azure Lighthouse en una empresa](concepts/enterprise.md).
- Vea los detalles de [disponibilidad](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) y del [ámbito de auditoría de FedRAMP y DoD CC SRG](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) para Azure Lighthouse.
