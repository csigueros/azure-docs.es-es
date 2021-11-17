---
title: 'Tutorial: Configuración de BenQ IAM para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD en BenQ IAM.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 8c21c81c-f9dc-4818-b2fe-7a06b205af8d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: thwimmer
ms.openlocfilehash: 3a1b83ecaa35f141563374b560c07b39827078f7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549933"
---
# <a name="tutorial-configure-benq-iam-for-automatic-user-provisioning"></a>Tutorial: Configuración de BenQ IAM para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en BenQ IAM y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en [BenQ IAM](https://service-portal.benq.com/login) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="supported-capabilities"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en BenQ IAM
> * Eliminación de usuarios en BenQ IAM cuando ya no necesiten acceso
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y BenQ IAM
> * [Inicio de sesión único](benq-iam-tutorial.md) en BenQ IAM (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global).
* Una cuenta de administrador con BenQ IAM. Puede registrarse para obtener una cuenta de administrador de BenQ en [BenQ IAM](https://service-portaltest.benq.com/login).


## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y BenQ IAM](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-benq-iam-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de BenQ IAM para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en BenQ IAM con la cuenta de administrador de BenQ y haga clic en [SSO Setting](https://service-portaltest.benq.com/login) (Configuración de SSO) en la sección **Account Management** (Administración de cuentas).
    ![SSO Setting](media/benq-iam-provisioning-tutorial/sso-setting.png) (Configuración de SSO)

2. Seleccione **SSO by SAML** (SSO mediante SAML) como configuración de SSO en el menú emergente y haga clic en Next (Siguiente). 
    ![sso-with-saml](media/benq-iam-provisioning-tutorial/sso-by-saml.png)

3. Siga el [tutorial de integración de inicio de sesión seguro de Azure AD con BenQ IAM](benq-iam-tutorial.md) para completar la configuración necesaria.

4. Después de finalizar la configuración de SSO por SAML, verá un mensaje de confirmación como el que se muestra en la imagen siguiente. Haga clic en **Create Token** (Crear token) en la sección User Provisioning (Aprovisionamiento de usuarios).
    ![created-token](media/benq-iam-provisioning-tutorial/create-token.png)

5. Copie el token en un lugar seguro. Este token se usará en Azure Portal en el **paso 5**.
    ![copying-token](media/benq-iam-provisioning-tutorial/copy-token.png)

## <a name="step-3-add-benq-iam-from-the-azure-ad-application-gallery"></a>Paso 3. Incorporación de BenQ IAM desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento en BenQ IAM, agregue BenQ IAM desde la galería de aplicaciones de Azure AD. Si ha configurado previamente BenQ IAM para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a BenQ IAM, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-benq-iam"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en BenQ IAM 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-benq-iam-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para BenQ IAM en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **BenQ IAM**.

    ![El vínculo a BenQ IAM en la lista Aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña de aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña Aprovisionamiento](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la dirección URL de inquilino y el token secreto de BenQ IAM. Haga clic en **Probar conexión** para asegurarse de que Azure AD pueda conectarse a BenQ IAM. Si la conexión no se establece, asegúrese de que la cuenta de BenQ IAM tenga permisos de administrador y vuelva a intentarlo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to BenQ IAM** (Sincronizar usuarios de Azure Active Directory con BenQ IAM).

9. Examine los atributos de grupo que se sincronizan entre Azure AD y BenQ IAM en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con las cuentas del usuario en BenQ IAM a fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de BenQ IAM admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;
   |externalId|String|
   |active|Boolean|
   |DisplayName|String|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para BenQ IAM, cambie la opción **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en BenQ IAM.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

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
