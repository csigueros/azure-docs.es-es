---
title: 'Tutorial: Configuración de Freshservice Provisioning para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD en Freshservice Provisioning.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e03ec65a-25ef-4c91-a364-36b2f007443c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: thwimmer
ms.openlocfilehash: aa4242b65157776ef48f4095d64366344c0cbce6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734223"
---
# <a name="tutorial-configure-freshservice-provisioning-for-automatic-user-provisioning"></a>Tutorial: Configuración de Freshservice Provisioning para el aprovisionamiento automático de usuarios

En este tutorial se indican los pasos necesarios en Freshservice Provisioning y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona de forma automática usuarios en [Freshservice Provisioning](https://effy.co.in/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Freshservice Provisioning
> * Eliminación de usuarios en Freshservice Provisioning cuando ya no necesiten acceso
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y Freshservice Provisioning

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../users-groups-roles/directory-assign-admin-roles.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una [cuenta de Freshservice](https://www.freshservice.com) con los permisos de administrador de la organización.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../manage-apps/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y Freshservice Provisioning](../manage-apps/customize-application-attributes.md). 

## <a name="step-2-configure-freshservice-provisioning-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Freshservice Provisioning para admitir el aprovisionamiento con Azure AD

1. En la cuenta de Freshservice, instale la aplicación **Azure Provisioning (SCIM)** desde Marketplace; para ello, vaya a **Freshservice Admin** > **Apps** > **Get Apps** (Administrador de Freshservice > Aplicaciones > Obtener aplicaciones).
2. En la pantalla de configuración, proporcione los valores de **Freshservice Domain** (Dominio de Freshservice) (por ejemplo, `acme.freshservice.com`) y de **Organization Admin API key** (Clave de API de administración de la organización).
3. Haga clic en **Continuar**.
4. Resalta y copie **Bearer Token** (Token de portador). Este valor se escribe en el campo **Secret Token** (Token secreto) de la pestaña Provisioning (Aprovisionamiento) de la aplicación Freshservice Provisioning en Azure Portal.
5. Haga clic en **Install** (Instalar) para completar la instalación.
6. **Tenant URL** (URL de inquilino) es `https://scim.freshservice.com/scim/v2`. Este valor se escribirá en el campo **Tenant URL** (URL de inquilino) de la pestaña Provisioning (Aprovisionamiento) de la aplicación Freshservice Provisioning en Azure Portal.

## <a name="step-3-add-freshservice-provisioning-from-the-azure-ad-application-gallery"></a>Paso 3. Incorporación de Freshservice Provisioning desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento en Freshservice Provisioning, agregue Freshservice Provisioning desde la galería de aplicaciones de Azure AD. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario. Si elige delimitar quién se aprovisionará en la aplicación en función de la asignación, puede usar los [pasos](../manage-apps/assign-user-or-group-access-portal.md) siguientes para asignar usuarios a la aplicación. Si elige delimitar quién se aprovisionará en función únicamente de los atributos del usuario, puede usar un filtro de ámbito, tal como se describe [aquí](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios a Freshservice Provisioning, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios asignados, puede controlarlo asignando uno o dos usuarios a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-freshservice-provisioning"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Freshservice Provisioning 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios en Freshservice Provisioning en función de las asignaciones de usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-freshservice-provisioning-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Freshservice Provisioning en Azure AD

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Freshservice Provisioning**.

    ![Vínculo a Freshservice Provisioning en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales del administrador**, escriba los valores de URL de inquilino y de token secreto de Freshservice Provisioning. Haga clic en **Probar conexión** para asegurarse de que Azure AD pueda conectarse a Freshservice Provisioning. Si la conexión no se establece, asegúrese de que la cuenta de Freshservice Provisioning tenga permisos de administrador e inténtelo de nuevo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Freshservice Provisioning** (Sincronizar usuarios de Azure Active Directory con Freshservice Provisioning).

9. Examine los atributos de usuario que se sincronizan entre Azure AD y Freshservice Provisioning en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con las cuentas de usuario de Freshservice Provisioning a fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../manage-apps/customize-application-attributes.md), tiene que asegurarse de que la API de Freshservice Provisioning admita el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |DisplayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |addresses[type eq "work"].formatted|String|
   |locale|String|
   |title|String|
   |timezone|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia|
   |urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent|String|

> [!NOTE]
> Se pueden agregar atributos de extensión personalizados al esquema para satisfacer las necesidades de la aplicación siguiendo estos pasos:
> * En la sección Asignaciones, seleccione **Aprovisionar usuarios de Azure Active Directory** (Aprovisionar usuarios de Azure Active Directory).
> * En la parte inferior de la página, seleccione **Mostrar opciones avanzadas**.
> * Seleccione **Editar lista de atributos para Freshservice**.
> * En la parte inferior de la lista de atributos, escriba la información sobre el atributo personalizado en los campos proporcionados. El espacio de nombres urn de un atributo personalizado debe seguir el patrón que se muestra en el ejemplo siguiente. Se puede personalizar **CustomAttribute** según los requisitos de la aplicación, por ejemplo: urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:**isAgent**.
> * Debe seleccionar el tipo de datos adecuado para el atributo personalizado y hacer clic en **Guardar**.
> * Vuelva a la pantalla de asignaciones predeterminadas y haga clic **Agregar nueva asignación**. Los atributos personalizados se mostrarán en la lista desplegable **Atributo de destino**.

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Freshservice Provisioning, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios que desea aprovisionar en Freshservice Provisioning.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../manage-apps/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)