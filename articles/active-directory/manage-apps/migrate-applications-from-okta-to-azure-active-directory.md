---
title: Tutorial para migrar sus aplicaciones de Okta a Azure Active Directory
titleSuffix: Active Directory
description: Aprenda a migrar las aplicaciones de Okta a Azure Active Directory
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 5b91946886866d86542725155114d86ab5316f03
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440005"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>Tutorial: Migración de sus aplicaciones de Okta a Azure Active Directory

En este tutorial, aprenderá a migrar sus aplicaciones de Okta a Azure Active Directory (Azure AD).

## <a name="create-an-inventory-of-current-okta-applications"></a>Creación de un inventario de las aplicaciones actuales de Okta

Al convertir aplicaciones de Okta a Azure AD, se recomienda documentar el entorno actual y la configuración de las aplicaciones antes de efectuar la migración.

Okta ofrece una API que se puede usar para recopilar esta información desde una ubicación centralizada. Para usar la API, se requiere una herramienta de exploración de API como [Postman](https://www.postman.com/).

Siga estos pasos para crear un inventario de aplicaciones:

1. Instale la aplicación Postman. Después de la instalación, genere un token de API desde la consola de administración de Okta.

2. Vaya al panel de API en la sección Seguridad y seleccione **Tokens** > **Crear token**.

   ![imagen que muestra cómo crear tokens](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. Inserte un nombre de token y seleccione **Crear token**.

   ![imagen que muestra el token creado](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. Después de seleccionar **Crear token**, registre el valor y guárdelo, ya que no estará disponible después de seleccionar **De acuerdo**.

   ![imagen que muestra el registro creado](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. Una vez que haya registrado el token de API, vuelva a la aplicación Postman y seleccione **Importar** en el área de trabajo.

   ![imagen que muestra la API de importación](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. En la página Importar, seleccione el **vínculo** y use el siguiente vínculo para importar la API: <https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment>

   ![imagen que muestra el vínculo para importar](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

>[!NOTE]
>No modifique el vínculo con los valores del inquilino.

7. Para pasar al menú siguiente, seleccione **Importar**.

   ![imagen que muestra el siguiente menú de importación](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. Una vez realizada la importación, cambie la selección de entorno a **{yourOktaDomain}** .

   ![imagen que muestra el entorno de cambio](media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png)

9. Después de cambiar la selección del entorno, edite el entorno de Okta. Para ello, seleccione el botón del ojo y, a continuación, **Editar**.

   ![imagen que muestra el entorno de edición](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. Actualice los valores de la dirección URL y la clave de API en los campos **Valor inicial** y **Valor actual** cambiando también el nombre para que refleje el entorno y, a continuación, guarde los valores.

    ![image que muestra los valores de actualización de la API](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. Después de guardar la clave de API, [cargue la API de aplicaciones en Postman](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17).

12. Una vez que la API se haya cargado en Postman, seleccione la lista desplegable **Aplicaciones**, **Obtener aplicaciones de lista** y, por último, **Enviar**.

A partir de este momento podrá imprimir todas las aplicaciones del inquilino de Okta en formato JSON.

![imagen que muestra la lista de aplicaciones](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

Se recomienda copiar y convertir esta lista con formato JSON a CSV mediante una herramienta de conversión pública, como <https://konklone.io/json/> o PowerShell con [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json?view=powershell-7.1) y [ConvertTo-CSV](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertto-csv?view=powershell-7.1).

Después de descargar el archivo CSV, las aplicaciones del inquilino de Okta se habrán registrado correctamente y se podrán consultar en el futuro.

## <a name="migrate-a-saml-application-to-azure-ad"></a>Migración de una aplicación SAML a Azure AD

Para migrar una aplicación SAML 2.0 a Azure AD, debe configurar primero la aplicación en el inquilino de Azure AD con fines de acceso a la aplicación. En este ejemplo, vamos a convertir una instancia de Salesforce. Siga los pasos de [este tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial) para incorporar las aplicaciones.

Para finalizar el proceso de migración, repita los pasos de configuración con todas las aplicaciones detectadas en el inquilino de Okta.

1. Vaya al [portal de Azure AD](https://aad.portal.azure.com), seleccione **Azure Active Directory** >  **Aplicaciones empresariales** > **Nueva aplicación**.

   ![imagen que muestra la lista de nuevas aplicaciones](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. Salesforce está disponible en la galería de Azure AD. Busque la aplicación Salesforce, selecciónela y, a continuación, seleccione **Crear**.

   ![imagen que muestra la aplicación Salesforce](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. Una vez creada la aplicación, vaya a la pestaña de **inicio de sesión único** (SSO) y seleccione **SAML**.

   ![imagen que muestra la aplicación SAML](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. Después de seleccionar SAML, descargue el **archivo XML de metadatos de federación y el certificado (sin procesar)** para su importación a Salesforce.

   ![imagen que muestra la descarga de metadatos de federación](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. Una vez obtenido el XML, vaya a la consola de administración de Salesforce y seleccione **Identidad** > **Configuración de inicio de sesión único** > **Nuevo en Archivo de metadatos**.

   ![imagen que muestra la consola de administración de Salesforce](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. Cargue el archivo XML descargado del portal de Azure AD y seleccione **Crear**.

   ![imagen que muestra cómo cargar el archivo XML](media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png)

7. Cargue el certificado descargado de Azure y, a continuación, seleccione **Guardar** en el siguiente menú para crear el proveedor SAML en Salesforce.

   ![imagen que muestra la creación de un proveedor SAML](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. Registre los valores **Id. de entidad**, Dirección, **URL de inicio de sesión** y **URL de cierre de sesión** para su uso en Azure y seleccione la opción **Descargar metadatos**.

   ![imagen que muestra los valores de registro en Azure](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. Vuelva al menú de aplicaciones empresariales de Azure AD y **cargue el archivo de metadatos** en el portal de Azure AD, como parte de la configuración de SSO de SAML. Confirme que los valores importados coinciden con los valores registrados antes de guardarlos.

   ![imagen que muestra cómo cargar el archivo de metadatos en Azure AD](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. Una vez guardada la configuración de SSO, vuelva a la consola de administración de Salesforce y seleccione **Configuración de la empresa** > **Mi dominio**. Vaya a **Configuración de autenticación** y seleccione **Editar**.

    ![imagen que muestra cómo cargar la configuración de edición de la empresa](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. Seleccione el nuevo proveedor SAML configurado en los pasos anteriores como una opción de inicio de sesión disponible y después seleccione **Guardar**.

    ![imagen que muestra cómo guardar la opción de proveedor SAML](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. Vuelva a la aplicación empresarial en Azure AD, seleccione **Usuarios y grupos** y agregue **usuarios de prueba**.

    ![imagen que muestra cómo agregar un usuario de prueba](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. Para hacer la prueba, inicie sesión como uno de los usuarios de prueba, vaya a <https://aka.ms/myapps> y seleccione el icono de **Salesforce**.

    ![imagen que muestra el inicio de sesión como usuario de prueba](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. Después de seleccionar el icono de Salesforce, seleccione el proveedor de identidades (IdP) recién configurado para iniciar sesión.

    ![imagen que muestra cómo seleccionar un nuevo proveedor de identidades](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

15. Si todo se ha configurado correctamente, el usuario llegará a la página principal de Salesforce. Si hay algún problema, siga las indicaciones de la [guía de depuración](https://docs.microsoft.com/azure/active-directory/manage-apps/debug-saml-sso-issues).

16. Después de probar la conexión SSO desde Azure, vuelva a la aplicación empresarial para asignar los usuarios restantes a la aplicación Salesforce con los roles correctos.

>[!NOTE]
>Una vez agregados los usuarios restantes a la aplicación de Azure AD, se recomienda que los usuarios prueben la conexión para cerciorarse de que no haya ningún problema de acceso antes de dirigirse al paso siguiente.

17. Una vez que los usuarios han confirmado que no hay problemas de inicio de sesión, vuelva a la consola de administración de Salesforce y seleccione **Company Configuración de la empresa** > **Mi dominio**.

18. Vaya a **Configuración de autenticación** **,seleccione Editar** y desactive Okta como servicio de autenticación.

    ![imagen que muestra como cancelar la selección de Okta como servicio de autenticación](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce se ha configurado correctamente para el SSO en Azure AD. Los pasos para limpiar el portal de Okta se incluirán más adelante en este documento.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>Migración de una aplicación de OIDC/OAuth 2.0 a Azure AD

En primer lugar, configure la aplicación en el inquilino de Azure AD con fines de acceso. En este ejemplo, vamos a convertir una aplicación de OIDC personalizada.

Para finalizar el proceso de migración, repita los pasos de configuración con todas las aplicaciones detectadas en el inquilino de Okta.

1. Vaya al [portal de Azure AD](https://aad.portal.azure.com), seleccione **Azure Active Directory** >  **Aplicaciones empresariales**. Seleccione el menú **Todas las aplicaciones** y la opción **Nueva aplicación**.

2. Seleccione **Cree su propia aplicación**. En el menú lateral que aparece, asigne un nombre a la aplicación de OIDC y seleccione el botón de opción **Registrar una aplicación con la que está trabajando para integrarla con Azure AD** para integrarla con Azure AD y, a continuación, seleccione **Crear**.

   ![imagen que muestra la nueva aplicación OIDC](media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png)

3. En la página siguiente, tendrá que elegir un inquilino para el registro de la aplicación. Consulte [este artículo](https://docs.microsoft.com/azure/active-directory/develop/single-and-multi-tenant-apps) para más información.

En este ejemplo, seleccionaremos **Cuentas en cualquier directorio organizativo**, es decir, cualquier directorio de Azure AD, **Multiinquilino** y, por último, **Registro**.

![imagen que muestra el multiinquilino del directorio de Azure AD](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. Después de registrar la aplicación, vaya a la página **Registros de aplicaciones** en **Azure Active Directory** y abra el registro recién creado.

   En función del [escenario de la aplicación](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios), es posible que se necesiten varios pasos de configuración. Como la mayoría de los escenarios requieren el secreto de cliente de la aplicación, veremos esos ejemplos.

5. En la página **Información general**, registre el identificador de aplicación (cliente) para usarlo en la aplicación más adelante.

   ![imagen que muestra el identificador de cliente de la aplicación](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. Después de grabar el identificador de aplicación, seleccione **Certificados y secretos** en el menú de la izquierda. Seleccione **Nuevo secreto de cliente**, asígnele un nombre y establezca una fecha de expiración.

   ![imagen que muestra el nuevo secreto de cliente](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. Registre el valor y el identificador del secreto antes de salir de esta página.

>[!NOTE]
>Si pierde el secreto, tendrá que volver a generar uno, ya que no podrá registrar esta información posteriormente.

8. Después de registrar la información para los pasos anteriores, seleccione **Permisos de API** en la parte izquierda y conceda a la aplicación acceso a la pila de OIDC.

9. Seleccione **Agregar permiso**, **Microsoft Graph** y **Permisos delegados**.

10. En la sección **Permisos de OpenId**, agregue el correo electrónico, el valor de OpenID y el perfil. A continuación, seleccione **Agregar permisos**.

    ![imagen que muestra cómo agregar permisos OpenID](media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png)

11. Después de agregar los permisos, para mejorar la experiencia de usuario y suprimir las solicitudes de consentimiento del usuario, seleccione la opción **Grant admin consent for Tenant Domain Name** (Conceder consentimiento de administrador para nombre de dominio de inquilino) y espere a que aparezca el estado **Concedido**.

    ![imagen que muestra cómo conceder el consentimiento de administrador](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. Si la aplicación tiene un URI de redireccionamiento, o la URL de respuesta dirige a la pestaña **Autenticación**, seguido de **Agregar una plataforma** y **Web**,escriba la dirección URL adecuada. Para ello, seleccione Tokens de acceso y Tokens de identificador en la parte inferior y después seleccione **Configurar**.

    ![imagen que muestra cómo configurar tokens](media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png)

    Si es necesario, en **Avanzado**, en el menú Autenticación, establezca **Allow public client flows** (Permitir flujos de clientes público) en Sí.

    ![imagen que muestra cómo permitir flujos de clientes públicos](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. Vuelva a la aplicación configurada de OIDC e importe el identificador de aplicación y el secreto de cliente en la aplicación antes de hacer pruebas. Configure la aplicación para que use la configuración anterior, es decir, el valor de clientID, el secreto y los ámbitos.

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>Migración de un servidor de autorización personalizado a Azure AD

Los servidores de autorización de Okta asignan uno por uno los registros de aplicaciones que [exponen una API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-expose-web-apis#add-a-scope).

El servidor de autorización de Okta predeterminado debe asignarse a los ámbitos o permisos de Microsoft Graph.

![imagen que muestra la autorización de Okta predeterminada](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>Pasos siguientes 

- [Migración de la federación de Okta a Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migración del aprovisionamiento de sincronización de Okta a la sincronización basada en Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migración de directivas de inicio de sesión de Okta al acceso condicional de Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
