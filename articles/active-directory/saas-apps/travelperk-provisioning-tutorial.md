---
title: 'Tutorial: Configuración de TravelPerk para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente cuentas de usuario de Azure AD a TravelPerk.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: thwimmer
ms.openlocfilehash: 44fb6342a8910a1edb27ea8e3a9f0ff12415ea2b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324771"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Tutorial: Configuración de TravelPerk para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en TravelPerk y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente usuarios y grupos a [TravelPerk](https://www.travelperk.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades admitidas

> [!div class="checklist"]
>
> - Creación de usuarios en TravelPerk
> - Eliminación de usuarios de TravelPerk cuando ya no necesiten acceso
> - Mantenimiento de la sincronización de los atributos de usuario entre Azure AD y TravelPerk
> - [Inicio de sesión único](./travelperk-tutorial.md) en TravelPerk (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

- [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
- Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global).
- Una cuenta de administrador de [TravelPerk](https://app.travelperk.com/signup) activa.
- Un [plan](https://www.travelperk.com/pricing/) Premium o Pro.


## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento

1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos se [asignarán entre Azure AD y TravelPerk](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de TravelPerk para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en la aplicación [TravelPerk](https://app.travelperk.com/company/integrations/scim) con su cuenta de administrador.

2. Vaya a **Configuración de la empresa** > **Integraciones** > **SCIM**

3. Haga clic en **Enable SCIM API** (Habilitar SCIM API).

   ![Habilitar](./media/travelperk-provisioning-tutorial/configuration.png)

4. También puede habilitar las aprobaciones a través de SCIM. Las aprobaciones le ayudan a establecer una gobernanza adicional asegurándose de que primero los aprobadores especificados aprueban los viajes. Puede obtener más información sobre ellas [aquí](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. Puede especificar si desea que el administrador de cada persona se convierta automáticamente en el usuario responsable de la aprobación de los viajes. Por lo tanto, se asignará un aprobador en el proceso de aprobación automática correspondiente. TravelPerk asignará el valor de **administrador** de Azure al aprobador deseado del usuario. El usuario debe existir en la plataforma antes de convertirse en el aprobador de usuarios aprovisionados.
No se crearán aprobadores si no están configurados correctamente en TravelPerk.

6. La creación del proceso de aprobación automática está disponible en la **configuración de SCIM** después de habilitar SCIM en la página de integraciones. Para activarla, seleccione **Through an Identity Provider** (A través de un proveedor de identidades) y cambie la alternancia de **Enable automatic approval process creation** (Habilitar la creación del proceso de aprobación automática).

7. Haga clic en **Guardar cambios** una vez configurado el proceso de aprobación necesario.

   ![Automatizar](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de TravelPerk desde la galería de aplicaciones de Azure AD

Agregue TravelPerk desde la galería de aplicaciones de Azure AD para iniciar la administración de aprovisionamiento a TravelPerk. Si ha configurado previamente TravelPerk para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- Al asignar usuarios a TravelPerk, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales.

- Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios a TeamViewer

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para TravelPerk en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

   ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **TravelPerk**.

   ![Vínculo a TravelPerk en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

   ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

   ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En **Credenciales de administrador**, haga clic en **Autorizar**. Se le redirigirá a la página de inicio de sesión de **TravelPerk**. Escriba su **Nombre de usuario** y **Contraseña** y haga clic en el botón **Iniciar sesión**. Haga clic en **Authorize App** (Autorizar aplicación) en la página de autorización. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a TravelPerk. Si la conexión no se establece, asegúrese de que la cuenta de SecureLogin tiene permisos de administrador e inténtelo de nuevo.

   ![Credenciales de administrador](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Pantalla de inicio](./media/travelperk-provisioning-tutorial/login.png)

   ![Acceso](./media/travelperk-provisioning-tutorial/authorization.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

   ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to TravelPerk** (Sincronizar usuarios de Azure Active Directory con TravelPerk).

9. Revise los atributos de usuario que se sincronizan desde Azure AD en TravelPerk en la sección **Attribute-Mapping** (Asignación: atributos). Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias en las cuentas de usuario de TravelPerk para realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de TravelPerk admite el filtrado de usuarios en función de ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   | Atributo                                                                         | Tipo      | Compatible con el filtrado |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | String    | &check;                 |
   | externalId                                                                        | String    |
   | active                                                                            | Boolean   |
   | name.honorificPrefix                                                              | String    |
   | name.familyName                                                                   | String    |
   | name.givenName                                                                    | String    |
   | name.middleName                                                                   | String    |
   | preferredLanguage                                                                 | String    |
   | locale                                                                            | String    |
   | phoneNumbers[type eq "work"].value                                                | String    |
   | externalId                                                                        | String    |
   | title                                                                             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager                | Referencia |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:gender                 | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:dateOfBirth            | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:invoiceProfiles        | Array     |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:emergencyContact.name  | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:emergencyContact.phone | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:travelPolicy           | String    |

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para TravelPerk, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Para definir los usuarios o grupos que quiere aprovisionar a TravelPerk, elija los valores deseados en **Ámbito**, en la sección **Configuración**.

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

- [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

- [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)