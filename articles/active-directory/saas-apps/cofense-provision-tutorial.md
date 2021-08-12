---
title: 'Tutorial: Configuración de Cofense Recipient Sync para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y cancelar el aprovisionamiento de forma automática de las cuentas de usuario de Azure AD en Cofense Recipient Sync.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 84fe20ef-0de0-4f7c-9b42-6385f3d834db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: thwimmer
ms.openlocfilehash: a2e3ebb1c1062e989e159a69bd08042c269d91f2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764081"
---
# <a name="tutorial-configure-cofense-recipient-sync-for-automatic-user-provisioning"></a>Tutorial: Configuración de Cofense Recipient Sync para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Cofense Recipient Sync y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios de manera automática en [Cofense Recipient Sync ](https://cofense.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Cofense Recipient Sync
> * Eliminación de usuarios de Cofense Recipient Sync cuando ya no necesiten acceso
> * Mantenimiento de los atributos de usuario sincronizados entre Azure AD y Cofense Recipient Sync

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta de operador estándar en Cofense PhishMe.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos [asignar entre Azure AD y Cofense Recipient Sync ](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-cofense-recipient-sync-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Cofense Recipient Sync para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en Cofense PhishMe. Vaya a **Recipients > Recipient Sync** (Destinatarios > Sincronización de destinatarios). 
2. Acepte los términos y condiciones y, luego, haga clic en **Get Started** (Introducción).

    ![TNC de sincronización de destinatarios](media/cofense-provisioning-tutorial/recipient-sync-toc.png)

3. Copie los valores de los campos **URL** y **Token**.

    ![Sincronización de destinatarios](media/cofense-provisioning-tutorial/recipient-sync-getting-started.png)


## <a name="step-3-add-cofense-recipient-sync-from-the-azure-ad-application-gallery"></a>Paso 3. Incorporación de Cofense Recipient Sync desde la galería de aplicaciones de Azure AD

Agregue Cofense Recipient Sync desde la galería de aplicaciones de Azure AD para empezar a administrar el aprovisionamiento en Cofense Recipient Sync. Si ha configurado previamente Cofense Recipient Sync para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Cofense Recipient Sync, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-cofense-recipient-sync"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Cofense Recipient Sync 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios de Cofense Recipient Sync en función del usuario de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cofense-recipient-sync-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Cofense Recipient Sync en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Cofense Recipient Sync**.

    ![Vínculo a Cofense en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de **URL base de SCIM 2.0 y token de autenticación SCIM** que obtuvo anteriormente en el paso 2. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Cofense Recipient Sync. Si la conexión no se establece, asegúrese de que la cuenta de Cofense Recipient Sync tenga permisos de administrador y vuelva a intentarlo.

    ![Token de URL de inquilino](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Coda** (Sincronizar usuarios de Azure Active Directory con Cofense Recipient Sync).

9. Examine los atributos de usuario que se sincronizan entre Azure AD y Cofense Recipient Sync en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para hacer coincidir las cuentas de usuario de Cofense Recipient Sync a fin de realizar operaciones de actualización.  Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |externalId|String|&check;|
   |userName|String|
   |active|Boolean|
   |DisplayName|String|
   |name.formatted|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.honorificSuffix|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |preferredLanguage|String|
   |nickName|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Cofense Recipient Sync, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere que se aprovisionen en Cofense Recipient Sync.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
* Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
* Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md). 

## <a name="change-log"></a>Registro de cambios

* 15/01/2020: el cambio de "Solo durante la creación del objeto" a "Siempre" se ha implementado para la asignación de externalId de objectId.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)