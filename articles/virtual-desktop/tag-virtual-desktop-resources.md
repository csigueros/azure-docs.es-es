---
title: Etiquetado de recursos de Azure Virtual Desktop - Azure
description: Qué es el etiquetado y cómo se puede usar para administrar los costos de servicio de Azure en Azure Virtual Desktop.
author: heidilohr
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c77e7b2ec767cd843cbf34b7f3a44d074d20ab5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404288"
---
# <a name="tag-azure-virtual-desktop-resources-to-manage-costs"></a>Etiquetado de recursos de Azure Virtual Desktop para administrar costos

El etiquetado es una herramienta disponible en todos los servicios de Azure, que le ayuda a organizar los recursos dentro de su suscripción de Azure. La organización de recursos facilita el seguimiento de los costos en varios servicios. Las etiquetas también le ayudan a comprender cuánto cuesta cada agrupación de recursos de Azure por ciclo de facturación. Si desea más información sobre el etiquetado en general, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md). También puede ver un [vídeo rápido](https://www.youtube.com/watch?v=dUft4FZ40O8) sobre otras formas de usar etiquetas de Azure.

## <a name="how-tagging-works"></a>Funcionamiento del etiquetado

Puede etiquetar los servicios de Azure que administra en Azure Portal o en PowerShell. Las etiquetas aparecerán como pares de clave-valor de texto. A medida que utilice recursos de Azure etiquetados, el par de clave-valor de la etiqueta asociado se asociará al uso de recursos.

Una vez que la implementación notifique la información de uso etiquetada a [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md), puede utilizar la estructura de etiquetado para filtrar los datos de costos. Para más información sobre cómo filtrar por etiquetas en Azure Cost Management, consulte [Inicio rápido: Exploración y análisis de costos con análisis de costos](../cost-management-billing/costs/quick-acm-cost-analysis.md).

### <a name="add-edit-or-delete-tags"></a>Incorporación, edición o eliminación de etiquetas

Al aplicar una nueva etiqueta a un recurso, no estará visible en Azure Cost Management hasta que su recurso de Azure asociado notifique la actividad. Si aplica una etiqueta existente a los recursos, este cambio tampoco estará visible en Azure Cost Management hasta que los recursos de Azure notifiquen la actividad.

Si edita un nombre de etiqueta, los recursos asociados asociarán los costos a su nuevo par de clave-valor. Todavía puede filtrar los datos con la etiqueta anterior, pero todos los datos nuevos posteriores al cambio se notifican con la nueva etiqueta.

Si elimina una etiqueta, Azure Virtual Desktop ya no notificará los datos asociados con la etiqueta eliminada a Azure Cost Management. Todavía puede filtrar con etiquetas eliminadas los datos notificados antes de eliminar la etiqueta.

>[!IMPORTANT]
>Los recursos de Azure etiquetados que no han estado activos desde que se les han aplicado etiquetas nuevas o actualizadas no notificarán ninguna actividad asociada con las etiquetas modificadas a Azure Cost Management. No podrá filtrar por etiquetas específicas hasta que el servicio notifique la actividad asociada a Azure Cost Management.

### <a name="view-all-existing-tags"></a>Visualización de todas las etiquetas existentes

Para ver todas las etiquetas existentes para los servicios de Azure, vaya a Azure Portal y abra [la **pestaña** Etiquetas](https://ms.portal.azure.com/#blade/HubsExtension/TagsBlade) que mostrará todas las etiquetas de los objetos a los que tiene acceso. También puede ordenar las etiquetas por sus claves o valores siempre que necesite actualizar rápidamente un gran número de etiquetas al mismo tiempo.

### <a name="what-tags-can-and-cant-do"></a>Qué es lo que pueden y no pueden hacer las etiquetas

Las etiquetas solo notificarán los datos de uso y costo de los recursos de Azure a los que están asignadas directamente. Si ha etiquetado un recurso sin etiquetar los demás recursos que hay en él, Azure Virtual Desktop solo notificará la actividad relacionada con el recurso etiquetado de nivel superior. También deberá etiquetar todos los recursos de ese recurso de nivel superior si desea que los datos de facturación sean precisos.

Para más información sobre cómo funcionan las etiquetas de Azure Cost Management, consulte [Uso de las etiquetas en los datos de costo y uso](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data).

Para obtener una lista de las limitaciones conocidas de las etiquetas de Azure, consulte el artículo sobre el [uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md#limitations).

## <a name="using-tags-in-azure-virtual-desktop"></a>Uso de etiquetas en Azure Virtual Desktop

Ahora que comprende los conceptos básicos de las etiquetas de Azure, vamos a ver cómo se pueden utilizar en Azure Virtual Desktop.

Puede usar etiquetas de Azure para organizar los costos de creación, administración e implementación de experiencias virtualizadas para sus clientes y usuarios. El etiquetado también puede ayudarle a realizar un seguimiento de los recursos que compra directamente en Azure Virtual Desktop y otros servicios de Azure conectados a las implementaciones de Azure Virtual Desktop.

## <a name="suggested-tags-for-azure-virtual-desktop"></a>Etiquetas sugeridas para Azure Virtual Desktop

Como Azure Virtual Desktop puede funcionar con otros servicios de Azure para que sean compatibles con sus implementaciones, no hay un sistema universal para etiquetar los recursos de implementación. Lo más importante es que desarrolle una estrategia que funcione para usted y su organización. Sin embargo, tenemos algunas sugerencias que pueden ayudarle, especialmente si es la primera vez que utiliza Azure. 

Las siguientes sugerencias se aplican a todas las implementaciones de Azure Virtual Desktop:

- Familiarícese con los servicios de Azure adquiridos para comprender el alcance de lo que quiere etiquetar. A medida que aprenda a usar Azure Portal, mantenga una lista de los grupos de servicios y objetos en los que pueda aplicar etiquetas. Algunos recursos de los que debe realizar un seguimiento incluyen grupos de recursos, máquinas virtuales, discos y tarjetas de interfaz de red (NIC). Para obtener una lista más completa de los componentes de servicio de generación de costos que puede etiquetar, consulte [Descripción de los costos totales de implementación de Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

- Cree una agregación de informes de costos para organizar las etiquetas. Puede [seguir un patrón de etiquetado común](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging.md) o crear un nuevo patrón que satisfaga las necesidades de su organización.

- Mantenga la coherencia de sus etiquetas donde las aplique. Incluso el error tipográfico más pequeño puede afectar a los informes de datos, así que asegúrese de agregar el par de clave-valor exacto que desee buscar más adelante.

- Mantenga un registro de todas las etiquetas que actualice o edite. Este registro le permitirá combinar los datos históricos de cada etiqueta cuando sea necesario. Al editar o actualizar una etiqueta, también debe aplicar estos cambios en todos los recursos que incluyan la etiqueta modificada.

## <a name="suggested-tags-for-azure-virtual-desktop-host-pools"></a>Etiquetas sugeridas para grupos de hosts de Azure Virtual Desktop

Cada máquina virtual de un grupo de hosts de Azure Virtual Desktop crea una construcción que genera costos. Como los grupos de hosts son la base de una implementación de Azure Virtual Desktop, sus máquinas virtuales suelen ser la principal fuente de costos para las implementaciones de Azure Virtual Desktop. Si desea configurar un sistema de etiquetado, se recomienda empezar con el etiquetado de todos los grupos de hosts de la implementación para realizar un seguimiento de los costos de proceso de las máquinas virtuales. El etiquetado de los grupos de hosts puede ayudarle a utilizar el filtrado en Azure Cost Management para identificar estos costos de máquinas virtuales.

Al igual que con las [sugerencias generales](#suggested-tags-for-azure-virtual-desktop), no hay ningún sistema universal para etiquetar grupos de hosts. Sin embargo, tenemos algunas sugerencias que le ayudarán a organizar las etiquetas de grupos de hosts:

- El etiquetado de grupos de hosts mientras los crea es opcional, pero el etiquetado durante el proceso de creación le facilitará ver todo el uso etiquetado en Azure Cost Management más adelante. Las etiquetas de grupos de hosts seguirán todos los componentes de generación de costos de los hosts de sesión dentro del grupo de hosts. Más información sobre estos otros costos específicos de host en [Descripción de los costos totales de implementación de Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

- Si utiliza Azure Portal para crear un nuevo grupo de hosts, el flujo de trabajo de creación le dará la oportunidad de agregar etiquetas existentes. Estas etiquetas se pasarán a todos los recursos que cree durante el proceso de creación del grupo de hosts. Las etiquetas también se aplicarán a los hosts de sesión que agregue a un grupo de hosts existente en Azure Portal. Sin embargo, deberá escribir las etiquetas manualmente cada vez que agregue un nuevo host de sesión.

- Es poco probable que obtenga un informe de costos completo de cada servicio de Azure compatible que funcione con sus grupos de hosts, ya que las opciones de configuración son ilimitadas y únicas para cada cliente. Es decisión del usuario decidir con qué nivel de atención desea realizar un seguimiento de los costos en cualquier servicio de Azure asociado a la implementación de Azure Virtual Desktop. Cuanto más exhaustivo sea el seguimiento de estos costos mediante el etiquetado, más preciso será el informe de costos mensual de Azure Virtual Desktop.

- Si crea el sistema de etiquetado en torno a sus grupos de hosts, asegúrese de utilizar pares de clave-valor que tengan sentido para agregarlos a otros servicios de Azure más adelante.

## <a name="suggested-tags-for-other-azure-virtual-desktop-resources"></a>Etiquetas sugeridas para otros recursos de Azure Virtual Desktop

La mayoría de los clientes de Azure Virtual Desktop implementan otros servicios de Azure para que sean compatibles con sus implementaciones. Si desea incluir el costo de estos servicios adicionales en el informe de costos, debe tener en cuenta las siguientes sugerencias:

- Si ya ha adquirido un servicio o recursos de Azure que desea integrar en las implementaciones de Azure Virtual Desktop, tiene dos opciones:
   
   - Separar los servicios de Azure adquiridos entre distintas suscripciones de Azure.
   - Combinar todos los servicios de Azure adquiridos en la misma suscripción con las etiquetas de Azure Virtual Desktop.

   La separación de los servicios le dará una idea más clara de los costos de cada servicio, pero puede acabar siendo más costoso al final. Es posible que tenga que comprar almacenamiento adicional para estos servicios a fin de asegurarse de que Azure Virtual Desktop tenga su propio almacenamiento designado. 

   La combinación de los servicios comprados es menos costosa, pero puede inflar el informe de costos porque los datos de uso de los recursos compartidos no serán tan precisos. Para compensar la falta de precisión, puede agregar varias etiquetas a los recursos para ver los costos compartidos mediante filtros que realicen un seguimiento de los distintos factores.

- Si ha comenzado a crear el sistema de etiquetado con otro servicio de Azure, asegúrese de que los pares de clave-valor que cree se puedan aplicar a la implementación de Azure Virtual Desktop o a otros servicios más adelante.

## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información sobre los costos comunes relacionados con Azure Virtual Desktop, consulte [Descripción de los costos totales de implementación de Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

Para más información sobre las etiquetas de Azure, consulte los siguientes recursos:

- [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md)

- [Vídeo en el que se explica el valor del uso de etiquetas de Azure](https://www.youtube.com/watch?v=dUft4FZ40O8)

- [Uso de las etiquetas en los datos de costo y uso](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

- [Desarrollo de la estrategia de nomenclatura y etiquetado de los recursos de Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

- [Definición de la estrategia de etiquetado](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

- [Guía de decisiones de nomenclatura y etiquetado de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging)

Para más información sobre Azure Cost Management, consulte los artículos siguientes:

- [¿Qué es Azure Cost Management y facturación?](../cost-management-billing/cost-management-billing-overview.md)

- [Inicio rápido: Explore y analice los costos con Análisis de costos](../cost-management-billing/costs/quick-acm-cost-analysis.md)
