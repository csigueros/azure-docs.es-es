---
title: 'Tutorial: Configuración de BLDNG APP para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD en BLDNG APP.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 5ccc1176-c244-4003-8486-67586bcdf317
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: thwimmer
ms.openlocfilehash: f9b8c1501ecb3aba941e412ca1f0583a6df23f38
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970506"
---
# <a name="tutorial-configure-bldng-app-for-automatic-user-provisioning-in-bldngai"></a>Tutorial: Configuración de BLDNG APP para el aprovisionamiento automático de usuarios en BLDNG. AI

En este tutorial, se describen los pasos que debe realizar en BLDNG APP y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [BLDNG APP](https://dashboard.bldng.ai/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en BLDNG.AI. 
> * Eliminación de usuarios en BLDNG.AI cuando dejen de necesitar acceso.
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y BLDNG.AI.
> * Aprovisionamiento de grupos y pertenencias a grupos en BLDNG.AI.
> * [Inicio de sesión único](../manage-apps/add-application-portal-setup-oidc-sso.md) en BLDNG.AI (se recomienda).


## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un contrato de [BLDNG.AI](https://dashboard.bldng.ai/).
* Una invitación de BLDNG.IA para habilitar el aprovisionamiento de usuarios y usar BLDNG.AI.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos se van a [asignar entre Azure AD y BLDNG.AI](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-bldng-app-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de BLDNG.AI para admitir el aprovisionamiento con Azure AD

* Para configurar el aprovisionamiento de usuarios, los grupos de usuarios y las pertenencias a grupos desde Azure, necesitará un contrato de BLDNG.AI y un inquilino.
* Para lograr un contrato, póngase en contacto con un [representante de ventas](mailto:salg@bldng.ai). No podrá continuar ni usar BLDNG APP si no existe un contrato.
* Si ya tiene un contrato activo pero necesita habilitar solo el aprovisionamiento de usuarios, póngase en contacto directamente con el [soporte técnico](mailto:support@bldng.ai).

Cuando se haya establecido un contrato, recibirá un correo electrónico con instrucciones detalladas para configurar el aprovisionamiento de usuarios. El correo electrónico también incluirá detalles relacionados con el consentimiento del administrador (en nombre de su organización) para usar BLDNG APP, en caso de que sea necesario. 

El correo electrónico también incluirá la dirección URL del inquilino y el token secreto para su uso al configurar el aprovisionamiento automático de usuarios. 

## <a name="step-3-add-bldng-app-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de BLDNG APP desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de BLDNG APP, agregue esta aplicación desde la galería de aplicaciones de Azure AD. Si ha configurado previamente BLDNG APP para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a BLDNG APP, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-bldng-app"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en BLDNG APP 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en BLDNG APP en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bldng-app-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para BLDNG APP en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

1. En la lista de aplicaciones, seleccione **BLDNG APP**.

    ![Vínculo a BLDNG APP en la lista de aplicaciones](common/all-applications.png)

1. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

1. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

1. En la sección **Credenciales de administrador**, escriba los valores de **URL de inquilino** y del **Token secreto** de BLDNG APP. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a BLDNG APP. Si la conexión no se establece, asegúrese de que la cuenta de BLDNG APP tiene permisos de administrador y pruebe otra vez.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

1. Seleccione **Guardar**.

1. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to BLDNG APP** (Sincronizar usuarios de Azure Active Directory con BLDNG APP).

1. Revise los atributos de usuario que se sincronizan entre Azure AD y BLDNG APP en la sección de **asignaciones de atributos**. Los atributos seleccionados como propiedades **Matching** se usan para buscar coincidencias con las cuentas de usuario de BLDNG APP para las operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que BLDNG APP API admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

> [!NOTE]
> Es importante tener en cuenta que si cambia la asignación de **externalId**, los usuarios del inquilino no podrán iniciar sesión con la aplicación BLDNG.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |DisplayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   

1. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to BlogIn** (Sincronizar grupos de Azure Active Directory con BLDNG APP).

1. Examine los atributos de grupo que se sincronizan entre Azure AD y BLDNG APP en la sección de **asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con los grupos en BLDNG APP para realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|Compatible con el filtrado|
      |---|---|---|
      |DisplayName|String|&check;
      |members|Referencia|
      |externalId|String|      

1. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para BLDNG APP, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

1. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en BLDNG APP.

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