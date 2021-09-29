---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Boomi | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 682fdc1069a9a54544ebc660edb16269a6930dfd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817657"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Boomi

En este tutorial aprenderá a integrar Boomi con Azure Active Directory (Azure AD). Al integrar Boomi con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Boomi.
* Permitir que los usuarios inicien sesión automáticamente en Boomi con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Boomi.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Boomi permite utilizar el inicio de sesión único con **IDP**.

## <a name="add-boomi-from-the-gallery"></a>Incorporación de Boomi desde la galería

Para configurar la integración de Boomi en Azure AD, deberá agregar Boomi desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Boomi** en el cuadro de búsqueda.
1. Seleccione **Boomi** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Boomi

Configure y pruebe el inicio de sesión único de Azure AD con Boomi mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Boomi.

Para configurar y probar el inicio de sesión único de Azure AD con Boomi, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Boomi](#configure-boomi-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en Boomi](#create-boomi-test-user)** : para tener un homólogo de B.Simon en Boomi vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Boomi**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si tiene el **archivo de metadatos del proveedor de servicios** y quiere realizar la configuración en el modo iniciado por **IdP**, siga este procedimiento:

    a. Haga clic en **Cargar el archivo de metadatos**.

    ![Carga del archivo de metadatos](common/upload-metadata.png)

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.

    ![Elección del archivo de metadatos](common/browse-upload-metadata.png)

    c. Una vez que se haya cargado correctamente el archivo de metadatos, el valor de **Identificador** y **URL de respuesta** se rellena automáticamente en la sección Configuración básica de SAML.

    d. Escriba la **dirección URL de inicio de sesión**, por ejemplo, `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}`.

    > [!Note]
    > Obtendrá el **archivo de metadatos del proveedor de servicios** en la sección **Configuración del inicio de sesión único en Boomi**, que se explica más adelante en el tutorial. Si los valores **Identificador** y **Dirección URL de respuesta** no se rellenan automáticamente, hágalo manualmente según sus necesidades.

1. La aplicación espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![Captura de pantalla que muestra User Attributes & Claims (Atributos y reclamaciones del usuario) con valores predeterminados, como emailaddress User.mail y givenname user.givenname.](common/default-attributes.png)

1. Además de lo anterior, la aplicación Boomi espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre |  Atributo de origen|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Boomi** (Configurar Boomi), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Boomi mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Boomi**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-boomi-sso"></a>Configuración del inicio de sesión único en Boomi

1. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de Boomi como administrador.

1. Vaya a **Company Name** (Nombre de la compañía) y haga clic en **Set up** (Configurar).

1. Haga clic en la pestaña **SSO Options** (Opciones de SSO) y realice los siguientes pasos.

    ![Configuración del inicio de sesión único en la aplicación](./media/boomi-tutorial/import.png)

    a. Marque la casilla **Enable SAML Single Sign-On** (Habilitar el inicio de sesión único de SAML).

    b. Haga clic en **Import** (Importar) para cargar el certificado descargado de Azure AD en **Identity Provider Certificate** (Certificado del proveedor de identidades).

    c. En el cuadro de texto **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades), coloque el valor de **Dirección URL de inicio de sesión** de la ventana de configuración de aplicaciones de Azure AD.

    d. En **Federation Id Location** (Ubicación del identificador de federación), seleccione el botón de radio **Federation Id is in FEDERATION_ID Attribute element** (El id. de federación está en el elemento de atributo FEDERATION_ID).

    e. Copie el valor de **AtomSphere MetaData URL** (Dirección URL de metadatos de AtomSphere), vaya a la **dirección URL de metadatos** a través del explorador que prefiera y guarde la salida en un archivo. Cargue la **dirección URL de metadatos** en la sección **Configuración básica de SAML** en Azure Portal.

    f. Haga clic en el botón **Guardar** .

### <a name="create-boomi-test-user"></a>Creación de un usuario de prueba de Boomi

Para permitir que los usuarios de Azure AD inicien sesión en Boomi, es necesario aprovisionar Boomi con ellos. En el caso de Boomi, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en el sitio de la empresa de Boomi como administrador.

1. Después de iniciar sesión, vaya a **User Management** (Administración de usuarios) y vaya a **Users** (Usuarios).

    ![Captura de pantalla que muestra la página de User Management (Administración de usuarios) con Users (Usuarios) seleccionados.](./media/boomi-tutorial/user.png "Usuarios")

1. Haga clic en el icono **+** , se abre el cuadro de diálogo **Add/Maintain User Roles** (Agregar o mantener roles de usuario).

    ![Captura de pantalla que muestra el icono + seleccionado.](./media/boomi-tutorial/add.png "Usuarios")

    ![Captura de pantalla muestra Add / Maintain User Roles (Agregar o mantener roles de usuario) donde se configuran los usuarios.](./media/boomi-tutorial/roles.png "Usuarios")

    a. En el cuadro de texto **Dirección de correo electrónico del usuario**, escriba la dirección de correo electrónico de un usuario, por ejemplo, B.Simon@contoso.com.

    b. En el cuadro de texto **First name** (Nombre), escriba el nombre del usuario, en este caso, B.

    c. En el cuadro de texto **Apellidos**, escriba el nombre del usuario, en este caso, Simon.

    d. Escriba el **id. de federación** del usuario. Cada usuario debe tener un id. de federación que identifica de forma única al usuario dentro de la cuenta.

    e. Asigne el rol **Usuario estándar** al usuario. No asigne el rol Administrador porque les proporcionaría acceso normal a Atmosphere, además de acceso de inicio de sesión único.

    f. Haga clic en **OK**.

    > [!NOTE]
    > El usuario no recibirá un correo electrónico de notificación de bienvenida con una contraseña que se pueda usar para iniciar sesión en la cuenta de AtomSphere porque su contraseña se administra mediante el proveedor de identidades. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Boomi que proporcione Boomi para aprovisionar cuentas de usuario de AAD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* En Azure Portal, haga clic en "Probar esta aplicación". Al hacerlo, debería iniciar sesión automáticamente en la instancia de Boomi en la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Si hace clic en el icono de Boomi de Aplicaciones, debería iniciar sesión automáticamente en la solución Boomi en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Boomi, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).