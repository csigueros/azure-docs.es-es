---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Screencast-O-Matic | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Screencast-O-Matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: a08356ad94b47f2d1088f66de2b5af138a246e9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124741504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Screencast-O-Matic

En este tutorial aprenderá a integrar Screencast-O-Matic con Azure Active Directory (Azure AD). Al integrar Screencast-O-Matic con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Screencast-O-Matic.
* Permitir que los usuarios inicien sesión automáticamente en Screencast-O-Matic con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción con el inicio de sesión único habilitado de Screencast-O-Matic.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Screencast-O-Matic admite el inicio de sesión único iniciado por **SP**.
* Screencast-O-Matic admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-screencast-o-matic-from-the-gallery"></a>Adición de Screencast-O-Matic desde la galería

Para configurar la integración de Screencast-O-Matic en Azure AD, deberá agregar Screencast-O-Matic desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Screencast-O-Matic** en el cuadro de búsqueda.
1. Seleccione **Screencast-O-Matic** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-screencast-o-matic"></a>Configuración y prueba del inicio de sesión único de Azure AD para Screencast-O-Matic

Configure y pruebe el inicio de sesión único de Azure AD con Screencast-O-Matic mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Screencast-O-Matic.

Para configurar y probar el inicio de sesión único de Azure AD con Screencast-O-Matic, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Screencast-O-Matic](#configure-screencast-o-matic-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Screencast-O-Matic](#create-screencast-o-matic-test-user)** : para tener un homólogo de B.Simon en Screencast-O-Matic vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Screencast-O-Matic**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Screencast-O-Matic](mailto:support@screencast-o-matic.com) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Screencast-O-Matic** (Configurar Screencast-O-Matic), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Screencast-O-Matic mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Screencast-O-Matic**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-screencast-o-matic-sso"></a>Configuración del inicio de sesión único en Screencast-O-Matic

1. Para automatizar la configuración en Screencast-O-Matic, es preciso instalar **My Apps Secure Sign-in browser extension** (Extensión del explorador de inicio de sesión seguro de Aplicaciones). Para ello, haga clic en **Install the extension** (Instalar la extensión).

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

1. Después de agregar la extensión al explorador, haga clic en **Set up Screencast-O-Matic** (Configurar Screencast-O-Matic) para ir a la aplicación del mismo nombre. En ella, escriba las credenciales de administrador para iniciar sesión en Screencast-O-Matic. La extensión del navegador configurará automáticamente la aplicación y automatizará los pasos del 3 al 10.

    ![Configuración](common/setup-sso.png)

1. Si quiere configurar Screencast-O-Matic manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Screencast-O-Matic como administrador y lleve a cabo los siguientes pasos:

1. Haga clic en **Suscripción**.

    ![Captura de pantalla que muestra la Suscripción.](./media/screencast-tutorial/subscribe.png)

1. En la sección **Access page** (Página de acceso), haga clic en **Setup** (Configurar).

    ![Captura de pantalla que muestra la sección "Access Page" (Página de acceso) con el botón "Setup" (Configurar).](./media/screencast-tutorial/setup.png)

1. En **Setup Access Page** (Configurar página de acceso), realice los pasos siguientes.

1. En la sección **Access URL** (Dirección URL de acceso), escriba el nombre de instancia en el cuadro de texto indicado.

    ![Captura de pantalla que muestra la sección "Access U R L" (Dirección U R L de acceso) con el cuadro de texto de nombre de instancia resaltado.](./media/screencast-tutorial/access-page.png)

1. Seleccione **Require Domain User** (Requerir usuario de dominio) en la sección **SAML User Restriction (optional)** [Restricción de usuario SAML (opcional)].

1. En **Upload IDP Metadata XML File** (Cargar archivo XML de metadatos de IdP), haga clic en **Choose File** (Elegir archivo) para cargar los metadatos que descargó de Azure Portal.

1. Haga clic en **OK**.

    ![Captura de pantalla que muestra el acceso.](./media/screencast-tutorial/metadata.png)

### <a name="create-screencast-o-matic-test-user"></a>Creación de un usuario de prueba de Screencast-O-Matic

En esta sección, se crea un usuario llamado Britta Simon en Screencast-O-Matic. Screencast-O-Matic admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Screencast-O-Matic, se crea uno después de la autenticación. Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el [equipo de soporte técnico de Screencast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirige a la dirección URL de inicio de sesión de Screencast-O-Matic, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Screencast-O-Matic e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono Screencast-O-Matic de Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Screencast-O-Matic. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Screencast-O-Matic, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).