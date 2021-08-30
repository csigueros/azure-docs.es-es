---
title: Procedimientos de seguridad recomendados
description: Al usar Azure Lighthouse, es importante tener en cuenta la seguridad y el control de acceso.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 897f09c41d550d6200cc3d78743bb3b010f2c402
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768258"
---
# <a name="recommended-security-practices"></a>Procedimientos de seguridad recomendados

Al usar [Azure Lighthouse](../overview.md), es importante tener en cuenta la seguridad y el control de acceso. Los usuarios de su inquilino tendrán acceso directo a las suscripciones y los grupos de recursos del cliente, por lo que es recomendable tomar medidas para proteger la seguridad del inquilino. También es recomendable asegurarse de que solo se permite el acceso necesario para administrar de forma eficaz los recursos de los clientes. En este tema se proporcionan recomendaciones para ayudarle a hacerlo.

> [!TIP]
> Estas recomendaciones también se aplican a [empresas que administran varios inquilinos](enterprise.md) con Azure Lighthouse.

## <a name="require-azure-ad-multi-factor-authentication"></a>Requerir Azure AD Multi-Factor Authentication

[Multi-Factor Authentication de Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md) (también conocido como verificación en dos pasos) ayuda a evitar que los atacantes obtengan acceso a una cuenta, al exigir varios pasos de autenticación. Debe requerir Multi-Factor Authentication de Azure AD para todos los usuarios del inquilino administrador, incluidos los usuarios que tendrán acceso a los recursos delegados de cliente.

Le recomendamos que pida a sus clientes que implementen también Multi-Factor Authentication de Azure AD en sus inquilinos.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Asignar permisos a grupos mediante el principio de privilegios mínimos

A fin de facilitar la administración, se recomienda usar grupos de Azure Active Directoy (Azure AD) para cada rol necesario para administrar los recursos de los clientes. Esto le permite agregar o quitar usuarios individuales del grupo según sea necesario, en lugar de asignar permisos directamente a cada usuario.

> [!IMPORTANT]
> Para agregar permisos a un grupo de Azure AD, el **Tipo de grupo** debe establecerse en **Seguridad**. Esta opción se selecciona cuando se crea el grupo. Para obtener más información vea [Creación de un grupo básico e incorporación de miembros con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Al crear la estructura de permisos, asegúrese de seguir el principio de privilegios mínimos para que los usuarios solo tengan los permisos necesarios para completar su trabajo, lo que ayuda a reducir la posibilidad de errores involuntarios.

Por ejemplo, puede que quiera usar una estructura como la siguiente:

|Nombre del grupo  |Tipo  |principalId  |Definición de roles  |Id. de definición de roles  |
|---------|---------|---------|---------|---------|
|Arquitectos     |Grupo de usuarios         |\<principalId\>         |Colaborador         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Evaluación     |Grupo de usuarios         |\<principalId\>         |Lector         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Especialistas en VM     |Grupo de usuarios         |\<principalId\>         |Colaborador de VM         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Nombre de entidad de seguridad de servicio (SPN)         |\<principalId\>         |Colaborador         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Una vez que se hayan creado estos grupos, puede asignarles usuarios según sea necesario. Agregue solo los usuarios que realmente necesitan tener acceso. Asegúrese de revisar la pertenencia a grupos con regularidad y quite los usuarios que ya no sean adecuados o que no sea necesario incluir.

Tenga en cuenta que al [incorporar clientes a través de una oferta pública de servicios administrados](../how-to/publish-managed-services-offers.md), cualquier grupo (o entidad de servicio o usuario) que incluya tendrá los mismos permisos para todos los clientes que compren el plan. A fin de asignar grupos diferentes con el objetivo de que trabajen con cada cliente, tendrá que publicar un plan privado independiente exclusivo para cada cliente o incorporar los clientes individualmente mediante plantillas de Resource Manager. Por ejemplo, podría publicar un plan público que tenga un acceso muy limitado y, después, trabajar con el cliente directamente para incorporar sus recursos para obtener acceso adicional mediante una plantilla de recursos de Azure personalizada que concede acceso adicional según sea necesario.

## <a name="next-steps"></a>Pasos siguientes

- Revise la [información de la línea de base de seguridad](../security-baseline.md) para comprender cómo se aplica la guía de Azure Security Benchmark a Azure Lighthouse.
- [Implemente Multi-Factor Authentication de Azure AD](../../active-directory/authentication/howto-mfa-getstarted.md).
- Más información sobre las [experiencias de administración entre inquilinos](cross-tenant-management-experience.md).
