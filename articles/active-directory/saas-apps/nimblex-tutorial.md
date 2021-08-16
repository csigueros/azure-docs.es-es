---
title: 'Tutorial: Integración de Azure Active Directory con Nimblex | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Nimblex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: b3e6105c45bb3f3bda5e7d8af872050ed0172cf9
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111557900"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Tutorial: Integración de Azure Active Directory con Nimblex

En este tutorial va a aprender a integrar Nimblex con Azure Active Directory (Azure AD). Al integrar Nimblex con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Nimblex.
* Permitir que los usuarios inicien sesión automáticamente en Nimblex con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) de Nimblex.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Nimblex admite el SSO iniciado por **SP**.

* Nimblex admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-nimblex-from-the-gallery"></a>Incorporación de Nimblex desde la galería

Para configurar la integración de Nimblex en Azure AD, deberá agregarla desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Nimblex** en el cuadro de búsqueda.
1. Seleccione **Nimblex** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-nimblex"></a>Configuración y prueba del inicio de sesión único de Azure AD para Nimblex

Configure y pruebe el SSO de Azure AD con Nimblex mediante un usuario de prueba de nombre **B.Simon**. Para que el SSO funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Nimblex.

Para configurar y probar el SSO de Azure AD con Nimblex, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Nimblex](#configure-nimblex-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Nimblex](#create-nimblex-test-user)** , para tener un homólogo de B.Simon en Nimblex que esté vinculado a la representación del usuario de Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Nimblex**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<YOUR_APPLICATION_PATH>/Login.aspx`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<YOUR_APPLICATION_PATH>/`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte al cliente de Nimblex](mailto:support@ebms.com.au) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Nimblex** (Configurar Nimblex), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a NAVEX One mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **NAVEX One**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-nimblex-sso"></a>Configuración del inicio de sesión único de Nimblex

1. En otra ventana del explorador web, inicie sesión en Nimblex como administrador de seguridad.

2. En la parte superior derecha de la página, haga clic en el logotipo de **Configuración**.

    ![Captura de pantalla que muestra el icono de configuración.](./media/nimblex-tutorial/settings.png)

3. En la página **Panel de Control**, en la sección **Seguridad**, haga clic en **Inicio de sesión único**.

    ![Captura de pantalla que muestra Single Sign-On (Inicio de sesión único) seleccionado en el menú Security (Seguridad).](./media/nimblex-tutorial/security.png)

4. En la página **Administrar el inicio de sesión único**, seleccione el nombre de instancia y haga clic en **Editar**.

    ![Captura de pantalla que muestra la opción Manage Single Sign-On (Administrar inicio de sesión único) donde puede seleccionar Edit (Editar).](./media/nimblex-tutorial/edit-tab.png)

5. En la página **Edit SSO Provider** (Editar proveedor de SSO), realice los pasos siguientes:

    ![Captura de pantalla que muestra Edit S S O Provider (Editar proveedor de S S O) donde puede especificar los valores descritos.](./media/nimblex-tutorial/certificate.png)

    a. En el cuadro de texto **Description** (Descripción), escriba el nombre de instancia.

    b. En el Bloc de notas, abra el certificado codificado en Base 64 que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Certificate** (Certificado).

    c. En el cuadro de texto **Identity Provider Single Sign-On URL** (Dirección URL de inicio de sesión único del proveedor de identidades), pegue el valor de **Dirección URL de inicio de sesión**  que ha copiado de Azure Portal.

    d. Haga clic en **Save**(Guardar).

### <a name="create-nimblex-test-user"></a>Creación de un usuario de prueba de Nimblex

En esta sección se creará un usuario llamado Britta Simon en Nimblex. Nimblex admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Nimblex, se crea uno después de la autenticación.

>[!Note]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el [equipo de soporte técnico de Nimblex](mailto:support@ebms.com.au).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirige a la dirección URL de inicio de sesión de Nimblex, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Nimblex e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Nimblex en Aplicaciones, se le redirige a la dirección URL de inicio de sesión de Nimblex. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Nimblex, puede aplicar el control de sesión, que protege a la organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
