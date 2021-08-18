---
title: Corrección de riesgos y desbloqueo de usuarios en Azure AD Identity Protection
description: Obtenga información sobre las opciones que tiene para cerrar detecciones de riesgo activas.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc03e619903ae3fb4065dfa1d7f1478c8e08942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727646"
---
# <a name="remediate-risks-and-unblock-users"></a>Corregir riesgos y desbloquear usuarios

Después de completar la [investigación](howto-identity-protection-investigate-risk.md), deberá tomar medidas para corregir el riesgo o desbloquear usuarios. Las organizaciones también tienen la opción de habilitar la corrección automática mediante las [directivas de riesgo](howto-identity-protection-configure-risk-policies.md). Las organizaciones deben intentar cerrar todas las detecciones de riesgo que se presenten en un período de tiempo con el que la organización se sienta cómoda. Microsoft recomienda terminar los eventos en cuanto sea posible, ya que el tiempo es importante cuando se trabaja con riesgos.

## <a name="remediation"></a>Corrección

Todas las detecciones de riesgo activas contribuyen al cálculo de un valor llamado nivel de riesgo del usuario. El nivel de riesgo del usuario es un indicador (bajo, medio, alto) de la probabilidad de que se haya puesto en peligro una cuenta. Como administrador, querrá tener todas las detecciones de riesgo cerradas para que los usuarios afectados ya no estén en riesgo.

Es posible que la protección de identidades marque algunas detecciones de riesgos como "Cerrada (sistema)" porque los eventos ya no se consideran peligrosos.

Los administradores tienen las siguientes opciones para la corrección:

- Corrección automática con directiva de riesgo
- Restablecimiento manual de contraseña
- Descartar el riesgo del usuario
- Cierre manual de las detecciones de riesgo individuales

### <a name="self-remediation-with-risk-policy"></a>Corrección automática con directiva de riesgo

Si permite que los usuarios se corrijan automáticamente, con Azure AD Multi-Factor Authentication (MFA) y el autoservicio de restablecimiento de contraseña (SSPR) en las directivas de riesgo, pueden desbloquearse a sí mismos cuando se detecte riesgo. Estas detecciones se considerarán cerradas. Los usuarios deben haberse registrado previamente en Azure AD MFA y SSPR para usarlos cuando se detecte el riesgo.

Es posible que algunas detecciones no produzcan riesgos en el nivel en el que sería necesaria una corrección automática del usuario, pero los administradores deberían evaluar estas detecciones igualmente. Los administradores pueden determinar que son necesarias medidas adicionales, como [bloquear el acceso desde ubicaciones](../conditional-access/howto-conditional-access-policy-location.md) o reducir el riesgo aceptable en sus directivas.

### <a name="manual-password-reset"></a>Restablecimiento manual de contraseña

Si el requisito de un restablecimiento de contraseña mediante una directiva de riesgo de usuario no es una opción, puede cerrar todas las detecciones de riesgo de un usuario con un restablecimiento de contraseña manual.

A los administradores se les proporcionan dos opciones al restablecer una contraseña para los usuarios:

- **Generar una contraseña temporal**: mediante la generación de una contraseña temporal, puede retornar inmediatamente una identidad a un estado seguro. Este método requiere ponerse en contacto con los usuarios afectados porque tienen que saber cuál es la contraseña temporal. Dado que la contraseña es temporal, se pedirá al usuario que cambie la contraseña por otra nueva en el inicio de sesión siguiente.

- **Requerir que el usuario restablezca la contraseña**: requerir que los usuarios restablezcan las contraseñas permite la propia recuperación sin ponerse en contacto con el departamento de soporte técnico o un administrador. Este método solo se aplica a los usuarios que están registrados para Azure AD MFA y SSPR. Para los usuarios que aún no se han registrado, esta opción no está disponible.

### <a name="dismiss-user-risk"></a>Descartar el riesgo del usuario

Si un restablecimiento de contraseña no es una opción, ya que, por ejemplo, se ha eliminado el usuario, puede optar por descartar las detecciones de riesgo de usuario.

Al hacer clic en **Descartar todos los eventos**, se cierran todos los eventos y el usuario afectado deja de estar en riesgo. Sin embargo, dado que este método no tiene impacto en la contraseña existente, no retorna la identidad relacionada a un estado seguro. 

