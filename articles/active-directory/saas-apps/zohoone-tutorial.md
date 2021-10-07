---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Zoho One'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 032586f43a43b57b14044aea98ce5719cbb3be93
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632792"
---
# <a name="tutorial-azure-ad-sso-integration-with-zoho-one"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Zoho One

En este tutorial, aprenderá a integrar Zoho One con Azure Active Directory (Azure AD). Si integra Zoho One con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Zoho One.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Zoho One con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Prerrequisitos

Para configurar la integración de Azure AD con Zoho One, se necesitan los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita utilizar el inicio de sesión único en Zoho One.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Zoho One admite el inicio de sesión único iniciado por **SP** e **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-zoho-one-from-the-gallery"></a>Incorporación de Zoho One desde la galería

Para configurar la integración de Zoho One en Azure AD, deberá agregar Zoho One desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zoho One** en el cuadro de búsqueda.
1. Seleccione **Zoho One** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Zoho One

Configure y pruebe el inicio de sesión único de Azure AD con Zoho One utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso vincular un usuario de Azure AD y el usuario correspondiente de Zoho One.

Para configurar y probar el inicio de sesión único de Azure AD con Zoho One, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Zoho One](#configure-zoho-one-sso)** : para configurar el inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Zoho One](#create-zoho-one-test-user)** : para tener un homólogo de B.Simon en Zoho One que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones **Zoho One**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba el valor: `one.zoho.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > El valor anterior de **URL de respuesta** no es real. El valor de `<saml-identifier>` lo obtendrá del paso 4 de la sección **Configuración del inicio de sesión único en Zoho One**, que se explica más adelante en el tutorial.

    c. Haga clic en **Establecer direcciones URL adicionales**.

    d. En el cuadro de texto **Estado de la retransmisión**, escriba la dirección URL `https://one.zoho.com`

5. Si desea configurar la aplicación en modo iniciado por **SP**, realice el siguientes paso:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > El valor de **URL de inicio de sesión** anterior no es real. Lo actualizará con la dirección URL de inicio de sesión real de la sección **Configuración del inicio de sesión único en Zoho One**, que se explica más adelante en el tutorial. 

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Configurar Zoho One**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a Zoho One.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Zoho One**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-zoho-one-sso"></a>Configuración del inicio de sesión único en Zoho One

1. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de Zoho One como administrador.

2. En la pestaña **Organización**, haga clic en **Configuración** en **Autenticación SAML**.

    ![Org. Zoho One](./media/zoho-one-tutorial/set-up.png)

3. En la página emergente, realice los pasos siguientes:

    ![Ini. Zoho One](./media/zoho-one-tutorial/save.png)

    a. En el cuadro de texto **Sign-in URL** (Dirección URL de inicio de sesión), pegue el valor de **Dirección URL de inicio de sesión** que copió de Azure Portal.

    b. En el cuadro de texto **Sign-out URL** (URL de cierre de sesión), pegue el valor de **Logout Out URL** (URL de cierre de sesión) que copió de Azure Portal.

    c. Haga clic en **Examinar** para cargar el **Certificado (Base64)** que ha descargado de Azure Portal.

    d. Haga clic en **Save**(Guardar).

4. Después de guardar la configuración de autenticación de SAML, copie el valor de **SAML-Identifier** (Identificador de SAML) y anéxelo a la **dirección URL de respuesta** en lugar de `<saml-identifier>`, por ejemplo, `https://accounts.zoho.com/samlresponse/one.zoho.com` y pegue el valor generado en el cuadro de texto **URL de respuesta** en la sección **Basic SAML Configuration** (Configuración básica de SAML).

    ![SAML de Zoho One](./media/zoho-one-tutorial/saml-identifier.png)

5. Vaya a la pestaña **Dominios** y haga clic en **Agregar dominio**.

    ![Dominio de Zoho One](./media/zoho-one-tutorial/add-domain.png)

6. En la página **Agregar dominio**, siga estos pasos:

    ![Agregar dominio de Zoho One](./media/zoho-one-tutorial/add-domain-name.png)

    a. En el cuadro de texto **Domain Name** (Nombre de dominio), escriba el dominio, por ejemplo, contoso.com.

    b. Haga clic en **Agregar**.

    >[!Note]
    >Después de agregar el dominio, siga [estos pasos](https://www.zoho.com/one/help/admin-guide/domain-verification.html) para comprobar el dominio. Una vez comprobado el dominio, use el nombre de dominio de **Dirección URL de inicio de sesión** de la sección **Basic SAML Configuration** (Configuración básica de SAML) de Azure Portal.

### <a name="create-zoho-one-test-user"></a>Creación de un usuario de prueba de Zoho One

Para permitir que los usuarios de Azure AD inicien sesión en Zoho One, deben aprovisionarse en Zoho One. En Zoho One, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en Zoho One como administrador de seguridad.

2. En la pestaña **Usuarios**, haga clic en **Logotipo de usuario**.

    ![Usuario de Zoho One](./media/zoho-one-tutorial/user.png)

3. En la página **Add User** (Agregar usuario), siga estos pasos:

    ![Agregar usuario de Zoho One](./media/zoho-one-tutorial/add-user.png)
    
    a. En el cuadro de texto **Nombre**, escriba el nombre de un usuario, por ejemplo, **Britta Simon**.
    
    b. En el cuadro de texto **Dirección de correo electrónico**, escriba el correo electrónico del usuario; por ejemplo, brittasimon@contoso.com.

    >[!Note]
    >Seleccione el dominio verificado en la lista de dominios.

    c. Haga clic en **Agregar**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. De este modo, accederá automáticamente a la dirección URL de inicio de sesión de Zoho One, donde podrá comenzar el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de Zoho One y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* En Azure Portal, haga clic en **Probar esta aplicación**. Al hacerlo, debería iniciar sesión automáticamente en la instancia de Zoho One en la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Cuando en Aplicaciones haga clic en el icono de Zoho One, si seleccionó el modo SP en la configuración, debería acceder automáticamente a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Por el contrario, si seleccionó el modo IDP en la configuración, debería iniciar sesión automáticamente en la instancia de Zoho One en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Zoho One, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).