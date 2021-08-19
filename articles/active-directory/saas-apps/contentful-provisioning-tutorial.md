---
title: 'Tutorial: Configuración de Contentful para el aprovisionamiento automático de usuarios con Azure Active Directory'
description: Aprenda a aprovisionar y cancelar de forma automática el aprovisionamiento de las cuentas de usuario de Azure Active Directory (Azure AD) para Contentful.
services: active-directory
documentationcenter: ''
author: twimmers
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: thwimmer
ms.openlocfilehash: bb87222737e4c85eebb2869a812f44d9e703c8d4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322439"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Tutorial: Configuración de Contentful para el aprovisionamiento automático de usuarios

En este artículo, se describen los pasos que debe realizar en Contentful y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos en [Contentful](https://www.contentful.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio y de su funcionamiento, así como ver las preguntas más frecuentes, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades admitidas

> [!div class="checklist"]
> * Crear usuarios en Contentful
> * Quitar usuarios de Contentful cuando ya no necesiten acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y Contentful
> * Aprovisionamiento de grupos y pertenencias a grupos en Contentful
> * [Inicio de sesión único](contentful-tutorial.md) en Contentful (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md). 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Una cuenta de Contentful de la organización que tenga una suscripción que admite el aprovisionamiento de System for Cross-domain Identity Management (SCIM). Si tiene alguna pregunta sobre la suscripción de su organización, póngase en contacto con el [Soporte técnico de Contentful](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Planeación de la implementación de aprovisionamiento

1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y Contentful](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Configuración de Contentful para admitir el aprovisionamiento con Azure AD

1. Cree una cuenta de **usuario de servicio** en Contentful. Todos los permisos de aprovisionamiento de Azure se proporcionan a través de esta cuenta. Se recomienda elegir el rol de **Propietario** como el rol de organización para esta cuenta.

2. Inicie sesión en Contentful como **usuario de servicio**.

3. En el menú de la izquierda, seleccione **Configuración de la organización** > **Herramientas de acceso** > **Aprovisionamiento de usuarios**.

   ![Captura de pantalla del menú de configuración de la organización en Contentful, con el aprovisionamiento de usuarios resaltado en las herramientas de acceso.](media/contentful-provisioning-tutorial/access.png)

4. Copie y guarde la **URL de SCIM**. Deberá escribir este valor en Azure Portal, en la pestaña **Aprovisionamiento** de la aplicación de Contentful.

5. Seleccione **Generate personal access token** (Generar token de acceso personal).

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. En la ventana modal, escriba un nombre para el token de acceso personal y, a continuación, seleccione **Generar**.

7. Se generarán la URL de SCIM y el token secreto. Copie y guarde estos valores. Debe escribir estos valores en la pestaña **Aprovisionamiento** de la aplicación Contentful en Azure Portal.

    ![Captura de pantalla del panel del token de acceso personal, con C F P A T y el nombre del marcador de posición del token resaltado.](media/contentful-provisioning-tutorial/token.png)


Si tiene alguna pregunta mientras configura el aprovisionamiento en la consola de administración de Contentful, póngase en contacto con el [Soporte técnico de Contentful](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Incorporación de Contentful desde la galería de aplicaciones de Azure AD

Para administrar el aprovisionamiento en Contentful, agregue Contentful desde la galería de aplicaciones de Azure AD. Si ha configurado previamente Contentful para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda crear una aplicación independiente para probar inicialmente la integración. Obtenga información sobre cómo [agregar una aplicación en la galería](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Determinar quién estará en el ámbito de aprovisionamiento 

Puede usar el servicio de aprovisionamiento de Azure AD para definir quién se aprovisionará en función de la asignación a la aplicación o en función de los atributos del usuario o el grupo. 

Si elige determinar el ámbito de quién se aprovisionará en la aplicación en función de la asignación, puede realizar los pasos [para asignar usuarios y grupos a la aplicación](../manage-apps/assign-user-or-group-access-portal.md).

Si decide determinar el ámbito de quién se aprovisionará únicamente en función de los atributos del usuario o el grupo, puede usar un filtro de ámbito para [definir las reglas condicionales para el aprovisionamiento de cuentas de usuario](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Contentful, debe seleccionar un rol que no sea el de **Acceso predeterminado**. Los usuarios que tienen el rol de acceso predeterminado se excluyen del aprovisionamiento y se indican en los registros de aprovisionamiento como que no se han tenido en cuenta correctamente. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de la aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar más roles. 
* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo para todos. Cuando se establece el ámbito de aprovisionamiento en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Configuración del aprovisionamiento automático de usuarios en Contentful 

Esta sección le guiará por los pasos que debe realizar para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en una aplicación de prueba en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Configuración del aprovisionamiento automático de usuarios para Contentful en Azure AD

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y, a continuación, **Todas las aplicaciones**.

   ![Captura de pantalla que muestra el menú Aplicaciones empresariales en Azure Portal, con todas las aplicaciones resaltadas.](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Contentful**.

   ![Captura de pantalla que muestra los 20 primeros resultados que se han devuelto en la lista de aplicaciones.](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

   ![Captura de pantalla con la pestaña Aprovisionamiento resaltada en la sección Administrar del menú de la izquierda.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

   ![Captura de pantalla que muestra las opciones del Modo de aprovisionamiento, con la opción Automático resaltada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de la URL de inquilino de Contentful y el token secreto. Para asegurarse de que Azure AD puede conectarse a Contentful, seleccione **Probar conexión**. Si la conexión no se establece, asegúrese de que la cuenta de Contentful tiene permisos de administrador y pruebe otra vez.

   ![Captura de pantalla que muestra los cuadros de texto de la URL de inquilino y el token secreto, con el botón Probar conexión resaltado.](common/provisioning-testconnection-tenanturltoken.png)

6. En el cuadro **Correo electrónico de notificación**, escriba la dirección de correo electrónico de la persona o grupo que deba recibir las notificaciones de error de aprovisionamiento, y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

   ![Captura de pantalla que muestra el cuadro de texto de Correo electrónico de notificación.](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Contentful** (Sincronizar usuarios de Azure Active Directory con Contentful).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Contentful en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Contentful con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Contentful admita el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to Contentful** (Sincronizar grupos de Azure Active Directory con Contentful).

11. Revise los atributos de grupo que se sincronizan entre Azure AD y Contentful en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer correspondencia con los grupos de Contentful para operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    |Atributo|Tipo|Compatible con el filtrado|
    |---|---|---|
    |DisplayName|String|&check;|
    |members|Referencia|

12. Para configurar los filtros del ámbito, complete los pasos que se describen en el [tutorial del filtro del ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Contentful, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Captura de pantalla que muestra el cambio del estado de aprovisionamiento a activado y desactivado.](common/provisioning-toggle-on.png)

14. Para definir los usuarios o grupos que quiere aprovisionar en Contentful, seleccione la opción correspondiente en la opción **Ámbito** de la sección **Configuración**.

    ![Captura de pantalla que muestra las opciones que se pueden seleccionar en el panel del ámbito.](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

    ![Captura de pantalla que muestra los botones Guardar y Cancelar.](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en la opción **Ámbito** de la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="monitor-your-deployment"></a>Supervisión de la implementación

Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Para determinar qué usuarios se han aprovisionado correctamente y los que no, consulte los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md).
* Para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice, consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Si la configuración de aprovisionamiento parece ser incorrecta, la aplicación pasará a estar en cuarentena. Obtenga más información sobre los [estados de cuarentena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