### <a name="close-individual-risk-detections-manually"></a>Cierre manual de las detecciones de riesgo individuales

Puede cerrar de forma manual las detecciones de riesgo individuales. Al cerrar manualmente las detecciones de riesgo, puede reducir el nivel de riesgo del usuario. Normalmente, las detecciones de riesgo se cierran manualmente en respuesta a una investigación relacionada. Por ejemplo, cuando al hablar con un usuario se revela que una detección de riesgo activa ya no es necesaria. 
 
Al cerrar manualmente las detecciones de riesgo, puede elegir realizar cualquiera de las siguientes acciones para cambiar el estado de una detección de riesgo:

- Confirmar vulneración de la identidad del usuario
- Descartar el riesgo del usuario
- Confirmar la seguridad del inicio de sesión
- Confirmar vulneración de inicio de sesión

## <a name="unblocking-users"></a>Desbloqueo de usuarios

Un administrador puede optar por bloquear un inicio de sesión en función de su directiva de riesgo o investigaciones. Puede producirse un bloqueo en función de un riesgo de inicio de sesión o de usuario.

### <a name="unblocking-based-on-user-risk"></a>Desbloqueo en función del riesgo de usuario

Para desbloquear una cuenta bloqueada debido al riesgo de usuario, los administradores tienen las siguientes opciones:

1. **Restablecer contraseña** : puede restablecer la contraseña del usuario.
1. **Descartar el riesgo del usuario**: la directiva de riesgo de usuario bloquea un usuario si se ha alcanzado el nivel de riesgo del usuario configurado para bloquear el acceso. Para reducir el nivel de riesgo de un usuario, descarte el riesgo de usuario o cierre manualmente las detecciones de riesgo notificadas.
1. **Exclude the user from policy** (Excluir al usuario de la directiva): si piensa que la configuración actual de la directiva de inicio de sesión provoca problemas a usuarios concretos, puede excluir los usuarios de ella. Para más información, consulte la sección Exclusiones en el artículo [Procedimientos: Configuración y habilitación de directivas de riesgo](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Deshabilitar directiva** : si piensa que la configuración de la directiva provoca problemas a todos los usuarios, puede deshabilitar la directiva. Para más información, consulte el artículo [Procedimientos: Configuración y habilitación de directivas de riesgo](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Desbloqueo en función del riesgo de inicio de sesión

Para desbloquear una cuenta en función del riesgo de inicio de sesión, los administradores tienen las siguientes opciones:

1. **Iniciar sesión desde una ubicación o dispositivo conocidos**: una razón común para el bloqueo de inicios de sesión sospechosos es que se intente iniciar sesión desde ubicaciones o dispositivos desconocidos. Los usuarios pueden determinar rápidamente si esta es la razón del bloqueo. Para ello, deben intentar iniciar sesión desde una ubicación o dispositivo conocidos.
1. **Exclude the user from policy** (Excluir al usuario de la directiva): si piensa que la configuración actual de la directiva de inicio de sesión provoca problemas a usuarios concretos, puede excluir los usuarios de ella. Para más información, consulte la sección Exclusiones en el artículo [Procedimientos: Configuración y habilitación de directivas de riesgo](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Deshabilitar directiva** : si piensa que la configuración de la directiva provoca problemas a todos los usuarios, puede deshabilitar la directiva. Para más información, consulte el artículo [Procedimientos: Configuración y habilitación de directivas de riesgo](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>Versión preliminar de PowerShell

Con el módulo de versión preliminar del SDK de PowerShell de Microsoft Graph, las organizaciones pueden administrar el riesgo que conlleva el uso de PowerShell. Los módulos de versión preliminar y el código de ejemplo se pueden encontrar en el [repositorio de GitHub de Azure AD](https://github.com/AzureAD/IdentityProtectionTools). 

El script de `Invoke-AzureADIPDismissRiskyUser.ps1` incluido en el repositorio permite a las organizaciones descartar todos los usuarios de riesgo en su directorio.

## <a name="next-steps"></a>Pasos siguientes

Para obtener una visión general de Azure AD Identity Protection, consulte la [Introducción a Azure AD Identity Protection](overview-identity-protection.md).
