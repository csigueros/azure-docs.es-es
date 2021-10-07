---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con ExpenseIn'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ExpenseIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2021
ms.author: jeedes
ms.openlocfilehash: 3e61a1d206b7e39e3f464556e295cb47779debde
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596731"
---
# <a name="tutorial-azure-ad-sso-integration-with-expensein"></a>Tutorial: Integración del inicio de sesión único de Azure AD con ExpenseIn

En este tutorial, obtendrá información sobre cómo integrar ExpenseIn con Azure Active Directory (Azure AD). Al integrar ExpenseIn con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ExpenseIn.
* Permitir que los usuarios puedan iniciar sesión automáticamente en ExpenseIn con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción de ExpenseIn con el inicio de sesión único (SSO) habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. 

* ExpenseIn admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-expensein-from-the-gallery"></a>Adición de ExpenseIn desde la galería

Para configurar la integración de ExpenseIn en Azure AD, deberá agregar ExpenseIn desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ExpenseIn** en el cuadro de búsqueda.
1. Seleccione **ExpenseIn** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Configuración y prueba del inicio de sesión único de Azure AD para ExpenseIn

Configure y pruebe el inicio de sesión único de Azure AD con ExpenseIn utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de ExpenseIn.

Para configurar y probar el inicio de sesión único de Azure AD con ExpenseIn, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en ExpenseIn](#configure-expensein-sso)** , para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en ExpenseIn](#create-expensein-test-user)** , para tener un homólogo de B.Simon en ExpenseIn que esté vinculado a la representación del usuario en Azure AD.
1. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **ExpenseIn**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.expensein.com/saml`

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **dirección URL de metadatos de federación de aplicación** y haga clic en **Descargar** para descargar el **certificado (Base64)** y guardarlo en el equipo.

   ![Vínculo de descarga del certificado](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. En la sección **Set up ExpenseIn** (Configurar ExpenseIn), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a ExpenseIn mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **ExpenseIn**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-expensein-sso"></a>Configuración del inicio de sesión único de ExpenseIn

1. Para automatizar la configuración en ExpenseIn, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Configurar ExpenseIn** para ir a la aplicación ExpenseIn. En ella, escriba las credenciales de administrador para iniciar sesión en ExpenseIn. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 5.

    ![Configuración](common/setup-sso.png)

1. Si desea configurar ExpenseIn manualmente, inicie sesión en el sitio de la empresa ExpenseIn como administrador.

1. Haga clic en **Admin** en la parte superior de la página, a continuación, vaya a **Single Sign-On** (Inicio de sesión único) y haga clic en **Add provider** (Agregar proveedor).

     ![Captura de pantalla que muestra la pestaña "Admin" (Administrador) y la página "Single Sign-On - Providers" (Inicio de sesión único - Proveedores) y la opción "Add Provider" (Agregar proveedor) seleccionadas.](./media/expenseIn-tutorial/admin.png)

1. En el elemento emergente **New Identity Provider** (Nuevo proveedor de identidades), realice los pasos siguientes:

    ![Captura de pantalla que muestra el elemento emergente "Edit Identity Provider" (Editar proveedor de identidad) con los valores especificados.](./media/expenseIn-tutorial/certificate.png)

    a. En el cuadro de texto **Provider Name** (Nombre del proveedor), escriba el nombre (por ejemplo, Azure).

    b. Seleccione **Sí** en **Allow Provider Initiated Sign-On** (Permitir el inicio sesión iniciado por el proveedor).

    c. En el cuadro de texto **Target Url** (Dirección URL de destino), pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    d. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **Identificador Azure AD** que ha copiado de Azure Portal.

    e. Abra el certificado (Base64) en el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **Certificate** (Certificado).

    f. Haga clic en **Crear**.

### <a name="create-expensein-test-user"></a>Creación de un usuario de prueba de ExpenseIn

Para permitir que los usuarios de Azure AD inicien sesión en ExpenseIn, deben aprovisionarse en ExpenseIn. En ExpenseIn, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en ExpenseIn como administrador.

2. Haga clic en **Admin** en la parte superior de la página, a continuación, vaya a **Users** (Usuarios) y haga clic en **New User** (Nuevo usuario).

     ![Captura de pantalla que muestra la pestaña "Admin" (Administrador) y la página "Manage Users" (Administrar usuarios) con la opción "New User" (Nuevo usuario) seleccionada.](./media/expenseIn-tutorial/users.png)

3. En el elemento emergente **Detalles**, lleve a cabo los pasos siguientes:

    ![Configuración de ExpenseIn](./media/expenseIn-tutorial/details.png)

    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre del usuario, en este caso **B**.

    b. En el cuadro de texto **Last Name** (Apellidos), escriba el nombre de usuario, en este caso **Simon**.

    c. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario, por ejemplo, `B.Simon@contoso.com`.

    d. Haga clic en **Crear**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de ExpenseIn, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de ExpenseIn e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de ExpenseIn para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de ExpenseIn en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de ExpenseIn para la que haya configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que configure ExpenseIn, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).