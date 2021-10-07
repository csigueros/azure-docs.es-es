---
title: Instalación del agente de aprovisionamiento de Azure AD Connect
description: Aprenda a instalar el agente de aprovisionamiento de Azure AD Connect y cómo configurarlo en Azure Portal.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11428df3662bb03d7d74decbd7230be0fdffa44f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742654"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalación del agente de aprovisionamiento de Azure AD Connect
Este artículo le guía en el proceso de instalación del agente de aprovisionamiento de Azure Active Directory (Azure AD) Connect y en el modo de configurarlo inicialmente en Azure Portal.

>[!IMPORTANT]
>En las siguientes instrucciones de instalación se supone que se han cumplido todos los [requisitos previos](how-to-prerequisites.md).

>[!NOTE]
>En este artículo se habla de la instalación del agente de aprovisionamiento mediante el asistente. Para información sobre cómo instalar el agente de aprovisionamiento de Azure AD Connect mediante una interfaz de línea de comandos (CLI), consulte [Instalación del agente de aprovisionamiento de Azure AD Connect mediante una interfaz de línea de comandos (CLI) y PowerShell](how-to-install-pshell.md).

Para obtener información adicional y ver un ejemplo, vea el vídeo siguiente.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mR]

## <a name="group-managed-service-accounts"></a>Cuentas de servicio administradas de grupo
Una cuenta de servicio administrado (gMSA) de grupo es una cuenta de dominio administrada que proporciona administración automática de contraseñas, administración simplificada de nombres de entidad de seguridad de servicio (SPN) y la posibilidad de delegar la administración a otros administradores. También amplía esta funcionalidad a varios servidores. Azure AD Connect Cloud Sync admite y recomienda el uso de una cuenta de servicio administrada de grupo para ejecutar el agente. Para más información sobre las cuentas de servicio administradas de grupo, consulte [Cuentas de servicio administradas de grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).


### <a name="upgrade-an-existing-agent-to-use-the-gmsa"></a>Actualización de un agente existente para usar la cuenta gMSA
Para actualizar un agente existente para que use la cuenta de servicio administrada de grupo creada durante la instalación, actualice el servicio del agente a la versión más reciente mediante la ejecución de AADConnectProvisioningAgent.msi. Ahora vuelva a ejecutar el asistente para la instalación y escriba las credenciales para crear la cuenta cuando se lo solicite.

## <a name="install-the-agent"></a>Instalación del agente

