---
title: 'Introducción: autenticación de AD DS en recursos compartidos de archivos de Azure'
description: Más información sobre la autenticación de Azure Active Directory Domain Services (Azure DS) para recursos compartidos de archivos de Azure En este artículo se abordan los escenarios de soporte técnico y la disponibilidad, y se explica cómo funcionan los permisos entre AD DS y Azure Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 585199f1898d3e2857f69c5c62fdfe4d07448bd5
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118087"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Introducción: autenticación de Active Directory Domain Services local en SMB para recursos compartidos de archivos de Azure

[Azure Files](storage-files-introduction.md) admite la autenticación basada en identidades a través del protocolo SMB (Bloque de mensajes del servidor) mediante dos tipos de servicios de dominio: Active Directory Domain Services (AD DS) local and Azure Active Directory Domain Services (Azure AD DS). Se recomienda encarecidamente consultar la sección [Funcionamiento](./storage-files-active-directory-overview.md#how-it-works) para seleccionar el servicio de dominio adecuado para la autenticación. La instalación es diferente en función del servicio de dominio que se elija. Esta serie de artículos se centra en habilitar y configurar AD DS local para la autenticación con recursos compartidos de archivos de Azure.

Si no está familiarizado con los recursos compartidos de archivos de Azure, se recomienda leer la [guía de plan](storage-files-planning.md) antes de leer la siguiente serie de artículos.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="supported-scenarios-and-restrictions"></a>Escenarios y restricciones admitidos

- Las identidades de AD DS que se usen para la autenticación de AD DS local de Azure Files se deben sincronizar con Azure AD. La sincronización de hash de contraseña es opcional. 
- Admite recursos compartidos de archivos de Azure administrados por Azure File Sync.
- Admite la autenticación Kerberos con AD y con [cifrado RC4-HMAC y AES 256](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). La compatibilidad con el cifrado AES 256 está limitada actualmente a las cuentas de almacenamiento con nombres de menos de 15 caracteres de longitud. Todavía no se admite el cifrado de Kerberos con AES 128.
- Admite la experiencia de inicio de sesión único.
- Solo se admite en clientes que ejecutan versiones de SO posteriores a Windows 7 o Windows Server 2008 R2.
- Solo se admite en el bosque de AD en el que está registrada la cuenta de almacenamiento. De forma predeterminada, solo se puede acceder a los recursos compartidos de archivos de Azure con las credenciales de AD DS desde un solo bosque. Si necesita acceso al recurso compartido de archivos de Azure desde otro bosque, asegúrese de tener configurada la confianza de bosque adecuada. Para más información, consulte las [preguntas más frecuentes](storage-files-faq.md#ad-ds--azure-ad-ds-authentication).
- No admite la autenticación en cuentas de equipo creadas en AD DS.
- No admite la autenticación en recursos compartidos de archivos de Network File System (NFS).

Al habilitar AD DS para recursos compartidos de archivos de Azure en SMB, las máquinas unidas a AD DS pueden montar recursos compartidos de archivos de Azure con sus credenciales de AD DS existentes. Esta funcionalidad se puede habilitar con un entorno de AD DS hospedado en máquinas locales o en Azure.

## <a name="videos"></a>Vídeos

Para ayudarle a configurar la autenticación con AD para Azure Files para algunos casos de uso comunes, hemos publicado dos vídeos con una guía paso a paso para los siguientes escenarios:

| Reemplazo de servidores de archivos locales por Azure Files (incluida la configuración en un vínculo privado para la autenticación de archivos y AD) | Uso de Azure Files como contenedor de perfiles para Windows Virtual Desktop (incluida la configuración de la autenticación de AD y la configuración de FsLogix)  |
|-|-|
| [![Presentación en pantalla del vídeo sobre cómo reemplazar servidores de archivos locales: haga clic para reproducirlo.](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![Presentación en pantalla del vídeo sobre cómo usar Azure Files como el contenedor de perfiles: haga clic para reproducirlo.](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>Requisitos previos 

Antes de habilitar la autenticación de AD DS para los recursos compartidos de archivos de Azure, asegúrese de que cumple los siguientes requisitos previos: 

- Seleccione o cree su [entorno de AD DS](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) y [sincronícelo en Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) con Azure AD Connect. 

    La característica se puede habilitar en un entorno de AD DS nuevo o existente. Las identidades usadas para el acceso deben estar sincronizadas con Azure AD. El inquilino de Azure AD y el recurso compartido de archivos al que accede debe estar asociado con la misma suscripción.

- Unir una máquina local o una máquina virtual de Azure por dominio a un AD DS local. Para información acerca de cómo unirse a un dominio, consulte [Unión de un equipo a un dominio](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Si la máquina no está unida a un dominio en AD DS, es posible que pueda seguir aprovechando las credenciales de AD para la autenticación si la máquina tiene una línea de visión del controlador de dominio de AD.

- Seleccione o cree una cuenta de almacenamiento de Azure.  Para conseguir un rendimiento óptimo, se recomienda implementar la cuenta de almacenamiento en la misma región que el cliente desde el que vaya a acceder al recurso compartido. A continuación, [monte el recurso compartido de archivos de Azure](storage-how-to-use-files-windows.md) con la clave de la cuenta de almacenamiento. Al montar con la clave de la cuenta de almacenamiento, se comprueba la conectividad.

    Asegúrese de que la cuenta de almacenamiento que contiene los recursos compartidos de archivos todavía no está configurada para la autenticación de Azure AD DS. Si la autenticación de Azure AD DS de Azure Files está habilitada en la cuenta de almacenamiento, se debe deshabilitar antes de cambiar al uso de una versión local de AD DS. Esto implica que las ACL existentes configuradas en el entorno de Azure AD DS se deberán volver a configurar para que se apliquen los permisos adecuados.


    Si tiene problemas para conectarse a Azure Files, vea [la herramienta de solución de problemas publicada para solucionar los errores de montaje de Azure Files en Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). También proporcionamos una [guía](./storage-files-faq.md#on-premises-access) para solucionar aquellos escenarios en los que el puerto 445 está bloqueado. 


- Realice una configuración de red relevante antes de habilitar y configurar la autenticación de AD DS en los recursos compartidos de archivos de Azure. Consulte [Consideraciones de redes para Azure Files](storage-files-networking-overview.md) para obtener más información.

## <a name="regional-availability"></a>Disponibilidad regional

La autenticación de Azure Files con AD DS está disponible en [todas las regiones públicas, en China y en las de Azure Government](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Información general

Si planea habilitar configuraciones de red en el recurso compartido de archivos, se recomienda que lea el artículo sobre [consideraciones de redes](./storage-files-networking-overview.md) y que complete la configuración relacionada antes de habilitar la autenticación de AD DS.

Habilitar la autenticación de AD DS para los recursos compartidos de archivos de Azure le permite autenticarse en los recursos compartidos de archivos de Azure con las credenciales de AD DS local. Además, le permite administrar mejor los permisos para permitir el control de acceso granular. Para ello, se requiere la sincronización de identidades desde AD DS local a Azure AD con AD Connect. El usuario controla el acceso de nivel de recursos compartidos con identidades sincronizadas con Azure AD al administrar el acceso de nivel de archivos o recursos compartidos con credenciales de AD DS local.

A continuación, siga los pasos que se indican a continuación para configurar Azure Files para la autenticación de AD DS: 

1. [Primera parte: Habilitación de la autenticación con AD DS de Azure Files en la cuenta de almacenamiento.](storage-files-identity-ad-ds-enable.md)

1. [Segunda parte: Asignación de permisos de acceso para un recurso compartido a la identidad de Azure AD (un usuario, grupo o entidad de servicio) que esté sincronizado con la identidad de AD de destino.](storage-files-identity-ad-ds-assign-permissions.md)

1. [Tercera parte: Configuración de ACL de Windows en SMB para directorios y archivos.](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Cuarta parte: Montaje de un recurso compartido de archivos de Azure en una VM unida a su instancia de AD DS.](storage-files-identity-ad-ds-mount-file-share.md)

1. [Actualice la contraseña de la identidad de la cuenta de almacenamiento en AD DS.](storage-files-identity-ad-ds-update-password.md)

En el diagrama siguiente se ilustra el flujo de trabajo de un extremo a otro para habilitar la autenticación de Azure AD en SMB en los recursos compartidos de archivos de Azure. 

![Diagrama de flujo de trabajo de AD de Files](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Las identidades que se usan para acceder a los recursos compartidos de archivos de Azure se deben sincronizar con Azure AD para aplicar los permisos de archivo de nivel de recurso compartido mediante el modelo de [control de acceso basado en roles de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Se conservarán y aplicarán las [DACL tipo Windows](/previous-versions/technet-magazine/cc161041(v=msdn.10)) en archivos o directorios transferidos desde servidores de archivos existentes. Esto ofrece una perfecta integración con el entorno de AD DS de la empresa. A medida que reemplaza los servidores de archivos locales por recursos compartidos de archivos de Azure, los usuarios existentes pueden acceder a estos desde sus clientes actuales con una experiencia de inicio de sesión único, sin ningún cambio en las credenciales en uso.  

## <a name="next-steps"></a>Pasos siguientes

Para habilitar la autenticación de AD DS local para el recurso compartido de archivos de Azure, continúe con el siguiente artículo:

[Primera parte: Habilitación de autenticación de AD DS para la cuenta](storage-files-identity-ad-ds-enable.md)
