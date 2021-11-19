---
title: 'Tutorial: Configuración de Real Links para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD para Real Links.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: a024c7db-ffe6-4fc9-a0ec-7075930bbf75
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: thwimmer
ms.openlocfilehash: bd15b2e58eb1959287b64397e4f99ba807c2be33
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495229"
---
# <a name="tutorial-configure-real-links-for-automatic-user-provisioning"></a>Tutorial: Configuración de Real Links para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Real Links y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona de forma automática usuarios y grupos en [Real Links](https://www.reallinks.io) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Real Links
> * Eliminación de usuarios de Real Links cuando dejen de necesitar acceso
> * Conservación de los atributos de usuario sincronizados entre Azure AD y Real Links
> * [Inicio de sesión único](real-links-tutorial.md) en Real Links (recomendado).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una suscripción de [Real Links](https://www.reallinks.io/): todos los niveles incluyen el aprovisionamiento automático de usuarios.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos se [asignan entre Azure AD y Real Links](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-real-links-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Real Links para que admita el aprovisionamiento con Azure AD

Para configurar el aprovisionamiento en una plataforma de Real Links, debe ponerse en contacto con el [equipo de soporte técnico de Real Links](mailto:support@reallinks.io) y solicitar los detalles de aprovisionamiento de SCIM-v2. Dichos detalles incluirán:

* Dirección URL del inquilino de la plataforma
* Su token secreto único

## <a name="step-3-add-real-links-from-the-azure-ad-application-gallery"></a>Paso 3. Incorporación de Real Links desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento en Real Links, agregue Real Links desde la galería de aplicaciones de Azure AD. Si ya ha configurado Real Links para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Real Links, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-real-links"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Real Links 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en Real Links en función de las asignaciones de usuarios y grupos de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-real-links-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Real Links en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

1. En la lista de aplicaciones, seleccione **Real Links**.

    ![Vínculo a Real Links en la lista de aplicaciones](common/all-applications.png)

1. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de URL de inquilino y de token secreto de Real Links. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Real Links. Si la conexión no se establece, asegúrese de que la cuenta de Real Links tenga permisos de administrador e inténtelo de nuevo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

1. Seleccione **Guardar**.

1. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Real Links** (Sincronizar usuarios de Azure Active Directory con Real Links).

1. Examine los atributos de usuario que se sincronizan entre Azure AD y Real Links en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con las cuentas de usuario de Real Links, con el fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que Real Links API admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|Requerido por Real Links|
   |---|---|---|---|
   |userName|String|&check;|&check;
   |active|Boolean||&check;
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:firstName|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:lastName|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:emailAddress|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:phoneNumber|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:title|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:preferredLanguage|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:countryCode|String|

1. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar el servicio de aprovisionamiento de Azure AD con Real Links, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

1. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea aprovisionar en Real Links.

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