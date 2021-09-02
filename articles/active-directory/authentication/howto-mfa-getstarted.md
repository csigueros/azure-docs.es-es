---
title: Consideraciones para la implementación de Azure AD Multi-Factor Authentication
description: Obtenga información sobre las consideraciones relativas a la implementación de Azure AD Multi-Factor Authentication y la estrategia para realizarla correctamente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 120e97e62980427fb83c6bf7884da92dd9c5e1f7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602313"
---
# <a name="plan-an-azure-active-directory-multi-factor-authentication-deployment"></a>Plan de una implementación de Azure AD Multi-Factor Authentication 

Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) ayuda a proteger el acceso a datos y aplicaciones, lo que proporciona otra capa de seguridad mediante una segunda forma de autenticación. Las organizaciones pueden habilitar la autenticación multifactor con [acceso condicional](../conditional-access/overview.md) para que la solución se ajuste a sus necesidades concretas.

En esta guía de implementación se muestra cómo planear e implementar [Azure AD MFA](concept-mfa-howitworks.md).

## <a name="prerequisites-for-deploying-azure-ad-mfa"></a>Requisitos previos para implementar Azure AD MFA

Antes de comenzar la implementación, asegúrese de que cumple los siguientes requisitos previos en los escenarios pertinentes.

| Escenario | Requisito previo |
|----------|--------------|
|Entorno de identidades **solo en la nube** con autenticación moderna. | **Sin tareas necesarias previas** |
|Escenarios de **identidad híbrida** | Implemente [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) y sincronice las identidades de usuario entre Active Directory Domain Services (AD DS) en un entorno local y Azure AD. |
| **Aplicaciones heredadas locales** que se publican para el acceso a la nube| Implementación de [Azure AD Application Proxy](../app-proxy/application-proxy-deployment-plan.md) |

## <a name="choose-authentication-methods-for-mfa"></a>Elección de métodos de autenticación para MFA

Hay muchos métodos que se pueden usar para una autenticación de segundo factor. Puede elegir cualquiera de la lista de métodos de autenticación disponibles y evaluar todos ellos en términos de seguridad, facilidad de uso y disponibilidad.

>[!IMPORTANT]
>Habilite más de un método de autenticación multifactor para que los usuarios tengan disponible un método alternativo en caso de que su método principal no esté disponible. Entre los métodos, se incluyen:

