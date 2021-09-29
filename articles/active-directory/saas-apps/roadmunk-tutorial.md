---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Roadmunk'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: f0c139ac775cf0761e1866853c959d7e98b20cf3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Roadmunk

En este tutorial aprenderá a integrar Roadmunk con Azure Active Directory (Azure AD). Al integrar Roadmunk con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Roadmunk.
* Permitir que los usuarios inicien sesión automáticamente en Roadmunk con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción de Roadmunk con inicio de sesión único habilitado

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

Roadmunk admite el inicio de sesión único iniciado por el *proveedor de servicios* (SP) y por el *proveedor de identidades* (IDP).

## <a name="add-roadmunk-from-the-gallery"></a>Adición de Roadmunk desde la galería

Para integrar Roadmunk en Azure AD, en la galería, agregue Roadmunk a la lista de aplicaciones SaaS administradas:

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Roadmunk** en el cuadro de búsqueda.
1. Seleccione **Roadmunk** en los resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Configuración y prueba del inicio de sesión único de Azure AD para Roadmunk

Configure y pruebe el inicio de sesión único de Azure AD con Roadmunk mediante un usuario de prueba llamado *B.Simon*. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Roadmunk.

A continuación se proporciona información general sobre cómo configurar y probar el inicio de sesión único de Azure AD con Roadmunk:

1. [Configuración del inicio de sesión único en Azure AD](#configure-azure-ad-sso), para que los usuarios puedan usar esta característica.
    1. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único (SSO) de Azure AD con el usuario B.Simon.
    1. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. [Configuración del inicio de sesión único en Roadmunk](#configure-roadmunk-sso), para configurar los valores de inicio de sesión único en la aplicación.
    1. [Creación de un usuario de prueba de Roadmunk](#create-roadmunk-test-user), para poder vincular el homólogo de B.Simon en Roadmunk a la representación del usuario en Azure AD.
1. [Prueba del inicio de sesión único](#test-sso), para comprobar que la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal:

1. En Azure Portal, en la página de integración de la aplicación **Roadmunk**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Captura de pantalla que muestra el icono de edición para Configuración básica de SAML.](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si tiene un archivo de metadatos de SP y desea configurar la aplicación en modo iniciado por IDP, siga estos pasos:

    a. Seleccione **Cargar el archivo de metadatos**.

    ![Captura de pantalla que muestra el vínculo para cargar el archivo de metadatos.](common/upload-metadata.png)

    b. Seleccione el icono de carpeta para elegir el archivo de metadatos que descargó en el paso 4 del procedimiento "Configuración del inicio de sesión único en Roadmunk". Después, seleccione **Cargar**.

    ![Captura de pantalla que muestra cómo seleccionar el archivo de metadatos.](common/browse-upload-metadata.png)

    Una vez que se haya cargado el archivo de metadatos, en la sección **Configuración básica de SAML**, los valores de **Identificador** y **Dirección URL de respuesta** se rellenan automáticamente.

    ![Captura de pantalla que muestra la sección Configuración básica de SAML. Los campos Identificador y Dirección URL de respuesta están resaltados.](common/idp-intiated.png)

    > [!Note]
    > Si los valores **Identificador** y **Dirección URL de respuesta** no se rellenan automáticamente, rellénelos manualmente.

1. Seleccione **Establecer direcciones URL adicionales** si desea configurar la aplicación en el modo iniciado por SP. En el campo **URL de inicio de sesión**, escriba `https://login.roadmunk.com`.

    ![Captura de pantalla que muestra dónde establecer una dirección URL de inicio de sesión para el modo iniciado por SP.](common/metadata-upload-additional-signon.png)

1. En la página **Configuración del inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación**. A continuación, seleccione **Descargar** para descargar el certificado y guárdelo en el equipo.

    ![Captura de pantalla que muestra el vínculo de descarga del certificado de firma de SAML.](common/metadataxml.png)

1. En la sección **Configurar Roadmunk**, copie la dirección o direcciones URL que necesita.

    ![Captura de pantalla que muestra dónde copiar las direcciones URL de configuración.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará un usuario de prueba en Azure Portal. Asignará al usuario el nombre *B.Simon*.

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la ventana.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, escriba `B.Simon@contoso.com`:
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a Roadmunk mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Roadmunk**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, en la lista **Usuarios**, seleccione **B.Simon**. En la parte inferior del cuadro de diálogo, elija **Seleccionar**.
1. Si espera que se asigne un rol a los usuarios, selecciónelo en el menú desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, se seleccionará el rol **Acceso predeterminado**.
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

## <a name="configure-roadmunk-sso"></a>Configuración del inicio de sesión único en Roadmunk

1. Inicie sesión en el sitio web de Roadmunk como administrador.

1. En la parte inferior de la página, seleccione el icono de usuario y, a continuación, seleccione **Configuración de la cuenta**.

    ![Captura de pantalla que muestra dónde seleccionar la configuración de la cuenta de usuario.](./media/roadmunk-tutorial/account.png)

1. Vaya a **Empresa** > **Configuración de autenticación**.

1. En la página **Configuración de autenticación**, realice los siguientes pasos:

    ![Captura de pantalla de la página Configuración de autenticación.](./media/roadmunk-tutorial/saml-sso.png)

    a. Active **SAML Single Sign On (SSO)** (Inicio de sesión único [SSO] de SAML).

    b. En la sección **Paso 1**, cargue el archivo XML de metadatos o proporcione la dirección URL de los metadatos.

    c. En la sección **Paso 2**, descargue el archivo de metadatos de Roadmunk y, a continuación, guárdelo en el equipo.

    d. Si desea iniciar sesión mediante el inicio de sesión único, en la sección **Paso 3**, seleccione **Enforce SAML Sign-In Only** (Aplicar solo inicio de sesión único de SAML).

    e. Seleccione **Guardar**.


### <a name="create-roadmunk-test-user"></a>Creación de un usuario de prueba de Roadmunk

1. Inicie sesión en el sitio web de Roadmunk como administrador.

1. Seleccione el icono de usuario en la parte inferior de la página y, a continuación, seleccione **Configuración de la cuenta**.

    ![Captura de pantalla que muestra cómo abrir la configuración de la cuenta del usuario de prueba.](./media/roadmunk-tutorial/account.png)

1. Abra la pestaña **Usuarios** y, después, seleccione **Invitar usuario**.

    ![Captura de pantalla que muestra la pestaña Usuarios. El botón Invitar usuario está resaltado. En la ventana abierta, están resaltados los campos Correo electrónico y Rol.](./media/roadmunk-tutorial/create-user.png)

1. En el formulario que aparece, rellene la información necesaria y seleccione **Invitar**.


## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

En el portal Aplicaciones, al seleccionar el icono de **Roadmunk**, debería iniciar sesión automáticamente en la cuenta de Roadmunk para la que configuró el inicio de sesión único. Para más información, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Después de configurar Roadmunk, puede aplicar el control de sesión. Este control de sesión protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. 

Aprenda a [aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).