---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SAP Cloud for Customer | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Cloud for Customer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: ad84982ddb20e56741212502a1aa3e1fccc9a9ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746304"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SAP Cloud for Customer

En este tutorial aprenderá a integrar SAP Cloud for Customer con Azure Active Directory (Azure AD). Al integrar SAP Cloud for Customer con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SAP Cloud for Customer.
* Permitir que los usuarios inicien sesión automáticamente en SAP Cloud for Customer con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).


## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en SAP Cloud for Customer.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAP Cloud for Customer el inicio de sesión único iniciado por **PS**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adición de SAP Cloud for Customer desde la galería

Para configurar la integración de SAP Cloud for Customer en Azure AD, es preciso agregar SAP Cloud for Customer desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAP Cloud for Customer** en el cuadro de búsqueda.
1. Seleccione **SAP Cloud for Customer** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Configuración y prueba del inicio de sesión único de Azure AD para SAP Cloud for Customer

Configure y pruebe el inicio de sesión único de Azure AD con SAP Cloud for Customer mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de SAP Cloud for Customer.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Cloud for Customer, complete los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de SAP Cloud for Customer](#configure-sap-cloud-for-customer-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** , para tener un homólogo de B.Simon en SAP Cloud for Customer que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SAP Cloud for Customer**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server name>.crm.ondemand.com`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación SAP Cloud for Customer espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos de usuario.

    ![Captura de pantalla que muestra el cuadro de diálogo "User Attributes" (Atributos de usuario) con el icono "Edit" (Editar) seleccionado.](common/edit-attribute.png)

1. En la sección **Atributos del usuario** del cuadro de diálogo **Atributos y notificaciones de usuario**, siga estos pasos:

    a. Haga clic en el **icono Editar** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![Captura de pantalla que muestra la sección "User Attributes & Claims" (Atributos y notificaciones del usuario) con el icono "Edit" (Editar) seleccionado.](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![imagen](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Seleccione **Transformación** como **origen**.

    c. En la lista **Transformación**, seleccione **ExtractMailPrefix()** .

    d. En la lista **Parámetro 1**, seleccione el atributo de usuario que desea usar en la implementación.
    Por ejemplo, si quiere usar EmployeeID como identificador de usuario único y ha almacenado el valor del atributo en ExtensionAttribute2, seleccione user.extensionattribute2.

    e. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar SAP Cloud for Customer**, copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

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

En esta sección, va a permitir que B.Simon acceda a SAP Cloud for Customer mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAP Cloud for Customer**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sap-cloud-for-customer-sso"></a>Configuración del inicio de sesión único de SAP Cloud for Customer

1. Abra una ventana del explorador web e inicie sesión en el sitio de empresa de SAP Cloud for Customer como administrador.

2. En el lado izquierdo del menú, haga clic en **Identity Providers** > **Corporate Identity Providers** > **Add** (Proveedores de identidades > Proveedores de identidades corporativas > Agregar) y, en el elemento emergente, agregue el nombre del proveedor de identidades como **Azure AD**; después, haga clic en **Save** (Guardar) y en **SAML 2.0 Configuration** (Configuración de SAML 2.0).

    ![Captura de pantalla que muestra la página "Identity Providers" (Proveedores de identidades) con el cuadro de texto "Add Identity Provider" (Agregar proveedor de identidades) resaltado y el botón "Save" (Guardar) seleccionado.](./media/sap-customer-cloud-tutorial/configure01.png)

3. En la sección **SAML 2.0 Configuration** (Configuración de SAML 2.0), realice los pasos siguientes:

    ![Captura de pantalla que muestra "S A M L 2.0 Configuration" (Configuración de S A M L 2.0) con el botón "Browse" (Explorar) seleccionado.](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Haga clic en **Browse** (Examinar) para cargar el archivo XML de metadatos de federación que descargó de Azure Portal.

    b. Cuando el archivo XML se haya cargado correctamente, se rellenarán de forma automática los valores siguientes; después, haga clic en **Save** (Guardar).

### <a name="create-sap-cloud-for-customer-test-user"></a>Creación de un usuario de prueba de SAP Cloud for Customer

Para permitir que los usuarios de Azure AD inicien sesión en SAP Cloud for Customer, deben aprovisionarse en esta última aplicación. En SAP Cloud for Customer, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en SAP Cloud for Customer como administrador de seguridad.

2. En el lado izquierdo del menú, haga clic en **Users & Authorizations** > **User administración** > **Add User** (Usuarios y autorizaciones > Administración de usuarios > Agregar usuario).

    ![Captura de pantalla que muestra la página "User Management" (Administración de usuarios) con el botón "Add User" (Agregar usuario) seleccionado.](./media/sap-customer-cloud-tutorial/configure03.png)

3. En la sección **Add New User** (Agregar nuevo usuario), lleve a cabo estos pasos:

    ![Configuración de SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre del usuario, en este caso **B**.

    b. En el cuadro de texto **Last Name** (Apellidos), escriba el apellido del usuario, en este caso **Simon**.

    c. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario, en este caso `B.Simon@contoso.com`.

    d. En el cuadro de texto **Login Name** (Nombre de inicio de sesión), escriba el nombre de usuario de la siguiente manera: **B.Simon**.

    e. Seleccione el valor de **User Type** (Tipo de usuario) adecuado a sus necesidades.

    f. Seleccione la opción de **Account Activation** (Activación de cuentas) según sus necesidades.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de SAP Cloud for Customer donde podrá iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de SAP Cloud for Customer e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de SAP Cloud for Customer en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de SAP Cloud for Customer. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado SAP Cloud for Customer, puede aplicar controles de sesión que protegen la información confidencial de la organización de la filtración y la infiltración en tiempo real. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).