---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con BenSelect'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y BenSelect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: a1d977d4ba84b327d922003f89e9812726033287
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093675"
---
# <a name="tutorial-azure-ad-sso-integration-with-benselect"></a>Tutorial: Integración del inicio de sesión único de Azure AD con BenSelect

En este tutorial, aprenderá a integrar BenSelect con Azure Active Directory (Azure AD). Al integrar BenSelect con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a BenSelect
* Permitir que los usuarios puedan iniciar sesión automáticamente en BenSelect con sus cuentas de Azure AD
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en BenSelect.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* BenSelect admite el inicio de sesión único iniciado por **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-benselect-from-the-gallery"></a>Adición de BenSelect desde la galería

Para configurar la integración de BenSelect en Azure AD, es preciso agregar BenSelect desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **BenSelect** en el cuadro de búsqueda.
1. Seleccione **BenSelect** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-benselect"></a>Configuración y prueba del SSO de Azure AD para BenSelect

Configure y pruebe el inicio de sesión único de Azure AD con BenSelect mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de BenSelect.

Para configurar y probar el inicio de sesión único de Azure AD con BenSelect, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de BenSelect](#configure-benselect-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de BenSelect](#create-benselect-test-user)** , para tener un homólogo de B.Simon en BenSelect vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **BenSelect**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de cliente de BenSelect](mailto:support@selerix.com) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación BenSelect espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![Captura de pantalla que muestra User Attributes (Atributos de usuario) con atributos predeterminados, como emailaddress user.mail y givenname user.givenname.](common/edit-attribute.png)

1. Haga clic en el icono **Editar** para editar el **Valor de identificador de nombre**.

    ![Captura de pantalla que muestra el panel User Attributes & Claims (Atributos y reclamaciones del usuario) con el icono de edición seleccionado.](media/benselect-tutorial/mail-prefix1.png)

1. En la sección **Administrar las notificaciones del usuario**, realice los siguientes pasos:

    ![Captura de pantalla que muestra Manage user claims (Administrar reclamaciones de usuario) para escribir los valores que se describen en este paso.](media/benselect-tutorial/mail-prefix2.png)

    a. Seleccione **Transformación** como **Origen**.

    b. En la lista desplegable **Transformación**, seleccione **ExtractMailPrefix()** .

    c. En la lista desplegable **Parámetro 1**, seleccione **user.userprincipalname**.

    d. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (sin procesar)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificateraw.png)

1. En la sección **Configurar BenSelect**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a BenSelect mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **BenSelect**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-benselect-sso"></a>Configuración del inicio de sesión único de BenSelect

Para configurar el inicio de sesión único en **BenSelect**, es preciso enviar el **certificado (sin procesar)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de BenSelect](mailto:support@selerix.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!NOTE]
> Debe mencionar que esta integración requiere el algoritmo SHA256 (no se admite SHA1) para establecer el SSO en el servidor apropiado, como app2101.

### <a name="create-benselect-test-user"></a>Creación de un usuario de prueba de BenSelect

En esta sección, creará un usuario llamado Britta Simon en BenSelect. Trabaje con el [equipo de soporte técnico de BenSelect](mailto:support@selerix.com) para agregar los usuarios a la plataforma de BenSelect. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de BenSelect para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de BenSelect que se encuentra en Aplicaciones, debería iniciar sesión automáticamente en la versión de BenSelect para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que ha configurado BenSelect, puede aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones e infiltraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).