---
title: Directiva de contraseñas combinadas y comprobación de contraseñas no seguras en Azure Active Directory
description: Más información sobre la directiva de contraseñas combinada y la comprobación de contraseñas no seguras en Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: justinha
author: sajiang
manager: daveba
ms.reviewer: sajiang
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21fb52047822df5c14e8b5a21c017a2e4ca5f1d6
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138760"
---
# <a name="combined-password-policy-and-weak-password-check-in-azure-active-directory"></a>Directiva de contraseñas combinadas y comprobación de contraseñas no seguras en Azure Active Directory

A partir de octubre de 2021, la validación de Azure Active Directory (Azure AD) del cumplimiento de las directivas de contraseña también incluye una comprobación de [contraseñas no seguras conocidas](concept-password-ban-bad.md) y sus variantes. A medida que la comprobación combinada de la directiva de contraseñas y las contraseñas prohibidas se implementan en los inquilinos, es posible que los usuarios de Azure AD y del Centro de administración de Office 365 observen diferencias al crear, cambiar o restablecer sus contraseñas. En este tema se explican los detalles sobre los criterios de directiva de contraseña que comprueba Azure AD. 

## <a name="azure-ad-password-policies"></a>Directivas de contraseña de Azure AD

Una directiva de contraseñas se aplica a todas las cuentas de usuario y administrador que se crean y administran directamente en Azure AD. Puede [prohibir las contraseñas no seguras](concept-password-ban-bad.md) y definir parámetros para [bloquear una cuenta](howto-password-smart-lockout.md) después de varios intentos de contraseña incorrecta. Otras opciones de configuración de directiva de contraseñas no se pueden modificar.

La directiva de contraseñas de Azure AD no se aplica a las cuentas de usuario que se sincronizan desde un entorno de AD DS local mediante Azure AD Connect, a menos que habilite EnforceCloudPasswordPolicyForPasswordSyncedUsers.

Se aplican los siguientes requisitos de directiva de contraseña de Azure AD a todas las contraseñas que se crean, cambian o restablecen en Azure AD. Los requisitos se aplican durante el aprovisionamiento de usuarios, el cambio de contraseña y los flujos de restablecimiento de contraseña. A menos que se indique lo contrario, estos ajustes no se pueden cambiar.

| Propiedad | Requisitos |
| --- | --- |
| Caracteres permitidos |Caracteres en mayúsculas (A - Z)<br>Caracteres en minúsculas (a - z)<br>Números (0 - 9)<br>Símbolos:<br>- @ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ><br>- espacio en blanco |
| Caracteres no permitidos | Caracteres Unicode |
| Longitud de la contraseña |Las contraseñas requieren lo siguiente<br>- Al menos 8 caracteres.<br>- 256 caracteres como máximo.</li> |
| Complejidad de la contraseña |Las contraseñas requieren tres de los cuatro elementos siguientes:<br>- Caracteres en mayúsculas<br>- Caracteres en minúsculas<br>- Números <br>- Símbolos<br> Nota: La comprobación de complejidad de la contraseña no es necesaria para los inquilinos del ámbito educativo. |
| Contraseña no usada recientemente | Cuando un usuario cambia o restablece su contraseña, la nueva contraseña no puede ser la misma que la contraseña actual u otras usadas recientemente. |
| La [protección de contraseñas de Azure AD](concept-password-ban-bad.md) no ha prohibido la contraseña. | La contraseña no puede estar en la lista global de contraseñas prohibidas de la protección de contraseñas de Azure AD ni en la lista personalizable de contraseñas prohibidas específicas de la organización. |

## <a name="password-expiration-policies"></a>Directivas de expiración de contraseñas

Las directivas de expiración de contraseña no han cambiado, pero se incluyen en este tema para completarlo. Un *administrador global* o un *administrador de usuarios* puede usar el módulo [Microsoft Azure AD para Windows PowerShell](/powershell/module/Azuread/) al configurar las contraseñas de usuario para que no expiren.

> [!NOTE]
> De forma predeterminada, solo las contraseñas de cuentas de usuario que no se sincronizan a través de Azure AD Connect pueden configurarse para que no expiren. Para obtener más información sobre la sincronización de directorios, vea el artículo sobre cómo [conectar AD con Azure AD](../hybrid/how-to-connect-password-hash-synchronization.md#password-expiration-policy).

También puede usar PowerShell para quitar la configuración sin expiración o consultar las contraseñas de usuario configuradas para no expirar nunca.

Los siguientes requisitos de expiración se aplican a otros proveedores que usan Azure AD para servicios de identidad y directorio, como Intune y Microsoft 365. 

| Propiedad | Requisitos |
| --- | --- |
| Duración de la expiración de la contraseña (vigencia máxima de la contraseña) |<ul><li>Valor predeterminado: **90** días.</li><li>El valor se puede configurar con el cmdlet `Set-MsolPasswordPolicy`del módulo Active Directory para Windows PowerShell.</li></ul> |
| Notificación de expiración de contraseña (cuándo se notifica a los usuarios la expiración de la contraseña) |<ul><li>Valor predeterminado: **14** días (antes de que expire la contraseña).</li><li>El valor se puede configurar con el cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiración de las contraseñas (permitir que las contraseñas no expiren nunca) |<ul><li>Valor predeterminado: **falso** (indica que las contraseñas tienen una fecha de expiración).</li><li>El valor se puede configurar para cuentas de usuario individuales mediante el cmdlet `Set-MsolUser`.</li></ul> |

## <a name="next-steps"></a>Pasos siguientes

- [Restricciones de cuenta y directivas de contraseñas en Azure Active Directory](concept-sspr-policy.md)
- [Eliminación de contraseñas incorrectas mediante Protección con contraseña de Azure Active Directory](concept-password-ban-bad.md)
