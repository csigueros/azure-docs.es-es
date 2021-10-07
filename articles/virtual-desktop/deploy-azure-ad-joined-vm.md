---
title: 'Implementación de máquinas virtuales unidas a Azure AD en Azure Virtual Desktop: Azure'
description: Configuración e implementación de máquinas virtuales unidas a Azure AD en Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
ms.openlocfilehash: e6325c6511c6df9c3f3c021bc24a3f66b2e56c0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207742"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Implementación de máquinas virtuales unidas a Azure AD en Azure Virtual Desktop

Este artículo le guía por el proceso de implementar máquinas virtuales unidas a Azure Active Directory, y acceder a ellas, en Azure Virtual Desktop. Las máquinas virtuales unidas a Azure AD eliminan la necesidad de tener un campo visual desde la máquina virtual hasta un controlador de dominio (DC) de Active Directory local o virtualizado o de implementar servicios de dominio de Azure AD (Azure AD DS). En algunos casos, puede eliminar la necesidad de un controlador de dominio por completo, lo que simplifica la implementación y administración del entorno. Estas máquinas virtuales también se pueden inscribir automáticamente en Intune para facilitar la administración.

> [!NOTE]
> Las máquinas virtuales unidas a Azure AD solo se admiten actualmente en la nube comercial de Azure.

## <a name="supported-configurations"></a>Configuraciones admitidas

Las siguientes configuraciones están actualmente disponibles con las máquinas virtuales unidas a Azure AD:

- Escritorios personales con perfiles de usuario locales.
- Escritorios agrupados que se usan como jumpbox. En esta configuración, los usuarios acceden primero a la máquina virtual de Azure Virtual Desktop antes de conectarse a otro equipo de la red. Los usuarios no deben guardar datos en la máquina virtual.
- Escritorios o aplicaciones agrupados donde los usuarios no necesitan guardar datos en la máquina virtual. Por ejemplo, para aplicaciones que guarden los datos en línea o se conecten a una base de datos remota.

Las cuentas de usuario pueden ser usuarios solo de nube o de entornos híbridos del mismo inquilino de Azure AD.

## <a name="known-limitations"></a>Limitaciones conocidas

Las limitaciones conocidas a continuación pueden afectar al acceso a sus recursos locales o unidos a un dominio de Active Directory y deben tenerse en cuenta a la hora de decidir si las máquinas virtuales unidas a Azure AD son adecuadas para su entorno. Actualmente se recomienda usar máquinas virtuales unidas a Azure AD para escenarios en los que los usuarios solo necesitan acceder a recursos basados en la nube o a la autenticación basada en Azure AD.

- Tenga en cuenta que Azure Virtual Desktop (clásico) no admite máquinas virtuales unidas a Azure AD.
- Actualmente, las máquinas virtuales unidas a Azure AD no admiten usuarios externos;
- en este momento, solo admiten perfiles de usuario locales.
- Las máquinas virtuales unidas a Azure AD no pueden acceder a recursos compartidos de Azure Files para la asociación de aplicaciones de FSLogix o MSIX. Necesitará la autenticación Kerberos para acceder a cualquiera de estas características.
- El cliente Windows Store no admite actualmente máquinas virtuales unidas a Azure AD.
- Azure Virtual Desktop no admite actualmente el inicio de sesión único para las máquinas virtuales unidas a Azure AD.

## <a name="deploy-azure-ad-joined-vms"></a>Implementación de máquinas virtuales unidas a Azure AD

Puede implementar máquinas virtuales unidas a Azure AD directamente desde Azure Portal al [crear un grupo de hosts](create-host-pools-azure-marketplace.md) o al [expandir un grupo de hosts existente](expand-existing-host-pool.md). En la pestaña Máquinas virtuales, seleccione si quiere unir la máquina virtual a Active Directory o Azure Active Directory. Al seleccionar **Azure Active Directory**, se le proporciona automáticamente la opción **Enroll the VM with Intune** (Inscribir la máquina virtual con Intune) para que pueda administrar fácilmente máquinas virtuales [Windows 10 Enterprise](/mem/intune/fundamentals/windows-virtual-desktop) y [Windows 10 Enterprise de sesión múltiple](/mem/intune/fundamentals/windows-virtual-desktop-multi-session). Tenga en cuenta que la opción de Azure Active Directory unirá las máquinas virtuales al mismo inquilino de Azure AD que el de la suscripción en la que se encuentra.

> [!NOTE]
> - Los grupos de hosts solo deben contener máquinas virtuales del mismo tipo de unión a un dominio. Por ejemplo, las máquinas virtuales unidas a AD solo deben estar con otras máquinas virtuales de AD y viceversa.
> - Las máquinas virtuales del grupo de hosts deben tener Windows 10 de sesión única o múltiple, versión 2004 o posterior.

### <a name="assign-user-access-to-host-pools"></a>Asignación de acceso de usuario a grupos de hosts

