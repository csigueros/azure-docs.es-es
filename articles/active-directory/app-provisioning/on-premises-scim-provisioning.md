---
title: Aprovisionamiento de aplicaciones locales de Azure AD para aplicaciones habilitadas para SCIM
description: En este artículo se describe cómo aprovisionar aplicaciones locales en aplicaciones habilitadas para SCIM.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: bf0b13bf1e2de31d15599bff2aa8750396f021ac
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570045"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>Aprovisionamiento de aplicaciones locales de Azure AD para aplicaciones habilitadas para SCIM

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Puede solicitar acceso a la funcionalidad [aquí](https://aka.ms/onpremprovisioningpublicpreviewaccess). Abriremos la versión preliminar para más clientes y conectores durante los próximos meses a medida que nos preparamos para la disponibilidad general.

El servicio de aprovisionamiento de Azure AD admite un cliente de [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) que se puede usar para aprovisionar automáticamente a los usuarios en aplicaciones locales o en la nube. En este documento se describe cómo se puede usar el servicio de aprovisionamiento de Azure AD para aprovisionar usuarios en una aplicación local que está habilitada para SCIM. Si quiere aprovisionar usuarios en aplicaciones locales que no son para SCIM, como un directorio LDAP que no es de AD o una base de datos SQL, consulte aquí (vínculo al nuevo documento que hay que crear). Si quiere aprovisionar usuarios en aplicaciones en la nube, como DropBox, Atlassian, etc., revise los [tutoriales](../../active-directory/saas-apps/tutorial-list.md) correspondientes a la aplicación. 

![arquitectura](./media/on-premises-scim-provisioning/scim-4.png)


## <a name="pre-requisites"></a>Requisitos previos
- Un inquilino de Azure AD con Azure AD Premium P1 o Premium P2 (o EMS E3 o E5). 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- Rol Administrador para instalar el agente.  Se trata de una tarea única y debe ser una cuenta de Azure que sea un administrador híbrido o un administrador global. 
- Rol Administrador para configurar la aplicación en la nube (administrador de aplicaciones, administrador de aplicaciones en la nube, administrador global, rol personalizado con permisos)

## <a name="steps-for-on-premises-app-provisioning-to-scim-enabled-apps"></a>Pasos para aprovisionar aplicaciones locales en aplicaciones habilitadas para SCIM
Siga los pasos a continuación para aprovisionar aplicaciones habilitadas para SCIM. 

 1. Agregue la aplicación "Agent-based SCIM provisioning" desde la [galería](../../active-directory/manage-apps/add-application-portal.md).
 2. Vaya a la aplicación > Aprovisionamiento > Descargar el agente de aprovisionamiento.
 3. Haga clic en conectividad local y descargue el agente de aprovisionamiento.
 4. Copie el agente en la máquina virtual o el servidor en el que se hospeda el punto de conexión de SCIM.
 5. Abra el instalador del agente de aprovisionamiento, acepte los términos del servicio y haga clic en Instalar.
 6. Abra el asistente para el agente de aprovisionamiento y seleccione el aprovisionamiento local cuando se le solicite la extensión que quiere habilitar.
 7. Proporcione las credenciales de un administrador de Azure AD cuando se le pida la autorización (se requiere un administrador híbrido o administrador global).
 8. Haga clic en Confirmar para confirmar que la instalación se completó correctamente.
 9. Vuelva a la aplicación > Conectividad local.
 10. Seleccione el agente que instaló en la lista desplegable y haga clic en Asignar agente.
 11. Espere 10 minutos o reinicie el servicio del agente de aprovisionamiento de Azure AD Connect en el servidor o VM.
 12. Proporcione la dirección URL del punto de conexión de SCIM en el campo de dirección URL del inquilino (por ejemplo, Https://localhost:8585/scim).
     ![asignar agente](./media/on-premises-scim-provisioning/scim-2.png)
 13. Haga clic en Probar conexión y guarde las credenciales.
 14. Configure las [asignaciones de atributos](customize-application-attributes.md) o las [reglas de ámbito](define-conditional-rules-for-provisioning-user-accounts.md) necesarias para la aplicación.  
 15. Agregue usuarios al ámbito mediante la [asignación de usuarios y grupos](../../active-directory/manage-apps/add-application-portal-assign-users.md) a la aplicación.
 16. Pruebe el aprovisionamiento de algunos usuarios [a petición](provision-on-demand.md).
 17. Para agregar usuarios adicionales al ámbito, asígnelos a la aplicación.
 18. Vaya a la hoja Aprovisionamiento y seleccione Iniciar aprovisionamiento.
 19. Supervise mediante los [registros de aprovisionamiento](../../active-directory/reports-monitoring/concept-provisioning-logs.md).
 

## <a name="things-to-be-aware-of"></a>Aspectos que debe tener en cuenta
* Asegúrese de que la implementación de [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) cumpla los [requisitos de Azure AD](use-scim-to-provision-users-and-groups.md).
  * Azure AD ofrece [código de referencia](https://github.com/AzureAD/SCIMReferenceCode/wiki) de código abierto que los desarrolladores pueden usar para arrancar su implementación de SCIM (el código es tal y como está).
* Admita el punto de conexión /schemaDiscovery para reducir la configuración necesaria en Azure Portal. 

## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de aplicaciones](user-provisioning.md)
- [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
- [Conector SQL genérico](on-premises-sql-connector-configure.md)
- [Tutorial: Conector SQL genérico del host del conector ECMA de Azure AD](tutorial-ecma-sql-connector.md)
