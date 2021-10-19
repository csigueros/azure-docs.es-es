---
title: MFA o 2FA y Privileged Identity Management Azure AD | Microsoft Docs
description: Obtenga información sobre cómo Azure AD Privileged Identity Management (PIM) valida la autenticación multifactor (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e263de51c234c0572584911b3bd8f7d4eb7c487c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667219"
---
# <a name="multifactor-authentication-and-privileged-identity-management"></a>Autenticación multifactor y Privileged Identity Management

Se recomienda exigir la autenticación multifactor (MFA o 2FA) para todos los administradores. La autenticación multifactor reduce el riesgo de un ataque que se realice con una contraseña que se ha puesto en peligro.

Puede requerir que los usuarios completen un desafío de autenticación multifactor cuando inicien sesión. También puede requerir que los usuarios completen un desafío de autenticación multifactor al activar un rol en Azure Active Directory (Azure AD) Privileged Identity Management (PIM). De este modo, incluso si el usuario no completa un desafío de autenticación multifactor al iniciar sesión, se le pedirá que lo haga mediante Privileged Identity Management.

> [!IMPORTANT]
> En este momento, Azure AD Multi-Factor Authentication solo funciona con cuentas profesionales o educativas, no con cuentas personales de Microsoft (normalmente una cuenta personal que se usa para iniciar sesión en servicios de Microsoft como Skype, Xbox o Outlook.com). Por este motivo, cualquier persona que use una cuenta personal no puede ser un administrador válido porque no puede usar la autenticación multifactor para activar sus roles. Si estos usuarios tienen que seguir administrando cargas de trabajo con una cuenta de Microsoft, conviértalos en administradores permanentes por el momento.

## <a name="how-pim-validates-mfa"></a>Cómo PIM valida MFA

Hay dos opciones para validar la autenticación multifactor cuando un usuario activa un rol.

La opción más sencilla consiste en confiar en Azure AD Multi-Factor Authentication para los usuarios que activan un rol con privilegios. Para ello, primero compruebe que esos usuarios tienen licencia, si es necesario, y que se han registrado para Azure AD Multi-Factor Authentication. Para más información sobre cómo implementar Azure AD Multi-Factor Authentication, consulte [Implementar Azure AD Multi-Factor Authentication basado en la nube](../authentication/howto-mfa-getstarted.md). Se recomienda, pero no es necesario, configurar Azure AD para aplicar la autenticación multifactor a estos usuarios cuando inician sesión. Esto se debe a que las comprobaciones de autenticación multifactor se realizarán mediante Privileged Identity Management.

Como alternativa, si los usuarios se autentican de forma local, puede hacer que su proveedor de identidades sea responsable de la autenticación multifactor. Por ejemplo, si ha configurado Servicios de federación de AD para exigir la autenticación basada en tarjeta inteligente antes de acceder a Azure AD, el artículo [Protección de recursos en la nube con Azure AD Multi-Factor Authentication y AD FS](../authentication/howto-mfa-adfs.md) incluye instrucciones para configurar AD FS para enviar notificaciones a Azure AD. Cuando un usuario intenta activar un rol, Privileged Identity Management aceptará que ya se ha validado la autenticación multifactor para el usuario una vez que reciba las notificaciones adecuadas.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del rol de Azure AD en Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Configuración de las opciones de rol de recursos de Azure en Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
