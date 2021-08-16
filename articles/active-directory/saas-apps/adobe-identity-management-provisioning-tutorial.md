---
title: 'Tutorial: Configuración de Adobe Identity Management para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure AD para Adobe Identity Management.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2021
ms.author: Zhchia
ms.openlocfilehash: f9608a3a9e086cc6c03dc80f0666becbcbc06290
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "110688394"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>Tutorial: Configuración de Adobe Identity Management para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Adobe Identity Management y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en Adobe Identity Management mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Adobe Identity Management
> * Eliminación de usuarios en Adobe Identity Management cuando ya no requieren acceso
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y Adobe Identity Management
> * Aprovisionamiento de grupos y pertenencias a grupos en Adobe Identity Management
> * Inicio de sesión único en Adobe Identity Management (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un directorio federado en la [consola de administración de Adobe](https://adminconsole.adobe.com/) con dominios comprobados.
* Revise la [documentación de Adobe](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/add-azure-sync.ug.html) sobre el aprovisionamiento de usuarios. 

> [!NOTE]
> Si su organización usa la herramienta de sincronización de usuarios o una integración con UMAPI, primero debe pausar la integración. A continuación, agregue el aprovisionamiento automático de Azure AD para automatizar la administración de usuarios desde Azure Portal. Una vez que el aprovisionamiento automático de Azure AD esté configurado y en ejecución, puede eliminar completamente la herramienta de sincronización de usuarios o la integración de UMAPI.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos [asignar entre Azure AD y Adobe Identity Management](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>Paso 2. Configurar Adobe Identity Management para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en [la consola de administración de Adobe](https://adminconsole.adobe.com/). Vaya a **Settings > Directory Details > Sync** (Configuración > Detalles del directorio > Sincronizar). 

2. Haga clic en **Add Sync** (Agregar sincronización).

    ![Sumar](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. Seleccione **Sync users from Microsoft Azure** (Sincronizar usuarios desde Microsoft Azure) y haga clic en **Next** (Siguiente).

    ![Captura de pantalla que muestra la opción "Sync users from Microsoft Azure Active Directory" (Sincronizar usuarios desde Microsoft Azure Active Directory) seleccionada.](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. Copie y guarde la **dirección URL del inquilino** y el **token secreto**. Estos valores se escriben en los campos **Tenant URL** (Dirección URL del inquilino) y **Secret Token** (Token secreto) de la pestaña Provisioning (Aprovisionamiento) de la aplicación Adobe Identity Management en Azure Portal.

    ![Sync](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>Paso 3. Agregar Adobe Identity Management desde la galería de aplicaciones de Azure AD

Agregue Adobe Identity Management desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en Adobe Identity Management. Si previamente ha configurado Adobe Identity Management para SSO, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Adobe Identity Management, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Adobe Identity Management 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios de Adobe Identity Management en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Adobe Identity Management**.

    ![Vínculo a Adobe Identity Management en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la dirección URL del inquilino de Adobe Identity Management y el token secreto recuperados anteriormente en el paso 2. Haga clic en **Probar conexión** para asegurarse de que Azure AD pueda conectarse a Adobe Identity Management. Si se produce un error de conexión, asegúrese de que la cuenta de Adobe Identity Management tenga permisos de administrador y vuelva a intentarlo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Adobe Identity Management** (Sincronizar usuarios de Azure Active Directory con Adobe Identity Management).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Adobe Identity Management en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para hacer coincidir las cuentas de usuario de Adobe Identity Management a fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Adobe Identity Management admite el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |active|Boolean|
   |addresses[type eq "work"].country|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:Adobe:2.0:User:emailAliases|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to Adobe Identity Management** (Sincronizar grupos de Azure Active Directory con Adobe Identity Management).

11. Revise los atributos de grupo que se sincronizan entre Azure AD y Adobe Identity Management en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para hacer coincidir los grupos de Adobe Identity Management a fin de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|
      |---|---|
      |DisplayName|String|
      |members|Referencia|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Adobe Identity Management, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere que se aprovisionen en Adobe Identity Management.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
