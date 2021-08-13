---
title: 'Tutorial: Configuración de OpenText Directory Services para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure AD para OpenText Directory Services.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: thwimmer
ms.openlocfilehash: f82c46c6853f33e2137cd54c03b2bfa1df9dca8a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760248"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Tutorial: Configuración de OpenText Directory Services para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe dar tanto en OpenText Directory Services como en Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en OpenText Directory Services mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de un usuario en OpenText Directory Services
> * Eliminación de usuarios de OpenText Directory Services cuando ya no necesitan acceso
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y OpenText Directory Services
> * Aprovisionamiento de grupos y pertenencias a grupos en OpenText Directory Services
> * [Inicio de sesión único](./opentext-directory-services-tutorial.md) en OpenText Directory Services (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una instalación de OTDS accesible mediante Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos [se asignarán entre Azure AD y OpenText Directory Services](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de OpenText Directory Services para admitir el aprovisionamiento con Azure AD

> [!NOTE]
> Los pasos siguientes se aplican a una instalación de OpenText Directory Services. No se aplican a los inquilinos de OpenText CoreShare ni OpenText OT2.

1. Cree un **cliente de OAuth** confidencial dedicado.
2. Establezca una **Vigencia del token de acceso** duradera.

      ![Vigencia del token de acceso](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. No especifique ninguna dirección URL de redireccionamiento. No son necesarias. 
4. OTDS generará y mostrará el **secreto de cliente**. Guarde el **identificador de cliente** y el **secreto de cliente** en una ubicación segura.

      ![Secreto del cliente](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Cree una partición para los usuarios y grupos que se van a sincronizar desde Azure AD.

      ![Página Partición](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Conceda derechos administrativos al cliente de OAuth que creó en la partición que va a usar para los usuarios y grupos de Azure AD que se van a sincronizar.    
      * Partition (Partición) -> Actions (Acciones) -> Edit Administrators (Editar administradores)

      ![Página del administrador](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Se debe recuperar y configurar un token secreto en Azure AD. Para ello, se puede usar cualquier aplicación cliente HTTP. A continuación se indican los pasos de recuperación con la aplicación API Swagger incluida en OTDS.
      * En un explorador web, vaya a {URL de OTDS}/otdsws/oauth2
      * Vaya a/token y haga clic en el icono de candado de la parte superior derecha. Escriba el id. de cliente de OAuth y el secreto recuperados anteriormente como nombre de usuario y contraseña, respectivamente. Haga clic en Autorizar.

      ![Botón de autorización](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Seleccione **client_credentials** de grant_type y haga clic en **Execute** (Ejecutar).

      ![Botón de ejecución](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. El token de acceso en la respuesta debe usarse en el campo **Token secreto** en Azure AD.

      ![Token de acceso](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de OpenText Directory Services desde la galería de aplicaciones de Azure AD

Agregue OpenText Directory Services desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en OpenText Directory Services. Si ha configurado previamente OpenText Directory Services para SSO, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a OpenText Directory Services, debe seleccionar un rol que no sea de **acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en OpenText Directory Services 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para OpenText Directory Services en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **OpenText Directory Services**.

    ![El vínculo de OpenText Directory Services en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la URL de inquilino de OpenText Directory Services.
   * URL de inquilino no específico: {URL de OTDS}/scim/{nombreDePartición}
   * URL de inquilino específica: {URL de OTDS}/otdstenant/{idDeInquilino}/scim/{nombreDePartición}

6. Escriba el token secreto recuperado del paso 2. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a OpenText Directory Services. Si la conexión no se establece, asegúrese de que la cuenta de OpenText Directory Services tenga permisos de administrador y pruebe de nuevo.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to OpenText Directory Services** (Sincronizar usuarios de Azure Active Directory con OpenText Directory Services).

9. Examine los atributos de usuario que se sincronizan entre Azure AD y OpenText Directory Services en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Matching** (Coincidente) se usan para buscar coincidencias con las cuentas de usuario de OpenText Directory Services para las operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de OpenText Directory Services admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Boolean|
   |DisplayName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia| 

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to OpenText Directory Services** (Sincronizar grupos de Azure Active Directory con OpenText Directory Services).

11. Examine los atributos de grupo que se sincronizan entre Azure AD y OpenText Directory Services en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Matching** (Coincidente) se usan para buscar coincidencias con los grupos de OpenText Directory Services para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|
      |---|---|
      |DisplayName|String|
      |externalId|String|
      |members|Referencia|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para OpenText Directory Services, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en OpenText Directory Services.

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