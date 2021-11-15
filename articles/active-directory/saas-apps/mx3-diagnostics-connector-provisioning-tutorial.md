---
title: 'Tutorial: Configuración de MX3 Diagnostics Connector para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD en MX3 Diagnostics Connector.
services: active-directory
documentationcenter: ''
author: twimmers
writer: Thwimmer
manager: beatrizd
ms.assetid: 6d54ea28-0208-45bc-8e29-c6cf9a912f00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2021
ms.author: Thwimmer
ms.openlocfilehash: 2021b7a7af7aee23e118de0b555e86cb7ee20272
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270184"
---
# <a name="tutorial-configure-mx3-diagnostics-connector-for-automatic-user-provisioning"></a>Tutorial: Configuración de MX3 Diagnostics Connector para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en MX3 Diagnostics Connector y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en [MX3 Diagnostics Connector](https://www.mx3diagnostics.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Cree usuarios en MX3 Diagnostics Connector.
> * Quite los usuarios de MX3 Diagnostics Connector cuando ya no necesiten acceso.
> * Mantenga los atributos de usuario sincronizados entre Azure AD y MX3 Diagnostics Connector.
> * Aprovisione grupos y pertenencias a grupos en MX3 Diagnostics Connector.
> * Inicio de sesión único en MX3 Diagnostics Connector.

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md). 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta MX3 con la característica de organización.
* Una cuenta en el portal MX3 con SSO.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos quiere [asignar entre Azure AD y MX3 Diagnostics Connector](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mx3-diagnostics-connector-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de MX3 Diagnostics Connector para admitir el aprovisionamiento con Azure AD

1. Si la cuenta MX3 no tiene habilitada la característica de organización, solicítela como se describe en esta [documentación](https://www.mx3diagnostics.com/files/files/MX3_PortalGuide_0321.pdf).

1. Si la cuenta MX3 no tiene habilitada la característica de inicio de sesión único, configure el SSO de Azure AD como se describe en esta documentación.

1. Inicie sesión en el [portal MX3](https://portal.mx3.app). Para ir a la página de configuración de SSO, haga clic en configuración y, a continuación, haga clic en **Inicio de sesión único**.

    ![Captura de pantalla de la configuración de inicio de sesión único de MX3 Diagnostics Connector.](media/mx3-provisioning/sso-settings.png)


1. Desplácese hacia abajo para ver el token. Copie y guarde el token. Esta información la necesitará en el **Paso 5**.

    ![Captura de pantalla del token secreto de MX3 Diagnostics Connector para Azure AD.](media/mx3-provisioning/sso-settings-token.png)

## <a name="step-3-add-mx3-diagnostics-connector-from-the-azure-ad-application-gallery"></a>Paso 3. Agregue MX3 Diagnostics Connector desde la galería de aplicaciones de Azure AD

Agregue MX3 Diagnostics Connector desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en MX3 Diagnostics Connector. Si había configurado previamente MX3 Diagnostics Connector para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a MX3 Diagnostics Connector, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-mx3-diagnostics-connector"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en MX3 Diagnostics Connector 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en MX3 Diagnostics Connector en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mx3-diagnostics-connector-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para MX3 Diagnostics Connector en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Captura de pantalla que muestra la hoja de aplicación empresarial.](common/enterprise-applications.png)

1. En la lista de aplicaciones, seleccione **MX3 Diagnostics Connector**.

    ![El vínculo a MX3 Diagnostics Connector en la lista de aplicaciones](common/all-applications.png)

1. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de la pestaña Aprovisionamiento y dónde encontrarla.](common/provisioning.png)

1. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la pestaña Aprovisionamiento que muestra la selección automática.](common/provisioning-automatic.png)

1. En la sección **Credenciales de administrador**, escriba los valores de URL de inquilino https://scim.mx3.app) y de token secreto de MX3 Diagnostics Connector. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a MX3 Diagnostics Connector. Si la conexión no se establece, asegúrese de que la cuenta de MX3 Diagnostics Connector tenga permisos de administrador y pruebe otra vez.

    ![Captura de pantalla que muestra el campo de texto para escribir el token y la dirección URL de SCIM](common/provisioning-testconnection-tenanturltoken.png)

1. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Captura de pantalla de un campo de texto donde puede escribir un correo electrónico para obtener la notificación.](common/provisioning-notification-email.png)

1. Seleccione **Guardar**.

1. En la sección **Asignaciones**, seleccione **Sincronizar usuarios de Azure Active Directory con MX3 Diagnostics Connector**.

1. Revise los atributos de usuario que se sincronizan entre Azure AD y MX3 Diagnostics Connector en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de MX3 Diagnostics Connector con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de MX3 Diagnostics Connector admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

    |Atributo|Tipo|Compatible con el filtrado|
    |---|---|---|
    |userName|String|&check;
    |externalId|String|&check;
    |active|Boolean|
    |name.givenName|String|
    |name.familyName|String|

1. En la sección **Asignaciones**, seleccione **Sincronizar grupos de Azure Active Directory con MX3 Diagnostics Connector**.

1. Revise los atributos de grupo que se sincronizan entre Azure AD y MX3 Diagnostics Connector en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los grupos de MX3 Diagnostics Connector con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|Compatible con el filtrado|
      |---|---|---|
      |DisplayName|String|&check;
      |externalId|String|
      |members|Referencia|

1. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar el servicio de aprovisionamiento de Azure AD para MX3 Diagnostics Connector, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

1. Defina los usuarios o grupos que quiere que se aprovisionen en MX3 Diagnostics Connector; para ello, elija los valores deseados en **Ámbito**, en la sección **Configuración**.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

1. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
* Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
* Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="more-resources"></a>Más recursos

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)