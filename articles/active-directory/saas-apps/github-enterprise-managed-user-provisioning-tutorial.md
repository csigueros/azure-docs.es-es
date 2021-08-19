---
title: 'Tutorial: Configuración de GitHub Enterprise Managed User para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para GitHub Enterprise Managed User.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: thwimmer
ms.openlocfilehash: a2e42e274fb65c06744f838ba4a40b2dc6119d5d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323354"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Tutorial: Configuración de GitHub Enterprise Managed User para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en GitHub Enterprise Managed User y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en GitHub Enterprise Managed User mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en GitHub Enterprise Managed User
> * Elimine los usuarios de GitHub Enterprise Managed User cuando ya no necesiten acceso.
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y GitHub Enterprise Managed User
> * Aprovisionamiento de grupos y pertenencias a grupos en GitHub Enterprise Managed User
> * Inicio de sesión único para GitHub Enterprise Managed User (recomendado)

> [!NOTE]
> Este conector de aprovisionamiento solo está habilitado para los participantes de la versión beta correspondientes a los usuarios administrados por la empresa.


## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md)
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global).
* GitHub Enterprise Managed User habilitado y configurado para iniciar sesión con el inicio de sesión único de SAML mediante su inquilino de Azure AD

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y GitHub Enterprise Managed User](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de GitHub Enterprise Managed User para admitir el aprovisionamiento con Azure AD

1. La dirección URL del inquilino es `https://api.github.com/scim/v2/enterprises/{enterprise}`. Este valor se escribirá en el campo URL del inquilino de la pestaña Provisioning (Aprovisionamiento) de la aplicación GitHub Enterprise Managed User en Azure Portal.

2. Como administrador de GitHub Enterprise Managed User, vaya a la esquina superior derecha: > haga clic en su foto de perfil >, a continuación, haga clic en **Settings** (Configuración).

3. En la barra lateral izquierda, haga clic en **Developer settings** (Configuración del desarrollador).

4. En la barra lateral izquierda, haga clic en **Personal access tokens** (Tokens de acceso personal).

5. Haga clic en **Generate new token** (Generar nuevo token).

6. Seleccione el ámbito de **admin:enterprise** para este token.

7. Haga clic en **Generate Token** (Generar token).

8. Copie y guarde el **token secreto**. Este valor se escribirá en el campo Token secreto de la pestaña de aprovisionamiento de la aplicación GitHub Enterprise Managed User en Azure Portal.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de GitHub Enterprise Managed User desde la galería de aplicaciones de Azure AD

Agregue GitHub Enterprise Managed User desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento a GitHub Enterprise Managed User. Si ha configurado previamente GitHub Enterprise Managed User para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Al asignar usuarios y grupos a GitHub Enterprise Managed User, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento.

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios para GitHub Enterprise Managed User

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para GitHub Enterprise Managed User en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **GitHub Enterprise Managed User**.

    ![Vínculo de GitHub Enterprise Managed User en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales del administrador**, escriba la URL de inquilino de GitHub Enterprise Managed User y el token secreto recuperado anteriormente en el paso 2. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a GitHub Enterprise Managed User. Si se produce un error en la conexión, asegúrese de que la cuenta de GitHub Enterprise Managed User ha creado el token secreto como propietario empresarial e inténtelo de nuevo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users con GitHub Enterprise Managed User** (Sincronizar usuarios de Azure Active Directory con GitHub Enterprise Managed User).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y GitHub Enterprise Managed User en la sección de **asignación de atributos**. Los atributos seleccionados como propiedades de **coincidencia** se usan para buscar coincidencias con las cuentas de usuario de GitHub Enterprise Managed User con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de GitHub Enterprise Managed User admite el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |externalId|String|&check;|
   |userName|String|
   |active|Boolean|
   |roles|String|
   |DisplayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to GitHub AE** (Sincronizar grupos de Azure Active Directory con GitHub Enterprise Managed User).

11. Revise los atributos de grupo que se sincronizan entre Azure AD y en GitHub Enterprise Managed User en la sección de **asignación de atributos**. Los atributos seleccionados como propiedades de **coincidencia** se usan para buscar coincidencias con los grupos de GitHub Enterprise Managed User con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|Compatible con el filtrado|
      |---|---|---|
      |externalId|String|&check;|
      |DisplayName|String|
      |members|Referencia|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para GitHub Enterprise Managed User, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en GitHub Enterprise Managed User.

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