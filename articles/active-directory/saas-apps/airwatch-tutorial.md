---
title: 'Tutorial: Integración de Azure Active Directory con AirWatch | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 0b6c102cc7f7bd7c8c32a2ebc04ddce5458a2563
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748276"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integración de AirWatch con Azure Active Directory

En este tutorial, aprenderá a integrar AirWatch con Azure Active Directory (Azure AD). Al integrar AirWatch con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AirWatch.
* Permitir que los usuarios puedan iniciar sesión automáticamente en AirWatch con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:
 
* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en AirWatch.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. 

* AirWatch admite el inicio de sesión único iniciado por **SP**.

## <a name="add-airwatch-from-the-gallery"></a>Incorporación de AirWatch desde la galería

Para configurar la integración de AirWatch en Azure AD, es preciso agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AirWatch** en el cuadro de búsqueda.
1. Seleccione **AirWatch** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Configuración y prueba del inicio de sesión único de Azure AD para AirWatch

Configure y pruebe el inicio de sesión único de Azure AD con AirWatch mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de AirWatch.

Para configurar y probar el inicio de sesión único de Azure AD con AirWatch, realice los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en AirWatch](#configure-airwatch-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de AirWatch](#create-airwatch-test-user)** : para tener un homólogo de B.Simon en AirWatch vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **AirWatch**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. En el cuadro de texto **Identificador (Id. de entidad)** , escriba el valor como: `AirWatch`

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de AirWatch](https://www.air-watch.com/company/contact-us/) para obtenerlo. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación AirWatch espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Atributos de usuario**.

    ![imagen](common/edit-attribute.png)

1. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, edite las notificaciones mediante el **icono Editar** o agregue notificaciones mediante **Agregar nueva notificación** para configurar el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:

    | Nombre |  Atributo de origen|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Deje **Espacio de nombres** en blanco.

    d. Seleccione **Atributo** como origen.

    e. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    f. Haga clic en **Aceptar**.

    g. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el archivo XML de metadatos y guardarlo en su equipo.

   ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar AirWatch**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a AirWatch mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AirWatch**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-airwatch-sso"></a>Configuración del inicio de sesión único de AirWatch

1. En otra ventana del explorador web, inicie sesión en el sitio de AirWatch de la compañía como administrador.

1. En la página de configuración. Seleccione **Settings > Enterprise Integration > Directory Services** (Configuración > Integración empresarial > Servicios de directorio).

   ![Configuración](./media/airwatch-tutorial/services.png "Configuración")

1. Haga clic en la pestaña **Usuario**; en el cuadro de texto **DN base**, escriba el nombre de dominio y haga clic en **Guardar**.

   ![Captura de pantalla donde se resalta el cuadro de texto DN base.](./media/airwatch-tutorial/user.png "Usuario")

1. Haga clic en la pestaña **Server** (Servidor).

   ![Server](./media/airwatch-tutorial/directory.png "Server")

1. En la sección **LDAP**, realice los pasos siguientes:

    ![Captura de pantalla que muestra los cambios que debe realizar en la sección LDAP.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. En **Directory Type** (Tipo de directorio), seleccione **None** (Ninguno).

    b. Seleccione **Use SAML For Authentication**(Usar SAML para autenticación).

1. En la sección **SAML 2.0**, para cargar el certificado descargado, haga clic en **Upload** (Cargar).

    ![Cargar](./media/airwatch-tutorial/uploads.png "Cargar")

1. En la sección **Request** (Solicitud), siga estos pasos:

    ![Sección Solicitud](./media/airwatch-tutorial/request.png "Solicitud")  

    a. En **Request Binding Type** (Tipo de enlace de solicitud), seleccione **POST**.

    b. En Azure Portal, en la página de diálogo **Configurar inicio de sesión único en Airwatch**, copie el valor de **Dirección URL de inicio de sesión** y péguelo en el cuadro de texto **Identity Provider Single Sign On URL** (Dirección URL de inicio de sesión del proveedor de identidades).

    c. En la lista **NameID Format** (Formato de NameID), seleccione **Email address** (Dirección de correo electrónico).

    d. En **Authentication Request Security** (Seguridad de solicitud de autenticación), seleccione **None** (Ninguno).

    e. Haga clic en **Save**(Guardar).

1. Haga clic de nuevo en la pestaña **User** (Usuario).

    ![User](./media/airwatch-tutorial/users.png "Usuario")

1. En la sección **Attribute** (Atributo), realice estos pasos:

    ![Atributo](./media/airwatch-tutorial/attributes.png "Atributo")

    a. En el cuadro de texto **Identificador de objetos**, escriba `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. En el cuadro de texto **Nombre de usuario**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. En el cuadro de texto **Nombre para mostrar**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. En el cuadro de texto **Nombre**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. En el cuadro de texto **Apellidos**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. En el cuadro de texto **Correo electrónico**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Haga clic en **Save**(Guardar).

### <a name="create-airwatch-test-user"></a>Creación de un usuario de prueba de AirWatch

Para permitir que los usuarios de Azure AD inicien sesión en AirWatch, tienen que aprovisionarse en AirWatch. En el caso de AirWatch, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en el sitio de la compañía de **AirWatch** como administrador.

2. En el panel de navegación izquierdo, haga clic en **Accounts** (Cuentas) y luego en **Users** (Usuarios).
  
   ![Usuarios](./media/airwatch-tutorial/accounts.png "Usuarios")

3. En el menú **Users** (Usuarios), haga clic en **List View** (Vista de lista) y, a continuación, haga clic en **Add > Add User** (Agregar > Agregar usuario).
  
   ![Captura de pantalla donde se resaltan los botones Agregar y Agregar usuario.](./media/airwatch-tutorial/add.png "Agregar usuario")

4. En el cuadro de diálogo **Add / Edit User** (Agregar/Editar usuario), realice los siguientes pasos:

   ![Agregar usuario](./media/airwatch-tutorial/save.png "Agregar usuario")

   a. Especifique **Username** (Nombre de usuario), **Password** (Contraseña), **Confirm Password** (Confirmar contraseña), **First Name** (Nombre), **Last Name** (Apellido), **Email Address** (Correo electrónico) de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.

   b. Haga clic en **Save**(Guardar).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AirWatch ofrecida por AirWatch para aprovisionar cuentas de usuario de Azure AD.

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de AirWatch, donde puede iniciar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de AirWatch y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de AirWatch en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de AirWatch. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado AirWatch, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).