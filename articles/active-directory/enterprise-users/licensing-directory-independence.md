---
title: 'Características de la interacción multiinquilino: Azure AD | Microsoft Docs'
description: Descripción de la independencia de datos de las organizaciones de Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79673341845504590d1c70f802a04c644d20b7db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750540"
---
# <a name="understand-how-multiple-azure-active-directory-tenant-organizations-interact"></a>Información de cómo interactúan varias organizaciones inquilinas de Azure Active Directory

En Azure Active Directory (Azure AD), cada organización de Azure AD es totalmente independiente: un elemento del mismo nivel, que es lógicamente independiente de otras organizaciones de Azure AD que administre. Esta independencia entre organizaciones incluye la independencia de recursos, la independencia administrativa y la independencia de sincronización. No hay ninguna relación de elementos primarios y secundarios entre las organizaciones.

## <a name="resource-independence"></a>Independencia de recursos

* Si crea o elimina un recurso de Azure AD en una organización, esto no tiene efecto en ningún recurso de las demás organizaciones, con la excepción parcial de los usuarios externos.
* Si registra uno de los nombres de dominio con una organización, no podrá usarse por ninguna otra organización.

## <a name="administrative-independence"></a>Independencia administrativa

Si un usuario no administrativo de la organización "Contoso" crea la organización de prueba "Prueba", en ese caso:

* De forma predeterminada, el usuario que crea una organización se agrega como un usuario externo en esa nueva organización y se le asigna el rol de administrador global en esa organización.
* Los administradores de la organización "Contoso" no tienen privilegios administrativos directos en la organización "Prueba", a menos que el administrador de "Prueba" les conceda específicamente estos privilegios. Sin embargo, los administradores de "Contoso" pueden controlar el acceso a la organización "Prueba" si inician sesión con la cuenta de usuario que creó "Prueba".
* Si agrega o quita un rol de Azure AD para un usuario de una organización, el cambio no afectará a los roles que el usuario esté asignado en otras organizaciones Azure AD.

## <a name="synchronization-independence"></a>Independencia de sincronización

Puede configurar cada organización de Azure AD de manera independiente para que los datos se sincronicen desde una sola instancia de:

* La herramienta Azure AD Connect, para sincronizar los datos con un único bosque de AD.
* El conector de Azure Active Directory para Forefront Identity Manager, para sincronizar datos con uno o varios bosques locales u orígenes de datos distintos de Azure AD.

## <a name="add-an-azure-ad-organization"></a>Incorporación de una de organización de Azure AD

Para agregar una organización de Azure AD en Azure Portal, inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que sea un administrador global de Azure AD y seleccione **Nuevo**.

> [!NOTE]
> A diferencia de otros recursos de Azure, las organizaciones de Azure AD no son recursos secundarios de una suscripción a Azure. Aunque su suscripción a Azure se cancele o expire, puede acceder a los datos de la organización de Azure AD mediante Azure PowerShell, Microsoft Graph API o el Centro de administración de Microsoft 365. También puede [asociar otra suscripción con la organización](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Pasos siguientes

Para consideraciones sobre la concesión de licencias de Azure AD y las prácticas recomendadas, consulte [¿Qué es la licencia de Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
