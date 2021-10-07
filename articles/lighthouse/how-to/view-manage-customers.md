---
title: Ver y administrar clientes y recursos delegados en Azure Portal
description: Como proveedor de servicio o empresa que usa Azure Lighthouse, puede ver todos los recursos y suscripciones delegados desde la sección Mis clientes de Azure Portal.
ms.date: 09/17/2021
ms.topic: how-to
ms.openlocfilehash: 4cce38b435ee2613b885dfad959871579579ddce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673641"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Ver y administrar clientes y recursos delegados en Azure Portal

Los proveedores de servicio que usan [Azure Lighthouse](../overview.md) pueden usar la página **Mis clientes** de [Azure Portal](https://portal.azure.com) para ver las suscripciones y los recursos de clientes delegados.

> [!TIP]
> Aunque aquí nos referiremos a los proveedores de servicios y clientes, las [empresas que administren varios inquilinos](../concepts/enterprise.md) pueden usar el mismo proceso para consolidar su experiencia de administración.

Para acceder a la página **Mis clientes** en Azure Portal, seleccione **Todos los servicios**, busque **Mis clientes** y seleccione esta opción. También puede acceder aquí escribiendo "Mis clientes" en el cuadro de búsqueda situado cerca de la parte superior de Azure Portal.

Tenga en cuenta que la sección superior **Clientes** de la página **Mis clientes** solo muestra información sobre los clientes que tienen suscripciones o grupos de recursos delegados en el inquilino de Azure Active Directory (Azure AD) a través de Azure Lighthouse. Si trabaja con otros clientes (por ejemplo, a través del [programa Proveedor de soluciones en la nube (CSP)](/partner-center/csp-overview)), no verá información sobre los clientes en la sección **Clientes**, a menos que haya [incorporado sus recursos a Azure Lighthouse](onboard-customer.md) (aunque puede ver detalles sobre determinados clientes de CSP en la [sección Proveedor de soluciones en la nube [versión preliminar]](#cloud-solution-provider-preview) en la parte inferior de la página).

> [!NOTE]
> Los clientes pueden ver información sobre los proveedores de servicios navegando a **Proveedores de servicios** en Azure Portal. Para obtener más información, consulte [View and manage service providers](view-manage-service-providers.md) (Ver y administrar los proveedores de servicios).

## <a name="view-and-manage-customer-details"></a>Ver y administrar los detalles del usuario

Para ver los detalles del cliente, seleccione **Clientes** en el lado izquierdo de la página **Mis clientes**.

> [!IMPORTANT]
> Para ver esta información, a los usuarios se les debe haber concedido el rol [lector](../../role-based-access-control/built-in-roles.md#reader) (u otro rol integrado que incluya acceso de lectura) en el proceso de incorporación.

Para cada cliente, verá el nombre del cliente, el identificador de cliente (id. de inquilino) y el **identificador de oferta** y la **versión de oferta** asociados a la participación. En la columna **Delegaciones**, verá el número de suscripciones delegadas o el número de grupos de recursos delegados.

Las opciones de la parte superior de la página le permiten ordenar, filtrar y agrupar la información del cliente por clientes, ofertas o palabras clave en concreto.

Puede ver la siguiente información desde esta página:

- Para ver todas las suscripciones, ofertas y delegaciones asociadas con un cliente, seleccione el nombre del cliente.
- Para ver más detalles sobre una oferta y sus delegaciones, seleccione el nombre de la oferta.
- Para ver más detalles acerca de las asignaciones de roles en las suscripciones o grupos de recursos delegados, seleccione la entrada de la columna **Delegaciones**.

> [!NOTE]
> Si un cliente cambia el nombre de una suscripción después de delegarla, verá el nombre de la suscripción actualizado. Si cambia el nombre del inquilino, es posible que todavía vea el nombre del inquilino anterior en algunos lugares de Azure Portal.

## <a name="view-and-manage-delegations"></a>Visualización y administración de delegaciones

Las delegaciones muestran el grupo de recursos o la suscripción que se ha delegado, junto con los usuarios y permisos que tienen acceso a ella. Para ver esta información, seleccione **Delegaciones** en el lado izquierdo de la página **Mis clientes**.

Las opciones de la parte superior de la página le permiten ordenar, filtrar y agrupar esta información por clientes, ofertas o palabras clave en concreto.

### <a name="view-role-assignments"></a>Visualización de asignaciones de roles

Los usuarios y los permisos asociados con cada delegación aparecen en la columna **Asignaciones de roles**. Puede seleccionar cada entrada para ver la lista completa de usuarios, grupos y entidades de servicio a los que se ha concedido acceso a la suscripción o al grupo de recursos. Desde allí, puede seleccionar un usuario, grupo o nombre de entidad de seguridad de servicio determinado para obtener más detalles.

### <a name="remove-delegations"></a>Eliminación de delegaciones

Si ha incluido usuarios con el [rol para eliminar la asignación de registros de servicios administrados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) al incorporar el cliente a Azure Lighthouse, esos usuarios pueden eliminar una delegación mediante la selección del icono de la papelera que aparece en la fila de dicha delegación. Al hacerlo, ningún usuario en el inquilino del proveedor de servicios podrá tener acceso a los recursos que se habían delegado previamente.

Para obtener más información, vea [Quitar el acceso a una delegación](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Visualización de la actividad de cambio de delegación

La sección **Registro de actividad** de la página **Mis clientes** hace un seguimiento de cada vez que se delegan suscripciones de clientes o grupos de recursos a su inquilino, y de cada vez que se eliminan recursos previamente delegados. Esta información sólo puede ser vista por los usuarios a los que se les ha [asignado el rol Lector de supervisión en el ámbito raíz](monitor-delegation-changes.md).

Para obtener más información, consulte [Visualización de cambios de delegación en Azure Portal](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabajar en el contexto de una suscripción delegada

Puede trabajar directamente en el contexto de una suscripción delegada en Azure Portal sin cambiar el directorio en el que ha iniciado sesión. Para ello:

1. Seleccione el icono **Directory + subscriptions** (Directorio y suscripciones) o **Configuración** cerca de la parte superior de Azure Portal.
1. En la página de configuración de [Directories + subscriptions (Directorios y suscripciones)](../../azure-portal/set-preferences.md#directories--subscriptions), asegúrese de que el botón de alternancia **Filtros avanzados** está [desactivado](../../azure-portal/set-preferences.md#subscription-filters).
1. En la sección **Filtro de suscripción predeterminada**, seleccione el directorio y la suscripción adecuados. (Si se le ha concedido acceso a uno o varios grupos de recursos, en lugar de obtener acceso a una suscripción completa, seleccione la suscripción a la que pertenece ese grupo de recursos. A continuación, trabajará en el contexto de esa suscripción, pero solo podrá acceder a los grupos de recursos designados.)

:::image type="content" source="../media/subscription-filter-delegated.png" alt-text="Captura de pantalla del filtro de suscripción predeterminado con una suscripción delegada seleccionada.":::

Después de esto, cuando acceda a un servicio que admite las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md), el servicio adoptará de forma predeterminada el contexto de la suscripción delegada que incluyó en el filtro.

Puede cambiar la suscripción predeterminada en cualquier momento siguiendo los pasos anteriores y eligiendo una suscripción diferente o seleccionando varias suscripciones. También puede seleccionar **Todos los directorios** y, a continuación, seleccionar la casilla **Seleccionar todo** si quiere que el filtro incluya todas las suscripciones a las que tiene acceso.

:::image type="content" source="../media/subscription-filter-all.png" alt-text="Captura de pantalla del filtro de suscripción predeterminado con todos los directorios y suscripciones seleccionados.":::

> [!IMPORTANT]
> Al seleccionar la casilla **Seleccionar todo**, se configura el filtro para mostrar todas las suscripciones a las que *actualmente* tiene acceso. Si más adelante obtiene acceso a suscripciones adicionales (por ejemplo, después de incorporar un nuevo cliente a Azure Lighthouse), estas suscripciones no se agregarán automáticamente al filtro. Para ello, deberá volver a **Directorios y suscripciones** y seleccionar las suscripciones adicionales (o desactivar y volver a seleccionar **Seleccionar todo**).

También puede acceder a la funcionalidad relacionada con las suscripciones o grupos de recursos delegados desde los servicios que admiten experiencias de administración entre inquilinos seleccionando la suscripción o el grupo de recursos desde un servicio.

## <a name="cloud-solution-provider-preview"></a>Proveedor de soluciones en la nube (versión preliminar)

Una sección independiente llamada **Proveedor de soluciones en la nube (versión preliminar)** de la página **Mis clientes** muestra la información de facturación y los recursos para los clientes de CSP que han [firmado el Contrato de cliente de Microsoft (MCA)](/partner-center/confirm-customer-agreement) y usan [el plan de Azure](/partner-center/azure-plan-get-started). Para obtener más información, consulte [Introducción a la cuenta de facturación para un contrato Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).

Esos clientes de CSP aparecerán en esta sección independientemente de si también los ha incorporado a Azure Lighthouse. Del mismo modo, no es necesario que un cliente de CSP aparezca en la sección **Proveedor de soluciones en la nube (versión preliminar)** de **Mis clientes** para poder incorporarlo a Azure Lighthouse.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
- Aprenda cómo los clientes pueden [ver y administrar proveedores de servicios](view-manage-service-providers.md) desde **Proveedores de servicios**, en Azure Portal.
