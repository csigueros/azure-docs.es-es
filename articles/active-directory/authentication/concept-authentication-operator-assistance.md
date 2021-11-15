---
title: Asistencia para operadores en Azure Active Directory
description: Obtenga información sobre el desuso de la característica de asistencia para operadores en Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: justinha
author: rckyplln
manager: daveba
ms.reviewer: ripull
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c00fb3c68955a888bbc383fe59aef8f7ed75835
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270149"
---
# <a name="how-to-enable-and-disable-operator-assistance"></a>Habilitación y deshabilitación de la asistencia para operadores

La asistencia para operadores es una característica de Azure AD que permite a un operador transferir manualmente llamadas telefónicas por oposición a la transferencia automática. Cuando este valor está habilitado, se marca el número de teléfono de la oficina y, cuando se contesta, el sistema solicita al operador que transfiera la llamada a una extensión dada.

La asistencia para operadores se puede habilitar para todo un inquilino o para un único usuario. Si el valor es **Activado**, todo el inquilino está habilitado para esta característica. Si elige **Llamada de teléfono** como método predeterminado y tiene una extensión especificada como parte del número de teléfono de la oficina (definido por **x**), un operador puede transferir manualmente la llamada telefónica.

Por ejemplo, supongamos que un cliente de Estados Unidos tiene el número de teléfono de oficina 425-555-1234x5678. Cuando la asistencia para operadores está habilitada, el sistema marca 425-555-1234. Una vez contestada, se pide al cliente (también conocido como operador) que transfiera la llamada a la extensión 5678. Una vez transferida y contestada, el sistema vuelve a solicitar MFA normal y espera la aprobación.

Si el valor es **Desactivado**, el sistema marca automáticamente las extensiones como parte del número de teléfono. El administrador puede seguir especificando usuarios individuales que deben habilitarse para la asistencia para operadores anteponiendo "@" a la extensión. Por ejemplo, 425-555-1234x@5678 indicaría que se debe usar la asistencia para operadores, aunque el valor sea **Desactivado**.

Para comprobar el estado de esta característica en su propio inquilino, vaya al [portal de Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade) y, luego, en el panel izquierdo, haga clic en **Seguridad** > **MFA** > **Configuración de la llamada telefónica**. Marque **Operador necesario para transferir extensiones** para ver si el valor es **Activado** o **Desactivado**. 

![Captura de pantalla de la configuración de la asistencia para operadores](./media/concept-authentication-operator-assistance/settings.png)

Puede mejorar la confiabilidad y la seguridad y crear una experiencia de MFA sin problemas mediante las instrucciones siguientes:

- Ha [registrado un número de teléfono directo](https://aka.ms/mfasetup) (no contiene ninguna extensión) u [otro método](concept-authentication-methods.md) para Multi-Factor Authentication o el autoservicio de restablecimiento de contraseña si está habilitado. 
- Los administradores han registrado un número de teléfono directo (no contiene ninguna extensión) en nombre del usuario para [Multi-Factor Authentication](howto-mfa-userdevicesettings.md#add-authentication-methods-for-a-user) o el [autoservicio de restablecimiento de contraseña](tutorial-enable-sspr.md) si está habilitado. 
- El sistema del teléfono admite la funcionalidad del asistente automatizado. 
 
