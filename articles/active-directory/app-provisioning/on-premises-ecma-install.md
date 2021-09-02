---
title: Instalación del host del conector ECMA de Azure AD
description: En este artículo se describe cómo instalar el host del conector ECMA de Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea990e1075e8a0714959f42fdef38286a120912
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449596"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Instalación del host del conector ECMA de Azure AD

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general.

El host del conector ECMA de Azure Active Directory (Azure AD) se incluye como parte del paquete del agente de aprovisionamiento de Azure AD Connect. El agente de aprovisionamiento y el host del conector ECMA de Azure AD son dos servicios de Windows independientes. Se instalan mediante un instalador, que se implementa en la misma máquina.

Este flujo le guía a través del proceso de instalación y configuración del host del conector ECMA de Azure AD.

 ![Diagrama que muestra el flujo de instalación.](./media/on-premises-ecma-install/flow-1.png)

Para más información sobre la instalación y configuración, consulte:

   - [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
   - [Configuración del host del conector ECMA de Azure AD y el agente de aprovisionamiento](on-premises-ecma-configure.md)
   - [Configuración del conector SQL genérico del host del conector ECMA de Azure AD](on-premises-sql-connector-configure.md)

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Descarga e instalación del paquete del agente de aprovisionamiento de Azure AD Connect

 1. Inicie sesión en Azure Portal.
 1. Vaya a **Aplicaciones empresariales** > **Agregar una nueva aplicación**.
 1. Busque la aplicación **Aprovisionamiento local** y agréguela a la imagen del inquilino.
 1. Vaya al panel **Aprovisionamiento**.
 1. Seleccione **Conectividad local**.
 1. Descargue el instalador del agente.
 1. Ejecute el instalador de aprovisionamiento de Azure AD Connect, **AADConnectProvisioningAgentSetup.msi**.
 1. En la pantalla **Paquete del agente de aprovisionamiento de Microsoft Azure AD Connect**, acepte los términos de la licencia y seleccione **Instalar**.
 
    ![Pantalla del paquete del agente de aprovisionamiento de Microsoft Azure AD Connect.](media/on-premises-ecma-install/install-1.png)</br>
 1. Una vez finalizada esta operación, se inicia el Asistente para configuración. Seleccione **Siguiente**.
 
    ![Captura de pantalla que muestra la pantalla inicial.](media/on-premises-ecma-install/install-2.png)</br>

 1. En la pantalla **Seleccionar extensión**, seleccione **Aprovisionamiento de aplicaciones locales (de Azure AD a la aplicación)** . Seleccione **Siguiente**. 
 
    ![Captura de pantalla que muestra la selección de extensión.](media/on-premises-ecma-install/install-3.png)</br>
 1. Use la cuenta de administrador global para iniciar sesión en Azure AD.
 
     ![Captura de pantalla que muestra el inicio de sesión de Azure.](media/on-premises-ecma-install/install-4.png)</br>
 1. En la pantalla **Configuración del agente**, seleccione **Confirmar**.
 
     ![Captura de pantalla que muestra la confirmación de la instalación.](media/on-premises-ecma-install/install-5.png)</br>
 1. Una vez completada la instalación, debería ver un mensaje en la parte inferior del asistente. Seleccione **Salir**.
 
     ![Captura de pantalla que muestra la finalización.](media/on-premises-ecma-install/install-6.png)</br>
 

Ahora que el paquete del agente se ha instalado correctamente, tendrá que configurar el host del conector ECMA de Azure AD y crear o importar conectores.
 
## <a name="next-steps"></a>Pasos siguientes

- [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
- [Conector SQL genérico](on-premises-sql-connector-configure.md)
