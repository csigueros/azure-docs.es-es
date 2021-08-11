---
title: 'Tutorial: Configuración de Snowflake para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente cuentas de usuario de Snowflake.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: thwimmer
ms.openlocfilehash: c7eced7fb6c073eece1edbee93da0d9f33e3ed27
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690298"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configuración de Snowflake para el aprovisionamiento automático de usuarios

Este tutorial muestra los pasos que debe realizar en Snowflake y Azure Active Directory (Azure AD) para configurar este último con el objetivo de aprovisionar y desaprovisionar automáticamente usuarios y grupos en [Snowflake](https://www.Snowflake.com/pricing/). Para obtener información importante acerca de lo que hace este servicio, cómo funciona y las preguntas más frecuentes, consulte [¿Qué es el aprovisionamiento automatizado de usuarios de aplicaciones SaaS en Azure AD?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Este conector está actualmente en versión preliminar pública. Para más información sobre las condiciones de uso, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Crear usuarios en Snowflake
> * Quitar usuarios de Snowflake cuando ya no necesiten acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y Snowflake
> * Aprovisionar grupos y pertenencias a grupos en Snowflake
> * Permitir el [inicio de sesión único](./snowflake-tutorial.md) en Snowflake (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md)
* Una cuenta de usuario de Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (como administrador de aplicaciones, administrador de aplicaciones en la nube, propietario de la aplicación o administrador global).
* [Un inquilino de Snowflake](https://www.Snowflake.com/pricing/).
* Una cuenta de usuario de Snowflake con permisos de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1: Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos quiere [asignar entre Azure AD y Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Paso 2: Configuración de Snowflake para admitir el aprovisionamiento con Azure AD

Antes de configurar Snowflake para el aprovisionamiento automático de usuarios con Azure AD, debe habilitar el aprovisionamiento de System for Cross-domain Identity Management en Snowflake.

1. Inicie sesión en la consola de administración de Snowflake. Escriba la consulta siguiente en la hoja de cálculo resaltada y, a continuación, seleccione **Run** (Ejecutar).

   ![Captura de pantalla de la consola de administración de Snowflake con la consulta y el botón Run (Ejecutar).](media/Snowflake-provisioning-tutorial/image00.png)
    
   ```
   use role accountadmin;
   
   create or replace role aad_provisioner;
   grant create user on account to aad_provisioner;
   grant create role on account to aad_provisioner;
   grant role aad_provisioner to role accountadmin;
   create or replace security integration aad_provisioning type=scim scim_client=azure run_as_role='AAD_PROVISIONER';
   
   select SYSTEM$GENERATE_SCIM_ACCESS_TOKEN('AAD_PROVISIONING');
   ```

1.  Se genera un token de acceso de SCIM para el inquilino de Snowflake. Para recuperarlo, seleccione el vínculo resaltado en la captura de pantalla siguiente.

   ![Captura de pantalla de una hoja de cálculo en la interfaz de usuario de Snowflake con el token de acceso de SCIM resaltado.](media/Snowflake-provisioning-tutorial/image01.png)

1. Copie el valor del token generado y seleccione **Done** (Listo). Este valor se escribe en el cuadro **Secret Token** (Token secreto) de la pestaña **Provisioning** (Aprovisionamiento) de la aplicación Snowflake en Azure Portal.

   ![Captura de pantalla de la sección de detalles que muestra el token copiado en el campo de texto y la opción Listo resaltada.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Paso 3: Adición de Snowflake desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de Snowflake, agregue Snowflake desde la galería de aplicaciones de Azure AD. Si ha configurado previamente Snowflake para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda crear una aplicación independiente al probar inicialmente la integración. [Más información sobre cómo agregar una aplicación desde la galería](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4: Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se va a aprovisionar en función de las asignaciones de la aplicación o de los atributos del usuario o grupo. Si elige determinar el ámbito de los usuarios a los que se aprovisionará la aplicación en función de la asignación, puede usar los [pasos para asignar usuarios y grupos a la aplicación](../manage-apps/assign-user-or-group-access-portal.md). Si elige determinar el ámbito de los usuarios a los que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede [usar un filtro de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tenga en cuenta estas sugerencias:

* Al asignar usuarios y grupos a Snowflake, debe seleccionar un rol que no sea Acceso predeterminado. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de la aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando se establece el ámbito de aprovisionamiento en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Paso 5: Configuración del aprovisionamiento automático de usuarios en Snowflake 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios y grupos en Snowflake. Puede basar la configuración en las asignaciones de usuarios y grupos de Azure AD.

Para configurar el aprovisionamiento automático de usuarios para Snowflake en Azure AD, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Captura de pantalla que muestra el panel de aplicaciones empresariales.](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Snowflake**.

    ![Captura de pantalla que muestra una lista de aplicaciones.](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la dirección URL base de SCIM 2.0 y el token de autenticación que recuperó anteriormente en los campos **URL de inquilino** y **Token secreto** respectivamente. 

   Para asegurarse de que Azure AD puede conectarse a Snowflake, seleccione **Probar conexión**. Si la conexión no se establece, asegúrese de que la cuenta de Snowflake tiene permisos de administrador e inténtelo de nuevo.

    ![Captura de pantalla que muestra los cuadros de la URL de inquilino y el token secreto, con el botón Probar conexión.](common/provisioning-testconnection-tenanturltoken.png)

6. En el cuadro **Dirección de correo electrónico para notificaciones**, escriba la dirección de correo electrónico de la persona o grupo que deben recibir las notificaciones de error de aprovisionamiento. A continuación, active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Captura de pantalla que muestra los cuadros del correo electrónico de notificación.](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Snowflake** (Sincronizar usuarios de Azure Active Directory con Snowflake).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Snowflake en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Snowflake con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |active|Boolean|
   |DisplayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to Snowflake** (Sincronizar grupos de Azure Active Directory con Snowflake).

11. Revise los atributos de grupo que se sincronizan entre Azure AD y Snowflake en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los grupos de Snowflake con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    |Atributo|Tipo|
    |---|---|
    |DisplayName|String|
    |members|Referencia|

12. Para configurar los filtros de ámbito, consulte las instrucciones que se describen en [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Snowflake, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

     ![Captura de pantalla que muestra el estado de aprovisionamiento activado.](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Snowflake. 

    Si esta opción no está disponible, configure los campos obligatorios en **Credenciales de administrador**, seleccione **Guardar** y actualice la página. 

     ![Captura de pantalla que muestra las opciones del ámbito de aprovisionamiento.](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

     ![Captura de pantalla del botón para guardar una configuración de aprovisionamiento.](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las sincronizaciones posteriores. Las sincronizaciones posteriores se producen aproximadamente cada 40 minutos, mientras se ejecuta el servicio de aprovisionamiento de Azure AD.

## <a name="step-6-monitor-your-deployment"></a>Paso 6: Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

- Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente y los que no.
- Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
- Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. [Más información sobre los estados de cuarentena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitaciones del conector

Los tokens de SCIM que genera Snowflake expiran en 6 meses. Tenga en cuenta que tiene que actualizarlos antes de que expiren para que las sincronizaciones de aprovisionamiento sigan funcionando. 

## <a name="troubleshooting-tips"></a>Sugerencias de solución de problemas

El servicio de aprovisionamiento de Azure AD funciona actualmente en [intervalos IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges) concretos. Si es necesario, puede restringir otros intervalos IP y agregar estos intervalos IP concretos a la lista de permitidos de la aplicación. Esta técnica permitirá el flujo de tráfico desde el servicio de aprovisionamiento de Azure AD a la aplicación.

## <a name="change-log"></a>Registro de cambios

* 21/07/2020: se ha habilitado la eliminación temporal para todos los usuarios (a través del atributo active).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
