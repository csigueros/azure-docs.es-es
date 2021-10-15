---
title: Tutorial para migrar sus aplicaciones de Okta a Azure Active Directory
titleSuffix: Active Directory
description: Aprenda a migrar las aplicaciones de Okta a Azure Active Directory.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 76acf97366407a34faa2f6a6583d5871ba4c5b4a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355946"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>Tutorial: Migración de sus aplicaciones de Okta a Azure Active Directory

En este tutorial, aprenderá a migrar sus aplicaciones de Okta a Azure Active Directory (Azure AD).

## <a name="create-an-inventory-of-current-okta-applications"></a>Creación de un inventario de las aplicaciones actuales de Okta

Antes de comenzar la migración, debe documentar la configuración actual del entorno y de la aplicación. Puede usar la API de Okta para recopilar esta información desde una ubicación centralizada. Para usar la API, necesita una herramienta de exploración de API como [Postman](https://www.postman.com/).

Siga estos pasos para crear un inventario de aplicaciones:

1. Instale la aplicación Postman. A continuación, genere un token de API desde la consola de administración de Okta.

2. En el panel de API, en la sección **Seguridad**, seleccione **Tokens** > **Crear token**.

   ![Captura de pantalla que muestra el botón para crear un token.](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. Inserte un nombre de token y seleccione **Crear token**.

   ![Captura de pantalla que muestra dónde escribir el nombre del token.](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. Registre el valor del token y guárdelo. Una vez que seleccione **De acuerdo**, ya no será accesible.

   ![Captura de pantalla que muestra el valor del token.](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. En la aplicación Postman, en el área de trabajo, seleccione **Importar**.

   ![Captura de pantalla que muestra la API de importación.](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. En la página **Importar**, seleccione **Vincular**. A continuación, inserte el vínculo siguiente para importar la API: `https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment`

   ![Captura de pantalla en la que se muestra el vínculo para importar.](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

   >[!NOTE]
   >No modifique el vínculo con los valores del inquilino.

7. A continuación, seleccione **Importar**.

   ![Captura de pantalla que muestra la siguiente página de Importación.](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. Una vez realizada la importación de la API, cambie la selección de **entorno** a **{suDominiodeOkta}** .

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png" alt-text="Captura de pantalla que muestra cómo cambiar el entorno." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png":::

9. Para editar el entorno de Okta, seleccione el icono de ojo. Después, seleccione **Editar**.

   ![Captura de pantalla que muestra cómo editar el entorno de Okta.](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. Actualice los valores de la URL y la clave de API en los campos **Valor inicial** y **Valor actual**. Cambie el nombre para que se refleje el entorno. A continuación, guarde los valores.

    ![Captura de pantalla que muestra cómo actualizar los valores de la API.](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. [Cargue la API en Postman](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17).

12. Seleccione **Aplicaciones** > **Obtener aplicaciones de lista** > **Enviar**.

A partir de este momento podrá imprimir todas las aplicaciones del inquilino de Okta. La lista está en formato JSON:

![Captura de pantalla que muestra una lista de aplicaciones en el inquilino de Okta.](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

Se recomienda que copie y convierta esta lista JSON a un formato CSV. Puede usar un convertidor público como [Konklone](https://konklone.io/json/). O bien, para PowerShell, use [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) y [ConvertTo-CSV.](/powershell/module/microsoft.powershell.utility/convertto-csv)

Descargue el archivo CSV para mantener un registro de las aplicaciones en el inquilino de Okta para futuras referencias.

## <a name="migrate-a-saml-application-to-azure-ad"></a>Migración de una aplicación SAML a Azure AD

Para migrar una aplicación SAML 2.0 a Azure AD, debe configurar primero la aplicación en el inquilino de Azure AD con fines de acceso a la aplicación. En este ejemplo, vamos a convertir una instancia de Salesforce. Siga los pasos de [este tutorial](../saas-apps/salesforce-tutorial.md) para configurar las aplicaciones.

Para finalizar la migración, repita los pasos de configuración con todas las aplicaciones detectadas en el inquilino de Okta.

1. Vaya al [portal de Azure AD](https://aad.portal.azure.com), seleccione **Azure Active Directory** > **Aplicaciones empresariales** > **Nueva aplicación**.

   ![Captura de pantalla que muestra una lista de nuevas aplicaciones.](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. En la **galería de Azure AD**, busque la aplicación **Salesforce**, selecciónela y, a continuación, seleccione **Crear**.

   ![Captura de pantalla que muestra la aplicación Salesforce en la galería de Azure AD.](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. Una vez creada la aplicación, en la pestaña **Inicio de sesión único**, seleccione **SAML**.

   ![Captura de pantalla que muestra la aplicación SAML.](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. Descargue el **archivo XML de metadatos de federación y el certificado (sin procesar)** para su importación en Salesforce.

   ![Captura de pantalla que muestra dónde descargar los metadatos de federación.](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. En la consola de administración de Salesforce, seleccione **Identidad** > **Configuración de inicio de sesión único** > **Nuevo archivo de metadatos**.

   ![Captura de pantalla que muestra la consola de administración de Salesforce.](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. Cargue el archivo XML descargado del portal de Azure AD. Seleccione **Crear**.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png" alt-text="Captura de pantalla que muestra dónde cargar el archivo XML." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png":::

7. Cargue el certificado que descargó de Azure. A continuación, seleccione **Guardar** para crear el proveedor SAML en Salesforce.

   ![Captura de pantalla que muestra la creación del proveedor SAML en Salesforce.](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. Guarde los valores de los campos siguientes. Usará estos valores en Azure. 
   * El **identificador de entidad**
   * **Dirección URL de inicio de sesión** 
   * **Dirección URL de cierre de sesión** 

   Después, seleccione **Descargar metadatos**.

   ![Captura de pantalla que muestra los valores que debe guardar para su uso en Azure.](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. Vuelva a la página **Aplicaciones empresariales** de Azure AD y, en la configuración de SSO de SAML, seleccione **Cargar el archivo de metadatos** para cargar el archivo en el portal de Azure AD. Antes de guardar, asegúrese de que los valores importados coinciden con los valores registrados.

   ![Captura de pantalla que muestra cómo cargar el archivo de metadatos en Azure AD.](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. En la consola de administración de Salesforce, seleccione **Configuración de la compañía** > **Mi dominio**. Vaya a **Configuración de autenticación** y seleccione **Editar**.

    ![Captura de pantalla que muestra cómo editar la configuración de la compañía.](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. Para una opción de inicio de sesión, seleccione el nuevo proveedor SAML que configuró anteriormente. Después, seleccione **Guardar**.

    ![Captura de pantalla que muestra dónde guardar la opción del proveedor SAML.](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. En Azure AD, en la página **Aplicaciones empresariales**, seleccione **Usuarios y grupos**. A continuación, agregue usuarios de prueba.

    ![Captura de pantalla que muestra los usuarios de prueba agregados.](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. Para probar la configuración, inicie sesión como uno de los usuarios de prueba. Vaya a la [galería de aplicaciones](https://aka.ms/myapps) de Microsoft y seleccione **Salesforce**.

    ![Captura de pantalla que muestra cómo abrir Salesforce desde la galería de aplicaciones.](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. Seleccione el proveedor de identidades (IdP) recién configurado para iniciar sesión.

    ![Captura de pantalla que muestra dónde iniciar sesión.](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

    Si todo se ha configurado correctamente, el usuario de prueba llegará a la página principal de Salesforce. Para obtener ayuda para solucionar problemas, consulte la [guía de depuración](../manage-apps/debug-saml-sso-issues.md).

16. En la página **Aplicaciones empresariales**, asigne los usuarios restantes a la aplicación Salesforce con los roles correctos.

    >[!NOTE]
    >Después de agregar los usuarios restantes a la aplicación de Azure AD, los usuarios deben probar la conexión para asegurarse de que tienen acceso. Pruebe la conexión antes de continuar en el paso siguiente.

17. En la consola de administración de Salesforce, seleccione **Configuración de la compañía** > **Mi dominio**.

18. En **Configuración de autenticación**, seleccione **Editar**. Desactive la selección de **Okta** como servicio de autenticación.

    ![Captura de pantalla que muestra dónde desactivar la selección de Okta como servicio de autenticación.](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce se ha configurado correctamente para el inicio de sesión único en Azure AD.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>Migración de una aplicación de OIDC/OAuth 2.0 a Azure AD

Para migrar una aplicación de OpenID Connect (OIDC) o OAuth 2.0 a Azure AD, en el inquilino de Azure AD, configure primero la aplicación para el acceso. En este ejemplo, vamos a convertir una aplicación de OIDC personalizada.

Para finalizar la migración, repita los siguientes pasos de configuración con todas las aplicaciones detectadas en el inquilino de Okta.

1. En el [portal de Azure AD](https://aad.portal.azure.com), seleccione **Azure Active Directory** > **Aplicaciones empresariales**. En **Todas las aplicaciones**, seleccione **Nueva aplicación**.

2. Seleccione **Cree su propia aplicación**. En el menú que aparece, asigne un nombre a la aplicación de OIDC y seleccione **Registrar una aplicación con la que está trabajando para integrarla con Azure AD**. Seleccione **Crear**.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png" alt-text="Captura de pantalla que muestra cómo crear una aplicación de OIDC." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png":::

3. En la página siguiente, configure los inquilinos del registro de la aplicación. Para más información, consulte [Inquilinos en Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

   En este ejemplo, seleccionaremos **Cuentas en cualquier directorio organizativo (cualquier directorio de Azure AD: multiinquilino)**  > **Registro**.

   ![Captura de pantalla que muestra cómo seleccionar varios inquilinos de directorio de Azure AD.](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. En la página **Registros de aplicaciones**, en **Azure Active Directory**, abra el registro recién creado.

   En función del [escenario de la aplicación](../develop/authentication-flows-app-scenarios.md), es posible que se necesiten varios pasos de configuración. Como la mayoría de los escenarios requieren el secreto de cliente de la aplicación, veremos esos escenarios.

5. En la página **Información general**, guarde el **identificador de aplicación (cliente)** . Usará este identificador en la aplicación.

   ![Captura de pantalla que muestra el identificador de cliente de la aplicación.](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. A la izquierda, seleccione **Certificados y secretos**. A continuación, seleccione **Nuevo secreto de cliente**. Asigne al secreto de cliente el nombre y establezca su expiración.

   ![Captura de pantalla que muestra el nuevo secreto de cliente.](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. Guarde el valor y el identificador del secreto.

   >[!NOTE]
   >Si pierde el secreto de cliente, no podrá recuperarlo. En su lugar, deberá volver a generar un secreto.

8. Seleccione **Permisos de API** en la parte izquierda. A continuación, conceda a la aplicación acceso a la pila de OIDC.

9. Seleccione **Agregar permiso** > **Microsoft Graph** > **Permisos delegados**.

10. En la sección **Permisos de OpenId**, agregue **Correo electrónico**, **openid** y **Perfil**. Seleccione **Agregar permisos**.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png" alt-text="Captura de pantalla que muestra dónde agregar permisos de Open ID." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png":::

11. Para mejorar la experiencia de usuario y suprimir las solicitudes de consentimiento del usuario, seleccione la opción **Grant admin consent for Tenant Domain Name** (Conceder consentimiento de administrador para nombre de dominio de inquilino) A continuación, espere a que aparezca el estado **Concedido**.

    ![Captura de pantalla que muestra dónde conceder el consentimiento del administrador.](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. Si la aplicación tiene un URI de redireccionamiento, escriba el URI adecuado. Si la dirección URL de respuesta tiene como destino la pestaña **Autenticación** seguida de **Agregar una plataforma** y **Web**, escriba la dirección URL adecuada. Seleccione **Tokens de acceso** y **Tokens de id.** A continuación, seleccione **Configurar**.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png" alt-text="Captura de pantalla que muestra cómo configurar tokens." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png":::
    
    En el menú **Autenticación**, en **Configuración avanzada** y en **Permitir flujos de clientes públicos**, seleccione **Sí**.

    ![Captura de pantalla que muestra cómo permitir flujos de clientes públicos.](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. En la aplicación configurada con OIDC, importe el identificador de la aplicación y el secreto de cliente antes de realizar la prueba. Siga los pasos anteriores para configurar la aplicación con valores como el identificador de cliente, el secreto y los ámbitos.

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>Migración de un servidor de autorización personalizado a Azure AD

Los servidores de autorización de Okta asignan uno por uno los registros de aplicaciones que [exponen una API](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope).

El servidor de autorización de Okta predeterminado debe asignarse a los ámbitos o permisos de Microsoft Graph.

![Captura de pantalla que muestra la autorización predeterminada de Okta.](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>Pasos siguientes 

- [Migración de la federación de Okta a Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migración del aprovisionamiento de sincronización de Okta a la sincronización basada en Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migración de directivas de inicio de sesión de Okta al acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
