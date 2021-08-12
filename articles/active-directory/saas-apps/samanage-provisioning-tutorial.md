---
title: 'Tutorial: Configuración de SolarWinds Service Desk (anteriormente Samanage) para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y cancelar el aprovisionamiento de forma automática de las cuentas de usuario de Azure AD para SolarWinds Service Desk (anteriormente Samanage).
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: thwimmer
ms.openlocfilehash: 3999575e13a1bb12478f6a9270dafa7c18c22f35
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759657"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Tutorial: Configuración de SolarWinds Service Desk (anteriormente Samanage) para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en SolarWinds Service Desk (anteriormente Samanage) y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [SolarWinds Service Desk](https://www.samanage.com/pricing/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migración a la nueva aplicación de SolarWinds Service Desk

Si tiene una integración existente con SolarWinds Service Desk, consulte la siguiente sección sobre los próximos cambios. Si está configurando SolarWinds Service Desk por primera vez, puede omitir esta sección y pasar a **Funcionalidades admitidas**.

#### <a name="whats-changing"></a>¿Qué está cambiando?

* Cambios en Azure AD: el método de autorización para aprovisionar usuarios en Samanage ha sido históricamente la **autorización básica**. Pronto verá que el método de autorización cambia a **token secreto de larga duración**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>¿Qué tengo que hacer para migrar mi integración personalizada existente a la nueva aplicación?

Si tiene una integración de SolarWinds Service Desk existente con credenciales de administrador válidas, **no es necesario realizar ninguna acción**. La migración de los clientes se realizará automáticamente a la nueva aplicación. Este proceso se realiza por completo en segundo plano. Si las credenciales existentes expiran, o si necesita volver a autorizar el acceso a la aplicación, debe generar un token secreto de larga duración. Para generar un nuevo token, consulte el paso 2 de este artículo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>¿Cómo puedo saber si se ha migrado mi aplicación? 

Una vez migrada la aplicación, en la sección **Credenciales de administrador**, los campos **Nombre de usuario de administrador** y **Contraseña de administrador** se reemplazarán por un único campo **Token secreto**.

## <a name="capabilities-supported"></a>Funcionalidades admitidas

> [!div class="checklist"]
> * Crear usuarios en SolarWinds Service Desk
> * Quitar usuarios de SolarWinds Service Desk cuando ya no necesiten el acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y SolarWinds Service Desk
> * Aprovisionar grupos y pertenencias a grupos en SolarWinds Service Desk
> * [Inicio de sesión único](./samanage-tutorial.md) en SolarWinds Service Desk (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un [inquilino de SolarWinds Service Desk](https://www.samanage.com/pricing/) con el paquete Professional.
* Una cuenta de usuario de SolarWinds Service Desk con permisos de administrador.

> [!Note]
> Los roles no deben editarse manualmente en Azure Active Directory al realizar importaciones de roles.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos [se asignarán entre Azure AD y SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de SolarWinds Service Desk para admitir el aprovisionamiento con Azure AD

Para generar un token secreto para la autenticación, consulte el [tutorial de autenticación de tokens para la integración de API](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de SolarWinds Service Desk desde la galería de aplicaciones de Azure AD

Agregue SolarWinds Service Desk desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en SolarWinds Service Desk. Si ha configurado previamente SolarWinds Service Desk para SSO, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a SolarWinds Service Desk, debe seleccionar un rol que no sea **de acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios para SolarWinds Service Desk 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para SolarWinds Service Desk en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **SolarWinds Service Desk**.

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla que muestra la pestaña Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla que muestra Modo de aprovisionamiento establecido en Automático.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://api.samanage.com` en la **URL de inquilino**.  Escriba el valor del token secreto recuperado anteriormente en **Token secreto**. Seleccione **Probar conexión** para asegurarse de que Azure AD pueda conectarse a SolarWinds Service Desk. Si la conexión no se establece, asegúrese de que la cuenta de SolarWinds Service Desk tiene permisos de administrador e inténtelo de nuevo.

    ![Captura de pantalla que muestra el botón Probar conexión seleccionado.](./media/samanage-provisioning-tutorial/provisioning.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to SolarWinds Service Desk** (Sincronizar usuarios de Azure Active Directory con SolarWinds Service Desk).

9. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y SolarWinds Service Desk. Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de SolarWinds Service Desk para las operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de SolarWinds Service Desk admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

      ![Asignaciones de usuario de Samanage](./media/samanage-provisioning-tutorial/user-attributes.png)

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to SolarWinds Service Desk** (Sincronizar grupos de Azure Active Directory con SolarWinds Service Desk).

11. En la sección de **asignaciones de atributos**, revise los atributos de grupo que se sincronizan entre Azure AD y SolarWinds Service Desk. Los atributos seleccionados como propiedades de **coincidencia** se usan para buscar coincidencias con los grupos de SolarWinds Service Desk con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      ![Asignaciones de grupo de Samanage](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para SolarWinds Service Desk, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en SolarWinds Service Desk.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Limitaciones del conector

Si selecciona la opción **Sincronizar todos los usuarios y grupos** y configura un valor para el atributo **roles** de SolarWinds Service Desk, el valor del cuadro **Valor predeterminado si es nulo (opcional)** se debe expresar en el formato siguiente:

- {"displayName":"rol"}, donde el rol es el valor predeterminado que quiera.

## <a name="change-log"></a>Registro de cambios

* 14/09/2020: Se ha cambiado el nombre de la empresa en dos tutoriales de SaaS de Samanage a SolarWinds Service Desk (anteriormente Samanage) de acuerdo con `https://github.com/ravitmorales`.
* 22/04/2020: se actualizó el método de autorización de autenticación básica a token secreto de larga duración.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
