---
title: Visualización y administración de proveedores de servicios
description: Los clientes pueden ver información sobre las ofertas de los proveedores de servicios, los recursos delegados y los proveedores de servicios de Azure Lighthouse en Azure Portal.
ms.date: 07/16/2021
ms.topic: how-to
ms.openlocfilehash: fef9d23209e0551f105263c019297df8208e940a
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389175"
---
# <a name="view-and-manage-service-providers"></a>Visualización y administración de proveedores de servicios

La página de **proveedores de servicios** de [Azure Portal](https://portal.azure.com) ofrece a los clientes control y visibilidad de sus proveedores de servicios que usan [Azure Lighthouse](../overview.md). Los clientes pueden delegar recursos específicos, revisar ofertas nuevas o actualizadas, quitar el acceso del proveedor de servicios y mucho más.

Para ver esta página en Azure Portal, seleccione **Todos los servicios**, busque la opción **Proveedores de servicios** y selecciónela. También puede encontrar esta página si escribe "Proveedores de servicios" o "Azure Lighthouse" en el cuadro de búsqueda situado cerca de la parte superior de Azure Portal.

> [!NOTE]
> Para ver la página **Proveedores de servicios**, un usuario del inquilino del cliente debe tener el [rol integrado Lector](../../role-based-access-control/built-in-roles.md#reader) (u otro que incluya el acceso de lectura).
>
> Para agregar o actualizar ofertas, delegar recursos y quitar ofertas, el usuario debe tener un rol con el permiso `Microsoft.Authorization/roleAssignments/write` como, por ejemplo, [Propietario](../../role-based-access-control/built-in-roles.md#owner).

Tenga en cuenta que la página de **Proveedores de servicios** solo muestra información sobre los proveedores de servicios que tienen acceso a las suscripciones o grupos de recursos del cliente mediante Azure Lighthouse. No muestra ninguna información sobre proveedores de servicios adicionales que no usan Azure Lighthouse.

## <a name="view-service-provider-details"></a>Ver detalles del proveedor de servicios

Para ver detalles sobre los proveedores de servicios actuales que usan Azure Lighthouse para trabajar en el inquilino del cliente, seleccione **Ofertas del proveedor de servicios** en el lado izquierdo de la página **Proveedores de servicios**.

Para cada oferta, verá el nombre del proveedor del servicio y la oferta asociada. Puede seleccionar una oferta para ver una descripción y otros detalles, incluidas las asignaciones de roles que se han concedido al proveedor de servicios.

En la columna **Delegaciones**, puede ver el número de suscripciones o grupos de recursos que se han delegado en el proveedor de servicios de esa oferta. El proveedor de servicios podrá tener acceso a estas suscripciones o grupos de recursos y administrarlos de acuerdo con los niveles de acceso especificados en la oferta.

## <a name="add-service-provider-offers"></a>Incorporación de ofertas del proveedor de servicios

Para agregar una nueva oferta del proveedor de servicios desde la página **Ofertas del proveedor de servicios**, seleccione **Agregar oferta**. Seleccione **Ofertas privadas** para ver las ofertas que ha publicado un proveedor de servicios para este cliente. Luego puede seleccionar una oferta y, a continuación, seleccione **Configurar y suscribirse**.

## <a name="update-service-provider-offers"></a>Actualización de las ofertas de proveedores de servicios

Una vez que un cliente ha agregado una oferta, un proveedor de servicios puede publicar una versión actualizada de la misma oferta en Azure Marketplace, a fin de agregar una definición de roles nueva. Si se ha publicado una versión nueva de la oferta, la página **Ofertas del proveedor de servicios** mostrará un icono de actualización en la fila de esa oferta. Seleccione este icono para ver las diferencias entre la versión actual de la oferta y la nueva.

 ![Icono de actualización de la oferta](../media/update-offer.jpg)

Después de revisar los cambios, el cliente puede optar por actualizar a la nueva versión. Las autorizaciones y otras opciones especificadas en la nueva versión se aplicarán a todas las suscripciones o grupos de recursos que se hayan delegado para esa oferta.

## <a name="remove-service-provider-offers"></a>Eliminación de las ofertas de proveedores de servicios

Puede quitar una oferta del proveedor de servicios en cualquier momento seleccionando el icono de la papelera en la fila de esa oferta.

Después de confirmar la eliminación, ese proveedor de servicios ya no tendrá acceso a los recursos que anteriormente se delegaron para esa oferta.

## <a name="delegate-resources"></a>Recursos delegados

Para que un proveedor de servicios pueda acceder a los recursos de un cliente y administrarlos, se deben delegar una o varias suscripciones o grupos de recursos específicos. Si un cliente ha aceptado una oferta, pero aún no ha delegado ningún recurso, aparecerá una nota en la parte superior de la sección **Ofertas del proveedor de servicios**. El proveedor de servicios no podrá trabajar en ningún recurso del inquilino del cliente hasta que se complete la delegación.

Para delegar suscripciones o grupos de recursos:

1. Active la casilla de la fila que contiene el proveedor de servicios, la oferta y el nombre. A continuación, seleccione **Delegar recursos** en la parte superior de la pantalla.
1. En la sección **Detalles de la oferta** de la página **Delegar recursos**, revise los detalles sobre el proveedor de servicios y la oferta. Para revisar las asignaciones de roles de la oferta, seleccione **Hacer clic aquí para ver los detalles de la oferta seleccionada**.
1. En la sección **Delegar**, seleccione **Delegar suscripciones** o **Delegar grupos de recursos**.
1. Elija las suscripciones o los grupos de recursos que quiera delegar para esta oferta y, después, seleccione **Agregar**.
1. Active la casilla situada en la parte inferior de la página para confirmar que quiere conceder a este proveedor de servicios acceso a los recursos que ha seleccionado y, después, seleccione **Delegar**.

## <a name="view-delegations"></a>Ver delegaciones

Las delegaciones representan una asociación de recursos de cliente específicos (suscripciones o grupos de recursos) con asignaciones de roles que conceden permisos al proveedor de servicios para esos recursos. Para ver los detalles de delegación, seleccione **Delegaciones** en el lado izquierdo de la página **Proveedores de servicios**.

Los filtros de la parte superior de la página permiten ordenar y agrupar la información de delegación. También puede filtrar por clientes, ofertas o palabras clave específicos.

> [!NOTE]
> Al [ver las asignaciones de roles para el ámbito delegado en Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) o a través de las API, los clientes no verán estas asignaciones de roles o los usuarios del inquilino del proveedor de servicios a los que se hayan concedido estos roles.

## <a name="audit-and-restrict-delegations-in-your-environment"></a>Auditoría y restricciones de delegaciones en el entorno

Es posible que los clientes quieran revisar todas las suscripciones y grupos de recursos que se delegaron a Azure Lighthouse. Esto es especialmente útil para los clientes con un gran número de suscripciones o para los que tienen muchos usuarios que realizan tareas de administración.

Se proporciona una [definición de directiva integrada de Azure Policy](../../governance/policy/samples/built-in-policies.md#lighthouse) para [auditar la delegación de ámbitos en un inquilino de administración](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Esta directiva se puede asignar a un grupo de administración que incluya todas las suscripciones que quiera auditar. Al comprobar el cumplimiento de esta directiva, las suscripciones o los grupos de recursos delegados (dentro del grupo de administración al que se asigna la directiva) se mostrarán en un estado no conforme. Luego, puede revisar los resultados y confirmar que no hay ninguna delegación inesperada.

Otra [definición de directiva integrada](../../governance/policy/samples/built-in-policies.md#lighthouse) le permite [restringir las delegaciones a inquilinos de administración específicos](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Esta directiva se puede aplicar a un grupo de administración que incluya las suscripciones para las que desea limitar las delegaciones. Una vez implementada la directiva, se denegarán todos los intentos de delegar una suscripción a un inquilino fuera de los que especifique.

Para más información sobre cómo asignar una directiva y ver los resultados del estado de cumplimiento, consulte [Inicio rápido: Creación de una asignación de directiva](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de [Azure Lighthouse](../overview.md).
- Aprenda a [auditar la actividad del proveedor de servicios](view-service-provider-activity.md).
- Descubra cómo los proveedores de servicios pueden [ver y administrar sus clientes](view-manage-customers.md) en la página **Mis clientes** de Azure Portal.
- Obtenga información sobre cómo las [empresas que administran varios inquilinos](../concepts/enterprise.md) pueden usar Azure Lighthouse para consolidar su experiencia de administración.
