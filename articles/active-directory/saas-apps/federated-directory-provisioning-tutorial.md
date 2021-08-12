---
title: 'Tutorial: Configuración de Federated Directory para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente cuentas de usuario en Federated Directory.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: thwimmer
ms.openlocfilehash: 14560565b3d1d0f12bea1e74a164a7596cfc8b7a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767977"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configuración de Federated Directory para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se deben realizar en Federated Directory y Azure Active Directory (Azure AD) con el fin de configurar Azure AD para aprovisionar y desaprovisionar automáticamente usuarios o grupos en Federated Directory.

> [!NOTE]
>  Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector está actualmente en versión preliminar pública. Para más información sobre los términos de uso generales de Microsoft Azure para las características en versión preliminar, consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD.
* [Una instancia de Federated Directory](https://www.federated.directory/pricing).
* Una cuenta de usuario de Federated Directory con permisos de administrador.

## <a name="assign-users-to-federated-directory"></a>Asignación de usuarios a Federated Directory
Azure Active Directory usa el concepto de asignación para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Federated Directory. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a Federated Directory:

 * [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Sugerencias importantes para asignar usuarios a Federated Directory
 * Se recomienda asignar un único usuario de Azure AD a Federated Directory para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a Federated Directory, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de acceso predeterminado quedan excluidos del aprovisionamiento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configuración de Federated Directory para el aprovisionamiento

Antes de configurar Federated Directory para el aprovisionamiento automático de usuarios con Azure AD, deberá habilitar el aprovisionamiento SCIM en Federated Directory.

1. Inicie sesión en la [consola de administración de Federated Directory](https://federated.directory/of).

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Captura de pantalla de la consola de administración de Federated Directory con un campo para escribir el nombre de la empresa. Los botones de inicio de sesión también están visibles." border="false":::

2. Vaya a **Directories > User directories** (Directorios > Directorios del usuario) y seleccione el inquilino. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Captura de pantalla de la consola de administración de Federated Directory, con las opciones Directorios y Federated Directory Azure AD Test (Prueba de Azure AD de Federated Directory) resaltadas." border="false":::

3.  Para generar un token de portador permanente, vaya a **Directory Keys > Create New Key** (Claves del directorio > Crear clave). 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Captura de pantalla de la página Directory keys (Claves del directorio) de la consola de administración de Federated Directory. El botón Create new key (Crear clave) está resaltado." border="false":::

4. Cree una clave de directorio. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Captura de pantalla de la página Create directory key (Crear clave de directorio) de la consola de administración de Federated Directory, con los campos para el nombre y la descripción y un botón para crear la clave." border="false":::
    

5. Copie el valor de **Access Token** (Token de acceso). Este valor se escribe en el campo **Token secreto** de la pestaña Aprovisionamiento de la aplicación Federated Directory en Azure Portal. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Captura de pantalla de una página de la consola de administración de Federated Directory. Aparecen un marcador de posición del token de acceso y el nombre, la descripción y el emisor de una clave." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Incorporación de Federated Directory desde la galería

Para configurar Federated Directory para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar la aplicación desde la galería de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar Federated Directory desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Federated Directory** y seleccione **Federated Directory** en el panel de resultados.

    ![Federated Directory en la lista de resultados](common/search-new-app.png)

5. Vaya a la **dirección URL** resaltada a continuación desde un explorador diferente. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Captura de pantalla de una página de Azure Portal en la que se muestra información sobre Federated Directory. El valor de la dirección URL está resaltado." border="false":::

6. Haga clic en **INICIAR SESIÓN**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Captura de pantalla del menú principal del sitio de Federated Directory. El botón para iniciar sesión está resaltado." border="false":::

7.  Como Federated Directory es una aplicación de OpenIDConnect, inicie sesión en Federated Directory con su cuenta Microsoft profesional.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Captura de pantalla de la página SCIM AD test (Prueba de AD de SCIM) del sitio de Federated Directory. La opción para iniciar sesión con la cuenta de Microsoft está resaltada." border="false":::
 
8. Después de autenticarse, acepte la petición de consentimiento de la página de consentimiento. La aplicación se agregará automáticamente a su inquilino y se le redirigirá a su cuenta de Federated Directory.

    ![Federated Directory: incorporación de SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configuración del aprovisionamiento automático de usuarios en Federated Directory 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Federated Directory en función de las asignaciones de grupos y usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios de Federated Directory en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Federated Directory**.

    ![Vínculo a Federated Directory en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://api.federated.directory/v2/` en la URL de inquilino. Escriba el valor de Federated Directory que recuperó y guardó en **Token secreto**. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Federated Directory. Si la conexión no se establece, asegúrese de que la cuenta de Federated Directory tiene permisos de administrador y pruebe de nuevo.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

9. Haga clic en **Save**(Guardar).

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Federated Directory** (Sincronizar usuarios de Azure Active Directory con Federated Directory).

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Captura de pantalla de la sección Asignaciones. En Nombre, se resalta la opción para sincronizar usuarios de Azure Active Directory con Federated Directory." border="false":::
    
    
11. Examine los atributos de usuario que se sincronizan entre Azure AD y Federated Directory en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Matching** (Coincidente) se usan para buscar coincidencias con las cuentas de usuario de Federated Directory para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Captura de pantalla de la página Asignaciones de atributos. En la tabla se enumeran los atributos de Azure Active Directory y de Federated Directory y el estado de coincidencia." border="false":::
    

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Federated Directory, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Federated Directory.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Federated Directory.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../app-provisioning/check-status-user-account-provisioning.md).
## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
