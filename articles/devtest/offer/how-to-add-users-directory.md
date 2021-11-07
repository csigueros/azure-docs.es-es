---
title: Adición de usuarios al inquilino del directorio para desarrolladores de Desarrollo/pruebas de Azure
description: Guía paso a paso para agregar usuarios a la suscripción de crédito de Azure y administrar su acceso con controles basados en roles.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 14ded977ab78e8fbf6baf70d1c59e4aa632927b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091013"
---
# <a name="add-users-to-your-azure-credit-subscription"></a>Adición de usuarios a la suscripción de crédito de Azure  

Para administrar y acceder a los recursos de suscripción, debe ser un usuario que esté en el directorio. En las propias suscripciones, la jerarquía se controla mediante los ámbitos de inquilinos de Azure Active Directory (Azure AD). Azure AD es un protocolo ligero de directorio (LDAP) que almacena nombres de cuenta y contraseñas.  

Antes de agregar usuarios, debe determinar la jerarquía empresarial y el nivel de acceso que necesitan dentro de la suscripción.  

## <a name="why-do-i-need-to-add-users"></a>¿Por qué es necesario agregar usuarios?

El paso 1 consiste en determinar si necesita agregar un nuevo usuario a la suscripción. Estos son algunos ejemplos de por qué tendría que agregar nuevos usuarios:  

- Trabaja en un proyecto y tiene que proporcionar acceso de TI para supervisar la seguridad.  
- Trabaja en nuevos protocolos de API que podrían ser beneficiosos para otro miembro de una organización.  
- Debe proporcionar acceso en el nivel de suscripción para que puedan acceder a todos los grupos de recursos.  
- En el nivel empresarial, un único grupo de recursos debe iniciar sesión en un equipo, pero no necesita acceso a la suscripción.  
- Proporciona más visibilidad y transparencia para los proyectos en los que está trabajando, pero aísla ciertas partes del trabajo si es necesario.  
- Quiere agregar un consultor como usuario o dentro de un grupo para que contribuya.  
- Quiere colaborar con alguien para probar y supervisar la preproducción.  

## <a name="where-do-i-add-users-and-their-roles-within-my-subscription"></a>¿Dónde puedo agregar usuarios y sus roles dentro de mi suscripción?

En Azure AD, la administración de acceso es una función crítica. [El control de acceso basado en rol de Azure](../../role-based-access-control/overview.md) \((RBAC de Azure)\) es un sistema de autorización que proporciona una administración avanzada del acceso a los recursos de Azure.  

Cuando haya determinado que necesita agregar un usuario, debe comprender dónde los va a agregar y a qué recursos necesitan acceder. El conjunto de recursos a los que un usuario puede acceder se conoce como ámbito.  

Si el proyecto y las tareas en las que está trabajando deben ser supervisados por TI para habilitar los protocolos de seguridad, necesitará que ese usuario tenga un rol de administrador en el grupo Administración para tener acceso completo y permisos dentro de la suscripción.  

![Captura de pantalla de los niveles de acceso en Azure.](media/how-to-add-users-directory/access-management.png "Administración de roles con grupos de administración en Azure.")

Sin embargo, si está colaborando con otro desarrollador o con un compañero, es posible que solo necesiten acceso en el nivel de recurso o grupo de recursos.  

Lea la [introducción a RBAC de Azure](../../role-based-access-control/overview.md) para comprender mejor cómo funciona RBAC de Azure y el propósito que sirve en su suscripción.  

Cómo agregar usuarios o eliminarlos mediante Azure Active Directory  

- [Incorporación o eliminación de usuarios: Azure Active Directory | Microsoft Docs](../../active-directory/fundamentals/add-users-azure-active-directory.md)  

- [Pasos para asignar un rol de Azure: RBAC de Azure | Microsoft Docs](../../role-based-access-control/role-assignments-steps.md)  
