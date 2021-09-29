---
title: 'Tutorial: Integración de Azure Active Directory con SAP HANA| Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: 69c209447ec76537a2dfa119acfe578f99e936b2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789738"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integración de Azure Active Directory con SAP HANA

En este tutorial, aprenderá a integrar SAP HANA con Azure Active Directory (Azure AD). Al integrar SAP HANA con Azure AD, puede hacer lo siguiente:

* Controlar quién tiene acceso en Azure AD a SAP HANA.
* Permitir que los usuarios puedan iniciar sesión automáticamente en SAP HANA con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP HANA, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de SAP HANA habilitada para el inicio de sesión único (SSO)
- Una instancia de HANA en ejecución en cualquier IaaS pública, instancias locales, VM de Azure o instancias grandes de SAP en Azure
- La interfaz web de administración de XSA, así como HANA Studio instalados en la instancia HANA

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción de SAP HANA. Pruebe primero la integración en el entorno de desarrollo o de ensayo de la aplicación y, después, use el entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/)
* Una suscripción habilitada para el inicio de sesión único en SAP HANA

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAP HANA admite el inicio de sesión único iniciado por **IDP**.
* SAP HANA admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.


## <a name="adding-sap-hana-from-the-gallery"></a>Adición de SAP HANA desde la galería

Para configurar la integración de SAP HANA en Azure AD, será preciso que agregue SAP HANA desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAP HANA** en el cuadro de búsqueda.
1. Seleccione **SAP HANA** en el panel de resultados y, después, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Configuración y prueba del inicio de sesión único de Azure AD para SAP HANA

Configure y pruebe el inicio de sesión único de Azure AD con SAP HANA mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de SAP HANA.

