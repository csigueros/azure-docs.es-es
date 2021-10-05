---
title: Azure Policy para autenticación exclusiva de Azure Active Directory
description: En este artículo se proporciona información sobre cómo aplicar una directiva de Azure para crear una instancia de Azure SQL Database o Azure SQL Managed Instance con la autenticación exclusiva de Azure Active Directory (Azure AD) habilitada
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 672a46b39a726d5fd21a8c2d740008e169cf1e2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699505"
---
# <a name="azure-policy-for-azure-active-directory-only-authentication-with-azure-sql"></a>Azure Policy para autenticación exclusiva de Azure Active Directory con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> La **autenticación exclusiva de Azure AD** y la característica Azure Policy asociada que se describen en este artículo se encuentran en **versión preliminar pública**. 

Azure Policy puede exigir la creación de una instancia de Azure SQL Database o Azure SQL Managed Instance con la [autenticación exclusiva de Azure AD](authentication-azure-ad-only-authentication.md) habilitada durante el aprovisionamiento. Con esta directiva aplicada, cualquier intento de crear un [servidor lógico en Azure](logical-servers.md) o una instancia administrada genera un error si no se crea con la autenticación exclusiva de Azure AD habilitada.

La instancia de Azure Policy se puede aplicar a toda la suscripción de Azure o solo dentro de un grupo de recursos.

En Azure Policy se han incorporado dos nuevas directivas integradas:

- Azure SQL Database debe tener habilitada solo la autenticación de Azure Active Directory
- Azure SQL Managed Instance debe tener habilitada solo la autenticación de Azure Active Directory

Para obtener más información sobre Azure Policy, vea [¿Qué es Azure Policy?](/azure/governance/policy/overview) y [Estructura de definición de Azure Policy](/azure/governance/policy/concepts/definition-structure).

## <a name="permissions"></a>Permisos

Para obtener información general sobre los permisos necesarios para administrar Azure Policy, vea [Permisos de Azure RBAC en Azure Policy](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy).

### <a name="actions"></a>Acciones

Si usa un rol personalizado para administrar Azure Policy, necesita las siguientes [Acciones](/azure/role-based-access-control/role-definitions#actions).

- */read
- Microsoft.Authorization/policyassignments/*
- Microsoft.Authorization/policydefinitions/*
- Microsoft.Authorization/policyexemptions/*
- Microsoft.Authorization/policysetdefinitions/*
- Microsoft.PolicyInsights/*

Para más información sobre los roles personalizados, consulte [Roles personalizados de Azure](/azure/role-based-access-control/custom-roles).

## <a name="manage-azure-policy-for-azure-ad-only-authentication"></a>Administración de Azure Policy para la autenticación exclusiva de Azure AD

Las directivas de autenticación exclusiva de Azure AD se pueden administrar si se va a [Azure Portal](https://portal.azure.com) y se busca el servicio **Directiva**. En **Definiciones**, busque *Azure Active Directory only authentication* (Autenticación exclusiva de Azure Active Directory).

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla de Azure Policy para autenticación exclusiva de Azure AD":::

Para obtener instrucciones para agregar una instancia de Azure Policy para la autenticación exclusiva de Azure AD, vea [Uso de Azure Policy para aplicar la autenticación exclusiva de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md).

Estas directivas tienen tres efectos:

- **Auditar**: es el valor predeterminado, y solo captura un informe de auditoría de los registros de actividad de Azure Policy
- **Denegar**: evita la creación de servidores lógicos o instancias administradas sin la [autenticación exclusiva de Azure AD](authentication-azure-ad-only-authentication.md) habilitada
- **Deshabilitado**: deshabilita la directiva y no evita que los usuarios creen un servidor lógico o una instancia administrada sin la autenticación exclusiva de Azure AD habilitada

Si la instancia de Azure Policy para la autenticación exclusiva de Azure AD está establecida en **Denegar**, se genera un error al crear un servidor lógico o una instancia administrada de Azure SQL. Los detalles de este error se registran en el **registro de actividad** del grupo de recursos.

## <a name="policy-compliance"></a>Cumplimiento de directivas

Puede consultar el valor **Cumplimiento** en el servicio **Directiva** para ver el estado de cumplimiento. El **estado de cumplimiento** indica si el servidor o la instancia administrada tiene habilitada la autenticación exclusiva de Azure AD en ese momento. 

La instancia de Azure Policy puede evitar que se cree un servidor lógico o una instancia administrada sin la autenticación exclusiva de Azure AD habilitada, pero la característica se puede cambiar después de la creación del servidor o la instancia administrada. Si un usuario ha deshabilitado la autenticación exclusiva de Azure AD después de crear el servidor o la instancia administrada, el estado de cumplimiento es `Non-compliant` si Azure Policy está establecido en **Denegar**.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/check-compliance-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla del menú Cumplimiento de Azure Policy para la autenticación exclusiva de Azure AD":::

## <a name="limitations"></a>Limitaciones

- Actualmente no se puede crear un servidor lógico ni una instancia administrada en Azure Portal con la autenticación exclusiva de Azure AD habilitada. Puede crear un servidor lógico o una instancia administrada con la autenticación exclusiva de Azure AD habilitada mediante la CLI de Azure, PowerShell, la API REST o con una plantilla de ARM. Para obtener más información, vea [Creación de un servidor con autenticación solo de Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md).
- Azure Policy aplica la autenticación exclusiva de Azure AD durante la creación del servidor lógico o la instancia administrada. Una vez creado el servidor, los usuarios de Azure AD autorizados con roles especiales (por ejemplo, Administrador de seguridad SQL) pueden deshabilitar la característica de autenticación exclusiva de Azure AD. Azure Policy lo permite, pero, en este caso, el servidor o la instancia administrada se muestran en el informe de cumplimiento como `Non-compliant` y el informe indica el nombre del servidor o la instancia administrada.  
- Para conocer más comentarios, problemas conocidos y los permisos necesarios, vea [Autenticación solo de Azure AD](authentication-azure-ad-only-authentication.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Uso de Azure Policy para aplicar la autenticación exclusiva de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md)