Después de crear el grupo de hosts, debe asignar permisos a los usuarios para que puedan acceder a sus recursos. Para conceder acceso a los recursos, agregue cada usuario al grupo de aplicaciones. Siga las instrucciones que se indican en [Administración de grupos de aplicaciones](manage-app-groups.md) para asignar el acceso de usuario a aplicaciones y escritorios. Se recomienda usar grupos de usuarios en lugar de usuarios individuales siempre que sea posible.

En el caso de las máquinas virtuales unidas a Azure AD, deberá realizar dos acciones adicionales además de cumplir los requisitos para las implementaciones basadas en Active Directory o Azure Active Directory Domain Services:  

- Asigne a los usuarios el rol de **inicio de sesión de usuario de máquina virtual** para que puedan iniciar sesión en las máquinas virtuales.
- Asigne a los administradores que necesiten privilegios administrativos locales el rol de **inicio de sesión de administrador de máquina virtual**.

Para conceder a los usuarios acceso a máquinas virtuales unidas a Azure AD, debe [configurar las asignaciones de roles para la máquina virtual](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm). Puede asignar el rol **Inicio de sesión de usuario de máquina virtual** o **Inicio de sesión de administrador de máquina virtual** para las máquinas virtuales, para el grupo de recursos que contiene las máquinas virtuales o para la suscripción. Se recomienda asignar el rol Inicio de sesión de usuario de máquina virtual al mismo grupo de usuarios que usó para el grupo de aplicaciones en el nivel de grupo de recursos para que se aplique a todas las máquinas virtuales del grupo de hosts.

## <a name="access-azure-ad-joined-vms"></a>Acceso a las máquinas virtuales unidas a Azure AD

En esta sección se explica cómo acceder a las máquinas virtuales unidas a Azure AD desde diferentes clientes de Azure Virtual Desktop.

### <a name="connect-using-the-windows-desktop-client"></a>Conexión con el cliente de escritorio de Windows

La configuración predeterminada admite conexiones desde Windows 10 mediante el [cliente de escritorio de Windows](user-documentation/connect-windows-7-10.md). Puede usar sus credenciales, una tarjeta inteligente, la [confianza de certificados de Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust) o la [confianza de claves de Windows Hello para empresas con certificados](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs) para iniciar sesión en el host de sesión. Sin embargo, para acceder al host de sesión, el equipo local debe cumplir una de las siguientes condiciones:

- El equipo local está unido a Azure AD al mismo inquilino de Azure AD que el host de sesión.
- El equipo local está unido a Azure AD híbrido al mismo inquilino de Azure AD que el host de sesión.
- El equipo local ejecuta Windows 10, versión 2004 y posteriores, y está registrado en Azure AD en el mismo inquilino de Azure AD que el host de sesión.

Para habilitar el acceso desde dispositivos Windows no unidos a Azure AD, agregue **targetisaadjoined:i:1** como una [propiedad RDP personalizada](customize-rdp-properties.md) al grupo de hosts. Estas conexiones están restringidas a la especificación de las credenciales de nombre de usuario y contraseña al iniciar sesión en el host de sesión.

### <a name="connect-using-the-other-clients"></a>Conexión con los demás clientes

Para acceder a máquinas virtuales unidas a Azure AD con los clientes web, de Android, macOS e iOS, agregue **targetisaadjoined:i:1** como una [propiedad RDP personalizada](customize-rdp-properties.md) al grupo de hosts. Estas conexiones están restringidas a la especificación de las credenciales de nombre de usuario y contraseña al iniciar sesión en el host de sesión.

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Habilitación de MFA en las máquinas virtuales unidas a Azure AD

Puede habilitar la [autenticación multifactor](set-up-mfa.md) en las máquinas virtuales unidas a Azure AD mediante el establecimiento de una directiva de acceso condicional en la aplicación de Azure Virtual Desktop. Para que las conexiones se realicen correctamente, debe [deshabilitar la autenticación multifactor heredada por usuario](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required). Si no quiere restringir el inicio de sesión a métodos de autenticación seguros, como Windows Hello para empresas, también deberá [excluir la aplicación de inicio de sesión de máquinas virtuales Windows de Azure](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required) de la directiva de acceso condicional.

## <a name="user-profiles"></a>Perfiles de usuario

Actualmente, Azure Virtual Desktop solo admite perfiles locales en máquinas virtuales unidas a Azure AD.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado algunas máquinas virtuales unidas a Azure AD, puede iniciar sesión en un cliente de Azure Virtual Desktop admitido para probarlo como parte de una sesión de usuario. Si quiere aprender a conectarse a una sesión, consulte estos artículos:

- [Conexión con el cliente de Escritorio de Windows](user-documentation/connect-windows-7-10.md)
- [Conexión con el cliente web](user-documentation/connect-web.md)
- [Solución de problemas de conexiones a máquinas virtuales unidas a Azure AD](troubleshoot-azure-ad-connections.md)
