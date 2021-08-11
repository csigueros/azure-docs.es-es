---
title: 'Tutorial: Configuración de Tic-Tac Mobile para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática cuentas de usuario desde Azure AD para Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: thwimmer
ms.openlocfilehash: a0784eebbabe302ecb610c702ae1b8900e05f858
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113758882"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Tutorial: Configuración de Tic-Tac Mobile para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Tic-Tac Mobile y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [Tic-Tac Mobile](https://www.tictacmobile.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información acerca de lo que hace este servicio, cómo funciona y las preguntas más frecuentes, consulte [Qué es el aprovisionamiento automatizado de usuarios de aplicaciones SaaS en Azure AD](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funcionalidades admitidas

> [!div class="checklist"]
> * Crear usuarios en Tic-Tac Mobile.
> * Eliminar usuarios de Tic-Tac Mobile cuando ya no necesiten acceso.
> * Mantener los atributos de usuario sincronizados entre Azure AD y Tic-Tac Mobile.

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permisos](../roles/permissions-reference.md) para configurar el aprovisionamiento. Algunos ejemplos son el administrador de aplicaciones, el administrador de aplicaciones en la nube, el propietario de la aplicación o el administrador global.
* Una cuenta de [Tic-Tac Mobile](https://www.tictacmobile.com/) con un rol de superadministrador.


## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento

1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos se [asignarán entre Azure AD y Tic-Tac Mobile](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Tic-Tac Mobile para admitir el aprovisionamiento con Azure AD

Póngase en contacto con support@tictacmobile.com para obtener la **dirección URL del inquilino** y el **token secreto**. Para recibir un token, debe tener un rol de superadministrador en Tic-Tac Mobile. El token se especificará en el cuadro **Token secreto** de la pestaña **Aprovisionamiento** de la aplicación Tic-Tac Mobile en Azure Portal.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de Tic-Tac Mobile desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de Tic-Tac Mobile, agregue Tic-Tac Mobile desde la galería de aplicaciones de Azure AD. Si ha configurado previamente Tic-Tac Mobile para el inicio de sesión único, puede usar la misma aplicación. Al probar la integración inicialmente, cree una aplicación independiente. Para más información sobre cómo agregar una aplicación desde la galería, consulte [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento

Con el servicio de aprovisionamiento de Azure AD, puede definir quién se aprovisionará en función de la asignación a la aplicación o en función de los atributos del usuario o el grupo. Si elige determinar el ámbito de quién se aprovisionará en la aplicación en función de la asignación, siga los pasos descritos en [Administración de la asignación de usuarios para una aplicación en Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige determinar el ámbito de quién se aprovisionará en función únicamente de los atributos del usuario o el grupo, puede usar un filtro de ámbito, tal como se describe en [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Al asignar usuarios y grupos a Tic-Tac Mobile, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles.
* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo para todos. Cuando el ámbito del aprovisionamiento se establece en los usuarios y grupos asignados, puede mantener el control mediante la asignación de uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Tic-Tac Mobile

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en una aplicación de prueba en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Configuración del aprovisionamiento automático de usuarios para Tic-Tac Mobile en Azure AD

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Captura de pantalla que muestra el panel de aplicaciones empresariales.](common/enterprise-applications.png)

1. En la lista de aplicaciones, seleccione **Tic-Tac Mobile**.

    ![Captura de pantalla que muestra el vínculo a Tic-Tac Mobile en la lista de aplicaciones.](common/all-applications.png)

1. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla que muestra la pestaña Aprovisionamiento.](common/provisioning.png)

1. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla que muestra la opción Automático de la pestaña Aprovisionamiento.](common/provisioning-automatic.png)

1. En la sección **Credenciales de administrador**, escriba los valores de la **dirección URL del inquilino** y el **token secreto** de Tic-Tac Mobile. Seleccione **Probar la conexión** para asegurarse de que Azure AD puede conectarse a Tic-Tac Mobile. Si la conexión no se establece, asegúrese de que la cuenta de Tic-Tac Mobile tiene permisos de administrador e inténtelo de nuevo.

    ![Captura de pantalla que muestra el cuadro de texto Token secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. En el cuadro **Dirección de correo electrónico para notificaciones**, escriba la dirección de correo electrónico de la persona o grupo que deben recibir las notificaciones de error de aprovisionamiento. Seleccione la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Captura de pantalla que muestra el cuadro de texto de la dirección de correo electrónico de notificación.](common/provisioning-notification-email.png)

1. Seleccione **Guardar**.

1. En la sección **Asignaciones**, seleccione **Sincronizar usuarios de Azure Active Directory con Tic-Tac Mobile**.

1. Revise los atributos de usuario que se sincronizan entre Azure AD y Tic-Tac Mobile en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Tic-Tac Mobile con el objetivo de realizar operaciones de actualización. Si cambia el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Tic-Tac Mobile admite el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. Para configurar los filtros de ámbito, consulte las instrucciones que se describen en [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar el servicio de aprovisionamiento de Azure AD para Tic-Tac Mobile, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Captura de pantalla que muestra el estado de aprovisionamiento activado.](common/provisioning-toggle-on.png)

1. Seleccione los valores deseados en **Ámbito** en la sección **Configuración** para definir los usuarios o grupos que quiere aprovisionar en Tic-Tac Mobile.

    ![Captura de pantalla que muestra el ámbito de aprovisionamiento.](common/provisioning-scope.png)

1. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

    ![Captura de pantalla que muestra cómo guardar la configuración de aprovisionamiento.](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose.

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación

Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación.

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente y los que no.
1. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
1. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Para más información sobre los estados de cuarentena, consulte [Aprovisionamiento de aplicaciones en el estado de cuarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)