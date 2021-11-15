---
title: 'Tutorial: Configuración de Appaegis Isolation Access Cloud para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD para Appaegis Isolation Access Cloud.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: c845e98a-6fcd-4285-94b7-a72a2175ca7e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: thwimmer
ms.openlocfilehash: c1700980368095e31da1d41b31ddccdf7c3ea89b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894912"
---
# <a name="tutorial-configure-appaegis-isolation-access-cloud-for-automatic-user-provisioning"></a>Tutorial: Configuración de Appaegis Isolation Access Cloud para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe seguir en Appaegis Isolation Access Cloud y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona usuarios y grupos de manera automática en [Appaegis Isolation Access Cloud](https://www.appaegis.com) mediante su servicio de aprovisionamiento. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="supported-capabilities"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Appaegis Isolation Access Cloud
> * Eliminación de usuarios de Appaegis Isolation Access Cloud cuando ya no necesiten acceso
> * Sincronización mantenida de los atributos de usuario entre Azure AD y Appaegis Isolation Access Cloud
> * [Inicio de sesión único](appaegis-isolation-access-cloud-tutorial.md) en Appaegis Isolation Access Cloud (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global).
* Una cuenta de [Appaegis Cloud](https://www.appaegis.com) con un nivel de suscripción Professional. 
* Una cuenta de usuario de Appaegis Cloud con permisos de **administrador global**.


## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se van a [asignar entre Azure AD y Appaegis Isolation Access Cloud](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-appaegis-isolation-access-cloud-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Appaegis Isolation Access Cloud para admitir el aprovisionamiento con Azure AD

1. Habilite el [inicio de sesión único](appaegis-isolation-access-cloud-tutorial.md) con Appaegis Cloud.
2. En la página **Identity Provider Details** (Detalles del proveedor de identidades), la cual enumera la dirección URL de ACS y el identificador de entidad, encontrará la dirección URL de SCIM y el token de SCIM.

## <a name="step-3-add-appaegis-isolation-access-cloud-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de Appaegis Isolation Access Cloud desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de Appaegis Isolation Access Cloud, agregue Appaegis Isolation Access Cloud desde la galería de aplicaciones de Azure AD. Si ha configurado previamente Appaegis Isolation Access Cloud para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Appaegis Isolation Access Cloud, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-appaegis-isolation-access-cloud"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Appaegis Isolation Access Cloud 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en una aplicación de prueba en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-appaegis-isolation-access-cloud-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Appaegis Isolation Access Cloud en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Appaegis Isolation Access Cloud**.

    ![Vínculo a Appaegis Isolation Access Cloud en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña de aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña Aprovisionamiento](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de la URL de inquilino y el token de secreto de Appaegis Isolation Access Cloud. Haga clic en **Prueba de conexión** para asegurarse de que Azure AD puede conectarse a Appaegis Isolation Access Cloud. Si la conexión no se establece, asegúrese de que la cuenta de Appaegis Isolation Access Cloud tiene permisos de administrador y vuelva a intentarlo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Appaegis Isolation Access Cloud** (Sincronizar usuarios de Azure Active Directory con Appaegis Isolation Access Cloud).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Appaegis Isolation Access Cloud en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Appaegis Isolation Access Cloud a fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Appaegis Isolation Access Cloud admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

    |Atributo|Tipo|Compatible con el filtrado|
    |---|---|---|
    |userName|String|&check;
    |active|Boolean|
    |DisplayName|String|
    |name.givenName|String|
    |name.familyName|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to Contoso** (Sincronizar grupos de Azure Active Directory con Contoso).

11. Revise los atributos de grupo que se sincronizan entre Azure AD y Contoso en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer correspondencia con los grupos de Contoso a fin de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|Compatible con el filtrado|
      |---|---|---|
      |DisplayName|String|&check;
      |members|Referencia|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Appaegis Isolation Access Cloud, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere que se aprovisionen en Appaegis Isolation Access Cloud.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Más recursos

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
