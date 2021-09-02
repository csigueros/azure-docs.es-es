---
title: Traslado de un grupo de discos de Azure (versión preliminar) a otra suscripción
description: Aprenda a mover un grupo de discos de Azure a otra suscripción.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 629ca5617bfc2166e10172863fc6d7560698919d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436942"
---
# <a name="move-a-disk-pool-preview-to-a-different-subscription"></a>Traslado de un grupo de discos (versión preliminar) a otra suscripción

El traslado de un grupo de discos de Azure (versión preliminar) implica mover el propio grupo de discos, los discos contenidos en el grupo de discos, el grupo de recursos administrado del grupo de discos y todos los recursos incluidos en el grupo de recursos administrado. Actualmente, Azure no admite el traslado de varios grupos de recursos a otra suscripción al mismo tiempo. 

- Exporte la plantilla del grupo de discos existente.
- Elimine el grupo de discos antiguo.
- Mueva los recursos de Azure necesarios para crear un grupo de discos.
- Vuelva a implementar el grupo de discos.

## <a name="export-your-existing-disk-pool-template"></a>Exportación de la plantilla de grupo de discos existente

Para que el proceso de reimplementación sea más sencillo, exporte la plantilla desde el grupo de discos existente. Esta plantilla se puede usar para volver a implementar el grupo de discos en la suscripción que se prefiera, con la misma configuración. Consulte [este artículo](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) para aprender a exportar una plantilla desde un recurso.

## <a name="delete-the-old-disk-pool"></a>Eliminación del grupo de discos antiguo

Ahora que ha exportado la plantilla, elimine el grupo de discos antiguo. Al eliminar el grupo de discos, se quita el recurso del grupo de discos y su grupo de recursos administrado. Consulte [este artículo](disks-pools-deprovision.md) para obtener instrucciones sobre cómo eliminar un grupo de discos.

## <a name="move-your-disks-and-virtual-network"></a>Traslado de discos y de una red virtual

Ahora que se ha eliminado el grupo de discos, puede mover la red virtual y los discos, y posiblemente los clientes, a la suscripción a la que desea cambiar. Consulte [este artículo](../azure-resource-manager/management/move-resource-group-and-subscription.md) para aprender a mover recursos de Azure a otra suscripción.

## <a name="redeploy-your-disk-pool"></a>Reimplementación de un grupo de discos

Una vez que haya movido los restantes recursos a la suscripción, actualice la plantilla del grupo de discos antiguo para que todas las referencias a los discos, la red virtual, la subred y los clientes apunten ahora a sus nuevos identificadores URI de recursos. Tras ello, vuelva a implementar la plantilla en la nueva suscripción. Para aprender a editar e implementar una plantilla, consulte [este artículo](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md#edit-and-deploy-the-template).

## <a name="next-steps"></a>Pasos siguientes

Para aprender a administrar un grupo de discos, consulte [Administración de un grupo de discos](disks-pools-manage.md).