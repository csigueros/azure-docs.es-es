---
title: 'Tutorial: Configuración de Bizagi Studio for Digital Process Automation para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure AD en Bizagi Studio for Digital Process Automation.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: thwimmer
ms.openlocfilehash: ac1a38662172e12cebfcab952ca076062f03f0ec
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324523"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Tutorial: Configuración de Bizagi Studio for Digital Process Automation para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en Bizagi Studio for Digital Process Automation y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura para hacerlo, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Bizagi Studio for Digital Process Automation
> * Eliminación de usuarios de Bizagi Studio for Digital Process Automation cuando ya no necesitan acceso
> * Sincronización de los atributos de usuario entre Azure AD y Bizagi Studio for Digital Process Automation
> * [Inicio de sesión único](./bizagi-studio-for-digital-process-automation-tutorial.md) en Bizagi Studio for Digital Process Automation (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md). 
* Una cuenta de usuario en Azure AD con [permisos](../roles/permissions-reference.md) para configurar el aprovisionamiento. Algunos ejemplos son el administrador de aplicaciones, el administrador de aplicaciones en la nube, el propietario de la aplicación o el administrador global. 
* Bizagi Studio for Digital Process Automation versión 11.2.4.2X o posterior.

## <a name="plan-your-provisioning-deployment"></a>Planeación de la implementación de aprovisionamiento
Para realizar el planeamiento, siga estos pasos:

