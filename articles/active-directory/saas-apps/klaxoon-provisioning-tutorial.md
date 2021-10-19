---
title: 'Tutorial: Configuración de Klaxoon para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo aprovisionar y desaprovisionar automáticamente cuentas de usuario de Azure AD en Klaxoon.
services: active-directory
documentationcenter: ''
author: twimmer
writer: Thwimmer
manager: beatrizd
ms.assetid: b7a61926-171c-415b-858f-54f6e53515f2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2021
ms.author: Thwimmer
ms.openlocfilehash: 6ec20f6d2b638385901d9934bf3634e639708131
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716327"
---
# <a name="tutorial-configure-klaxoon-for-automatic-user-provisioning"></a>Tutorial: Configuración de Klaxoon para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en Klaxoon y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona usuarios y grupos automáticamente en [Klaxoon](https://www.Klaxoon.com/) mediante su servicio de aprovisionamiento. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en Klaxoon.
> * Deshabilitación de usuarios en Klaxoon cuando ya no necesiten acceso.
> * Sincronización de los atributos de usuario entre Azure AD y Klaxoon.
> * Concesión de licencias a usuarios de Klaxoon en función de grupos de Azure AD.
> * [Inicio de sesión único](klaxoon-saml-tutorial.md) en Klaxoon (recomendado).

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md). 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un [contrato de Klaxoon](https://klaxoon.com/enterprise) en vigor.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
1. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine qué datos quiere [asignar entre Azure AD y Klaxoon](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-klaxoon-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Klaxoon para admitir el aprovisionamiento con Azure AD

* Póngase en contacto con [Klaxoon](https://klaxoon.com/) para recibir una **dirección URL de inquilino** única y un **token secreto**.

## <a name="step-3-add-klaxoon-from-the-azure-ad-application-gallery"></a>Paso 3. Agregación de Klaxoon desde la galería de aplicaciones de Azure AD

Agregue Klaxoon desde la galería de aplicaciones de Azure AD para empezar a administrar su aprovisionamiento. Si ha configurado antes Klaxoon para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Al asignar usuarios y grupos a Klaxoon, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-klaxoon"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Klaxoon 

En esta sección se le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD, con el fin de crear, actualizar y deshabilitar usuarios o grupos en Klaxoon en función de asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-klaxoon-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Klaxoon en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

1. En la lista de aplicaciones, seleccione **Klaxoon**.

    ![Vínculo a Klaxoon en la lista de aplicaciones](common/all-applications.png)

1. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

1. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

1. En la sección **Credenciales del administrador**, escriba la dirección URL del inquilino de Klaxoon y el token secreto que proporciona la herramienta. Haga clic en **Probar conexión** para asegurarse de que Azure AD pueda conectarse a Klaxoon. Si se produce un error en la conexión, póngase en contacto con Klaxoon para comprobar la configuración de la cuenta.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

1. Seleccione **Guardar**.

1. En la sección **Asignaciones**, seleccione **Sincronizar usuarios de Azure Active Directory con Klaxoon**.

1. Revise los atributos de usuario sincronizados entre Azure AD y Klaxoon en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **coincidentes** se usan para buscar coincidencias con las cuentas de usuario de Klaxoon, con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Klaxoon admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|Requerido por Klaxoon|
   |---|---|---|---|
   |userName|String|&check;|&check;|
   |emails[type eq "work"].value|String|&check;|&check;|
   |externalId|String||&check;|
   |name.givenName|String||&check;|
   |name.familyName|String||&check;|
   |active|Boolean||&check;|

1. En la sección **Asignaciones**, seleccione **Sincronizar grupos de Azure Active Directory con Klaxoon**.

1. Revise los atributos de grupo sincronizados entre Azure AD y Klaxoon en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **coincidentes** se usan para buscar coincidencias con los grupos de Klaxoon, con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|Compatible con el filtrado|Requerido por Klaxoon|
      |---|---|---|---|
      |DisplayName|String|&check;|&check;
      |externalId|String||&check;
      |members|Referencia||
      |urn:ietf:params:scim:schemas:extension:klaxoon:2.0:Group:license|String||


1. Defina el atributo **urn:ietf:params:scim:schemas:extension:xsdiqueon:2.0:Group:license** para proporcionar licencias PRO de Klaxoon a los usuarios vinculados a un grupo.

      |Valor|Grupo con licencia PRO de Klaxoon|
      |---|---|
      |true|&check;|
      |false|No|
      |no especificado (valor predeterminado)|&check;|

1. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Con el fin de habilitar el servicio de aprovisionamiento de Azure AD para Klaxoon, cambie **Estado del aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

1. Seleccione los valores deseados en **Ámbito**, en la sección **Configuración**, con el fin de definir los usuarios o grupos que quiera aprovisionar en Klaxoon.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

1. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

* Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
* Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
* Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="more-resources"></a>Más recursos

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)