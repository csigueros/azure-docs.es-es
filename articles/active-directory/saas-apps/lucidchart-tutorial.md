---
title: 'Tutorial: Integración de Azure Active Directory con Lucidchart | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 6c180ab24ea5d9c3a997c9e7253c57f1f45d994c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Lucidchart

En este tutorial, aprenderá a integrar Lucidchart con Azure Active Directory (Azure AD). Al integrar Lucidchart con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Lucidchart.
* Permitir que los usuarios inicien sesión automáticamente en Lucidchart con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión (SSO) único en Lucidchart.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Lucidchart admite SSO iniciado por **SP**
* Lucidchart admite el [aprovisionamiento y desaprovisionamiento **automático** de usuarios](lucidchart-provisioning-tutorial.md) (recomendado).
* Lucidchart admite aprovisionamiento de usuarios **Just-In-Time**

## <a name="add-lucidchart-from-the-gallery"></a>Incorporación de Lucidchart desde la galería

Para configurar la integración de Lucidchart en Azure AD, es preciso agregar Lucidchart desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Lucidchart** en el cuadro de búsqueda.
1. Seleccione **Lucidchart** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Configuración y prueba del inicio de sesión único de Azure AD para Lucidchart

Configure y pruebe el inicio de sesión único de Azure AD con Lucidchart mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Lucidchart.

Para configurar y probar el inicio de sesión único de Azure AD con Lucidchart, sigan estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Lucidchart](#configure-lucidchart-sso)**, para configurar los valores de Inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba de Lucidchart](#create-lucidchart-test-user)**, para tener un homólogo de B.Simon en Lucidchart vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Lucidchart**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

   En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL como: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up Lucidchart** (Configurar Lucidchart), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Lucidchart mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Lucidchart**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-lucidchart-sso"></a>Configuración del inicio de sesión único de Lucidchart

1. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Lucidchart.

2. En el menú de la parte superior, haga clic en **Team**.

    ![Equipo](./media/lucidchart-tutorial/ic791190.png "Team")

3. Haga clic en **Applications \> (Aplicaciones) Manage SAML** (Administrar SAML).

    ![Manage SAML (Administrar SAML)](./media/lucidchart-tutorial/ic791191.png "Administrar SAML")

4. En la página de diálogo **SAML Authentication Settings**, lleve a cabo los pasos siguientes:

    a. Seleccione **Enable SAML Authentication** y haga clic en **Optional**.

    ![Configuración de la autenticación SAML](./media/lucidchart-tutorial/ic791192.png "Configuración de la autenticación SAML")

    b. En el cuadro de texto **Nombre de dominio**, escriba el nombre del dominio y haga clic en **Change Certificate**.

    ![Cambiar certificado](./media/lucidchart-tutorial/ic791193.png "Change Certificate (Cambiar certificado)")

    c. Abra el archivo de metadatos descargados, copie el contenido y péguelo en el cuadro de texto **Upload Metadata**.

    ![Upload Metadata (Cargar metadatos)](./media/lucidchart-tutorial/ic791194.png "Upload Metadata (Cargar metadatos)")

    d. Seleccione **Automatically Add new users to the team** (Agregar nuevo usuario al equipo automáticamente) y, después, haga clic en **Save changes** (Guardar los cambios).

    ![Save Changes](./media/lucidchart-tutorial/ic791195.png "Guardar cambios")

### <a name="create-lucidchart-test-user"></a>Creación de un usuario de prueba en Lucidchart

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Lucidchart.  Cuando un usuario asignado intenta iniciar sesión en Lucidchart desde el Panel de acceso, Lucidchart comprueba si el usuario existe.  

Si no hay cuentas de usuario disponibles, Lucidchart crea una automáticamente.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Lucidchart, desde donde poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de Lucidchart y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Lucidchart en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de Lucidchart para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

 Una vez configurado Lucidchart, puede aplicar controles de sesión, que protegen la filtración y la infiltración de la información confidencial de la organización en tiempo real. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).