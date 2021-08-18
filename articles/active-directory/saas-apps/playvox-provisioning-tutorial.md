---
title: 'Tutorial: Configuración de Playvox para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD en Playvox.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: thwimmer
ms.openlocfilehash: 68342511a0cf4e6d432216c1d3a779c3bf19d051
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766398"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Tutorial: Configuración de Playvox para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Playvox y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente usuarios o grupos en [Playvox](https://www.playvox.com) mediante el servicio de aprovisionamiento de Azure AD. Para información importante sobre lo que hace este servicio y cómo funciona, y acceder a las preguntas frecuentes, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Playvox.
> * Eliminación de usuarios de Playvox cuando ya no necesiten acceso.
> * Mantenimiento de la sincronización de los atributos de usuario entre Azure AD y Playvox.

## <a name="prerequisites"></a>Requisitos previos

En el escenario de este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permisos](../roles/permissions-reference.md) para configurar el aprovisionamiento. Por ejemplo, una cuenta podría tener el rol de administrador de aplicaciones, administrador de aplicaciones en la nube, propietario de aplicaciones o administrador global.
* Una cuenta de usuario en [Playvox](https://www.playvox.com) con permisos de superadministrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1: Planeación de la implementación de aprovisionamiento

1. Conozca [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).

2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Determine qué datos se van a [asignar entre Azure AD y Playvox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Paso 2: Configuración de Playvox para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en la consola de administración de Playvox y vaya a **Settings > API Keys** (Configuración > Claves de API).

2. Elija **Create API Key** (Crear clave de API).

    ![Captura de pantalla parcial que muestra la ubicación del botón Create API Key (Crear clave de API) en la interfaz de usuario de Playvox.](media/playvox-provisioning-tutorial/create.png)

3. Escriba un nombre descriptivo para la clave de API y seleccione **Save** (Guardar). Una vez generada la clave de API, seleccione **Close** (Cerrar).

4. En la clave de API que ha creado, seleccione el icono **Details** (Detalles).

    ![Captura de pantalla parcial que muestra la ubicación del icono de detalles, que es una lupa, en la interfaz de usuario de Playvox.](media/playvox-provisioning-tutorial/api.png)

5. Copie y guarde el valor **BASE64 KEY** (CLAVE BASE64). Más adelante, en Azure Portal, especificará este valor en el cuadro de texto **Secret Token** (Token de secreto) en la pestaña **Provisioning** (Aprovisionamiento) de la aplicación Playvox.

    ![Captura de pantalla del cuadro de mensaje API Key (Clave de API) de la sección Details (Detalles) con el valor BASE64 KEY (Clave BASE64) resaltado.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Paso 3: Adición de Playvox desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento en Playvox, agregue Playvox a su inquilino de Azure AD desde la galería de aplicaciones. Para más información, vea esta [Inicio rápido: Incorporación de una aplicación al inquilino de Azure Active Directory (Azure AD)](../manage-apps/add-application-portal.md)

Si ha configurado previamente Playvox para el inicio de sesión único (SSO), puede usar la misma aplicación. Sin embargo, se recomienda crear una aplicación independiente al probar inicialmente la integración.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4: Determinar quién estará en el ámbito de aprovisionamiento

Usará el servicio de aprovisionamiento de Azure AD para definir quién se aprovisionará, en función de la asignación a la aplicación o según los atributos del usuario o el grupo. Para determinar quién se aprovisionará en la aplicación en función de la asignación, siga los pasos descritos en [Administración de la asignación de usuarios para una aplicación en Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) para aprender a asignar usuarios y grupos a la aplicación. Para determinar quién se aprovisionará en función únicamente de los atributos del usuario o el grupo, use un filtro de ámbito, tal como se describe en [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Recuerde estos puntos:

* Al asignar usuarios a Playvox, debe seleccionar un rol que no sea Acceso predeterminado. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar otros roles.

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios o grupos antes de implementarlo en todos. Cuando el ámbito de aprovisionamiento se basa en usuarios o grupos asignados, puede controlar el tamaño del conjunto asignando solo uno o dos usuarios o grupos a la aplicación. Cuando el ámbito de aprovisionamiento incluye todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para limitar el tamaño del conjunto de prueba.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Paso 5: Configuración del aprovisionamiento automático de usuarios en Playvox

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en función de las asignaciones de usuarios o grupos en Azure AD.

Para configurar el aprovisionamiento automático de usuarios para Playvox en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Captura de pantalla parcial de Azure Portal, con los elementos Aplicaciones empresariales y Todas las aplicaciones resaltados.](common/enterprise-applications.png)

2. En la lista de aplicaciones, busque y seleccione **Playvox**.

    ![Captura de pantalla parcial de la lista de aplicaciones, con el cuadro de búsqueda de aplicaciones resaltado.](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla parcial que muestra el elemento de menú Aprovisionamiento.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla parcial de la pestaña Aprovisionamiento, que muestra la opción Automático seleccionada en el cuadro de lista desplegable Modo de aprovisionamiento.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la **dirección URL de inquilino** de Playvox de la forma siguiente:

    `https://{tenant}.playvox.com/scim/v1`

    Escriba el **token secreto** que copió anteriormente en el paso 2. A continuación, seleccione **Probar conexión** para asegurarse de que Azure AD puede conectarse a Playvox. Si la conexión no se establece, asegúrese de que la cuenta de Playvox tenga permisos de administrador y pruebe otra vez.

    ![Captura de pantalla parcial que muestra la sección Credenciales de administrador, incluidos los cuadros de texto URL de inquilino y Token secreto, y con el vínculo Probar conexión resaltado.](common/provisioning-testconnection-tenanturltoken.png)

6. En el cuadro de texto **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que recibirá las notificaciones de error de aprovisionamiento. A continuación, seleccione la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Captura de pantalla parcial que muestra el cuadro de texto Correo electrónico de notificación y la casilla Notificación por correo electrónico.](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Playvox** (Sincronizar usuarios de Azure Active Directory con Playvox).

9. Examine los atributos de usuario que se sincronizan entre Azure AD y Playvox en la sección de **asignación de atributos**. Los atributos seleccionados como propiedades **Matching** (Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de Playvox con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Playvox admita el filtrado de usuarios basado en ese atributo. Para confirmar los cambios, seleccione **Guardar**.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |DisplayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. Para configurar los filtros de ámbito, consulte las instrucciones que se describen en [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para Playvox, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Captura de pantalla parcial de la sección Configuración, que muestra el estado de aprovisionamiento establecido en Activado.](common/provisioning-toggle-on.png)

12. Todavía en **Configuración**, defina los usuarios o grupos que se van a aprovisionar en Playvox eligiendo los valores que quiera en **Ámbito**.

    ![Captura de pantalla parcial de la sección Configuración, que muestra el cuadro de lista desplegable Ámbito.](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, seleccione **Guardar**.

    ![Captura de pantalla parcial que muestra las opciones Guardar y Descartar.](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo inicial tarda más que los ciclos posteriores. Las sincronizaciones se producen aproximadamente cada 40 minutos, siempre y cuando el servicio de aprovisionamiento de Azure AD esté en ejecución.

## <a name="step-6-monitor-your-deployment"></a>Paso 6: Supervisión de la implementación

Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente y los que no.
* Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
* Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Para más información sobre los estados de cuarentena, consulte [Aprovisionamiento de aplicaciones en el estado de cuarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)