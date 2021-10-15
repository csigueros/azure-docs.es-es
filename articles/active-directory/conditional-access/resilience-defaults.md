---
title: Valores predeterminados de resistencia para el acceso condicional de Azure AD
description: Valores predeterminados de resistencia y servicio de autenticación de copia de seguridad de Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9292ad6e167e62b27fa3b646a1b60ba7176ff87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367590"
---
# <a name="conditional-access-resilience-defaults"></a>Acceso condicional: valores predeterminados de resistencia

Si se ha producido una interrupción del servicio de autenticación principal, el servicio de autenticación de copia de seguridad Azure Active Directory (Azure AD) puede emitir automáticamente tokens de acceso a las aplicaciones para las sesiones existentes. Esta funcionalidad puede aumentar considerablemente la resistencia de Azure AD, ya que las reautenticaciones de las sesiones existentes representan más del 90 % de las autenticaciones para Azure AD. El servicio de autenticación de copia de seguridad no admite nuevas sesiones ni autenticaciones por parte de los usuarios invitados.

En el caso de las autenticaciones protegidas por el acceso condicional, las directivas se revalúan antes de que se emitan tokens de acceso para determinar:

1.  Qué directivas de acceso condicional se aplican.
1.  En el caso de las directivas que se aplican, si se cumplen los controles necesarios.

Durante una interrupción, el servicio de autenticación de copia de seguridad no puede evaluar todas las condiciones en tiempo real para determinar si se debe aplicar una directiva de acceso condicional. Los valores predeterminados de resistencia de acceso condicional son un nuevo control de sesión que permite a los administradores decidir si bloquear las autenticaciones durante una interrupción siempre que una condición de directiva no se pueda evaluar en tiempo real o permitir que las directivas se evalúen mediante los datos recopilados al principio de la sesión del usuario. 

> [!IMPORTANT]
> Los valores predeterminados de resistencia se habilitan automáticamente para todas las directivas nuevas y existentes, y Microsoft recomienda encarecidamente dejar habilitados los valores predeterminados de resistencia para mitigar el impacto de una interrupción. Los administradores pueden deshabilitar los valores predeterminados de resistencia para las directivas de acceso condicional individuales. 

## <a name="how-does-it-work"></a>¿Cómo funciona?

Durante una interrupción, el servicio de autenticación de copia de seguridad volverá a emitir automáticamente los tokens de acceso para determinadas sesiones:

| Descripción de la sesión | El acceso se le concede |
| --- | --- |
| Nueva sesión | No |
| Sesión existente: ninguna directiva de acceso condicional configurada | Yes |
| Sesión existente: las directivas de acceso condicional configuradas y los controles necesarios, como MFA, se satisficieron previamente | Yes |
| Sesión existente: las directivas de acceso condicional configuradas y los controles necesarios, como MFA, no se satisficieron previamente | Determinado por los valores predeterminados de resistencia |

Cuando una sesión existente expira durante una interrupción de Azure AD, la solicitud de un nuevo token de acceso se enruta al servicio de autenticación de copia de seguridad y se revalúan todas las directivas de acceso condicional. Si no hay directivas de acceso condicional o todos los controles necesarios, como MFA, se satisficieron previamente al principio de la sesión, el servicio de autenticación de copia de seguridad emite un nuevo token de acceso para ampliar la sesión. 

Si los controles necesarios de una directiva no se satisficieron previamente, la directiva se vuelve a evaluar para determinar si se debe conceder o denegar el acceso. Sin embargo, no todas las condiciones se pueden volver a evaluar en tiempo real durante una interrupción. Entre estas condiciones se incluyen: 

- Pertenencia a grupos
- Pertenencia a roles
- Riesgo de inicio de sesión
- Riesgo de usuario
- Ubicación del país (resolución de nuevas coordenadas IP o GPS)

## <a name="resilience-defaults-enabled"></a>Valores predeterminados de resistencia habilitados

Cuando se habilitan los valores predeterminados de resistencia, el servicio de autenticación de copia de seguridad puede usar los datos recopilados al principio de la sesión para evaluar si la directiva debe aplicarse en ausencia de datos en tiempo real. De manera predeterminada, todas las directivas tendrán habilitados los valores predeterminados de resistencia. La configuración puede deshabilitarse para las directivas individuales cuando se requiere la evaluación de directivas en tiempo real para el acceso a aplicaciones confidenciales durante una interrupción.

**Ejemplo**: una directiva con los valores predeterminados de resistencia habilitados requiere que todos los administradores globales accedan a Azure Portal para realizar MFA. Antes de una interrupción, si un usuario que no es un administrador global accede a Azure Portal, la directiva no se aplicaría y se concedería acceso al usuario sin que se le solicitara MFA. Durante una interrupción, el servicio de autenticación de copia de seguridad revaluaría la directiva para determinar si se debe solicitar MFA al usuario. **Dado que el servicio de autenticación de copia de seguridad no puede evaluar la pertenencia a roles en tiempo real, usaría los datos recopilados al principio de la sesión del usuario para determinar que la directiva todavía no se debe aplicar. Como resultado, se concedería acceso al usuario sin que se le solicitara MFA.**

## <a name="resilience-defaults-disabled"></a>Valores predeterminados de resistencia deshabilitados

Cuando se deshabilitan los valores predeterminados de resistencia, el servicio de autenticación de copia de seguridad no usará los datos recopilados al principio de la sesión para evaluar las condiciones. Durante una interrupción, si no se puede evaluar una condición de directiva en tiempo real, se denegará el acceso.

