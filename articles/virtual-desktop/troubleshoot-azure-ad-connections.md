---
title: Conexiones a máquinas virtuales unidas a Azure AD en Azure Virtual Desktop - Azure
description: Procedimiento para resolver problemas al conectarse a máquinas virtuales unidas a Azure AD en Azure Virtual Desktop
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.author: helohr
ms.openlocfilehash: 97c81eadf7091f04b77b094a4622c1731d19c537
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228884"
---
# <a name="connections-to-azure-ad-joined-vms"></a>Conexiones a máquinas virtuales unidas a Azure AD

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager.

Use este artículo para resolver problemas con las conexiones a máquinas virtuales unidas a Azure AD en Azure Virtual Desktop.

## <a name="provide-feedback"></a>Envío de comentarios

Visite la [comunidad técnica de Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum) para analizar el servicio Azure Virtual Desktop con el equipo de producto y los miembros activos de la comunidad.

## <a name="all-clients"></a>Todos los clientes

### <a name="your-account-is-configured-to-prevent-you-from-using-this-device"></a>Su cuenta está configurada para impedir el uso de este dispositivo

Si aparece un error en el que se indica **que la cuenta está configurada para impedir que use este dispositivo y que, para obtener más información, se ponga en contacto con el administrador del sistema**, asegúrese de que a la cuenta de usuario se le haya concedido el [rol de inicio de sesión de usuario de la máquina virtual](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#azure-role-not-assigned) en las máquinas virtuales. 

## <a name="windows-desktop-client"></a>Cliente de escritorio de Windows

### <a name="the-logon-attempt-failed"></a>Error al intentar iniciar sesión

Si se produce un error en el que se indica que ha producido un **error al intentar iniciar sesión** en la solicitud de credenciales de Seguridad de Windows, compruebe lo siguiente:

- Se encuentra en un dispositivo unido a Azure AD o unido a Azure AD de forma híbrida en el mismo inquilino de Azure AD que el host de sesión, o bien
- Se encuentra en un dispositivo que ejecuta Windows 10 2004 o posterior que está registrado en Azure AD en el mismo inquilino de Azure AD que el host de sesión.
- El [protocolo PKU2U está habilitado](/windows/security/threat-protection/security-policy-settings/network-security-allow-pku2u-authentication-requests-to-this-computer-to-use-online-identities) tanto en el equipo PC local como en el host de sesión.

### <a name="the-sign-in-method-youre-trying-to-use-isnt-allowed"></a>No se permite el método de inicio de sesión que intenta usar.

Si se produce un error en el que se indica que **el método de inicio de sesión que está intentando usar no está permitido y que pruebe otro o que se ponga en contacto con el administrador del sistema**, significa que tiene directivas de acceso condicional que restringen el acceso. Siga las instrucciones de [Habilitación de la autenticación multifactor](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) para habilitar la autenticación multifactor de las máquinas virtuales unidas a Azure AD.

## <a name="web-client"></a>Cliente web

### <a name="sign-in-failed-please-check-your-username-and-password-and-try-again"></a>Error al iniciar sesión. Compruebe el nombre de usuario y la contraseña e inténtelo de nuevo.

Si aparece un error en el que se indica que **no se ha podido conectar a NAME, que se ha producido un error al iniciar sesión y que compruebe el nombre de usuario y la contraseña y lo intente de nuevo**, al usar el cliente web, asegúrese de que [haya habilitado las conexiones de otros clientes](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients).

### <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>No se pudo conectar al equipo remoto debido a un error de seguridad

Si aparece un error en el que se indica que **no se ha podido conectar a NAME ni al equipo PC remoto debido a un error de seguridad y que, si esto sigue ocurriendo, pida ayuda al administrador o al soporte técnico**, significa que tiene directivas de acceso condicional que restringen el acceso. Siga las instrucciones de [Habilitación de la autenticación multifactor](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) para habilitar la autenticación multifactor de las máquinas virtuales unidas a Azure AD.

## <a name="android-client"></a>Cliente de Android

### <a name="error-code-2607---we-couldnt-connect-to-the-remote-pc-because-your-credentials-did-not-work"></a>Código de error 2607: no se ha podido establecer la conexión con el equipo remoto porque las credenciales no han funcionado.

Si aparece un error en el que se indica que **no se ha podido conectar al equipo PC remoto porque no han funcionado las credenciales y que la máquina remota esta unida a AADJ**, con el código de error 2607 al usar el cliente de Android, asegúrese de que [haya habilitado las conexiones de otros clientes](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la solución de problemas de Azure Virtual Desktop y las pistas de escalación, consulte [Introducción a la solución de problemas, comentarios y soporte técnico](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante la creación de un entorno de Azure Virtual Desktop y de un grupo de hosts de este, consulte [Creación de entorno y grupo de hosts](troubleshoot-set-up-issues.md).
- Para solucionar problemas al configurar una máquina virtual (VM) en Azure Virtual Desktop, consulte [Configuración de la máquina virtual del host de sesión](troubleshoot-vm-configuration.md).
- Para solucionar problemas relacionados con el agente de Azure Virtual Desktop o la conectividad de sesión, consulte [Solución de problemas comunes del agente de Azure Virtual Desktop](troubleshoot-agent.md).
- Para solucionar problemas al usar PowerShell con Azure Virtual Desktop, consulte [PowerShell para Azure Virtual Desktop](troubleshoot-powershell.md).
- Para realizar un tutorial de solución de problemas, consulte [Tutorial: Solución de problemas de las implementaciones de plantillas de Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