- [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-overview)
- [Aplicación Microsoft Authenticator](concept-authentication-authenticator-app.md)
- [Clave de seguridad FIDO2 (versión preliminar)](concept-authentication-passwordless.md#fido2-security-keys)
- [Tokens de hardware OATH (versión preliminar)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
- [Tokens de software OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
- [Verificación por SMS](concept-authentication-phone-options.md#mobile-phone-verification)
- [Verificación por llamada de voz](concept-authentication-phone-options.md)

Al elegir los métodos de autenticación que se van a usar en el inquilino, tenga en cuenta su seguridad y facilidad de uso:

![Elección del método de autenticación adecuado](media/concept-authentication-methods/authentication-methods.png)

Para más información sobre la seguridad y la seguridad de estos métodos, así como su funcionamiento, consulte los siguientes recursos:

- [¿Qué métodos de autenticación y verificación hay disponibles en Azure Active Directory?](concept-authentication-methods.md)
- [Vídeo: Elección de los métodos de autenticación adecuados para mantener segura la organización](https://youtu.be/LB2yj4HSptc)

Este [script de PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) se puede usar para analizar las configuraciones de MFA de los usuarios y sugerir el método de autenticación multifactor adecuado. 

Para que la flexibilidad y facilidad de uso sean las mejores posibles, use la aplicación Microsoft Authenticator. Este método de autenticación ofrece la mejor experiencia de usuario y varios modos, como la opción sin contraseña, las notificaciones push MFA y los códigos OATH. La aplicación Microsoft Authenticator también cumple los [requisitos del nivel 2 de garantía del autenticador](../standards/nist-authenticator-assurance-level-2.md) del Instituto Nacional de Estándares y Tecnología (NIST).

Puede controlar los métodos de autenticación disponibles en su inquilino. Por ejemplo, si lo desea, puede bloquear algunos de los métodos menos seguros, como SMS.

| Método de autenticación | Lugar de administración | Ámbito |
|-----------------------|-------------|---------|
| Microsoft Authenticator (notificación push e inicio de sesión con teléfono sin contraseña)    | Configuración de MFA o
Directiva de métodos de autenticación | El ámbito del inicio de sesión telefónico sin contraseña de Authenticator pueden ser usuarios y grupos |
| Clave de seguridad FIDO2 | Directiva de métodos de autenticación | El ámbito pueden ser usuarios y grupos |
| Tokens OATH de software o hardware | Configuración de MFA |     |
| Verificación por SMS | Configuración de MFA | Administrar el inicio de sesión por SMS para la autenticación principal en la directiva de autenticación. El ámbito del inicio de sesión por SMS pueden ser usuarios y grupos. |
| Llamadas de voz | Directiva de métodos de autenticación |       |


## <a name="plan-conditional-access-policies"></a>Planificación de directivas de acceso condicional

Azure AD Multifactor Authentication se aplica mediante directivas de acceso condicional. Estas directivas permiten solicitar a los usuarios la autenticación multifactor cuando sea necesario por motivos de seguridad y permanecer al margen cuando no se requiera.

![Flujo del proceso de acceso condicional conceptual](media/howto-mfa-getstarted/conditional-access-overview-how-it-works.png)

En Azure Portal, configure las directivas de acceso condicional en **Azure Active Directory** > **Seguridad** > **Acceso condicional**.

Para más información sobre cómo crear directivas de acceso condicional, consulte [Directiva de acceso condicional para solicitar Azure AD MFA cuando un usuario inicia sesión en Azure Portal](tutorial-enable-azure-mfa.md). Esto le ayuda a:

- Familiarizarse con la interfaz de usuario
- Obtener una primera idea de cómo funciona el acceso condicional

Para obtener una guía de un extremo a otro para la implementación del acceso condicional de Azure AD, consulte el [plan de implementación de acceso condicional](../conditional-access/plan-conditional-access.md).

### <a name="common-policies-for-azure-ad-mfa"></a>Directivas comunes para Azure AD MFA

Estos son algunos casos de uso comunes que requieren Azure AD MFA:

- Para [administradores](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- Para [aplicaciones específicas](tutorial-enable-azure-mfa.md)
- Para [todos los usuarios](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- Para la [administración de Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- Desde [ubicaciones de red en las que no se confía](../conditional-access/untrusted-networks.md)

### <a name="named-locations"></a>Ubicaciones con nombre

Para administrar directivas de acceso condicional, la condición de ubicación de una directiva de acceso condicional le permite asociar la configuración de los controles de acceso a las ubicaciones de red de los usuarios. Se recomienda usar [ubicaciones con nombre](../conditional-access/location-condition.md), con el fin de que pueda crear agrupaciones lógicas de rangos de direcciones IP o de países y regiones. De esta forma se crea una directiva para todas las aplicaciones que bloquea el inicio de sesión desde la ubicación con nombre. Asegúrese de excluir a los administradores de esta directiva.

### <a name="risk-based-policies"></a>Directivas basadas en riesgo

Si su organización usa [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) para detectar señales de riesgo, considere la posibilidad de usar [directivas basadas en riesgo](../identity-protection/howto-identity-protection-configure-risk-policies.md) en lugar de ubicaciones con nombre. Las directivas se pueden crear para forzar el cambio de contraseña cuando hay una amenaza de identidad en peligro o para exigir la autenticación multifactor cuando se considera que un inicio de sesión es de [riesgo por los eventos](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation), como por ejemplo que se hayan filtrado credenciales, inicios de sesión desde direcciones IP anónimas, etc. 

Las directivas de riesgo incluyen:

- [Exigir a todos los usuarios que se registren en Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
- [Exigir un cambio de contraseña para los usuarios que son de alto riesgo](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)
- [Exigir MFA para los usuarios con un riesgo de inicio de sesión medio o alto](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertir a los usuarios de MFA por usuario a MFA basado en el acceso condicional

Si los usuarios se han habilitado con Azure AD Multi-Factor Authentication por usuario habilitado y aplicado, el siguiente comando de PowerShell puede ayudarle a realizar la conversión a Azure AD Multi-Factor Authentication basado en acceso condicional.

Ejecútelo en una ventana de ISE o guárdelo como un archivo `.PS1` para ejecutarlo localmente. La operación solo se puede realizar mediante el [módulo MSOnline](/powershell/module/msonline/?view=azureadps-1.0#msonline). 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="plan-user-session-lifetime"></a>Planeamiento de la duración de la sesión de usuario

Al planear la implementación de MFA, es importante pensar en la frecuencia con la que se desea preguntar a los usuarios. La petición de credenciales a los usuarios a menudo parece algo sensato, pero puede tener sus desventajas. Si los usuarios están entrenados para escribir sus credenciales sin necesidad de pensar, pueden proporcionarlas involuntariamente ante una petición de credenciales malintencionada.
Azure AD tiene varios valores que determinan la frecuencia con la que es preciso autenticarse. Sea consciente de las necesidades tanto de la empresa como de los usuarios y realice la configuración que proporcione el mejor equilibrio a su entorno.

Se recomienda usar dispositivos con tokens de actualización principales (PRT) para mejorar la experiencia del usuario final y reducir la duración de la sesión con la directiva de frecuencia de inicio de sesión solo en casos de uso empresarial específicos.

Para más información, consulte [Optimización de los avisos de reautenticación e información sobre la duración de la sesión en Azure Active Directory Multifactor Authentication](concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="plan-user-registration"></a>Planeamiento del registro del usuario

Un paso importante en todas las implementaciones de MFA es que los usuarios se registren para usar MFA. Los métodos de autenticación, como la voz y el SMS, permiten el registro previo, mientras que otros como la aplicación Authenticator requieren la interacción del usuario. Los administradores deben determinar la forma en que los usuarios registrarán sus métodos. 

### <a name="combined-registration-for-sspr-and-azure-ad-mfa"></a>Registro combinado de SSPR y Azure AD MFA
Se recomienda usar la [experiencia de registro combinado](howto-registration-mfa-sspr-combined.md) para Azure AD MFA y el [autoservicio de restablecimiento de contraseña (SSPR)](concept-sspr-howitworks.md). SSPR permite a los usuarios restablecer su contraseña de forma segura con los mismos métodos que usan para Azure AD MFA. El registro combinado es un paso único para los usuarios finales.

### <a name="registration-with-identity-protection"></a>Registro con Identity Protection
Azure AD Identity Protection contribuye con una directiva de registro y con directivas de detección y corrección automatizadas de riesgos al caso de Azure Active Directory Multifactor Authentication. Se pueden crear directivas para forzar los cambios de contraseña cuando hay una amenaza de identidad en peligro o pueden requerir MFA cuando un inicio de sesión se considera de riesgo.
Si usa Azure AD Identity Protection, [configure la directiva de registro de Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) para pedir a los usuarios que se registren la próxima vez que inicien sesión de forma interactiva.

### <a name="registration-without-identity-protection"></a>Registro sin Identity Protection
Si no tiene licencias que habiliten Azure AD Identity Protection, se solicita a los usuarios que se registren la próxima vez que se requiera MFA al iniciar sesión. Para solicitar a los usuarios que utilicen MFA, puede emplear directivas de acceso condicional y dirigirse a aplicaciones de uso frecuente como sistemas de RR. HH. Si la contraseña de un usuario está en peligro, se podría usar para registrarse en MFA, lo que tomaría el control de su cuenta. Por consiguiente, se recomienda [proteger el proceso de registro de seguridad con directivas de acceso condicional](../conditional-access/howto-conditional-access-policy-registration.md) que requieran dispositivos y ubicaciones de confianza. Para proteger aún más el proceso se puede exigir también un [Pase de acceso temporal](howto-authentication-temporary-access-pass.md). Un código de acceso por tiempo limitado que emite un administrador y satisface los requisitos de autenticación sólida y se puede usar para incorporar otros métodos de autenticación, incluidos aquellos que no requieren contraseña.

### <a name="increase-the-security-of-registered-users"></a>Aumento de la seguridad de los usuarios registrados
Si tiene usuarios que se han registrado en MFA mediante SMS o llamadas de voz, es posible que desee moverlos a métodos más seguros, como la aplicación Microsoft Authenticator. Microsoft ahora ofrece una versión preliminar pública de la funcionalidad que permite solicitar a los usuarios que configuren la aplicación Microsoft Authenticator durante el inicio de sesión. Puede establecer estas solicitudes por grupo, controlar a quién se le solicita y habilitar campañas dirigidas para mover a los usuarios al método más seguro. 

### <a name="plan-recovery-scenarios"></a>Planeamiento de escenarios de recuperación 
Como ya se ha indicado, asegúrese de que los usuarios están registrados en más de un método de MFA, con el fin de que, si alguno no está disponible, puedan usar otro. Si el usuario no tiene un método de respaldo disponible, puede hacer lo siguiente: 

- Proporciónele un Pase de acceso temporal para que pueda administrar sus propios métodos de autenticación. También puede proporcionarle un Pase de acceso temporal para habilitar el acceso temporal a los recursos. 
- Actualice sus métodos como administrador. Para ello, seleccione el usuario en Azure Portal, seleccione Métodos de autenticación y actualice sus métodos.
Comunicaciones de los usuarios

Es fundamental notificar a los usuarios los próximos cambios, los requisitos de registro de Azure AD MFA y todas aquellas acciones que deben realizar. Microsoft proporciona [plantillas de comunicación](https://aka.ms/mfatemplates) y [documentación de usuario final](../user-help/security-info-setup-signin.md) que le ayudan a crear borradores de sus comunicaciones. Envíe a los usuarios a [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) para que se registren, para lo que deben seleccionar el vínculo **Información de seguridad** en esa página.

## <a name="plan-integration-with-on-premises-systems"></a>Planificación de la integración con sistemas locales

Las aplicaciones que se autentican directamente con Azure AD y tienen autenticación moderna (WS-Fed, SAML, OAuth, OpenID Connect) pueden hacer uso de las directivas de acceso condicional.
Algunas aplicaciones heredadas y locales no se autentican directamente en Azure AD, requieren pasos adicionales para usar Azure AD MFA. Para integrarlas, utilice el proxy de aplicación de Azure AD o los [servicios de directiva de red](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures).

### <a name="integrate-with-ad-fs-resources"></a>Integración con los recursos de AD FS

Se recomienda migrar las aplicaciones protegidas con Servicios de federación de Active Directory (AD FS) a Azure AD. Sin embargo, si no está listo para migrarlas a Azure AD, puede usar el adaptador de Azure MFA con AD FS 2016, o cualquier versión posterior.
Si la organización está federada con Azure AD, puede configurar [Azure AD MFA como proveedor de autenticación con recursos de AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa) tanto en entornos locales como en la nube.  

### <a name="radius-clients-and-azure-ad-mfa"></a>Clientes RADIUS y Azure AD MFA

En el caso de las aplicaciones que usan la autenticación RADIUS, se recomienda mover las aplicaciones cliente a protocolos modernos como SAML, Open ID Connect u OAuth en Azure AD. Si la aplicación no se puede actualizar, puede implementar el [servidor de directivas de red (NPS) con la extensión de Azure MFA](howto-mfa-nps-extension.md). La extensión del servidor de directivas de red (NPS) actúa como adaptador entre las aplicaciones basadas en RADIUS y Azure AD MFA para proporcionar un segundo factor de autenticación.

#### <a name="common-integrations"></a>Integraciones comunes

Muchos proveedores ahora admiten la autenticación SAML para sus aplicaciones. Cuando sea posible, se recomienda federar estas aplicaciones con Azure AD y aplicar MFA a través del acceso condicional. Si el proveedor no admite la autenticación moderna, puede usar la extensión NPS.
Las integraciones comunes del cliente RADIUS incluyen aplicaciones como [puertas de enlace de Escritorio remoto](howto-mfa-nps-extension-rdg.md) y [servidores VPN](howto-mfa-nps-extension-vpn.md). 

Otras pueden incluir:

- Puerta de enlace de Citrix

  La [puerta de enlace de Citrix](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-gateway-microsoft-azure.html#microsoft-azure-mfa-deployment-methods) admite la integración tanto de RADIUS como de la extensión NPS, así como una integración de SAML.

- Cisco VPN
  - Cisco VPN admite la [autenticación tanto de RADIUS como de SAML para SSO](../saas-apps/cisco-anyconnect.md).
  - Al pasar de la autenticación de RADIUS a SAML, puede integrar Cisco VPN sin tener que implementar la extensión NPS.

- Todas las VPN

## <a name="deploy-azure-ad-mfa"></a>Implementación de Azure AD MFA

El plan de lanzamiento de MFA debe incluir un lanzamiento piloto seguido de fases de implementación dentro de su capacidad admitida. Comience el lanzamiento al aplicar las directivas de acceso condicional a un grupo reducido de usuarios piloto. Después de evaluar el efecto en los usuarios piloto, el proceso que se utiliza y los comportamientos de registro, puede agregar más grupos a la directiva o agregar más usuarios a los grupos existentes.

Siga estos pasos:

1. Cumpla los requisitos previos necesarios.
1. Configure los métodos de autenticación que eligió.
1. Configure las directivas de acceso condicional.
1. Configure la duración de la sesión.
1. Configure las directivas de registro de Azure AD MFA 

## <a name="manage-azure-ad-mfa"></a>Administración de Azure AD MFA
En esta sección se proporciona información de informes y solución de problemas para Azure AD MFA.

### <a name="reporting-and-monitoring"></a>Creación de informes y supervisión

Azure AD tiene informes que proporcionan información técnica y empresarial; siga el progreso de la implementación y compruebe si los usuarios han realizado correctamente el inicio de sesión con MFA. Haga que los propietarios de las aplicaciones empresariales y técnicas asuman la propiedad de estos informes y los consuman en función de los requisitos de su organización.

Desde el [panel de actividad del método de autenticación](howto-authentication-methods-activity.md) se puede supervisar el uso y registro del método de autenticación en toda la organización, lo que ayuda a saber qué métodos se registran y cómo se utilizan.

#### <a name="sign-in-report-to-review-mfa-events"></a>Informe de inicio de sesión para revisar eventos de MFA

Los informas de inicio de sesión de Azure AD incluyen detalles de autenticación de los eventos cuando se solicita a un usuario la autenticación multifactor y si había alguna directiva de acceso condicional en uso. También se puede usar PowerShell para informar sobre los usuarios registrados para MFA. 

Tanto la extensión NPS como los registros de AD FS se pueden ver desde **Seguridad** > **MFA** > **Informe de actividad**.

Para más información e informes adicionales de MFA, consulte el artículo en el que se indica cómo [examinar los eventos de Azure AD Multi-Factor Authentication](howto-mfa-reporting.md#view-the-azure-ad-sign-ins-report).

### <a name="troubleshoot-azure-ad-mfa"></a>Solución de problemas de Azure AD MFA
Para saber cuáles son los problemas más comunes, consulte [Solución de problemas de Azure AD MFA](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues).

## <a name="next-steps"></a>Pasos siguientes

[Implementación de otras características de identidad](../fundamentals/active-directory-deployment-plans.md)
