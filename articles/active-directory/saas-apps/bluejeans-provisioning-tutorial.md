---
title: 'Tutorial: Configuración de BlueJeans para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fc1518b169f3da8b34e97c999cd23e201aea59ce
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015679"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configuración de BlueJeans para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en BlueJeans y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona automáticamente a los usuarios en [BlueJeans](https://www.bluejeans.com) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en BlueJeans
> * Eliminación de usuarios de BlueJeans cuando ya no necesiten acceso
> * Mantenimiento de la sincronización de los atributos de usuario entre Azure AD y BlueJeans
> * [Inicio de sesión único](./bluejeans-tutorial.md) en BlueJeans (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un inquilino de BlueJeans con el plan [My Company](https://www.bluejeans.com) o superior habilitado.
* Una cuenta de usuario de BlueJeans con permisos de administrador.
* Aprovisionamiento de SCIM habilitado en BlueJeans Enterprise.

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API de BlueJeans](https://BlueJeans.github.io/developer), que está disponible para los equipos de BlueJeans con el plan Standard o uno superior.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se van a [asignar entre Azure AD y BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de BlueJeans para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en la consola de administración de BlueJeans. Vaya a Group Settings > Security (Configuración de grupo > Seguridad).
2. Seleccione **Single Sign On** (Inicio de sesión único) y **Configure Now** (Configurar ahora).

    ![ahora](./media/bluejeans-provisioning-tutorial/configure.png)

3. En la ventana **Provision & Integration** (Aprovisionamiento e integración), seleccione **Create and manage user accounts through IDP** (Crear y administrar cuentas de usuario mediante IDP) y haga clic en **GENERATE TOKEN** (GENERAR TOKEN).

    ![generate](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Copie y guarde el token. 
5. La dirección URL del inquilino de BlueJeans es `https://api.bluejeans.com/v2/scim`. La **dirección URL del inquilino** y el **token de secreto** del paso anterior se especificarán en la pestaña Aprovisionamiento de la aplicación BlueJeans en Azure Portal.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de BlueJeans desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de BlueJeans, agregue esta aplicación desde la galería de aplicaciones de Azure AD. Si ha configurado previamente BlueJeans para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario. Si elige delimitar quién se aprovisionará en la aplicación en función de la asignación, puede usar los [pasos](../manage-apps/assign-user-or-group-access-portal.md) siguientes para asignar usuarios a la aplicación. Si elige delimitar quién se aprovisionará en función únicamente de los atributos del usuario, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios a BlueJeans, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios asignados, puede controlarlo asignando uno o dos usuarios a la aplicación. Cuando el ámbito se establece en todos los usuarios, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en BlueJeans

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios en TestApp en función de las asignaciones de usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para BlueJeans en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **BlueJeans**.

    ![Vínculo a BlueJeans en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de URL de inquilino y token de secreto de BlueJeans recuperados en el paso 2. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a BlueJeans. Si la conexión no se establece, asegúrese de que la cuenta de BlueJeans tiene permisos de administrador de BlueJeans y pruebe otra vez.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Users to BlueJeans** (Sincronizar usuarios de Azure Active Directory con BlueJeans).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y BlueJeans en la sección **Attribute Mapping** (Asignaciones de atributos). Los atributos seleccionados como propiedades de **Matching**(Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de BlueJeans con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de BlueJeans admita el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

|Atributo|Tipo|Compatible con el filtrado|
|---|---|---|
|userName|String|&check;|
|active|Boolean|
|title|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para BlueJeans, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios que desea que se aprovisionen en BlueJeans.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitaciones del conector

* Bluejeans no permite valores de userNames que superen los 30 caracteres.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
