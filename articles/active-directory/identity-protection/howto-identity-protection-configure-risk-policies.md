---
title: 'Directivas de riesgo: Azure Active Directory Identity Protection'
description: Habilitación y configuración de las directivas de riesgo en Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 05/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11751323d1341cbcde19451bc101197c7d714368
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739978"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Instrucciones: Configuración y habilitación de directivas de riesgo

Como vimos en el artículo anterior, [Directivas de Identity Protection](concept-identity-protection-policies.md), tenemos dos directivas de riesgo que podemos habilitar en nuestro directorio. 

- Directiva de riesgo de inicio de sesión
- Directiva de riesgo de usuario

![Página de información general de seguridad para habilitar las directivas de riesgo de usuario e inicio de sesión](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ambas directivas se ocupan de automatizar la respuesta a las detecciones de riesgo en su entorno y permitir que los usuarios corrijan por sí mismos los posibles riesgos cuando estos se detecten. 

## <a name="choosing-acceptable-risk-levels"></a>Elección de niveles de riesgo aceptables

Las organizaciones deben decidir el nivel de riesgo que están dispuestos a aceptar para equilibrar la experiencia del usuario y la postura de seguridad. 

La recomendación de Microsoft es establecer el umbral de la directiva de riesgo de usuario en **Alto**, la directiva de riesgo de inicio de sesión en **Medio y superior** y permitir opciones de autocorrección. La elección de bloquear el acceso en lugar de permitir opciones de autocorrección, como el cambio de contraseña y la autenticación multifactor, afectará a los usuarios y los administradores. Sopese esta elección al configurar las directivas.

Elegir un umbral **Alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios. Sin embargo, excluye de la directiva las detecciones de riesgo **Bajo** y **Medio**, por lo que es posible que no impida que un atacante aproveche una identidad en peligro. Al seleccionar un umbral **Bajo** se presentan más interrupciones de usuario.

Identity Protection usa las [ubicaciones de red](../conditional-access/location-condition.md) de confianza configuradas en algunas detecciones de riesgos para reducir los falsos positivos.

### <a name="risk-remediation"></a>Corrección de riesgos

Las organizaciones pueden optar por bloquear el acceso cuando se detecta riesgo. El bloqueo a veces impide que los usuarios legítimos haga lo que necesitan. Una solución mejor es permitir la autocorrección mediante la autenticación multifactor (MFA) de Azure AD y el autoservicio de restablecimiento de contraseña (SSPR). 

- Cuando se desencadena una directiva de riesgo de usuario: 
   - Los administradores pueden requerir un restablecimiento de contraseña seguro, que requiere que Azure AD MFA se realice antes de que el usuario cree una nueva contraseña con SSPR y restablezca el riesgo del usuario. 
- Cuando se desencadena una directiva de riesgo de inicio de sesión: 
   - Se puede desencadenar Azure AD MFA, lo que permite a los usuarios probar que se trata de ellos mediante uno de los métodos de autenticación registrados y restablecer el riesgo de inicio de sesión. 

> [!WARNING]
> Los usuarios deben registrarse en Azure AD MFA y SSPR antes de enfrentarse a una situación que requiera corrección. Los usuarios no registrados se bloquean y requieren la intervención del administrador.
> 
> El cambio de contraseña (conozco mi contraseña y quiero cambiarla por otra) fuera del flujo de corrección de las directivas de riesgo de usuario no cumple el requisito de restablecimiento de contraseña seguro.

## <a name="exclusions"></a>Exclusiones

Las directivas permiten excluir a usuarios, como las [cuentas de acceso de emergencia o de administrador de emergencia](../roles/security-emergency-access.md). Las organizaciones pueden necesitar excluir otras cuentas de algunas directivas específicas en función de la forma en que se usan las cuentas. Las exclusiones deben revisarse periódicamente para ver si siguen siendo aplicables.

## <a name="enable-policies"></a>Habilitación de directivas

Hay dos ubicaciones donde se pueden configurar estas directivas: acceso condicional e Identity Protection. La configuración mediante directivas de acceso condicional es el método preferido y proporciona más contexto, que incluye: 

   - Datos de diagnóstico mejorados
   - Integración del modo de solo informe
   - Compatibilidad con Graph API
   - Uso de más atributos de acceso condicional en la directiva

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

### <a name="user-risk-with-conditional-access"></a>Riesgos del usuario con acceso condicional

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios**.
   1. En **Excluir**, seleccione **Usuarios y grupos** y, luego, elija las cuentas de acceso de emergencia de la organización. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Todas las aplicaciones en la nube**.
1. En **Condiciones** > **Riesgo de usuario**, establezca **Configurar** en **Sí**. En **Configurar los niveles de riesgo de usuario necesarios para que se aplique la directiva**, seleccione **Alto** y después, **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir cambio de contraseña** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directivas** en **Activado**.
1. Seleccione **Crear** para crear la directiva.

### <a name="sign-in-risk-with-conditional-access"></a>Riesgos del inicio de sesión con acceso condicional

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios**.
   1. En **Excluir**, seleccione **Usuarios y grupos** y, luego, elija las cuentas de acceso de emergencia de la organización. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Todas las aplicaciones en la nube**.
1. En **Condiciones** > **Riesgo de inicio de sesión**, establezca **Configurar** en **Sí**. En **Seleccionar el nivel de riesgo de inicio de sesión, esta directiva se aplicará a** 
   1. Seleccione **Alto** y **Medio**.
   1. Seleccione **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir autenticación multifactor** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear la directiva.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de la directiva de registro de Azure AD Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [¿Qué es el riesgo?](concept-identity-protection-risks.md)

- [Investigación de detecciones de riesgos](howto-identity-protection-investigate-risk.md)

- [Simulación de detecciones de riesgos](howto-identity-protection-simulate-risk.md)
