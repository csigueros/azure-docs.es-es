---
title: Migración de Servidor Multi-Factor Authentication de Azure a Azure Multi-Factor Authentication de Azure Active Directory
description: Guía paso a paso para migrar de Servidor Multi-Factor Authentication de Azure local a Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eff0705208442c09b859c09ac975e044528f833
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397759"
---
# <a name="migrate-from-azure-mfa-server-to-azure-multi-factor-authentication"></a>Migración de Servidor Multi-Factor Authentication de Azure a Azure Multi-Factor Authentication

La autenticación multifactor (MFA) es importante para proteger la infraestructura y los recursos de actores malintencionados. Servidor Azure MFA no está disponible para nuevas implementaciones y estará en desuso. Los clientes que usan Servidor Multi-Factor Authentication deben pasar a usar la autenticación multifactor de Azure Active Directory (Azure AD) basada en la nube. En esta documentación, se supone que tiene un entorno híbrido donde:

- Usa Servidor Multi-Factor Authentication para MFA.
- Usa la federación en Azure AD con Servicios de federación de Active Directory (AD FS) u otro producto de federación del proveedor de identidades.
  - Aunque este artículo está limitado a AD FS, se aplican pasos similares a otros proveedores de identidades.
- La instancia de Servidor Multi-Factor Authentication está integrada con AD FS. 
- Es posible que tenga aplicaciones que usan AD FS para la autenticación.

Hay varios estados finales posibles para la migración, en función de su objetivo.

| <br> | Objetivo: Retirar SOLO Servidor Multi-Factor Authentication | Objetivo: Retirar Servidor Multi-Factor Authentication y pasar a la autenticación de Azure AD | Objetivo: Retirar Servidor Multi-Factor Authentication y AD FS |
|------|------------------------------------|-------------------------------------------------------------------|-----------------------------------------|
|Proveedor de MFA | Cambiar el proveedor de MFA de Servidor Multi-Factor Authentication a Azure AD MFA. | Cambiar el proveedor de MFA de Servidor Multi-Factor Authentication a Azure AD MFA. |   Cambiar el proveedor de MFA de Servidor Multi-Factor Authentication a Azure AD MFA. |
|Autenticación de usuarios  |Seguir usando la federación con la autenticación de Azure AD. | Pasar a Azure AD con sincronización de hash de contraseña (opción preferida) o autenticación de paso a través **e** inicio de sesión único de conexión directa.| Pasar a Azure AD con sincronización de hash de contraseña (opción preferida) o autenticación de paso a través **e** inicio de sesión único de conexión directa. |
|Autenticación de la aplicación | Seguir usando la autenticación de AD FS en las aplicaciones. | Seguir usando la autenticación de AD FS en las aplicaciones. | Mover las aplicaciones a Azure AD antes de migrar a Azure MFA. |

Si es posible, mover su instancia de MFA y su autenticación de usuario a Azure. Puede encontrar instrucciones paso a paso en [Migración a Azure AD MFA y a la autenticación de usuario de Azure AD](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md). 

Si no puede mover la autenticación de usuario, consulte la guía paso a paso sobre [Migración a Azure AD MFA con federación](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md).

## <a name="prerequisites"></a>Requisitos previos

- Entorno de AD FS (obligatorio si no va a migrar todas las aplicaciones a Azure AD antes de migrar MFA)
  - Actualice a AD FS para Windows Server 2019, nivel de comportamiento de granja (FBL) 4. De esta forma, puede seleccionar el proveedor de autenticación en función de la pertenencia a grupos para lograr una transición de usuario más fluida. Aunque es posible realizar la migración mientras se está en AD FS para Windows Server 2016 FBL 3, no es tan fácil para los usuarios. Durante la migración, se pedirá a los usuarios que seleccionen un proveedor de autenticación (Servidor Multi-Factor Authentication o Azure MFA) hasta que se complete la migración. 
- Permisos
  - Rol de administrador de empresa en Active Directory para configurar la granja de AD FS para Azure AD MFA
  - Rol de administrador global en Azure AD para realizar la configuración de Azure AD mediante Azure AD PowerShell


## <a name="considerations-for-all-migration-paths"></a>Consideraciones sobre todas las rutas de migración

La migración de Servidor Multifactor Authentication a Azure AD MFA no consiste solo en mover los números de teléfono de MFA registrados. El Servidor Multifactor Authentication de Microsoft se puede integrar con muchos sistemas, así que debe evaluar cómo lo usan estos sistemas para comprender las mejores maneras de integrarlo con Azure AD MFA. 

### <a name="migrating-mfa-user-information"></a>Migración de la información de usuario de MFA

A la hora de mover usuarios en lotes, normalmente el camino elegido es moverlos por regiones, departamentos o roles, por ejemplo, administradores. Independientemente de la estrategia que elija, asegúrese de mover los usuarios de forma iterativa, empezando por los grupos de prueba y piloto, y de que dispone de un plan de reversión. 

