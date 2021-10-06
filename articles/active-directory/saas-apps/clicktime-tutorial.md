---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con ClickTime'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ClickTime.
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
ms.openlocfilehash: 81da4d29ef5cc394ea01279290f87df4bb08c4a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631620"
---
# <a name="tutorial-azure-ad-sso-integration-with-clicktime"></a>Tutorial: Integración del inicio de sesión único de Azure AD con ClickTime

En este tutorial, aprenderá a integrar ClickTime con Azure Active Directory (Azure AD). Al integrar ClickTime con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ClickTime.
* Permitir que los usuarios inicien sesión automáticamente en ClickTime con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en ClickTime.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* ClickTime admite el inicio de sesión único iniciado por **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-clicktime-from-the-gallery"></a>Adición de ClickTime desde la galería

Para configurar la integración de ClickTime en Azure AD, debe agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ClickTime** en el cuadro de búsqueda.
1. Seleccione **ClickTime** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-clicktime"></a>Configuración y prueba del inicio de sesión único de Azure AD para ClickTime

Configure y pruebe el inicio de sesión único de Azure AD con ClickTime mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ClickTime.

Para configurar y probar el inicio de sesión único de Azure AD con ClickTime, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en ClickTime](#configure-clicktime-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de ClickTime](#create-clicktime-test-user)** : para tener un homólogo de B.Simon en ClickTime que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **ClickTime**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la página **Configurar inicio de sesión único con SAML** realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://app.clicktime.com/sp/`

    b. En el cuadro de texto **URL de respuesta**, escriba una de las siguientes direcciones URL:

    | **URL de respuesta** |
    |----|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar ClickTime**, copie las direcciones URL adecuadas según sus requisitos.

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

En esta sección, permitirá que B.Simon acceda a ClickTime mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **ClickTime**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-clicktime-sso"></a>Configuración del inicio de sesión único de ClickTime

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de ClickTime como administrador.

1. En la barra de herramientas de la parte superior, haga clic en **Preferencias** y luego haga clic en **Configuración de seguridad**.

1. En la sección de configuración **Preferencias de inicio de sesión único** , siga estos pasos:

    ![Security Settings (Configuración de seguridad)](./media/clicktime-tutorial/toolbar.png "Configuración de seguridad")

    a.  Seleccione **Allow** sign-in using Single Sign-On (SSO) with **Azure AD** [Permitir inicio de sesión mediante inicio de sesión único (SSO) con Azure AD].

    b. En el cuadro de texto **Identity Provider Endpoint** (Punto de conexión del proveedor de identidades), pegue el valor de la **Dirección URL de inicio de sesión** que copió desde Azure Portal.

    c.  Abra el **certificado codificado en Base 64** que descargó de Azure Portal en el **Bloc de notas**, copie el contenido y, luego, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).

    d.  Haga clic en **Save**(Guardar).

### <a name="create-clicktime-test-user"></a>Creación de un usuario de prueba de ClickTime

Para permitir que los usuarios de Azure AD inicien sesión en ClickTime, deben aprovisionarse en ClickTime.  
En el caso de ClickTime, el aprovisionamiento es una tarea manual.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ClickTime ofrecida por ClickTime para aprovisionar cuentas de usuario de Azure AD.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **ClickTime** .

1. En la barra de herramientas de la parte superior, haga clic en **Compañía** y, luego, en **Contactos**.

    ![Captura de pantalla que muestra el inquilino de ClickTime con Compañía y Contactos seleccionados.](./media/clicktime-tutorial/account.png "Personas")

1. Haga clic en **Add Person**(Agregar persona).

    ![Add Person (Agregar persona)](./media/clicktime-tutorial/company.png "Agregar persona")

1. En la sección New Person (Nueva persona), lleve a cabo estos pasos:

    ![Captura de pantalla que muestra la sección Add Person (Agregar persona) en la que puede agregar la información de este paso.](./media/clicktime-tutorial/information.png "Nueva persona")

    a.  En el cuadro de texto de **nombre completo**, escriba el nombre completo de un usuario, por ejemplo, **Britta Simon**. 

    b.  En el cuadro de texto de **correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, **brittasimon\@contoso.com**.

    > [!NOTE]
    > Si lo desea, puede establecer propiedades adicionales del nuevo objeto persona.

    c.  Haga clic en **Save**(Guardar).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de ClickTime para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de ClickTime en Aplicaciones, debería iniciar sesión automáticamente en la instancia de ClickTime para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado ClickTime, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).