---
title: Procedimientos recomendados para identidades administradas asignadas por el sistema
description: Recomendaciones sobre cuándo usar identidades administradas asignadas por el usuario en lugar de asignadas por el sistema
services: active-directory
documentationcenter: ''
author: barclayn
manager: karenh444
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: barclayn
ms.openlocfilehash: b1dbdf7f7798458ec3ea3a7487f69a9dee244dda
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474295"
---
# <a name="managed-identity-best-practice-recommendations"></a>Procedimientos recomendados para identidades administradas

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="choosing-system-or-user-assigned-managed-identities"></a>Elección de identidades administradas asignadas por el sistema o por el usuario

Las identidades administradas asignadas por el usuario son más eficaces en una gama más amplia de escenarios que las asignadas por el sistema. Consulte la tabla siguiente para ver algunos escenarios y recomendaciones para el uso de identidades administradas asignadas por el usuario o por el sistema.

Varios recursos pueden usar identidades asignadas por el usuario y sus ciclos de vida se desacoplan de los ciclos de vida de los recursos a los que están asociados. [Conozca qué recursos admiten identidades administradas](./services-support-managed-identities.md).

Este ciclo de vida le permite separar las responsabilidades de creación de recursos y de administración de identidades. Las identidades asignadas por el usuario y sus asignaciones de roles se pueden configurar antes que los recursos que las requieren. Los usuarios que crean los recursos solo requieren el acceso para asignar una identidad asignada por el usuario, sin necesidad de crear nuevas identidades o asignaciones de roles.  

A medida que se crean y eliminan identidades asignadas por el sistema junto con los recursos, las asignaciones de roles no se pueden crear de antemano. Esta secuencia puede producir errores al implementar la infraestructura si el usuario que crea el recurso no tiene acceso para crear asignaciones de roles.

Si la infraestructura necesita que varios recursos requieran acceso a los mismos recursos, se les puede asignar una única identidad asignada por el usuario. Se reducirá la sobrecarga de administración, ya que hay menos identidades distintas y asignaciones de roles que administrar.

Si necesita que cada recurso tenga su propia identidad o tiene recursos que requieren un conjunto único de permisos y quiere que la identidad se elimine a medida que se elimina el recurso, deberá usar una identidad asignada por el sistema.


| Escenario| Recomendación|Notas|
|---|---|---|
| Creación rápida de recursos (por ejemplo, informática efímera) con identidades administradas |  Identidad asignada por el usuario | Si intenta crear varias identidades administradas en un breve espacio de tiempo (por ejemplo, la implementación de varias máquinas virtuales cada una con su propia identidad asignada por el sistema), puede que supere el límite de velocidad para las creaciones de objetos de Azure Active Directory y se producirá un error HTTP 429 en la solicitud. <br/><br/>Si los recursos se crean o eliminan rápidamente, también puede que supere el límite en el número de recursos de Azure Active Directory si se usan identidades asignadas por el sistema. Aunque un recurso ya no pueda acceder a una identidad asignada por el sistema eliminada, seguirá contando de cara al límite hasta que se purgue completamente después de 30 días.<br/><br/>La implementación de los recursos asociados a una única identidad asignada por el usuario requerirá la creación de una sola entidad de servicio en Azure Active Directory, lo que evitará el límite de velocidad. El uso de una identidad única creada de antemano también reducirá el riesgo de retrasos en la replicación que podrían producirse si se crean varios recursos cada uno con su propia identidad.<br/><br/>Obtenga más información sobre los [límites del servicio de suscripción de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits). |
| Recursos y aplicaciones replicados | Identidad asignada por el usuario  |  Los recursos que llevan a cabo la misma tarea (por ejemplo, servidores web duplicados o una funcionalidad idéntica que se ejecuta en un servicio de aplicaciones y en una aplicación en una máquina virtual) normalmente requieren los mismos permisos. <br/><br/>Al usar la misma identidad asignada por el usuario, se requieren menos asignaciones de roles, lo que reduce la sobrecarga administrativa. Los recursos no tienen que ser del mismo tipo. 
|Cumplimiento normativo| Identidad asignada por el usuario  | Si su organización requiere que toda la creación de identidades pase por un proceso de aprobación, el uso de una única identidad asignada por el usuario en varios recursos requerirá menos aprobaciones que en el caso de las identidades asignadas por el sistema, que se crean a medida que se crean nuevos recursos. |
Acceso necesario antes de implementar un recurso |Identidad asignada por el usuario| Algunos recursos pueden requerir acceso a determinados recursos de Azure como parte de su implementación.<br/><br/>En este caso, es posible que no se cree una identidad asignada por el sistema a tiempo, por lo que se debe usar una identidad asignada por el usuario que ya exista.|
Registro de auditoría|Identidad asignada por el sistema|Si necesita registrar qué recurso específico realizó una acción, en lugar de qué identidad, use una identidad asignada por el sistema.|
Administración del ciclo de vida de permisos|Identidad asignada por el sistema|Si necesita que se quiten los permisos de un recurso junto con este, use una identidad asignada por el sistema.