**Ejemplo**: una directiva con los valores predeterminados de resistencia deshabilitados requiere que todos los administradores globales accedan a Azure Portal para realizar MFA. Antes de una interrupción, si un usuario que no es un administrador global accede a Azure Portal, la directiva no se aplicaría y se concedería acceso al usuario sin que se le solicitara MFA. Durante una interrupción, el servicio de autenticación de copia de seguridad revaluaría la directiva para determinar si se debe solicitar MFA al usuario. **Puesto que el servicio de autenticación de copia de seguridad no puede evaluar la pertenencia a roles en tiempo real, impediría que el usuario acceda a Azure Portal.**

> [!WARNING]
> Deshabilitar los valores predeterminados de resistencia para una directiva que se aplica a un grupo o rol reducirá la resistencia de todos los usuarios del inquilino. Puesto que la pertenencia a grupos y roles no se puede evaluar en tiempo real durante una interrupción, incluso a los usuarios que no pertenezcan al grupo o rol en la asignación de directiva se les denegará el acceso a la aplicación en el ámbito de la directiva. Para evitar reducir la resistencia de todos los usuarios que no están en el ámbito de la directiva, considere la posibilidad de aplicar la directiva a usuarios individuales en lugar de grupos o roles. 

## <a name="testing-resilience-defaults"></a>Prueba de los valores predeterminados de resistencia

No es posible realizar un simulacro mediante el servicio de autenticación de copia de seguridad ni simular el resultado de una directiva con los valores predeterminados de resistencia habilitados o deshabilitados en este momento. Azure AD realizará ejercicios mensuales mediante el servicio de autenticación de copia de seguridad y los registros de inicio de sesión se mostrarán si se usó el servicio de autenticación de copia de seguridad para emitir el token de acceso.

## <a name="configuring-resilience-defaults"></a>Configuración de valores predeterminados de resistencia

Puede configurar los valores predeterminados de resistencia de acceso condicional desde Azure Portal, MS Graph API o PowerShell. 

### <a name="azure-portal"></a>Azure Portal

1.  Vaya a **Azure Portal** > **Seguridad** > **Acceso condicional**
1.  Seleccione una directiva existente o cree una nueva.
1.  Abra la configuración del control de sesión.
1.  Seleccione Deshabilitar valores predeterminados de resistencia para deshabilitar la configuración de esta directiva. Los inicios de sesión en el ámbito de la directiva se bloquearán durante una interrupción de Azure AD.
1.  Guarde los cambios realizados en la directiva.

### <a name="ms-graph-apis"></a>MS Graph API

También puede administrar los valores predeterminados de resistencia para las directivas de acceso condicional mediante MS Graph API y el [Explorador de Microsoft Graph](/graph/graph-explorer). 

Dirección URL de solicitud de ejemplo: 

`PATCH https://graph.microsoft.com/beta/identity/conditionalAccess/policies/policyId`

Cuerpo de solicitud de ejemplo: 

```json

{
"sessionControls": {
"disableResilienceDefaults": true
}
}
```

### <a name="powershell"></a>PowerShell

Esta operación de revisión puede implementarse mediante Microsoft PowerShell después de la instalación del módulo Microsoft.Graph.Authentication. Para instalar este módulo, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute

`Install-Module Microsoft.Graph.Authentication`

Conéctese a Microsoft Graph, solicitando los ámbitos necesarios:

`Connect-MgGraph -Scopes Policy.Read.All,Policy.ReadWrite.ConditionalAccess,Application.Read.All -TenantId <TenantID>`

Autentíquese cuando se le solicite.

Cree el cuerpo JSON para la solicitud PATCH:

`$patchBody = '{"sessionControls": {"disableResilienceDefaults": true}}'`

Ejecute la operación de revisión:

`Invoke-MgGraphRequest -Method PATCH -Uri https://graph.microsoft.com/beta/identity/conditionalAccess/policies/<PolicyID> -Body $patchBody`

## <a name="recommendations"></a>Recomendaciones

Microsoft recomienda habilitar los valores predeterminados de resistencia. Aunque no hay problemas de seguridad directos, los clientes deben evaluar si quieren permitir que el servicio de autenticación de copia de seguridad evalúe las directivas de acceso condicional durante una interrupción mediante los datos recopilados al principio de la sesión en lugar de en tiempo real. 

Es posible que la pertenencia a un rol o grupo de un usuario haya cambiado desde el principio de la sesión. Con [Evaluación continua de acceso (CAE)](concept-continuous-access-evaluation.md), los tokens de acceso son válidos durante 24 horas, pero están sujetos a eventos de revocación instantáneos. El servicio de autenticación de copia de seguridad se suscribe a la CAE de los mismos eventos de revocación. Si se revoca el token de un usuario como parte de CAE, el usuario no puede iniciar sesión durante una interrupción. Cuando se habilitan los valores predeterminados de resistencia, se extenderán las sesiones existentes que expiren durante una interrupción. Las sesiones se extienden incluso si la directiva se configuró con un control de sesión para aplicar una frecuencia de inicio de sesión. Por ejemplo, una directiva con los valores predeterminados de resistencia habilitados puede requerir que los usuarios vuelvan a autenticarse cada hora para acceder a un sitio de SharePoint. Durante una interrupción, la sesión del usuario se extendería aunque Azure AD no esté disponible para volver a autenticar al usuario. 

## <a name="next-steps"></a>Pasos siguientes

- [Evaluación continua de acceso (CAE)](concept-continuous-access-evaluation.md)