Aunque puede migrar los números de teléfono y tokens de hardware de MFA registrados de los usuarios, no puede migrar los registros de dispositivos, como su configuración de la aplicación Microsoft Authenticator. Los usuarios tendrán que registrar y agregar una nueva cuenta en la aplicación Authenticator y quitar la cuenta anterior. 

Para ayudar a los usuarios a diferenciar la cuenta recién agregada de la cuenta antigua vinculada a Servidor Multi-Factor Authentication, asegúrese de que el nombre de la cuenta de la aplicación móvil en dicho servidor sea diferente para que se distingan las dos cuentas. Por ejemplo, el nombre de cuenta que aparece en Aplicación móvil en el Servidor Multi-Factor Authentication se ha cambiado a Servidor Multi-Factor Authentication local. El nombre de cuenta de la aplicación Authenticator cambiará con la siguiente notificación push al usuario. 

La migración de números de teléfono también puede provocar la migración de números obsoletos y hacer que los usuarios se queden con la autenticación multifactor basada en teléfono en lugar de configurar métodos más seguros, como Microsoft Authenticator en modo sin contraseña. Por lo tanto, se recomienda que, independientemente de la ruta de migración que elija, todos los usuarios se registren para obtener [información de seguridad combinada](howto-registration-mfa-sspr-combined.md).


#### <a name="migrating-hardware-security-keys"></a>Migración de las claves de seguridad de hardware

Azure AD proporciona compatibilidad con tokens de hardware OATH. Para migrar los tokens del Servidor Multi-Factor Authentication a Azure AD MFA, los [tokens deben cargarse en Azure AD mediante un archivo CSV](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview), conocido normalmente como "archivo de provisión". El archivo de provisión contiene las claves secretas y los números de serie del token, así como otra información necesaria para cargar los tokens en Azure AD. 

Si ya no tiene el archivo de provisión con las claves secretas, no es posible exportar las claves secretas desde el Servidor Multi-Factor Authentication. Si ya no tiene acceso a las claves secretas, póngase en contacto con el proveedor de hardware para que le ayuden.

Para exportar el número de serie de cualquiera de los tokens OATH asignados a un usuario dado se puede usar el SDK del servicio web del Servidor Multi-Factor Authentication. Con esta información, junto con el archivo de provisión, los administradores de TI pueden importar los tokens en Azure AD y asignar el token OATH al usuario especificado en función del número de serie. También es necesario ponerse en contacto con el usuario en el momento de la importación para proporcionar información de OTP desde el dispositivo a fin de completar el registro. Consulte el tema GetUserInfo > userSettings > OathTokenSerialNumber del archivo de ayuda del Servidor Multi-Factor Authentication que puede encontrar en dicho servidor. 


### <a name="additional-migrations"></a>Migraciones adicionales

La decisión de migrar desde Servidor Multi-Factor Authentication hasta Azure MFA abre la puerta a otras migraciones. La realización de migraciones adicionales depende de muchos factores, incluidos específicamente:

- Su disposición a usar la autenticación de Azure AD para los usuarios
- Su disposición a mover las aplicaciones a Azure AD

Dado que el Servidor Multi-Factor Authentication está profundamente integrado con las aplicaciones y la autenticación de usuario, puede considerar la posibilidad de mover ambas funciones a Azure como parte de la migración de MFA para, finalmente, retirar AD FS. 

Nuestras recomendaciones: 

- Usar Azure AD para la autenticación, ya que permite una seguridad y una gobernanza más sólidas.
- Mover las aplicaciones a Azure AD cuando sea posible.

Para seleccionar el método de autenticación de usuario más adecuado para su organización, consulte [Elección del método de autenticación adecuado para la solución de identidad híbrida de Azure AD](../hybrid/choose-ad-authn.md). Se recomienda usar la sincronización de hash de contraseña (PHS).

### <a name="passwordless-authentication"></a>Autenticación sin contraseñas