### <a name="using-user-assigned-identities-to-reduce-administration"></a>Uso de identidades asignadas por el usuario para reducir la administración

Los diagramas muestran la diferencia entre las identidades asignadas por el sistema y por el usuario, cuando se usan para permitir que varias máquinas virtuales accedan a dos cuentas de almacenamiento. 

El diagrama muestra cuatro máquinas virtuales con identidades asignadas por el sistema. Cada máquina virtual tiene las mismas asignaciones de roles que les conceden acceso a dos cuentas de almacenamiento.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities.png" alt-text="Cuatro máquinas virtuales que usan identidades asignadas por el sistema para acceder a una cuenta de almacenamiento y un almacén de claves.":::

Cuando una identidad asignada por el usuario está asociada a las cuatro máquinas virtuales, solo se requieren dos asignaciones de roles, en comparación con las ocho de las identidades asignadas por el sistema. Si la identidad de las máquinas virtuales requiere más asignaciones de roles, se concederán a todos los recursos asociados a esta identidad.

:::image type="content" source="media/managed-identity-best-practice-recommendations/user-assigned-identities.png" alt-text="Cuatro máquinas virtuales que usan una identidad asignada por el usuario para acceder a una cuenta de almacenamiento y un almacén de claves.":::

Los grupos de seguridad también se pueden usar para reducir el número de asignaciones de roles necesarias. En este diagrama se muestran cuatro máquinas virtuales con identidades asignadas por el sistema que se han agregado a un grupo de seguridad, con las asignaciones de roles agregadas al grupo en lugar de las identidades asignadas por el sistema. Aunque el resultado es similar, esta configuración no ofrece las mismas funcionalidades de la plantilla de Resource Manager que las identidades asignadas por el usuario.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities-in-a-group.png" alt-text="Cuatro máquinas virtuales con sus identidades asignadas por el sistema agregadas a un grupo de seguridad que tiene asignaciones de roles.":::

### <a name="multiple-managed-identities"></a>Varias identidades administradas

Los recursos que admiten identidades administradas pueden tener una identidad asignada por el sistema y una o varias identidades asignadas por el usuario. 

Este modelo proporciona la flexibilidad de usar una identidad compartida asignada por el usuario y aplicar permisos específicos cuando es necesario.

En el ejemplo siguiente, "Virtual Machine 3" y "Virtual Machine 4" pueden acceder a las cuentas de almacenamiento y a los almacenes de claves, según la identidad asignada por el usuario que usen durante la autenticación.

:::image type="content" source="media/managed-identity-best-practice-recommendations/multiple-user-assigned-identities.png" alt-text="Cuatro máquinas virtuales, dos con varias identidades asignadas por el usuario.":::

En el ejemplo siguiente, "Virtual Machine 4" tiene una identidad asignada por el usuario, lo que le proporciona acceso a las cuentas de almacenamiento y a los almacenes de claves, según la identidad que se utilice durante la autenticación. Las asignaciones de roles para la identidad asignada por el sistema son específicas de esa máquina virtual.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-and-user-assigned-identities.png" alt-text="Cuatro máquinas virtuales, una de ellas con identidades asignadas por el sistema y por el usuario.":::

