---
title: 'Tutorial: Configuración de Gtmhub para el aprovisionamiento automático de usuarios mediante Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure AD en Gtmhub.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 10b68d00-a544-480b-9bd6-f6ac291a90d0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: thwimmer
ms.openlocfilehash: 29daa8d65fceec441a197e6240fcf0c40f108f36
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323831"
---
# <a name="tutorial-configure-gtmhub-for-automatic-user-provisioning"></a>Tutorial: Configuración de Gtmhub para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Gtmhub y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se ha configurado, Azure AD aprovisiona y desaprovisiona usuarios y grupos de forma automática en [Gtmhub](https://www.gtmhub.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

>[!NOTE]
>Cuando se configuraba el aprovisionamiento automático de usuarios, Azure AD solo desaprovisionaba automáticamente los usuarios y grupos en Gtmhub, además de asignar usuarios a sus respectivos equipos mediante el servicio de aprovisionamiento de Azure AD. En 2021, después de habilitar el inicio de sesión único (SSO) con Gtmhub, los usuarios se aprovisionarán automáticamente al iniciar sesión mediante SSO y se les asignará al equipo correspondiente.


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Eliminar usuarios de Gtmhub cuando ya no necesitan acceder.
> * Mantener los atributos de usuario sincronizados entre Azure AD y Gtmhub.
> * Asignar automáticamente usuarios a los equipos y alinearlos.

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta empresarial de Gtmhub.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se van a [asignar entre Azure AD y Gtmhub](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-gtmhub-to-support-team-mapping-and-user-de-provisioning-with-azure-ad"></a>Paso 2. Configuración de Gtmhub para que admita la asignación de equipos y el desaprovisionamiento de usuarios mediante Azure AD

Para asociar la aplicación de aprovisionamiento a la cuenta de Gtmhub, debe emitir un token de SCIM y compilar la dirección URL del inquilino.

### <a name="to-issue-a-new-scim-token"></a>Para emitir un nuevo token de SCIM:

1. Inicie sesión en su **cuenta de Gtmhub**. Vaya a **Parámetros > Configuración > Tokens de API**.

    ![Pestaña Tokens de API](media/gtmhub-provisioning-tutorial/api-tokens.png)
2. Haga clic en **Emitir token** y seleccione **SCIM**. Escriba un nombre para el token y haga clic en el botón **Generar token de API**.

    ![Pestaña de generación de tokens](media/gtmhub-provisioning-tutorial/generate-token.png)
3. Una vez generado el token, puede copiarlo y usarlo en la aplicación de aprovisionamiento de Azure AD.

    ![Copiar token](media/gtmhub-provisioning-tutorial/token.png)

### <a name="to-compile-the-tenant-url"></a>Para compilar la dirección URL del inquilino:

1. La URL del inquilino debe tener el formato siguiente:

    `https://app.gtmhub.com/api/v1/scim/azure/{account_id}`

2. Si la cuenta de Gtmhub está ubicada en el centro de datos de EE. UU., tendrá que agregar también el centro de datos a la dirección URL:
    
     `https://app.us.gtmhub.com/api/v1/scim/azure/{account_id}`

3. Para obtener el identificador de cuenta, vaya a **Configuración**, seleccione la pestaña **Tokens de API** y copie el identificador de cuenta:  ![Id. de cuenta](media/gtmhub-provisioning-tutorial/account-id.png)

## <a name="step-3-add-gtmhub-from-the-azure-ad-application-gallery"></a>Paso 3. Incorporación de Gtmhub desde la galería de aplicaciones de Azure AD

Agregue Gtmhub desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en Gtmhub. Si ya ha configurado Gtmhub para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Gtmhub, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-gtmhub"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Gtmhub 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-gtmhub-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Gtmhub en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Gtmhub**.

    ![El vínculo a Gtmhub en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, especifique la URL del inquilino y el token secreto de Gtmhub. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Gtmhub. Si la conexión no funciona, asegúrese de que la cuenta de Gtmhub tiene permisos de administrador y pruebe de nuevo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Gtmhub** (Sincronizar usuarios de Azure Active Directory con Gtmhub).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Gtmhub en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Gtmhub en operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Gtmhub admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|&check;|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD con GitHub, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Para definir los usuarios o grupos que desea aprovisionar en Gtmhub, seleccione los valores deseados en **Ámbito**, en la sección **Configuración**.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
* Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
* Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)