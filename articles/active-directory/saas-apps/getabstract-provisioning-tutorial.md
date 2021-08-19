---
title: 'Tutorial: Configuración de getAbstract para el aprovisionamiento automático de usuarios mediante Azure Active Directory | Microsoft Docs'
description: Descubra cómo aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure Active Directory en getAbstract.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: thwimmer
ms.openlocfilehash: 1c7278b0d8a0e9ed3d94e69d1ad5da34ab0bc854
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322108"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Tutorial: Configuración de getAbstract para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en getAbstract y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se ha configurado, Azure AD aprovisiona y desaprovisiona usuarios y grupos de forma automática en [getAbstract](https://www.getabstract.com) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, vea [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones de software como servicio (SaaS) con Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades admitidas

> [!div class="checklist"]
> * Crear usuarios en getAbstract.
> * Quitar usuarios en getAbstract cuando ya no necesitan acceso.
> * Mantener los atributos de usuario sincronizados entre Azure AD y getAbstract.
> * Aprovisionar grupos y pertenencias a grupos en getAbstract.
> * Habilitar [Inicio de sesión único (SSO)](getabstract-tutorial.md) en getAbstract (recomendado).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permisos](../roles/permissions-reference.md) para configurar el aprovisionamiento. Algunos ejemplos son el Administrador de aplicaciones, el Administrador de aplicaciones en la nube, el Propietario de la aplicación o el Administrador global.
* Un inquilino de getAbstract (licencia corporativa de getAbstract).
* SSO habilitado en el inquilino de Azure AD y el inquilino de getAbstract.
* Aprobación y System for Cross-domain Identity Management (SCIM), que habilita para getAbstract. (Envíe un correo electrónico a b2b.itsupport@getabstract.com).

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento

1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos se van a [asignar entre Azure AD y getAbstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de getAbstract para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en getAbstract.
1. Seleccione el icono de configuración que se encuentra en la esquina superior derecha y la opción **My Central Admin** (Mi administración central).

    ![Captura de pantalla en la que se muestra My Central Admin (Mi administración central) en getAbstract.](media/getabstract-provisioning-tutorial/my-account.png)

1. Busque y seleccione la opción **SCIM Admin** (Administración de SCIM).

    ![Captura de pantalla en la que se muestra SCIM Admin (Administración de SCIM) en getAbstract.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Seleccione **Ir**.

    ![Captura de pantalla en la que se muestra el Id. de cliente de SCIM para getAbstract.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Seleccione **Generar nuevo token**.

    ![Captura de pantalla en la que se muestra el Token 1 de SCIM para getAbstract.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Si está seguro, seleccione **Generar nuevo token**. En caso contrario, haga clic en **Cancelar**.

    ![Captura de pantalla en la que se muestra el Token 2 de SCIM para getAbstract.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Seleccione el icono Copiar al portapapeles, o el token completo y cópielo. Tenga también en cuenta que la dirección URL base o del inquilino es `https://www.getabstract.com/api/scim/v2`. Estos valores se escriben en las casillas **Token secreto** y **URL de inquilino** de la pestaña **Aprovisionamiento** de la aplicación getAbstract en Azure Portal.

    ![Captura de pantalla en la que se muestra el Token 3 de SCIM para getAbstract.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de getAbstract desde la galería de aplicaciones de Azure AD

Agregue getAbstract desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en getAbstract. Si ya ha configurado getAbstract para el inicio de sesión único anteriormente, puede usar la misma aplicación. Sin embargo, se recomienda crear una aplicación independiente al probar inicialmente la integración. Para obtener más información sobre cómo agregar una aplicación desde la galería, vea [este inicio rápido](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento

Puede usar el servicio de aprovisionamiento de Azure AD para definir quién se aprovisionará en función de la asignación a la aplicación o en función de los atributos del usuario o el grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe en [Aprovisionamiento de aplicaciones con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Al asignar usuarios y grupos a getAbstract, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles.

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo para todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios, o grupos, a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en getAbstract

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en una aplicación de prueba en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Configuración del aprovisionamiento automático de usuarios para getAbstract en Azure AD

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Captura de pantalla que muestra el panel de aplicaciones empresariales.](common/enterprise-applications.png)

1. En la lista de aplicaciones, seleccione **getAbstract**.

    ![Captura de pantalla en la que se muestra el vínculo de getAbstract en la lista de aplicaciones.](common/all-applications.png)

1. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla que muestra la pestaña Aprovisionamiento.](common/provisioning.png)

1. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla que muestra Modo de aprovisionamiento establecido en Automático.](common/provisioning-automatic.png)

1. En la sección **Credenciales de administrador**, especifique la información de **URL de inquilino** y de **Token secreto** de getAbstract. Para asegurarse de que Azure AD puede conectarse a getAbstract, seleccione **Probar conexión**. Si la conexión no funciona, asegúrese de que la cuenta de getAbstract tiene permisos de administrador y pruebe de nuevo.

    ![Captura de pantalla en la que se muestran las casillas URL de inquilino y Token secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. En el cuadro **Dirección de correo electrónico para notificaciones**, escriba la dirección de correo electrónico de la persona o grupo que deben recibir las notificaciones de error de aprovisionamiento. Seleccione la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Captura de pantalla que muestra el cuadro de texto de la dirección de correo electrónico de notificación.](common/provisioning-notification-email.png)

1. Seleccione **Guardar**.

1. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to getAbstract** (Sincronizar usuarios de Azure Active Directory con getAbstract).

1. Revise los atributos de usuario que se sincronizan entre Azure AD y getAbstract en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de getAbstract en operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de getAbstract admite el filtrado de usuarios basado en ese atributo. Para confirmar los cambios, seleccione **Guardar**.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

1. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to getAbstract** (Sincronizar grupos de Azure Active Directory con getAbstract).

1. Revise los atributos de grupo que se sincronizan entre Azure AD y getAbstract en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer coincidencias con los grupos de getAbstract con el objetivo de realizar operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.

    |Atributo|Tipo|Compatible con el filtrado|
    |---|---|---|
    |DisplayName|String|&check;|
    |externalId|String|
    |members|Referencia|

1. Para configurar los filtros de ámbito, vea las instrucciones que se proporcionan en [Tutorial de los filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar el servicio de aprovisionamiento de Azure AD para getAbstract, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Captura de pantalla que muestra el estado de aprovisionamiento activado.](common/provisioning-toggle-on.png)

1. Seleccione los valores deseados en **Ámbito** en la sección **Configuración** para definir los usuarios o grupos que quiere aprovisionar en getAbstract.

    ![Captura de pantalla en la que se muestra el Ámbito de aprovisionamiento.](common/provisioning-scope.png)

1. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

    ![Captura de pantalla en la que se muestra el botón Guardar.](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose.

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación

Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar los usuarios que se han aprovisionado correctamente y los que no.
* Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
* Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Para obtener más información sobre los estados de cuarentena, vea [Estado de cuarentena del aprovisionamiento de aplicaciones](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
