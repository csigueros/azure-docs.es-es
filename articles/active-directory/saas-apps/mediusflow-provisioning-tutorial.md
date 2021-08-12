---
title: 'Tutorial: Configuración de MediusFlow para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure AD en MediusFlow.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: thwimmer
ms.openlocfilehash: ef4ffd0417cc1566282466e28ee82f1d27f3db3c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768690"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Tutorial: Configuración de MediusFlow para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en MediusFlow y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [MediusFlow](https://www.mediusflow.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en MediusFlow
> * Eliminación de usuarios de MediusFlow cuando ya no necesiten el acceso
> * Sincronización de los atributos de usuario entre Azure AD y MediusFlow
> * Aprovisionamiento de grupos y pertenencias a grupos en MediusFlow
> * Inicio de sesión único en MediusFlow (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una suscripción de MediusFlow activa con un inquilino de control de calidad o de producción.
* Una cuenta de usuario de MediusFlow con derechos de acceso de administrador para poder definir la configuración en MediusFlow.
* Las empresas agregadas en el inquilino de MediusFlow donde se deben aprovisionar los usuarios.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se [asignarán entre Azure AD y MediusFlow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de MediusFlow para admitir el aprovisionamiento con Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Activación de la aplicación de Microsoft 365 en MediusFlow
Para empezar, habilite el acceso del inicio de sesión de Azure AD y la característica de configuración de Azure AD en MediusFlow mediante los pasos siguientes:

#### <a name="user-login"></a>Inicio de sesión de usuario
Para habilitar el flujo de inicio de sesión en Microsoft 365/Azure AD, consulte este artículo (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup).

#### <a name="user-transfer-configuration"></a>Configuración de transferencia de usuarios
Para habilitar el portal de configuración de los usuarios para el aprovisionamiento desde Azure AD, consulte [este](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artículo.

#### <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

1.  Inicie sesión en la [consola de administración de MediusFlow](https://office365.cloudapp.mediusflow.com/) con el identificador de inquilino.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Captura de pantalla de la consola de administración de MediusFlow. El cuadro de nombre de inquilino de MediusFlow y el botón Autenticar están resaltados en el primer paso de integración." border="false":::

2. Compruebe la conexión con MediusFlow.

    ![Verify](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Proporcione el id. de inquilino de Azure AD.

    ![Proporcionar el identificador de inquilino](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Puede obtener más información en las [preguntas más frecuentes ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) sobre cómo encontrarlo.

4. Guarde la configuración.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Captura de pantalla de la consola de administración de MediusFlow que muestra el cuarto paso de la integración. El botón Guardar configuración está resaltado." border="false":::

5. Seleccione el aprovisionamiento de usuarios y haga clic en **Aceptar**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Captura de pantalla de la consola de administración de MediusFlow que muestra el quinto paso de la integración. Los botones Use user provisioning (Usar aprovisionamiento de usuarios) y Aceptar están resaltados." border="false":::

6. Haga clic en **Generate Secret Key** (Generar clave secreta). Copie y guarde este valor, que se escribirá en el campo **Token secreto** de la pestaña **Aprovisionamiento** en la aplicación MediusFlow en Azure Portal.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Captura de pantalla de la pestaña de configuración Aprovisionamiento de usuarios en la consola de administración de MediusFlow. Los botones Generar clave secreta y Copiar están resaltados." border="false":::

7. Haga clic en **Aceptar**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Captura de pantalla de la consola de administración de MediusFlow con una notificación que indica a los usuarios que hagan clic en Aceptar para generar una clave secreta. El botón Aceptar está resaltado." border="false":::

8. Para que los usuarios se importen con un conjunto predefinido de roles, compañías y otras opciones de configuración generales en MediusFlow, antes deberá configurar esta opción. Para empezar, haga clic en **Add new configuration** (Agregar nueva configuración).

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Captura de pantalla de la pestaña de configuración Aprovisionamiento de usuarios en la consola de administración de MediusFlow. El botón Add new configuration (Agregar nueva configuración) está resaltado." border="false":::

9. Proporcione la configuración predeterminada para los usuarios. En esta vista, puede configurar el atributo predeterminado. Si la configuración estándar es correcta, basta con proporcionar un nombre de empresa válido. Dado que estos valores de configuración se capturan de MediusFlow, antes se deben configurar. Para obtener más información, consulte la sección **Requisitos previos** de este artículo:

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Captura de pantalla de la ventana para agregar una nueva configuración de MediusFlow. Muchos valores son visibles, como la configuración regional, un filtro y roles de usuario." border="false":::

10. Haga clic en el botón **Guardar** para guardar la configuración de los usuarios.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Captura de pantalla de la pestaña de configuración Aprovisionamiento de usuarios en la consola de administración de MediusFlow. El botón Guardar está resaltado." border="false":::

11. Para obtener el vínculo de aprovisionamiento de usuarios, haga clic en **Copiar vínculo de SCIM**. Copie y guarde este valor. Este valor se escribirá en el campo **Dirección URL del inquilino** de la pestaña **Aprovisionamiento** de la aplicación MediusFLow en Azure Portal.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Captura de pantalla de la pestaña de configuración Aprovisionamiento de usuarios en la consola de administración de MediusFlow. El botón Copiar vínculo de SCIM está resaltado." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Paso 3. Agregar MediusFLow desde la galería de aplicaciones de Azure AD

Agregue MediusFLow desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en MediusFLow. Si ha configurado previamente MediusFLow para SSO, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a MediusFLow, debe seleccionar un rol que no sea **de acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en MediusFlow 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para MediusFlow en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **MediusFlow**.

    ![Vínculo a MediusFLow en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba el valor de **URL de inquilino** recuperado anteriormente. Escriba el valor del token secreto recuperado anteriormente en **Token secreto**. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a MediusFLow. Si la conexión no se establece, asegúrese de que la cuenta de MediusFLow tenga permisos de administrador y vuelva a intentarlo.

      ![Captura de pantalla que muestra el cuadro de diálogo Credenciales de administrador, en el que se puede especificar el URL de inquilino y el secreto de inquilino.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Sincronizar usuarios de Azure Active Directory con MediusFLow**.

9. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y MediusFLow. Los atributos seleccionados como propiedades **Matching** se usan para buscar coincidencias con las cuentas de usuario de MediusFlow con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de MediusFlow admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:configurationFilter|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:identityProvider|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:nameIdentifier|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText1|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText2|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText3|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText4|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText5|String|


10. En la sección **Asignaciones**, seleccione **Sincronizar grupos de Azure Active Directory con MediusFLow**.

11. En la sección **Asignaciones de atributos**, revise los atributos de grupo que se sincronizan entre Azure AD y MediusFlow. Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con los grupos de MediusFlow para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    | Atributo | Tipo |
    |--|--|
    | DisplayName | String |
    | externalId | String |
    | members | Referencia |

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para MediusFlow, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere que se aprovisionen en MediusFlow.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Registro de cambios

* 21/01/2021: se han agregado los atributos de extensión personalizados **configurationFilter**, **identityProvider**, **nameIdentifier**, **customFieldText1**, **customFieldText2**, **customFieldText3**, **customFieldText3** y **customFieldText5**.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)