## <a name="limits"></a>Límites 

Vea los límites de las [identidades administradas](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits) y de los [roles personalizados y asignaciones de roles](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits).

## <a name="follow-the-principle-of-least-privilege-when-granting-access"></a>Cumplimiento del principio de privilegios mínimos al conceder acceso

Al conceder a cualquier identidad, incluida una identidad administrada, los permisos para acceder a los servicios, conceda siempre los permisos mínimos necesarios para realizar las acciones deseadas. Por ejemplo, si se usa una identidad administrada para leer datos de una cuenta de almacenamiento, no es necesario permitir que esa identidad tenga permisos para escribir también datos en la cuenta de almacenamiento. Conceder permisos adicionales, como por ejemplo convertir la identidad administrada en colaboradora en una suscripción de Azure cuando no es necesario, aumenta el radio de impacto de seguridad asociado a la identidad. Siempre se debe minimizar el radio de impacto de seguridad para que, si se pone en peligro esa identidad, se causen los daños mínimos.

### <a name="consider-the-effect-of-assigning-managed-identities-to-azure-resources"></a>Consideración del efecto de asignar identidades administradas a recursos de Azure

Es importante tener en cuenta que cuando se asigna una identidad administrada a un recurso de Azure, como una aplicación lógica de Azure, una función de Azure, una máquina virtual, etc., todos los permisos concedidos a la identidad administrada ahora están disponibles para el recurso de Azure. Esto es especialmente importante porque si un usuario tiene acceso para instalar o ejecutar código en este recurso, el usuario tiene acceso a todas las identidades asignadas o asociadas al recurso de Azure. El propósito de la identidad administrada es proporcionar al código que se ejecuta en un recurso de Azure acceso a otros recursos, sin necesidad de que los desarrolladores manipulen o introduzcan las credenciales directamente en el código para obtener ese acceso.

Por ejemplo, si a una identidad administrada (ClientId = 1234) se le ha concedido acceso de lectura y escritura a ***StorageAccount7755** _ y se ha asignado a _*_LogicApp3388_*_, Alice, que no tiene ningún permiso directo sobre la identidad administrada o la cuenta de almacenamiento, pero tiene permiso para ejecutar código en _*_LogicApp3388_*_, también puede leer o escribir datos en o desde _ *_StorageAccount7755_** mediante la ejecución del código que usa la identidad administrada.

:::image type="content" source="media/managed-identity-best-practice-recommendations/security-considerations.png" alt-text="escenario de seguridad":::

En general, al conceder a un usuario acceso administrativo a un recurso que puede ejecutar código (como una aplicación lógica) y tiene una identidad administrada, tenga en cuenta si el rol que se asigna al usuario puede instalar o ejecutar código en el recurso y, en caso afirmativo, asigne ese rol solo si el usuario realmente lo necesita.


## <a name="maintenance"></a>Mantenimiento

Las identidades asignadas por el sistema se eliminan automáticamente cuando se elimina el recurso, mientras que el ciclo de vida de una identidad asignada por el usuario es independiente de los recursos a los que está asociada.

Tendrá que eliminar manualmente una identidad asignada por el usuario cuando ya no sea necesaria, incluso si no hay recursos asociados a ella.

Las asignaciones de roles no se eliminan automáticamente cuando se eliminan las identidades administradas asignadas por el sistema o por el usuario. Estas asignaciones de roles se deben eliminar manualmente para que no se supere el límite de asignaciones de roles por suscripción. 

Las asignaciones de roles asociadas a identidades administradas eliminadas se mostrarán con "Identidad no encontrada" cuando aparezcan en el portal. [Más información](../../role-based-access-control/troubleshooting.md#role-assignments-with-identity-not-found).

:::image type="content" source="media/managed-identity-best-practice-recommendations/identity-not-found.png" alt-text="No se encontró la identidad para la asignación de roles.":::
