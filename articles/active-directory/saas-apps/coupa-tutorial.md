---
title: 'Tutorial: Integración de Azure Active Directory con Coupa | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: a91559570a529c7f9a97770cf1971b038136c6de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802984"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integración de Azure Active Directory con Coupa

En este tutorial, aprenderá a integrar Coupa con Azure Active Directory (Azure AD). Al integrar Coupa con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Coupa.
* Permitir que los usuarios inicien sesión automáticamente en Coupa con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Coupa.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Coupa admite el inicio de sesión único iniciado por **SP**

## <a name="add-coupa-from-the-gallery"></a>Adición de Coupa desde la galería

Para configurar la integración de Coupa en Azure AD, deberá agregar Coupa desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Coupa** en el cuadro de búsqueda.
1. Seleccione **Coupa** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Configuración y prueba del SSO de Azure AD para Coupa

Configure y pruebe el inicio de sesión único de Azure AD con Coupa con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Coupa.

Para configurar y probar el inicio de sesión único de Azure AD con Coupa, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Coupa](#configure-coupa-sso)** : para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Coupa](#create-coupa-test-user)** : para tener un homólogo de B.Simon en Coupa que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Coupa**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Coupa](https://success.coupa.com/Support/Contact_Us?) para obtener este valor.

    b. En el cuadro **Identificador**, escriba la dirección URL: 

    | Entorno  | URL |
    |:-------------|----|
    | Espacio aislado | `sso-stg1.coupahost.com`|
    | Producción | `sso-prd1.coupahost.com`|
    | | |

    c. En el cuadro de texto **URL de respuesta**, escriba la dirección URL: 

    | Entorno | URL |
    |------------- |----|
    | Espacio aislado | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Producción | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Configurar Coupa**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a conceder a B.Simon acceso a Coupa mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Coupa**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-coupa-sso"></a>Configuración del inicio de sesión único de Coupa

1. Inicie sesión en su sitio de la compañía de Coupa como administrador.

2. Vaya a **Configuración \> Control de seguridad**.

    ![Security Controls (Controles de seguridad)](./media/coupa-tutorial/setup.png "Controles de seguridad")

3. En la sección **Log in using Coupa credentials**, realice lo siguiente:

    ![Coupa SP metadata (Metadatos de SP de Coupa)](./media/coupa-tutorial/login.png "Metadatos de SP Coupa")

    a. Seleccione **Log in using SAML**.

    b. Haga clic en **Browse** (Examinar) para cargar los metadatos de Azure Portal.

    c. Haga clic en **Save**(Guardar).

### <a name="create-coupa-test-user"></a>Creación de un usuario de prueba de Coupa

Para permitir que los usuarios de Azure AD inicien sesión en Coupa, tienen que aprovisionarse en Coupa.  

* En el caso de Coupa, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en el sitio de **Coupa** como administrador.

2. En el menú de la parte superior, haga clic en **Setup** y elija **Users**.

    ![Usuarios](./media/coupa-tutorial/user.png "Usuarios")

3. Haga clic en **Crear**.

    ![Creación de usuarios](./media/coupa-tutorial/create.png "Crear usuarios")

4. En la sección **User Create**, lleve a cabo los siguientes pasos:

    ![User Details (Detalles del usuario)](./media/coupa-tutorial/details.png "Detalles del usuario")

    a. Escriba los atributos de inicio de sesión en el cuadro **Login**, nombre en el cuadro **First name**, apellido en el cuadro **Last Name**, identificador de inicio de sesión único en el cuadro **Single Sign-On ID** y correo electrónico en el cuadro **Email** de una cuenta válida de Azure Active Directory que desee aprovisionar.

    b. Haga clic en **Crear**.

    >[!NOTE]
    >El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
    >

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Coupa que proporcione Coupa para aprovisionar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Coupa, desde donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de Coupa y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Coupa en Aplicaciones, debería iniciar sesión automáticamente en la instancia de Coupa para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Coupa, puede aplicar el control de sesión, que protege frente a la filtración e infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).