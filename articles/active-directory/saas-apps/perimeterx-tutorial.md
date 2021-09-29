---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con PerimeterX | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y PerimeterX.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/27/2020
ms.author: jeedes
ms.openlocfilehash: f8160da443398787f2b0ebb3cf437383a5bdc522
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124783090"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeterx"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con PerimeterX

En este tutorial, aprenderá a integrar PerimeterX con Azure Active Directory (Azure AD). Al integrar PerimeterX con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a PerimeterX.
* Permitir que los usuarios puedan iniciar sesión automáticamente en PerimeterX con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en PerimeterX.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.


* PerimeterX admite el SSO iniciado por **IDP**.

## <a name="adding-perimeterx-from-the-gallery"></a>Adición de PerimeterX desde la galería

Para configurar la integración de PerimeterX en Azure AD, es preciso que agregue PerimeterX desde la galería a su lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **PerimeterX** en el cuadro de búsqueda.
1. Seleccione **PerimeterX** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-perimeterx"></a>Configuración y prueba del inicio de sesión único de Azure AD para PerimeterX

Configure y pruebe el inicio de sesión único (SSO) de Azure AD con PerimeterX mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de PerimeterX.

Para configurar y probar el inicio de sesión único de Azure AD con PerimeterX, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en PerimeterX](#configure-perimeterx-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de PerimeterX](#create-perimeterx-test-user)** : el objetivo es tener un homólogo de B. Simon en PerimeterX que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **PerimeterX**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.


1. La aplicación PerimeterX espera las aserciones de SAML en un formato específico, por lo que es necesario agregar asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación PerimeterX espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre | Atributo de origen|
    | ------------ | --------- |
    | firstName | user.givenname |
    | lastName  | user.surname |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar PerimeterX**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a PerimeterX mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **PerimeterX**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-perimeterx-sso"></a>Configuración del inicio de sesión único en PerimeterX

1. Inicie sesión en la consola de PerimeterX con permisos de administrador.

1. Vaya a **Admin > ACCOUNTS** (Administrador > CUENTAS). 

    ![Inicio de sesión único de PerimeterX](./media/perimeterx-tutorial/accounts.png)

1. Haga clic en **Editar**

4.  En el cuadro de diálogo Edit Account (Editar cuenta), realice los siguientes pasos:

    ![Edición de cuenta de inicio de sesión único de PerimeterX](./media/perimeterx-tutorial/saml-sso.png)

    a.  Marque la casilla **Enable Single Sign On (SSO)** (Habilitar inicio de sesión único [SSO]).

    b.  Seleccione **Azure SAML** (SAML de Azure).

    c.  En el cuadro de texto **SAML Endpoint** (Punto de conexión de SAML), pegue el valor de **Login URL** (Dirección URL de inicio de sesión) copiado de Azure Portal.

    d. En el cuadro **Issuer** (Emisor), pegue el valor de identificador de Azure AD copiado de Azure Portal.

    e. Abra en el Bloc de notas el archivo del **certificado (Base64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Certificate X.509** (Certificado).

    f. Haga clic en **Save Changes** (Guardar cambios).

### <a name="create-perimeterx-test-user"></a>Creación de un usuario de prueba de PerimeterX

Consulte la [guía de usuario de administración de PerimeterX](https://docs.perimeterx.com/pxconsole/docs/managing-users) para obtener instrucciones sobre cómo crear el usuario de prueba de PerimeterX.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

1. Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de PerimeterX para la que ha configurado el inicio de sesión único.

1. Puede usar el Panel de acceso de Microsoft. Al hacer clic en el icono de PerimeterX en el panel de acceso, debería iniciar sesión automáticamente en la versión de PerimeterX para la que ha configurado el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado PerimeterX, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).