Para configurar y probar el inicio de sesión único de Azure AD con SAP HANA, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en SAP](#configure-sap-hana-sso)** : para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SAP HANA](#create-sap-hana-test-user)** : el objetivo es tener un homólogo de Britta Simon en SAP HANA que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **SAP HANA**, busque la sección **Administrar** y seleccione **inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte al cliente de SAP HANA](https://cloudplatform.sap.com/contact.html) para obtener los valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. La aplicación SAP HANA espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Atributos de usuario**.

    ![Captura de pantalla que muestra la sección "User Attributes" (Atributos de usuario) con el icono "Edit" (Editar) resaltado.](common/edit-attribute.png)

6. En la sección **Atributos del usuario** del cuadro de diálogo **Atributos y notificaciones de usuario**, siga estos pasos:
 
    a. Haga clic en el **icono Editar** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![Captura de pantalla que muestra el cuadro de diálogo "User Attributes & Claims" (Atributos y notificaciones del usuario), con el botón "Edit" (Editar) seleccionado.](./media/saphana-tutorial/tutorial_usermail.png)

    ![imagen](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. En la lista **Transformación**, seleccione **ExtractMailPrefix()** .

    c. Desde la lista **Parámetro 1**, seleccione **user.mail**.

    d. Haga clic en **Save**(Guardar).

7. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B. Simon acceda a SAP HANA mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAP HANA**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sap-hana-sso"></a>Configuración del inicio de sesión único de SAP HANA

1. Para configurar el inicio de sesión único en SAP HANA, inicie sesión en la **consola web de HANA XSA** en el punto de conexión HTTPS respectivo.

    > [!NOTE]
    > En la configuración predeterminada, la dirección URL redirige la solicitud a una pantalla de inicio de sesión, que requiere las credenciales de un usuario de base de datos de SAP HANA autenticado. El usuario que inicie sesión debe tener los permisos para realizar las tareas de administración de SAML.

2. En la interfaz web de XSA, vaya al **proveedor de identidades SAML**. A partir de ahí, seleccione el botón **+** en la parte inferior de la pantalla para mostrar el panel **Add Identity Provider Info** (Agregar información del proveedor de identidades). A continuación, siga estos pasos:

    ![Agregación del proveedor de identidades](./media/saphana-tutorial/sap1.png)

    a. En el panel **Add Identity Provider Info** (Agregar información del proveedor de identidades), pegue el contenido del archivo XML de metadatos, que descargó desde Azure Portal, en el cuadro **Metadatos**.

    ![Captura de pantalla que muestra el panel "Add Identity Provider Info" (Agregar información del proveedor de identidades), con los cuadros de texto "Metadata" (Metadatos) y "Name" (Nombre) resaltados.](./media/saphana-tutorial/sap2.png)

    b. Si el contenido del documento XML es válido, el proceso de análisis extrae la información necesaria para los campos **Asunto, Id. de entidad y Emisor** del área de pantalla **Datos generales**. También extrae la información necesaria para los campos de dirección URL del área de pantalla **Destino**, por ejemplo, los campos **URL base y Dirección URL de inicio de sesión único (*)** .

    ![Adición de la configuración del proveedor de identidades](./media/saphana-tutorial/sap3.png)

    c. En el cuadro **Nombre** del área de pantalla **Datos generales**, escriba un nombre para el nuevo proveedor de identidades de SSO de SAML.

    > [!NOTE]
    > El nombre del proveedor de identidades de SAML es obligatorio y debe ser único. Aparece en la lista de proveedores de identidades de SAML disponibles que se muestra cuando se selecciona SAML como método de autenticación que debe usarse para aplicaciones SAP HANA XS. Por ejemplo, puede hacer eso en el área de pantalla **Autenticación** de la herramienta XS Artifact Administration.

3. Seleccione **Guardar** para guardar los detalles del proveedor de identidades de SAML y para agregar el nuevo proveedor de identidades de SAML a la lista de conocidos.

    ![Botón Guardar](./media/saphana-tutorial/sap4.png)

4. En HANA Studio, en las propiedades del sistema de la pestaña **Configuración**, filtre las opciones de configuración por **saml**. A continuación, ajuste **assertion_timeout** de **10 s** a **120 s**.

    ![assertion_timeout setting](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Creación de un usuario de prueba en SAP HANA

Para permitir que los usuarios de Azure AD inicien sesión en SAP HANA, debe aprovisionarlos en SAP HANA.
SAP HANA admite el **aprovisionamiento Just-In-Time**, que está habilitado de forma predeterminada.

Si tiene que crear manualmente un usuario, siga los pasos siguientes:

>[!NOTE]
>Puede cambiar la autenticación externa que usa el usuario. Pueden autenticarse con un sistema externo, como Kerberos. Para obtener información detallada acerca de las identidades externas, póngase en contacto con su [administrador de dominio](https://cloudplatform.sap.com/contact.html).

1. Abra [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador y, después, active el usuario de base de datos de SSO de SAML.

    ![Crear usuario](./media/saphana-tutorial/sap5.png)

2. Seleccione la casilla invisible a la izquierda de **SAML** y, después, seleccione el vínculo **Configurar**.

3. Seleccione **Agregar** para agregar el proveedor de identidades de SAML.  Seleccione el proveedor de identidades de SAML adecuado y, después, seleccione **Aceptar**.

4. Agregue el valor de **External Identity** (Identidad externa) (en este caso, B.Simmon). Después, seleccione **Aceptar**.

   > [!Note]
   > Tiene que rellenar el campo **External Identity** (Identidad externa) del usuario, que debe coincidir con el campo **NameID** del token SAML de Azure AD. La casilla **Cualquiera** no se debe activar, ya que esta opción requiere que el IDP envíe la propiedad SPProvderID al campo NameID, lo cual no es compatible actualmente con Azure AD. Consulte [este](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/db6db355bb571014b56eb25057daec5f.html) documento para más detalles.

5. Para realizar pruebas, asigne todos los roles de **XS** para el usuario.

    ![Asignación de roles](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Debe conceder permisos adecuados solo para los casos de uso.

6. Guarde el usuario.

### <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de SAP HANA para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de SAP HANA en Aplicaciones, debería iniciar sesión automáticamente en la versión de SAP HANA para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado SAP HANA, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).