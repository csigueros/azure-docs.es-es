---
title: 'Tutorial: Configuración de BlogIn para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD en BlogIn.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 4b2ef46c-97a1-450d-bbc8-b2fa76280219
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: thwimmer
ms.openlocfilehash: 90a0184029fb9f54977f53fae40a4ee896fba238
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764659"
---
# <a name="tutorial-configure-blogin-for-automatic-user-provisioning"></a>Tutorial: Configuración de BlogIn para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en BlogIn y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [BlogIn](https://blogin.co/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en BlogIn
> * Eliminación de usuarios de BlogIn cuando ya no necesiten acceso
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y BlogIn
> * Aprovisionamiento de grupos y pertenencias a grupos en BlogIn
> * [Inicio de sesión único](./blogin-tutorial.md) en BlogIn (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta de usuario en BlogIn con el rol Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se van a [asignar entre Azure AD y BlogIn](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-blogin-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de BlogIn para admitir el aprovisionamiento con Azure AD

Para configurar el aprovisionamiento de usuarios en **BlogIn**, inicie sesión en su cuenta de BlogIn y siga estos pasos:

1. Vaya a **Settings** > **User Authentication** > **Configure SSO & User provisioning** (Configuración > Autenticación de usuarios > Configurar SSO y el aprovisionamiento de usuarios).
2. Cambie a la pestaña **User provisioning** (Aprovisionamiento de usuarios) y cambie el estado de aprovisionamiento de usuarios a **On** (Activado).
3. Haga clic en el botón **Save changes** (Guardar cambios). La primera vez que se guarde, se generará el **Secret (Bearer) token** [Token de secreto (portador)].
4. Copie los valores de **Base (Tenant) URL** [URL base (inquilino)] y **Secret (Bearer) token** [Token de secreto (portador)]. Estos valores se escriben en los campos URL de inquilino y Token de secreto de la pestaña Aprovisionamiento de la aplicación BlogIn en Azure Portal.

Para obtener una explicación más detallada de cómo configurar el aprovisionamiento de usuarios en BlogIn, consulte [Configuración del aprovisionamiento de usuarios a través de SCIM](https://blogin.co/blog/set-up-user-provisioning-via-scim-254/). Póngase en contacto con el [equipo de soporte técnico de BlogIn](mailto:support@blogin.co) si tiene alguna pregunta o necesita ayuda.

## <a name="step-3-add-blogin-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de BlogIn desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de BlogIn, agregue BlogIn desde la galería de aplicaciones de Azure AD. Si ha configurado previamente BlogIn para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a BlogIn, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-blogin"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en BlogIn 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-blogin-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para BlogIn en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **BlogIn**.

    ![Vínculo a BlogIn en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de URL de inquilino y Token de secreto de BlogIn. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Clarizen. Si la conexión no se establece, asegúrese de que la cuenta de Clarizen tiene permisos de administrador de Clarizen y pruebe otra vez.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to BlogIn** (Sincronizar usuarios de Azure Active Directory con BlogIn).

9. Examine los atributos de grupo que se sincronizan entre Azure AD y BlogIn en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para buscar coincidencias con las cuentas de usuario de BlogIn con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de BlogIn admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Boolean|
   |title|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "work"].value|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to BlogIn** (Sincronizar grupos de Azure Active Directory con BlogIn).

11. Examine los atributos de grupo que se sincronizan entre Azure AD y BlogIn en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con los grupos en BlogIn a fin de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|
      |---|---|
      |DisplayName|String|
      |members|Referencia|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para BlogIn, cambie la opción de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Seleccione los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere aprovisionar en BlogIn.

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