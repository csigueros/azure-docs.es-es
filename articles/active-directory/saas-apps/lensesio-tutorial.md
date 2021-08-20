---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Lenses.io | Microsoft Docs'
description: En este tutorial, aprenderá a configurar el inicio de sesión único entre Azure Active Directory y Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: f65a690378723fe8ee214333d3bfee0b18ce0cd0
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112553664"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con el portal DataOps de Lenses.io

En este tutorial, obtendrá información sobre cómo integrar el portal DataOps de [Lenses.io](https://lenses.io/) con Azure Active Directory (Azure AD). Después de integrar Lenses.io con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso al portal de Lenses.io.
* Permitir que los usuarios inicien sesión automáticamente en Lenses.io con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una instancia de un portal de Lenses. Puede elegir entre varias [opciones de implementación](https://lenses.io/product/deployment/).
* Una [licencia](https://lenses.io/product/pricing/) de Lenses.io que admita el inicio de sesión único (SSO).

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Lenses.io admite el inicio de sesión único iniciado por un proveedor de servicios.

## <a name="add-lensesio-from-the-gallery"></a>Incorporación de Lenses.io desde la galería

Para configurar la integración de Lenses.io en Azure AD, agregue Lenses.io a la lista de aplicaciones SaaS administradas:

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Lenses.io** en el cuadro de búsqueda.
1. Seleccione **Lenses.io** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configuración y prueba del inicio de sesión único de Azure AD para Lenses.io

Configure y pruebe el inicio de sesión único de Azure AD con el portal de Lenses.io mediante un usuario de prueba llamado *B.Simon*. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Lenses.io.

Lleve a cabo los siguiente pasos:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso), para permitir que los usuarios puedan utilizar esta característica.
    1. [Creación de un usuario y un grupo de prueba de Azure AD](#create-an-azure-ad-test-user-and-group), para probar el inicio de sesión único de Azure AD con el usuario B.Simon.
    1. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. [Configuración del inicio de sesión único en Lenses.io](#configure-lensesio-sso), para configurar los valores de inicio de sesión único en la aplicación.
    1. [Creación de permisos de grupo de prueba de Lenses.io](#create-lensesio-test-group-permissions), para controlar qué usuario B.Simon puede acceder en Lenses.io (autorización).
1. [Comprobación del inicio de sesión único](#test-sso), para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal:

1. En Azure Portal, en la página de integración de aplicaciones de **Lenses.io**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Captura de pantalla que muestra el icono para editar la configuración básica de SAML.](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. **Identificador (identificador de entidad)** : Escriba una dirección URL con el siguiente formato: `https://<CUSTOMER_LENSES_BASE_URL>`. Un ejemplo es `https://lenses.my.company.com`.

    b. **Reply URL** (URL de respuesta): Escriba una dirección URL con el siguiente formato: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`. Un ejemplo es `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    c. **URL de inicio de sesión**: Escriba una dirección URL con el siguiente formato: `https://<CUSTOMER_LENSES_BASE_URL>`. Un ejemplo es `https://lenses.my.company.com`.

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, la dirección URL de respuesta y el identificador reales, según la dirección URL base de la instancia del portal de Lenses. Para más información, consulte la [documentación del inicio de sesión único de Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. En la página **Configuración del inicio de sesión único con SAML**, vaya a la sección **Certificado de firma de SAML**. Busque **XML de metadatos de federación**, seleccione **Descargar** para descargar y guardar el certificado en el equipo.

    ![Captura de pantalla del vínculo de descarga del certificado.](common/metadataxml.png)

1. En la sección **Configuración de Lenses.io**, use el archivo XML que descargó para configurar Lenses en el inicio de sesión único de Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Creación de un usuario de prueba y un grupo de Azure AD

En Azure Portal, se crea un usuario llamado B.Simon. A continuación, creará un grupo de prueba que controlará el acceso que B.Simon tiene en Lenses.

Para más información sobre cómo usa Lenses la asignación de pertenencia a grupos para la autorización, consulte la [documentación inicio de sesión único de Lenses](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Para crear el usuario de prueba:**

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. En la parte superior de la pantalla, seleccione **Nuevo usuario**.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el cuadro **Nombre**, escriba **B.Simon**.  
   1. En el cuadro **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo: B.Simon@contoso.com.
   1. Active la casilla de verificación **Mostrar contraseña**. Anote la contraseña que aparece en el cuadro de texto **Contraseña**.
   1. Seleccione **Crear**.

**Para crear el grupo:**

1. Vaya a **Azure Active Directory** y después seleccione **Grupos**.
1. En la parte superior de la pantalla, seleccione **Nuevo grupo**.
1. En **Propiedades de grupo**, siga estos pasos:
   1. En el cuadro **Tipo de grupo**, seleccione **Seguridad**.
   1. En el cuadro **Nombre del grupo**, escriba **LensesUsers**.
   1. Seleccione **Crear**.
1. Seleccione el grupo **LensesUsers** y copie el **identificador de objeto** (por ejemplo, f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Este identificador se usará en Lenses para asignar los usuarios de ese grupo a los [permisos correctos](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**Para asignar el grupo al usuario de prueba:**

1. Vaya a **Azure Active Directory** y después seleccione **Usuarios**.
1. Seleccione el usuario de prueba **B.Simon**.
1. Seleccione **Grupos**.
1. Seleccione **Agregar pertenencias** en la parte superior de la pantalla.
1. Busque **LensesUsers** y selecciónelo.
1. Haga clic en **Seleccionar**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon use el inicio de sesión único de Azure concediéndole acceso a Lenses.io.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Lenses.io**.
1. En la sección **Administrar** de la página de información general de la aplicación, seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**.
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista de usuarios. A continuación, haga clic en el botón **Seleccionar** situado en la parte inferior de la pantalla.
1. Si espera algún valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione el rol adecuado para el usuario en la lista. A continuación, haga clic en el botón **Seleccionar** situado en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-lensesio-sso"></a>Configuración del inicio de sesión único de Lenses.io

Para configurar el inicio de sesión único en el portal de **Lenses.io**, instale el archivo **XML de metadatos de federación** descargado en la instancia de Lenses.io y [configure Lenses.io para habilitar el inicio de sesión único](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Creación de permisos del grupo de prueba de Lenses.io

1. Para crear un grupo en Lenses, use el **identificador de objeto** del grupo **LensesUsers**. Este es el identificador que copió en la [sección de creación](#create-an-azure-ad-test-user-and-group).
1. Asigne los permisos deseados para B.Simon.

Para más información, consulte [Asignación de grupos de Lenses en Azure](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Lenses.io, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Lenses.io y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Lenses.io en Mis aplicaciones, se le redirigirá a la URL de inicio de sesión de esa aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Lenses.io, puede aplicar el control de sesión, que protege tanto la filtración como la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
