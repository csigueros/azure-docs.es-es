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
ms.openlocfilehash: e12ce0d979b323f10104d345f86b77618e094ba6
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570383"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Instalación del host del conector ECMA de Azure AD

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Puede solicitar acceso a la funcionalidad [aquí](https://aka.ms/onpremprovisioningpublicpreviewaccess). Abriremos la versión preliminar para más clientes y conectores durante los próximos meses a medida que nos preparamos para la disponibilidad general.

El host del conector ECMA de Azure AD se incluye y forma parte del paquete del agente de aprovisionamiento de Azure AD Connect.  El agente de aprovisionamiento y el host del conector ECMA de Azure AD son dos servicios de Windows independientes que se instalan mediante un instalador, implementados en la misma máquina. 

La instalación y configuración del host del conector ECMA de Azure AD es un proceso.  Use el flujo siguiente como guía para el proceso.

 ![Flujo de instalación](./media/on-premises-ecma-install/flow-1.png)  

Para más información sobre la instalación y configuración, consulte:
   - [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
   - [Configuración del host del conector ECMA de Azure AD y el agente de aprovisionamiento](on-premises-ecma-configure.md)
    - [Configuración del conector SQL genérico del host del conector ECMA de Azure AD](on-premises-sql-connector-configure.md)


## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Descarga e instalación del paquete del agente de aprovisionamiento de Azure AD Connect

 1. Inicie sesión en Azure Portal.
 2. Vaya a Aplicaciones empresariales > Agregar una nueva aplicación.
 3. Busque la aplicación "Aprovisionamiento local" y agréguela a la imagen del inquilino.
 4. Vaya a la hoja Aprovisionamiento.
 5. Haga clic en Conectividad local.
 6.  Descargue el instalador del agente.
 7. Ejecute el instalador de aprovisionamiento de Azure AD Connect, AADConnectProvisioningAgentSetup.msi.
 8. En la pantalla **Microsoft Azure AD Connect Provisioning Agent Package**, acepte los términos de licencia y seleccione **Instalar**.
   ![Pantalla Microsoft Azure AD Connect Provisioning Agent Package](media/on-premises-ecma-install/install-1.png)</br>
 9. Una vez finalizada esta operación, se inicia el Asistente para configuración. Haga clic en **Next**.
   ![Pantalla principal](media/on-premises-ecma-install/install-2.png)</br>
 10. En la pantalla **Seleccionar extensión**, seleccione **On-premises application provisioning (Azure AD to application)** (Aprovisionamiento de aplicaciones locales [de Azure AD a la aplicación]) y haga clic en **Siguiente**. 
   ![Seleccionar extensión](media/on-premises-ecma-install/install-3.png)</br>
 12. Use la cuenta de administrador global e inicie sesión en Azure AD.
     ![Inicio de sesión de Azure](media/on-premises-ecma-install/install-4.png)</br>
 13.  En la pantalla **Configuración del agente**, haga clic en **Confirmar**.
     ![Confirme la instalación](media/on-premises-ecma-install/install-5.png)</br>
 14.  Una vez completada la instalación, debería ver un mensaje al final del asistente.  Haga clic en **Finalizar**
     ![Haga clic en Finalizar](media/on-premises-ecma-install/install-6.png)</br>
 15. Haga clic en **Cerrar**.

Ahora que el paquete del agente se ha instalado correctamente, tendrá que configurar el host del conector ECMA de Azure AD y crear o importar conectores.  
## <a name="next-steps"></a>Pasos siguientes


- [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
- [Conector SQL genérico](on-premises-sql-connector-configure.md)