Para instalar el agente:

 1. Inicie sesión en el servidor que va a usar con permisos de administrador de empresa.
 1. Inicie sesión en Azure Portal y, a continuación, vaya a **Azure Active Directory**.
 1. En el menú de la izquierda, seleccione **Azure AD Connect**.
 1. Seleccione **Manage cloud sync** (Administrar sincronización en la nube)  > **Revisar todos los agentes**.
 1. Descargue el agente de aprovisionamiento de Azure AD Connect en Azure Portal.
 
    ![Captura de pantalla que muestra Descargar el agente local.](media/how-to-install/install-9.png)</br>
 1. Acepte los términos y seleccione **Descargar**.
 1. Ejecute el instalador de aprovisionamiento de Azure AD Connect, AADConnectProvisioningAgentSetup.msi.
 1. En la pantalla **Microsoft Azure AD Connect Provisioning Agent Package**, acepte los términos de licencia y seleccione **Instalar**.
 
    ![Captura de pantalla que muestra la pantalla Microsoft Azure AD Connect Provisioning Agent Package.](media/how-to-install/install-1.png)</br>
 1. Una vez finalizada esta operación, se inicia el Asistente para configuración. Inicie sesión con su cuenta de administrador global de Azure AD.
 1. En la pantalla **Configure Service Account** (Configurar cuenta de servicio), seleccione **Create gMSA** (Crear gMSA) o **Use custom gMSA** (Usar gMSA personalizada). Si permite que el agente cree la cuenta, se llamará provAgentgMSA$. Si especifica **Use custom gMSA** (Usar gMSA personalizada) se le pedirá que proporcione esta cuenta.
 1. Escriba las credenciales de administrador de dominio para crear la cuenta de servicio administrada de grupo que se usará para ejecutar el servicio del agente. Seleccione **Siguiente**.
  
    ![Captura de pantalla que muestra la opción Create gMSA (Crear gMSA).](media/how-to-install/install-12.png)</br>
 1. En la pantalla **Conectar Active Directory**, seleccione **Agregar directorio**. Después, inicie sesión con su cuenta de administrador de Active Directory. Esta operación permitirá agregar su directorio local. 
 1. De manera opcional, puede administrar la preferencia de los controladores de dominio que usará el agente; para ello, active la casilla **Select domain controller priority** (Seleccionar prioridad del controlador de dominio) y ordene la lista de controladores de dominio. Seleccione **Aceptar**.
 
    ![Captura de pantalla que muestra cómo ordenar los controladores de dominio.](media/how-to-install/install-2a.png)</br>
 1. Seleccione **Siguiente**.
 
    ![Captura de pantalla que muestra la pantalla Connect Active Directory (Conectar Active Directory).](media/how-to-install/install-3a.png)</br>
 1. En la pantalla **Agent Installation** (Instalación del agente), confirme la configuración y la cuenta que se creará y seleccione **Confirmar**.
 
    ![Captura de pantalla que muestra la opción Confirmar.](media/how-to-install/install-11.png)</br>
 1. Una vez finalizada esta operación, debería ver **Your agent installation is complete** (Se completó la instalación del agente). Seleccione **Salir**.
 
    ![Captura de pantalla que muestra la pantalla Configuración completada.](media/how-to-install/install-4a.png)</br>
 1. Si todavía ve la pantalla inicial **Microsoft Azure AD Connect Provisioning Agent Package**, seleccione **Cerrar**.

## <a name="verify-agent-installation"></a>Comprobación de la instalación del agente
La comprobación del agente se produce en Azure Portal y en el servidor local que ejecuta el agente.

### <a name="azure-portal-agent-verification"></a>Comprobación del agente en Azure Portal
Para comprobar que Azure puede ver el agente:

 1. Inicie sesión en Azure Portal.
 1. En la parte izquierda, seleccione **Azure Active Directory** > **Azure AD Connect**. En el centro, seleccione **Manage cloud sync** (Administrar sincronización en la nube).

    ![Captura de pantalla en la que se muestra Azure Portal.](media/how-to-install/install-6.png)</br>

 1. En la pantalla **Azure AD Connect cloud sync** (Sincronización en la nube de Azure AD Connect), seleccione **Revisar todos los agentes**.

    ![Captura de pantalla que muestra la opción Revisar todos los agentes.](media/how-to-install/install-7.png)</br>
 
 1. En la pantalla **Agentes de aprovisionamiento locales**, verá los agentes que ha instalado. Compruebe que el agente en cuestión está ahí y que se ha marcado como *Activo*.

    ![Captura de pantalla que muestra la pantalla On-premises provisioning agents (Agentes de aprovisionamiento locales).](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>En el servidor local
Para comprobar que el agente se está ejecutando:

1. Inicie sesión en el servidor con una cuenta de administrador.
1. Abra **Servicios**. Para ello, vaya ahí o seleccione **Inicio** > **Ejecutar** > **Services.msc**.
1. En **Servicios**, asegúrese de que tanto el **Actualizador del Agente de Microsoft Azure AD Connect** como el **Agente de aprovisionamiento de Microsoft Azure AD Connect** están ahí y que su estado es *En ejecución*.

    ![Captura de pantalla que muestra la pantalla Servicios.](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>El agente se ha instalado, pero debe configurarse y habilitarse para que empiece a sincronizar a los usuarios. Para configurar un nuevo agente, consulte [Creación de una configuración para la sincronización en la nube de Azure AD Connect](how-to-configure.md).

## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [¿Qué es la sincronización en la nube de Azure AD Connect?](what-is-cloud-sync.md)
- [Creación de una configuración para la sincronización en la nube de Azure AD Connect](how-to-configure.md).