Como parte de la inscripción de usuarios para que usen Microsoft Authenticator como segundo factor, se recomienda habilitar el inicio de sesión telefónico sin contraseña como parte de su registro. Para más información, incluidos otros métodos sin contraseña, como FIDO y Windows Hello para empresas, visite [Planeamiento de una implementación de autenticación sin contraseña con Azure AD](howto-authentication-passwordless-deployment.md#plan-for-and-deploy-the-microsoft-authenticator-app).

### <a name="microsoft-identity-manager-self-service-password-reset"></a>Autoservicio de restablecimiento de contraseña de Microsoft Identity Manager 

SSPR de Microsoft Identity Manager (MIM) puede usar el Servidor Multi-Factor Authentication para invocar códigos de acceso de un solo uso por SMS como parte del flujo de restablecimiento de contraseña. MIM no se puede configurar para usar Azure MFA. Se recomienda evaluar el traslado del servicio SSPR a SSPR de Azure AD.

Puede usar la oportunidad de que los usuarios que se registren en Azure MFA usen la experiencia de registro combinada para registrarse en SSPR de Azure AD.


### <a name="radius-clients-and-azure-ad-mfa"></a>Clientes RADIUS y Azure AD MFA

El Servidor Multi-Factor Authentication admite RADIUS para invocar MFA en aplicaciones y dispositivos de red que admiten el protocolo. Si usa RADIUS con el Servidor Multi-Factor Authentication, se recomienda mover las aplicaciones cliente a protocolos modernos, como SAML, Open ID Connect u OAuth en Azure AD. Si la aplicación no se puede actualizar, puede implementar el servidor de directivas de red (NPS) con la extensión de Azure MFA. La extensión del servidor de directivas de red (NPS) actúa como adaptador entre las aplicaciones basadas en RADIUS y Azure AD MFA para proporcionar un segundo factor de autenticación. Esto le permite mover los clientes RADIUS a Azure MFA y retirar el Servidor Multi-Factor Authentication.

#### <a name="important-considerations"></a>Consideraciones importantes

Existen limitaciones al usar NPS con clientes RADIUS y se recomienda evaluar los clientes RADIUS para determinar si puede actualizarlos a protocolos de autenticación modernos. Consulte con el proveedor de servicios las versiones de producto admitidas y sus funcionalidades. 

- La extensión NPS no usa las directivas de acceso condicional de Azure AD. Si permanece con RADIUS y usa la extensión NPS, todas las solicitudes de autenticación que van a NPS requerirán que el usuario realice la autenticación multifactor.
- Los usuarios deben registrarse en Azure AD MFA antes de usar la extensión NPS. De lo contrario, la extensión no podrá autenticar al usuario, lo que puede generar llamadas al departamento de soporte técnico.
- Cuando la extensión NPS invoca MFA, la solicitud MFA se envía al método MFA predeterminado del usuario. 
  - Dado que el inicio de sesión se produce en aplicaciones de terceros, es poco probable que el usuario vea una notificación visual de que se requiere MFA y que se ha enviado una solicitud a su dispositivo.
  - Durante el requisito de MFA, el usuario debe tener acceso a su método de autenticación predeterminado para completar el requisito de MFA. 
  - Los usuarios pueden cambiar su método de MFA predeterminado en la página Información de seguridad (aka.ms/mysecurityinfo).
- Los métodos MFA disponibles para los clientes RADIUS se controlan mediante los sistemas cliente que envían las solicitudes de acceso RADIUS.
  - Los métodos MFA que requieren la entrada del usuario después de escribir una contraseña solo se pueden usar con sistemas que admiten respuestas de desafío de acceso con RADIUS. Los métodos de entrada pueden incluir OTP, tokens OATH de hardware o la aplicación Microsoft Authenticator.
  - Algunos sistemas pueden limitar los métodos MFA disponibles a las notificaciones push y las llamadas telefónicas de Microsoft Authenticator.


>[!NOTE]
>El algoritmo de cifrado de contraseña usado entre el cliente RADIUS y el sistema NPS y los métodos de entrada que el cliente puede usar afectan a los métodos de autenticación que están disponibles. Para más información, consulte [Determinación de los métodos de autenticación que pueden utilizar los usuarios](howto-mfa-nps-extension.md). 

Las integraciones comunes del cliente RADIUS incluyen aplicaciones como [puertas de enlace de Escritorio remoto](howto-mfa-nps-extension-rdg.md) y [servidores VPN](howto-mfa-nps-extension-vpn.md). Otras pueden incluir:

- Puerta de enlace de Citrix
  - La [puerta de enlace de Citrix](https://docs.citrix.com/en-us/citrix-gateway) admite la integración tanto de RADIUS como de la extensión NPS, así como una integración de SAML.
- Cisco VPN
  - Cisco VPN admite la [autenticación tanto de RADIUS como de SAML para SSO](../saas-apps/cisco-anyconnect.md).
  - Al pasar de la autenticación de RADIUS a SAML, puede integrar Cisco VPN sin tener que implementar la extensión NPS.
- Todas las VPN
  - Si es posible, se recomienda federar la VPN como una aplicación SAML. Esto le permitirá usar el acceso condicional. Para más información, consulte una [lista de proveedores de VPN que están integrados ](../manage-apps/secure-hybrid-access.md#secure-hybrid-access-through-azure-ad-partner-integrations) en la galería de aplicaciones de Azure AD.


### <a name="resources-for-deploying-nps"></a>Recursos para implementar NPS

- [Adición de una nueva infraestructura de NPS](/windows-server/networking/technologies/nps/nps-top)
- [Procedimientos recomendados para la implementación de NPS](https://www.youtube.com/watch?v=qV9wddunpCY)
- [Script de comprobación del estado de la extensión NPS para Azure MFA](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)
- [Integración de la infraestructura NPS existente con Azure AD MFA](howto-mfa-nps-extension-vpn.md)

## <a name="next-steps"></a>Pasos siguientes

- [Migración a Azure AD MFA con federación](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)
- [Migración a Azure AD MFA y a la autenticación de usuario de Azure AD](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)


