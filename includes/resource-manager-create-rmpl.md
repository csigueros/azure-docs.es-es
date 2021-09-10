---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/29/2021
ms.author: tomfitz
ms.openlocfilehash: 735f19f1c9f66db6d311c42528b36a44bf924ec0
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227312"
---
Los vínculos privados permiten acceder a los servicios de Azure a través de un punto de conexión privado en la red virtual. Al combinar los vínculos privados con las operaciones de Azure Resource Manager, se bloquea a los usuarios que no están en el punto de conexión específico para la administración de recursos. Si un usuario malintencionado obtiene las credenciales de una cuenta de la suscripción, este no podrá administrar los recursos si no está en el punto de conexión específico.

Un vínculo privado proporciona las ventajas de seguridad siguientes:

* **Acceso privado**: los usuarios pueden administrar recursos desde una red privada a través de un punto de conexión privado.
* **Filtración de datos**: se deniega a los usuarios el acceso a los recursos no incluidos en el ámbito.

## <a name="understand-architecture"></a>Descripción de la arquitectura

Para esta versión, solo puede aplicar el acceso de administración de vínculos privados en el nivel del [grupo de administración](../articles/governance/management-groups/overview.md) raíz. Esta limitación significa que el acceso al vínculo privado se aplica en todo el inquilino.

Hay dos tipos de recursos que se usan al implementar la administración a través de un vínculo privado.

* Vínculo privado de administración de recursos (Microsoft.Authorization/resourceManagementPrivateLinks)
* Asociación de vínculo privado (Microsoft.Authorization/privateLinkAssociations)

En la imagen siguiente se muestra cómo crear una solución que restrinja el acceso para la administración de recursos.

:::image type="content" source="./media/resource-manager-create-rmpl/resource-management-private-link.svg" alt-text="Diagrama del vínculo privado de administración de recursos":::

La asociación de vínculo privado extiende el grupo de administración raíz. La asociación de vínculo privado y los puntos de conexión privados hacen referencia al vínculo privado de administración de recursos.

## <a name="workflow"></a>Flujo de trabajo

Para configurar un vínculo privado de los recursos, siga estos pasos. Los pasos se describen con más detalle posteriormente en este artículo.

1. Cree el vínculo privado de administración de recursos.
1. Cree una asociación de vínculo privado. La asociación de vínculo privado extiende el grupo de administración raíz. También hace referencia al identificador de recurso para el vínculo privado de administración de recursos.
1. Agregue un punto de conexión privado que haga referencia al vínculo privado de administración de recursos.

Después de completar esos pasos, puede administrar los recursos de Azure que se encuentren en la jerarquía del ámbito. Use un punto de conexión privado que esté conectado a la subred.

Puede supervisar el acceso al vínculo privado. Para obtener más información, vea el documento sobre [supervisión y registro](../articles/private-link/private-link-overview.md#logging-and-monitoring).

## <a name="required-permissions"></a>Permisos necesarios

Si quiere configurar el vínculo privado para la administración de recursos, debe tener el tipo de acceso siguiente:

* Propietario de la suscripción. Este acceso es necesario para crear un recurso de vínculo privado de administración de recursos.
* Propietario o Colaborador en el grupo de administración raíz. Este acceso es necesario para crear el recurso de asociación de vínculo privado.
* El Administrador global de Azure Active Directory no tiene permiso de forma automática para asignar roles en el grupo de administración raíz. Para habilitar la creación de vínculos privados de administración de recursos, el Administrador global debe tener permiso para leer el grupo de administración raíz y [elevar el acceso](../articles/role-based-access-control/elevate-access-global-admin.md) para tener permiso de Administrador de acceso de usuario en todas las suscripciones y grupos de administración del inquilino. Después de obtener el permiso de Administrador de acceso de usuario, el Administrador global debe conceder el permiso de Propietario o Colaborador en el grupo de administración raíz al usuario que crea la asociación de vínculo privado.