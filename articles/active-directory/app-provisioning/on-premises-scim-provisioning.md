---
title: Aprovisionamiento de aplicaciones locales de Azure AD para aplicaciones habilitadas para SCIM
description: En este artículo, se describe cómo usar el servicio de aprovisionamiento de Azure AD para aprovisionar usuarios en una aplicación local que está habilitada para SCIM.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: 0bc74d9c9bab1337638249900de92bee39a40a68
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571689"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>Aprovisionamiento de aplicaciones locales de Azure AD para aplicaciones habilitadas para SCIM

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general.

El servicio de aprovisionamiento de Azure Active Directory (Azure AD) admite un cliente [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) que se puede usar para aprovisionar automáticamente usuarios en aplicaciones locales o en la nube. En este artículo, se describe cómo se puede usar el servicio de aprovisionamiento de Azure AD para aprovisionar usuarios en una aplicación local que está habilitada para SCIM. Si desea aprovisionar usuarios en aplicaciones locales que no son SCIM y que usan SQL como almacén de datos, consulte [Conector SQL genérico del host del conector ECMA de Azure AD](tutorial-ecma-sql-connector.md). Si quiere aprovisionar usuarios en aplicaciones en la nube, como DropBox o Atlassian, revise los [tutoriales](../../active-directory/saas-apps/tutorial-list.md) específicos de la aplicación.

![Diagrama que muestra la arquitectura de SCIM.](./media/on-premises-scim-provisioning/scim-4.png)

## <a name="prerequisites"></a>Requisitos previos
- Un inquilino de Azure AD con Azure AD Premium P1 o Premium P2 (o EMS E3 o E5). [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- Rol Administrador para instalar el agente. Esta tarea es un esfuerzo de una sola vez y debe ser una cuenta de Azure que sea un administrador híbrido o un administrador global. 
- Rol de administrador para configurar la aplicación en la nube (administrador de aplicaciones, administrador de aplicaciones en la nube, administrador global o un rol personalizado con permisos).

## <a name="on-premises-app-provisioning-to-scim-enabled-apps"></a>Aprovisionamiento de aplicaciones locales para aplicaciones habilitadas para SCIM
Para aprovisionar usuarios en aplicaciones habilitadas para SCIM:

 1. Agregue la **aplicación SCIM local** desde la [galería](../../active-directory/manage-apps/add-application-portal.md).
 1. Vaya a la aplicación y seleccione **Aprovisionamiento** > **Descargar el agente de aprovisionamiento**.
 1. Seleccione **Conectividad local** y descargue el agente de aprovisionamiento.
 1. Copie el agente en la máquina virtual o el servidor en el que se hospeda el punto de conexión de SCIM.
 1. Abra el instalador del agente de aprovisionamiento, acepte los términos del servicio y seleccione **Instalar**.
 1. Abra el asistente para el agente de aprovisionamiento y seleccione **Aprovisionamiento local** cuando se le solicite la extensión que quiere habilitar.
 1. Proporcione las credenciales de un administrador de Azure AD cuando se le solicite para la autorización. Se requiere un administrador híbrido o un administrador global.
 1. Seleccione **Confirmar** para confirmar que la instalación se completó correctamente.
 1. Vuelva a la aplicación y seleccione **Conectividad local**.
 1. Seleccione el agente que instaló en la lista desplegable y seleccione **Assign Agent(s)** (Asignar agentes).
 1. Espere 10 minutos o reinicie el servicio del agente de aprovisionamiento de Azure AD Connect en el servidor o la máquina virtual.
 1. Proporcione la dirección URL del punto de conexión de SCIM en el cuadro de texto **Dirección URL del inquilino**. Un ejemplo es https://localhost:8585/scim.
 
     ![Captura de pantalla que muestra la asignación de un agente.](./media/on-premises-scim-provisioning/scim-2.png)
 1. Seleccione **Probar la conexión** y guarde las credenciales.
 1. Configure las [asignaciones de atributos](customize-application-attributes.md) o las [reglas de ámbito](define-conditional-rules-for-provisioning-user-accounts.md) necesarias para la aplicación.
 1. Agregue usuarios al ámbito mediante la [asignación de usuarios y grupos](../../active-directory/manage-apps/add-application-portal-assign-users.md) a la aplicación.
 1. Pruebe el aprovisionamiento de algunos usuarios [a petición](provision-on-demand.md).
 1. Para agregar usuarios adicionales al ámbito, asígnelos a la aplicación.
 1. Vaya al panel **Aprovisionamiento** y seleccione **Iniciar aprovisionamiento**.
 1. Supervise mediante los [registros de aprovisionamiento](../../active-directory/reports-monitoring/concept-provisioning-logs.md).

## <a name="additional-requirements"></a>Requisitos adicionales
* Asegúrese de que la implementación de [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) cumpla los [requisitos de Azure AD](use-scim-to-provision-users-and-groups.md).
  
  Azure AD ofrece [código de referencia](https://github.com/AzureAD/SCIMReferenceCode/wiki) de código abierto que los desarrolladores pueden usar para arrancar su implementación de SCIM. El código es tal y como está.
* Admita el punto de conexión /schemaDiscovery para reducir la configuración necesaria en Azure Portal. 

## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de aplicaciones](user-provisioning.md)
- [Conector SQL genérico](on-premises-sql-connector-configure.md)
- [Tutorial: Conector SQL genérico del host del conector ECMA de Azure AD](tutorial-ecma-sql-connector.md)
