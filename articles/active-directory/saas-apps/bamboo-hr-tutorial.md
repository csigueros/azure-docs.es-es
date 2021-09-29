---
title: 'Tutorial: Integración de Azure Active Directory con BambooHR | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 34adca214e5673340b12c8dfcdbdfd8a5bac9e0e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124759612"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Integración de Azure Active Directory con BambooHR

En este tutorial, aprenderá a integrar BambooHR con Azure Active Directory (Azure AD). Al integrar BambooHR con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a BambooHR.
* Permitir que los usuarios inicien sesión automáticamente en BambooHR con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) en BambooHR.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* BambooHR admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.


## <a name="adding-bamboohr-from-the-gallery"></a>Incorporación de BambooHR desde la galería

Para configurar la integración de BambooHR en Azure AD, deberá agregar BambooHR desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **BambooHR** en el cuadro de búsqueda.
1. Seleccione **BambooHR** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Configuración y prueba del inicio de sesión único de Azure AD para BambooHR

Configure y pruebe el inicio de sesión único de Azure AD con BambooHR mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de BambooHR.

Para configurar y probar el inicio de sesión único de Azure AD con BambooHR, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en BambooHR](#configure-bamboohr-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba de BambooHR](#create-bamboohr-test-user)**: para tener un homólogo de Britta Simon en BambooHR que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **BambooHR**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company>.bamboohr.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón:

    | URL de respuesta |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de cliente de BambooHR](https://www.bamboohr.com/contact.php) para obtener los valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up BambooHR** (Configurar BambooHR), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a BambooHR mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **BambooHR**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-bamboohr-sso"></a>Configuración del inicio de sesión único en BambooHR

1. En otra ventana, inicie sesión como administrador en el sitio de la compañía de BambooHR.

2. En la página principal, haga lo siguiente:
   
    ![Página de inicio de sesión único de BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Inicio de sesión único")   

    a. Seleccione **Aplicaciones**.
   
    b. En el panel **Aplicaciones**, seleccione **Single Sign-On** (Inicio de sesión único).
   
    c. Seleccione **SAML Single Sign-On** (Inicio de sesión único de SAML).

3. En el panel **SAML Single Sign-On** (Inicio de sesión único de SAML), haga lo siguiente:
   
    ![Panel de inicio de sesión único de SAML](./media/bamboo-hr-tutorial/IC796692.png "Inicio de sesión único SAML")
   
    a. En el cuadro **SSO Login URL** (URL de inicio de sesión de SSO), pegue la **dirección URL de inicio de sesión** que copió de Azure Portal en el paso 6.
      
    b. En el Bloc de notas, abra el certificado codificado en Base 64 que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).
   
    c. Seleccione **Guardar**.

### <a name="create-bamboohr-test-user"></a>Creación de un usuario de prueba de BambooHR

Para permitir que los usuarios de Azure AD inicien sesión en BambooHR, configúrelos manualmente en BambooHR haciendo lo siguiente:

1. Inicie sesión en su sitio de **BambooHR** como administrador.

2. En la barra de herramientas en la parte superior, seleccione **Configuración**.
   
    ![Botón de configuración](./media/bamboo-hr-tutorial/IC796694.png "Configuración")

3. Seleccione **Información general**.

4. En el panel izquierdo, seleccione **Seguridad** > **Usuarios**.

5. Escriba el nombre de usuario, la contraseña y la dirección de correo electrónico de la cuenta válida de Azure AD que desee configurar.

6. Seleccione **Guardar**.
        
>[!NOTE]
>Para configurar cuentas de usuario de Azure AD, también puede utilizar las herramientas de creación de cuentas de usuario de BambooHR o las API.

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

1. Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de BambooHR, donde puede iniciar el flujo de inicio de sesión. 

2. Vaya directamente a la dirección URL de inicio de sesión de BambooHR e inicie el flujo de inicio de sesión desde allí.

3. Puede usar el Panel de acceso de Microsoft. Al hacer clic en el icono de BambooHR en el Panel de acceso, se le redirigirá a la dirección URL de inicio de sesión de BambooHR. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado BambooHR, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).