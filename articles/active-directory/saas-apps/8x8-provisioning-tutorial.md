---
title: 'Tutorial: Configuración de 8x8 para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y cancelar el aprovisionamiento de forma automática de las cuentas de usuario de Azure AD para 8x8.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: thwimmer
ms.openlocfilehash: c055b8a0bd11d6f1e1a28e053d64aaf574ca2880
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765158"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Tutorial: Configuración de 8x8 para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en la consola de administración de 8x8 y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en [8x8](https://www.8x8.com) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Crear usuarios en 8x8
> * Quitar usuarios de 8x8 cuando ya no necesiten acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y 8x8
> * [Inicio de sesión único](./8x8virtualoffice-tutorial.md) en 8x8 (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global).
* Una suscripción de serie 8x8 X de cualquier nivel.
* Una cuenta de usuario de 8x8 con permisos de administrador en la [consola de administración](https://vo-cm.8x8.com).
* Ya se ha configurado el [Inicio de sesión único con Azure AD](./8x8virtualoffice-tutorial.md).

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y 8x8](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de 8x8 para admitir el aprovisionamiento con Azure AD

Esta sección le guía por los pasos para configurar 8x8 para asistir el aprovisionamiento con Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-admin-console"></a>Para configurar un token de acceso de aprovisionamiento de usuarios en la consola de administración de 8x8:

1. Iniciar sesión en la [Consola de administración](https://admin.8x8.com). Seleccione **Administración de identidades**.

   ![Administración](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Haga clic en el vínculo **Mostrar información de aprovisionamiento de usuarios** para generar un token.

   ![Presentación](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Copie los valores de **dirección URL de 8x8** y **Token de API de 8x8**. Estos valores se escribirán en los campos **Dirección URL de inquilino** y **Token secreto**, respectivamente, de la pestaña Aprovisionamiento de la aplicación 8x8 en Azure Portal.

   ![Token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de 8x8 desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de 8x8, agregue 8x8 desde la galería de aplicaciones de Azure AD. Si ha configurado previamente 8x8 para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Esta es la opción más sencilla y la mayoría de las personas la usan.

Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a para empezar a administrar el aprovisionamiento en 8x8, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en 8x8 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en 8x8 en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para 8x8 en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Hoja Todas las aplicaciones](./media/8x8-provisioning-tutorial/all-applications.png)

2. En la lista de aplicaciones, seleccione **8x8**.

    ![El vínculo 8x8 en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**. Haga clic en **Introducción**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

   ![Hoja Introducción](./media/8x8-provisioning-tutorial/get-started.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, copie la **dirección URL de 8x8** desde la consola de administración al campo **URL de inquilino**. Copie el **Token de API de 8x8** desde la consola de administración en el campo **Token secreto**. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a 8x8. Si la conexión no se establece, asegúrese de que la cuenta de 8x8 tiene permisos de administrador y pruebe de nuevo.

    ![Captura de pantalla que muestra el cuadro de diálogo Credenciales de administrador, en el que se puede especificar el URL de inquilino y el secreto de inquilino.](./media/8x8-provisioning-tutorial/provisioning.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Aprovisionar usuarios de Azure Active Directory**.

9. Examine los atributos de usuario que se sincronizan entre Azure AD y 8x8 en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con las cuentas del usuario en 8x8 a fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de 8x8 admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Notas|
   |---|---|---|
   |userName|String|Establecer el nombre de usuario y el Id. de Federación|
   |externalId|String||
   |active|Boolean||
   |title|String||
   |emails[type eq "work"].value|String||
   |name.givenName|String||
   |name.familyName|String||
   |phoneNumbers[type eq "mobile"].value|String|Número de contacto personal|
   |phoneNumbers[type eq "work"].value|String|Número de contacto personal|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|String|No se puede actualizar después de crear el usuario|
   |locale|String|No asignado de forma predeterminada|
   |timezone|String|No asignado de forma predeterminada|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para 8x8, cambie la opción **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en 8x8.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente y los que no.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)