1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos [asignar entre Azure AD y Bizagi Studio for Digital Process Automation](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Configuración para admitir el aprovisionamiento con Azure AD
Para configurar Bizagi Studio for Digital Process Automation de forma que admita el aprovisionamiento con Azure AD, siga estos pasos:

1. Inicie sesión en el portal de trabajo como usuario con **permisos de administrador**.

2. Vaya a **Admin** > **Security** > **OAuth 2 Applications** (Administración > Seguridad > Aplicaciones de OAuth2).

   ![Captura de pantalla de Bizagi, con OAuth 2 Applications (Aplicaciones de OAuth 2) resaltada.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Seleccione **Agregar**.
4. En **Grant Type** (Tipo de concesión), seleccione **Bearer token** (Token de portador). En **Allowed Scope** (Ámbito permitido), seleccione **API** y **USER SYNC** (Sincronización de usuarios). Después, seleccione **Guardar**.

   ![Captura de pantalla de la aplicación de registro, con Grant Type (Tipo de concesión) y Allowed Scope (Ámbito permitido) resaltadas.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Copie y guarde el valor de **Client Secret** (Secreto de cliente). En Azure Portal, en la aplicación Bizagi Studio for Digital Process Automation, en la pestaña **Aprovisionamiento**, el valor del secreto de cliente se especifica en el campo **Token secreto**.

   ![Captura de pantalla de OAuth con Secreto de cliente resaltado.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Incorporación de la aplicación desde la galería de Azure AD

Para empezar a administrar el aprovisionamiento en Bizagi Studio for Digital Process Automation, agregue la aplicación desde la galería de aplicaciones de Azure AD. Si ha configurado anteriormente Bizagi Studio for Digital Process Automation para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, cuando se está probando inicialmente la integración, se debe crear otra aplicación. Para más información, consulte [Inicio rápido: Incorporación de una aplicación al inquilino de Azure Active Directory (Azure AD)](../manage-apps/add-application-portal.md) 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definir quién está en el ámbito de aprovisionamiento 

Con el servicio de aprovisionamiento de Azure AD, puede determinar quién se aprovisiona en función de la asignación a la aplicación o de los atributos del usuario, el grupo o ambos. Si el ámbito se basa en la asignación, consulte los pasos descritos en [Asignación o desasignación de usuarios y grupos para una aplicación mediante Graph API](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si el ámbito se basa únicamente en los atributos del usuario o el grupo, puede utilizar un filtro de ámbito. Para más información, consulte [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tenga en cuenta los siguientes puntos sobre la definición del ámbito:

* Al asignar usuarios y grupos a Bizagi Studio for Digital Process Automation, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados de forma definitiva en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando se establece el ámbito de aprovisionamiento en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="configure-automatic-user-provisioning"></a>Configuración del aprovisionamiento automático de usuarios 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios y grupos. Esto lo hace en la aplicación de prueba, en función de las asignaciones de usuarios y grupos en Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Configuración del aprovisionamiento automático de usuarios para Bizagi Studio for Digital Process Automation en Azure AD

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Captura de pantalla de Azure Portal, con los elementos Aplicaciones empresariales y Todas las aplicaciones resaltados.](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Bizagi Studio for Digital Process Automation**.

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de Administrar, con Aprovisionamiento resaltado.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla del control Modo de aprovisionamiento, con Automático resaltado.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la dirección URL y el token secreto de Bizagi Studio for Digital Process Automation. 

      * **URL de inquilino:** escriba el punto de conexión de BizAgi SCIM con la siguiente estructura: `<Your_Bizagi_Project>/scim/v2/`.
         Por ejemplo: `https://my-company.bizagi.com/scim/v2/`.

      * **Token secreto:** este valor se recupera del paso descrito anteriormente en este artículo.

      Para asegurarse de que Azure AD puede conectarse a Bizagi Studio for Digital Process Automation, seleccione **Probar conexión**. Si la conexión no se establece, asegúrese de que la cuenta de Bizagi Studio for Digital Process Automation tenga permisos de administrador e inténtelo de nuevo.

   ![Captura de pantalla de Credenciales de administrador, con Probar conexión resaltada.](common/provisioning-testconnection-tenanturltoken.png)

6. En **Correo electrónico de notificación**, escriba la dirección de correo electrónico de la persona o grupo que deben recibir las notificaciones de error de aprovisionamiento. Seleccione la opción **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Captura de pantalla de opciones de Correo electrónico de notificación.](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Bizagi Studio for Digital Process Automation** (Sincronizar usuarios de Azure Active Directory con Bizagi Studio for Digital Process Automation).

9. En la sección **Asignación de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y Bizagi Studio for Digital Process Automation. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Bizagi Studio for Digital Process Automation con el objetivo de realizar operaciones de actualización. Si cambia el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Bizagi Studio for Digital Process Automation admita el filtrado de usuarios en función de ese atributo. Para confirmar los cambios, seleccione **Guardar**.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   Para agregar atributos de extensión personalizados, desplácese a **Mostrar opciones avanzadas > Edit attribute list for Bizagi** (Editar lista de atributos para Bizagi). Los atributos de extensión personalizados deben tener el prefijo **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:** . Por ejemplo, si el atributo de extensión personalizado es **IdentificationNumber**, el atributo debe agregarse como **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:IdentificationNumber**. Para confirmar los cambios, seleccione **Guardar**.
   
    ![Edite la lista de atributos.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Puede encontrar más información sobre cómo agregar atributos personalizados en [Personalización de atributos de la aplicación](../app-provisioning/customize-application-attributes.md).

> [!NOTE]
> Solo se admiten las propiedades de tipo básico (por ejemplo, String, Integer, Boolean, DateTime, etc.). Todavía no se admiten las propiedades vinculadas a tablas paramétricas o a varios tipos.

10. Para configurar los filtros de ámbito, consulte el [tutorial sobre el filtro de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Bizagi Studio for Digital Process Automation, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Captura de pantalla del botón de alternancia de estado de aprovisionamiento.](common/provisioning-toggle-on.png)

12. Defina los usuarios y grupos que quiera aprovisionar en Bizagi Studio for Digital Process Automation. En la sección **Configuración**, elija los valores deseados en **Ámbito**.

    ![Captura de pantalla de las opciones de Ámbito.](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

    ![Captura de pantalla del control Guardar.](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="monitor-your-deployment"></a>Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

- Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente y los que no.
- Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
- Si la configuración de aprovisionamiento está en mal estado, la aplicación pasará a estar en cuarentena. Para más información, consulte [Aprovisionamiento de aplicaciones en el estado de cuarentena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)