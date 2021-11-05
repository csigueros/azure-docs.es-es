---
title: 'Consulta y eliminación de solicitudes para un paquete de acceso en la administración de derechos de Azure AD: Azure Active Directory'
description: Aprenda a consultar y quitar solicitudes para un paquete de acceso en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 9/20/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140d648ddde6d520fdb27c6df0152cb9eb61c1fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051808"
---
# <a name="view-and-remove-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Consulta y eliminación de solicitudes para un paquete de acceso en la administración de derechos de Azure AD

En la administración de derechos de Azure AD, puede ver quién ha solicitado los paquetes de acceso, su directiva y su estado. En este artículo se describe cómo ver las solicitudes de un paquete de acceso y quitar las solicitudes que ya no son necesarias.

## <a name="view-requests"></a>Ver solicitudes

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. Haga clic en **Solicitudes**.

1. Haga clic en una solicitud específica para ver detalles adicionales.

    ![Lista de solicitudes de un paquete de acceso](./media/entitlement-management-access-package-requests/requests-list.png)

1. Puede hacer clic en **Detalles del historial de solicitudes** para ver quién aprobó una solicitud, cuáles eran sus justificaciones de aprobación y cuándo se entregó el acceso.

Si tiene un conjunto de usuarios cuyas solicitudes están en el estado "Entregado parcialmente" o "Error", puede reintentar esas solicitudes mediante la [funcionalidad de reprocesamiento](entitlement-management-reprocess-access-package-requests.md).

### <a name="view-assignments-with-microsoft-graph"></a>Visualización de asignaciones con Microsoft Graph
También puede recuperar las solicitudes de un paquete de acceso mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso delegado `EntitlementManagement.Read.All` o `EntitlementManagement.ReadWrite.All` puede llamar a la API para [enumerar elementos accessPackageRequests](/graph/api/accesspackageassignmentrequest-list?view=graph-rest-beta&preserve-view=true). Puede proporcionar un filtro para indicar un paquete de acceso específico, como: `$expand=accessPackage&$filter=accessPackage/id eq '9bbe5f7d-f1e7-4eb1-a586-38cdf6f8b1ea'`. Una aplicación que tenga el permiso de aplicación `EntitlementManagement.Read.All` o `EntitlementManagement.ReadWrite.All` también puede usar esta API.

## <a name="remove-request-preview"></a>Eliminación de solicitud (versión preliminar)

También puede quitar una solicitud completada que ya no sea necesaria. Para quitar una solicitud:

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. Haga clic en **Solicitudes**.

1. Busque la solicitud que desea quitar del paquete de acceso.

1. Seleccione el botón Quitar.

> [!NOTE]
> Si quita una solicitud completada de un paquete de acceso, esto no quita la asignación activa, solo los datos de la solicitud. Por lo tanto, el solicitante seguirá teniendo acceso. Si también necesita quitar una asignación y el acceso resultante de ese paquete de acceso, en el menú de la izquierda, haga clic en Asignaciones **,** , busque la asignación y, a continuación, quite [la asignación](entitlement-management-access-package-assignments.md).

### <a name="remove-a-request-with-microsoft-graph"></a>Quitar una solicitud con Microsoft Graph

También puede quitar una solicitud mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso delegado `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para [quitar un elemento accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Pasos siguientes

- [Reprocesar las solicitudes de un paquete de acceso](entitlement-management-reprocess-access-package-requests.md)
- [Modificación de la configuración de solicitudes y aprobación de un paquete de acceso](entitlement-management-access-package-request-policy.md)
- [Visualización, incorporación y eliminación de asignaciones en un paquete de acceso](entitlement-management-access-package-assignments.md)
- [Solución de problemas de solicitudes](entitlement-management-troubleshoot.md#requests)
