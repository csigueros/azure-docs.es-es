---
author: spelluru
ms.service: service-bus-relay
ms.topic: include
ms.date: 07/19/2021
ms.author: spelluru
ms.openlocfilehash: e4fd2d455a294b247353e4983c379066d704c87a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801459"
---
## <a name="overview"></a>Información general
Cuando una entidad de seguridad (usuario, grupo o aplicación) intenta acceder a una entidad de Relay, la solicitud debe estar autorizada. Con Azure AD, el acceso a un recurso es un proceso de dos pasos.

1. En primer lugar, se **autentica** la identidad de la entidad de seguridad y se devuelve un token de OAuth 2.0. El nombre del recurso para solicitar un token es `https://relay.azure.net`. Si una aplicación se está ejecutando dentro de una entidad de Azure, como puede ser una máquina virtual de Azure, un conjunto de escalado de máquinas virtuales o una aplicación de Azure Functions, puede usar una identidad administrada para acceder a los recursos.
2. Después, el token se pasa como parte de una solicitud al servicio Relay para **autorizar** el acceso al recurso especificado (conexiones híbridas, retransmisiones WCF). Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Azure Relay define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a las entidades Relay. También puede definir roles personalizados para acceder a los datos. Para obtener una lista de los roles integrados admitidos por Azure Relay, consulte [Roles integrados de Azure para Azure Relay](#azure-built-in-roles-for-azure-relay). Las aplicaciones nativas y las aplicaciones web que realizan solicitudes a Relay también pueden autorizar con Azure AD.  

## <a name="azure-built-in-roles-for-azure-relay"></a>Roles integrados de Azure para Azure Relay
En el caso de Azure Relay, la administración de los espacios de nombres y de todos los recursos relacionados mediante Azure Portal y la API de administración de recursos de Azure, ya se ha protegido mediante el modelo de Azure RBAC. Azure proporciona los siguientes roles integrados de Azure para autorizar el acceso a un espacio de nombres de Relay:

| Rol | Descripción | 
| ---- | ----------- | 
| [Propietario de Azure Relay](../../role-based-access-control/built-in-roles.md#azure-relay-owner) | Este rol se usa para conceder acceso **completo** a los recursos de Azure Relay. |
| [Cliente de escucha de Azure Relay](../../role-based-access-control/built-in-roles.md#azure-relay-listener) | Este rol se usa para conceder acceso de **escucha y lectura de entidades** a los recursos de Azure Relay. |
| [Emisor de Azure Relay](../../role-based-access-control/built-in-roles.md#azure-relay-sender) | Este rol se usa para conceder acceso de **envío y lectura de entidades** a los recursos de Azure Relay. | 

## <a name="resource-scope"></a>Ámbito de recursos
Antes de asignar un rol de Azure a una entidad de seguridad, determine el ámbito de acceso que debería tener la entidad de seguridad. Los procedimientos recomendados dictan que siempre es mejor conceder únicamente el ámbito más restringido posible.

En la lista siguiente se describen los niveles en los que puede definir el ámbito de acceso a recursos de Azure Relay, empezando por el ámbito más restringido:

- **Entidades Relay**: la asignación de roles se aplica a una entidad Relay específica, como una conexión híbrida o una retransmisión WCF.
- **Espacio de nombres Relay**: la asignación de roles se aplica a todas las entidades Relay en el espacio de nombres.
- **Grupo de recursos**: la asignación de roles se aplica a todos los recursos de Relay del grupo de recursos.
- **Suscripción**: la asignación de roles se aplica a todos los recursos de Relay de todos los grupos de recursos de la suscripción.

> [!NOTE]
> Tenga en cuenta que las asignaciones de roles de Azure pueden tardar hasta cinco minutos en propagarse. Para más información sobre cómo se definen los roles integrados, consulte [Descripción de definiciones de roles](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para más información acerca de la creación de roles personalizados de Azure, consulte [Roles personalizados de Azure](../../role-based-access-control/custom-roles.md). 

