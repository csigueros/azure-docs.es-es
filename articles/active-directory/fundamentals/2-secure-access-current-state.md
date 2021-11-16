---
title: Detección del estado actual de la colaboración externa con Azure Active Directory
description: Aprenda métodos para detectar el estado actual de su colaboración.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3874b4be6cd9f84a98a8e218f5faa983499ab5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300934"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Detección del estado actual de la colaboración externa en su organización

Antes de detectar el estado actual de la colaboración externa, debe [determinar su posición de seguridad deseada](1-secure-access-posture.md). Tendrá en cuenta las necesidades de su organización en cuanto al control centralizado frente al control delegado, así como cualquier objetivo de gobernanza, regulación y cumplimiento pertinente.

Es probable que las personas de su organización ya estén colaborando con usuarios de otras organizaciones. La colaboración puede realizarse a través de características de aplicaciones de productividad como Microsoft 365, por correo electrónico o compartiendo recursos de otra forma con usuarios externos. Los pilares de su plan de gobernanza se formarán a medida que detecte:

* a los usuarios que inician la colaboración externa;
* a los usuarios externos y las organizaciones con las que colabora;
* el acceso que se concede a los usuarios externos.

## <a name="users-initiating-external-collaboration"></a>Usuarios que inician la colaboración externa

Los usuarios que inician la colaboración externa comprenden mejor las aplicaciones más importantes para la colaboración externa y cuándo ese acceso debe finalizar. Comprender a estos usuarios puede ayudarlo a determinar a quién se le debe delegar permiso para invitar a usuarios externos, crear paquetes de acceso y completar revisiones de acceso.

Para encontrar los usuarios que están colaborando actualmente, revise el [registro de auditoría de Microsoft 365 en busca de actividades de solicitud de acceso y uso compartido](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). También puede revisar el [registro de auditoría de Azure AD para obtener más información sobre quién invitó a los usuarios de B2B](../external-identities/auditing-and-reporting.md) a su directorio.

## <a name="find-current-collaboration-partners"></a>Búsqueda de asociados de colaboración actuales

Los usuarios externos pueden ser [usuarios de B2B de Azure AD](../external-identities/what-is-b2b.md) (preferible) con credenciales administradas por el asociado, o usuarios externos con credenciales aprovisionadas localmente. Estos usuarios suelen marcarse (pero no siempre) con un tipo de usuario de invitado. Puede enumerar los usuarios invitados a través de [Microsoft Graph API](/graph/api/user-list?tabs=http), [PowerShell](/graph/api/user-list?tabs=http) o [Azure Portal](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Uso de dominios de correo electrónico y la propiedad companyName

Las organizaciones externas pueden determinarse mediante los nombres de dominio de direcciones de correo electrónico de usuario externo. Si se admiten proveedores de identidades de consumidor, como Google, puede que esto no sea posible. En este caso, se recomienda escribir el atributo companyName para identificar claramente la organización externa del usuario.

### <a name="use-allow-or-deny-lists"></a>Uso de listas de permitidos o denegados

Considere si su organización desea permitir la colaboración solo con organizaciones específicas. O bien, considere si su organización desea bloquear la colaboración con organizaciones específicas. En el nivel de inquilino, hay una [lista de permitidos o denegados](../external-identities/allow-deny-list.md), que se puede usar para controlar las invitaciones y los canjes de B2B globales, independientemente del origen (por ejemplo, Microsoft Teams, Microsoft SharePoint o Azure Portal).

Si usa la administración de derechos, también puede limitar el ámbito de los paquetes de acceso a un subconjunto de sus asociados mediante el valor Organizaciones conectadas específicas, tal como se muestra a continuación.

![Captura de pantalla de la lista de permitidos o denegados al crear un nuevo paquete de acceso.](media/secure-external-access/2-new-access-package.png)

## <a name="find-access-being-granted-to-external-users"></a>Búsqueda del acceso que se concede a los usuarios externos

Una vez que tenga un inventario de usuarios externos y organizaciones, puede determinar el acceso concedido a estos usuarios mediante Microsoft Graph API para determinar la [pertenencia a grupos](/graph/api/resources/groups-overview) de Azure AD o la [asignación de aplicaciones directa](/graph/api/resources/approleassignment) en Azure AD.

### <a name="enumerate-application-specific-permissions"></a>Enumeración de permisos específicos de la aplicación

También puede realizar la enumeración de permisos específicos de la aplicación. Por ejemplo, puede generar un informe de permisos mediante programación para SharePoint Online con [este script](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Investigue específicamente el acceso a todas las aplicaciones empresariales y críticas para la empresa, de modo que sea plenamente consciente de cualquier acceso externo.

### <a name="detect-ad-hoc-sharing"></a>Detección del uso compartido ad hoc

Si el correo electrónico y los planes de red lo posibilitan, puede investigar el contenido que se comparte a través del correo electrónico o a través de aplicaciones de software como servicio (SaaS) no autorizadas. La [protección de pérdida de datos de Microsoft 365](/microsoft-365/compliance/data-loss-prevention-policies) ayuda a identificar, prevenir y supervisar el uso compartido accidental de información confidencial en la infraestructura de Microsoft 365. [Microsoft Defender para aplicaciones en la nube](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) puede ayudarlo a identificar el uso de aplicaciones de SaaS no autorizadas en su entorno.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre cómo proteger el acceso externo a los recursos. Se recomienda realizar las acciones en el orden indicado.

1. [Determinación de la posición de seguridad para el acceso externo](1-secure-access-posture.md)

2. [Detección del estado actual](2-secure-access-current-state.md) (usted está aquí).

3. [Creación de un plan de gobernanza](3-secure-access-plan.md)

4. [Uso de grupos con fines de seguridad](4-secure-access-groups.md)

5. [Transición a Azure AD B2B](5-secure-access-b2b.md)

6. [Acceso seguro mediante la administración de derechos](6-secure-access-entitlement-managment.md)

7. [Acceso seguro mediante directivas de acceso condicional](7-secure-access-conditional-access.md)

8. [Acceso seguro mediante etiquetas de confidencialidad](8-secure-access-sensitivity-labels.md)

9. [Acceso seguro a Microsoft Teams, OneDrive y SharePoint](9-secure-access-teams-sharepoint.md)
