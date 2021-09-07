---
title: 'Autenticación de Azure Virtual Desktop: Azure'
description: Métodos de autenticación de Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c987ad26d28a6c96d187c420b32bd3103391c62d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228763"
---
# <a name="supported-authentication-methods"></a>Métodos de autenticación admitidos

En este artículo, le mostraremos una breve descripción de los tipos de autenticación que puede usar en Azure Virtual Desktop.

## <a name="identities"></a>Identities

Azure Virtual Desktop admite diferentes tipos de identidades en función de la configuración que elija. En esta sección se explican las identidades que puede usar para cada configuración.

### <a name="on-premise-identity"></a>Identidad local

Dado que los usuarios deben ser reconocibles a través de Azure Active Directory (Azure AD) para acceder a Azure Virtual Desktop, no se admiten las identidades de usuario que solo existen en los servicios de dominio de Active Directory (AD DS). Esto incluye implementaciones Active Directory independientes con Servicios de federación de Active Directory (AD FS).

### <a name="hybrid-identity"></a>Identidad híbrida

Azure Virtual Desktop admite [identidades híbridas](../active-directory/hybrid/whatis-hybrid-identity.md) a través de Azure AD, incluidas las federadas mediante AD FS. Puede administrar estas identidades de usuario en AD DS y sincronizarlas con Azure AD mediante [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md). También puede usar Azure AD para administrar estas identidades y sincronizarlas con [Azure AD Directory Services (Azure AD DS)](../active-directory-domain-services/overview.md).

Al acceder a Azure Virtual Desktop mediante identidades híbridas, a veces el nombre principal de usuario (UPN) o el identificador de seguridad (SID) del usuario en Active Directory (AD) y Azure AD no coinciden. Por ejemplo, la cuenta AD user@contoso.localpuede corresponder a user@contoso.com en Azure AD. Azure Virtual Desktop solo admite este tipo de configuración si el UPN o el SID de sus cuentas AD y Azure AD coinciden. SID hace referencia a la propiedad de objeto de usuario "ObjectSID" en AD y "OnPremisesSecurityIdentifier" en Azure AD.

### <a name="cloud-only-identity"></a>Identidad solo en la nube

Azure Virtual Desktop admite identidades solo en la nube cuando se usan [máquinas virtuales unidas a Azure AD](deploy-azure-ad-joined-vm.md).

### <a name="external-identity"></a>Identidad externa

Actualmente, Azure Virtual Desktop no admite [identidades externas](../active-directory/external-identities/index.yml).

## <a name="service-authentication"></a>Autenticación del servicio

Para acceder a los recursos de Azure Virtual Desktop, primero debe autenticarse en el servicio iniciando sesión en una cuenta Azure AD. La autenticación se produce al suscribirse a un área de trabajo para recuperar los recursos o cada vez que se conecta a aplicaciones o escritorios. Puede utilizar [proveedores de identidad de terceros](../active-directory/devices/azureadjoin-plan.md#federated-environment) siempre que se federen con Azure AD.

### <a name="multifactor-authentication"></a>Autenticación multifactor

Siga las instrucciones de [Configuración de la autenticación multifactor en Azure Virtual Desktop](set-up-mfa.md) para aprender a habilitar la autenticación multifactor (MFA) para la implementación. En ese artículo también se le indica cómo configurar la frecuencia con la que se solicita a los usuarios que escriban sus credenciales. Al implementar máquinas virtuales unidas a Azure AD, siga la guía de configuración en [Enabling MFA for Azure AD-joined VMs](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) (Habilitación de MFA para máquinas virtuales unidas a Azure AD).

### <a name="smart-card-authentication"></a>Autenticación con tarjeta inteligente

Para usar una tarjeta inteligente para autenticarse en Azure AD, primero debe [configurar AD FS para la autenticación con certificado de usuario](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication).

## <a name="session-host-authentication"></a>Autenticación de host de sesión

Si aún no ha habilitado el [inicio de sesión único](#single-sign-on-sso) o ha guardado sus credenciales localmente, también deberá autenticarse en el host de sesión. Estos son los métodos de inicio de sesión para el host de sesión que los clientes de Azure Virtual Desktop admiten actualmente:

- Cliente de escritorio de Windows
    - Nombre de usuario y contraseña
    - Tarjeta inteligente
    - [Confianza de certificados de Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)
    - [Clave de confianza con certificados de Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)
- Cliente de Microsoft Store
    - Nombre de usuario y contraseña
- Cliente web
    - Nombre de usuario y contraseña
- Android
    - Nombre de usuario y contraseña
- iOS
    - Nombre de usuario y contraseña
- macOS
    - Nombre de usuario y contraseña

Azure Virtual Desktop admite NT LAN Manager (NTLM) y Kerberos para la autenticación del host de sesión. La tarjeta inteligente y Windows Hello para empresas solo pueden usar Kerberos para iniciar sesión. Sin embargo, para utilizar Kerberos, el cliente debe obtener vales de seguridad de Kerberos de un servicio de Centro de distribución de claves (KDC) que se ejecute en un controlador de dominio. Para obtener vales, el cliente necesita una línea de visión de red directa al controlador de dominio. Puede obtener una línea de visión si se conecta directamente desde su red corporativa, mediante una conexión VPN o configurando un [servidor proxy KDC](key-distribution-center-proxy.md).

### <a name="single-sign-on-sso"></a>Inicio de sesión único (SSO)

Azure Virtual Desktop admite una [inicio de sesión único mediante Servicios de federación de Active Directory (AD FS)](configure-adfs-sso.md) para Windows y clientes web. SSO le permite omitir la autenticación del host de sesión.

De lo contrario, la única manera de evitar que le pidan sus credenciales del host de sesión es guardarlas en el cliente. Se recomienda hacer esto solo con dispositivos seguros a fin de evitar que otros usuarios tengan acceso a los recursos.

## <a name="in-session-authentication"></a>Autenticación en sesión

Una vez que esté conectado a la aplicación o al escritorio remoto, es posible que se le pida autenticación dentro de la sesión. En esta sección se explica cómo usar credenciales que no sean nombre de usuario y contraseña en este escenario.

### <a name="smart-cards"></a>Tarjetas inteligentes

Para usar una tarjeta inteligente en la sesión, asegúrese de haber instalado los controladores de tarjeta inteligente en el host de sesión y de haber habilitado la [redirección de tarjeta inteligente](configure-device-redirections.md#smart-card-redirection). Revise la [gráfica de comparación de clientes](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare#other-redirection-devices-etc) para asegurarse de que su cliente admite la redirección de tarjeta inteligente.

### <a name="fido2-and-windows-hello-for-business"></a>FIDO2 y Windows Hello para empresas

Azure Virtual Desktop no admite actualmente la autenticación en sesión con FIDO2 o Windows Hello para empresas.

## <a name="next-steps"></a>Pasos siguientes

- ¿Tiene curiosidad sobre otras formas de proteger la implementación? Consulte [Procedimientos recomendados de seguridad](security-guide.md).
- ¿Tiene problemas para conectarse a máquinas virtuales unidas a Azure AD? [Solución de problemas de conexiones a máquinas virtuales unidas a Azure AD](troubleshoot-azure-ad-connections.md).
- ¿Quiere usar tarjetas inteligentes desde fuera de su red corporativa? Aprenda a configurar un [servidor proxy KDC](key-distribution-center-proxy.md).
