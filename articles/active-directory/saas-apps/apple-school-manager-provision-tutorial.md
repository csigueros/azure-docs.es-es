---
title: 'Tutorial: Configuración de Apple School Manager para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y cancelar el aprovisionamiento de las cuentas de usuario de Azure AD a Apple School Manager de forma automática.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: thwimmer
ms.openlocfilehash: 5031da3751d596228a40a354b527b718920b86e5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764940"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Tutorial: Configuración de Apple School Manager para el aprovisionamiento automático de usuarios



En este tutorial se describen los pasos que debe realizar en Apple School Manager y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios de manera automática en [Apple School Manager](https://school.apple.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Crear usuarios en Apple School Manager
> * Eliminar usuarios de Apple School Manager cuando ya no necesitan acceso
> * Mantener los atributos de usuario específicos sincronizados entre Azure AD y Apple School Manager

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta de Apple School Manager con el rol de administrador, administrador de sitios o administrador de personas.

> [!NOTE]
> El proceso de transferencia de tokens a Azure AD y establecimiento de una conexión correcta debe completarse en 4 días naturales o deberá volver a iniciarse.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se [asignarán entre Azure AD y Apple School Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Paso 2. Configurar Apple School Manager para admitir el aprovisionamiento con Azure AD

1. En Apple School Manager, inicie sesión con una cuenta que tenga el rol administrador, administrador de sitios o administrador de personas.
2. Haga clic en Configuración en la parte inferior de la barra lateral, haga clic en Origen de datos debajo de configuración de la organización y luego haga clic en Conectarse a un origen de datos.
3. Haga clic en Conectar junto a SCIM, lea atentamente la advertencia, haga clic en Copiar y, a continuación, haga clic en Cerrar.
[Ventana Conectar con SCIM, que proporciona un token y un botón de copia.] Deje esta ventana abierta para copiar la dirección URL del inquilino de Apple Business Manager en Azure AD, que es: "https://federation.apple.com/feeds/school/scim".

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> El token secreto no debe compartirse con nadie que no sea el administrador de Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Paso 3. Agregar Apple School Manager desde la galería de aplicaciones de Azure AD

Agregue Apple School Manager desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en Apple School Manager. Si ha configurado Apple School Manager previamente para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios a Apple School Manager, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Paso 5. Configurar el aprovisionamiento automático de usuarios para Apple School Manager

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Apple School Manager**.

    ![Apple School Manager en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de **URL base de SCIM 2.0 y token de acceso** que recuperó anteriormente de Apple School Manager en **URL de inquilino** y **Token secreto**, respectivamente. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Apple School Manager. Si la conexión falla, asegúrese de que la cuenta de Apple School Manager tiene permisos de administrador e inténtelo de nuevo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Si la conexión es correcta, Apple School Manager muestra la conexión SCIM como activa. Apple School Manager puede tardar hasta 60 segundos en reflejar el estado de conexión más reciente.

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Haga clic en **Save**(Guardar).

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Apple School Manager** (Sincronizar usuarios de Azure Active Directory con Apple School Manager).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Apple School Manager en la sección **Asignaciones de atributos**. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para buscar coincidencias con las cuentas de usuario de Apple School Managers con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |active|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Apple School Manager, cambie el **Estado de aprovisionamiento** a **Activado** en la sección Configuración.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Apple School Manager.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose.

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación

Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Revisión de los requisitos de SCIM para Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Cómo se usa un identificador de persona en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Uso de SCIM para importar usuarios en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Resolución de conflictos de cuenta de usuario de SCIM en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Eliminación de cuentas de Azure AD que aparecen en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Visualización de la actividad de SCIM en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Administración del token de SCIM existente y las conexiones en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Terminación de la conexión de SCIM en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Solución de problemas de la conexión de SCIM en Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)