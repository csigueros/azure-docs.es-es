---
title: 'Tutorial: Configuración de GroupTalk para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo aprovisionar y desaprovisionar automáticamente cuentas de usuario de Azure AD para GroupTalk.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/18/2020
ms.author: thwimmer
ms.openlocfilehash: ea6f1218c368c7056134ae5ee25ab5b077977bd6
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195833"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Tutorial: Configuración de GroupTalk para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en GroupTalk y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en [GroupTalk](https://www.grouptalk.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en GroupTalk
> * Eliminación de usuarios de GroupTalk cuando ya no necesiten acceso
> * Sincronización de los atributos de usuario entre Azure AD y GroupTalk
> * Aprovisionamiento de grupos y pertenencias a grupos en GroupTalk

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta de usuario de GroupTalk con permisos de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se van a [asignar entre Azure AD y GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de GroupTalk para admitir el aprovisionamiento con Azure AD

1. Póngase en contacto con el servicio de soporte técnico de GroupTalk en support@grouptalk.com con el **nombre de inquilino** y el **ID** que desea integrar con Azure AD.
2. Cuando se le haya notificado que la configuración necesaria para la integración de Azure AD está lista, inicie sesión como administrador de GroupTalk y vaya a la vista de la organización. 
3. Debería estar visible un elemento de configuración de la integración de Azure AD. Haga clic en él para comprobar el **nombre de inquilino** y el **ID** para obtener un token **JWT (token secreto)** . 
4. La dirección URL del inquilino de GroupTalk es `https://api.grouptalk.com/api/scim/`. La **dirección URL del inquilino** y el **token secreto** recuperados en el paso anterior se especificarán en la pestaña Aprovisionamiento de la aplicación GroupTalk en Azure Portal. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de GroupTalk desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de GroupTalk, agregue **GroupTalk** desde la galería de aplicaciones de Azure AD.

1. Haga clic en el botón **Sign up for GroupTalk** (Registrarse en GroupTalk), que le dirigirá a la aplicación administrativa GroupTalk.
2. Si ya ha iniciado sesión en GroupTalk, cierre la sesión para ir a la pantalla de inicio de sesión. Seleccione la pestaña Azure AD y haga clic en el botón **Sign in** (Iniciar sesión).

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Inicie sesión con su cuenta administrativa de AD y acepte los derechos de acceso de la aplicación GroupTalk. Recibirá un mensaje de error que indica que el usuario no está presente. Esto es normal, ya que el usuario no se ha aprovisionado todavía en GroupTalk, pero ya ha agregado GroupTalk al inquilino.
4. Vuelva a Azure Portal y compruebe que **GroupTalk** se ha agregado ahora a las aplicaciones empresariales.

Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a GroupTalk, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en GroupTalk 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para GroupTalk en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **GroupTalk**.

    ![Vínculo a GroupTalk en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales del administrador**, escriba la dirección URL del inquilino de GroupTalk y el Token secreto recuperados anteriormente en el paso 2. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a GroupTalk. Si la conexión no se establece, asegúrese de que la cuenta de GroupTalk tiene permisos de administrador y pruebe de nuevo. Siempre puede obtener un nuevo token secreto tal y como se describe en el paso 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Sincronizar usuarios de Azure Active Directory con GroupTalk**.

9. Revise los atributos de usuario que se sincronizan entre Azure AD y GroupTalk en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de GroupTalk con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de GroupTalk admite el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |phoneNumbers[type eq "mobile"].value|String|&check;|
   |emails[type eq "work"].value|String|&check;|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label1|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label2|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label3|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label4|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label5|String|


10. En la sección **Asignaciones**, seleccione **Sincronizar grupos de Azure Active Directory con GroupTalk**.

11. Revise los atributos de grupo que se sincronizan entre Azure AD y GroupTalk en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer coincidencias con los grupos de GroupTalk con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|Compatible con el filtrado|
      |---|---|---|
      |DisplayName|String|&check;|
      |members|Referencia|
      |externalId|String|      
      |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:Group:description|String|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para GroupTalk, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en GroupTalk.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).
4. Puede ponerse en contacto con el equipo de soporte técnico de GroupTalk para los registros de aprovisionamiento adicionales configurados como informes personalizados dentro de la administración de GroupTalk. Pueden proporcionar sugerencias adicionales sobre el motivo por el que los usuarios y los grupos no se aprovisionan correctamente.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)