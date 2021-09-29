---
title: 'Tutorial: Integración de Azure Active Directory con Zscaler Private Access (ZPA) | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zscaler Private Access (ZPA).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/03/2021
ms.author: jeedes
ms.openlocfilehash: b1e4ddddea8cf6c25d6daa0b16c99f27fcf80c7b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751365"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integración de Zscaler Private Access (ZPA) con Azure Active Directory

En este tutorial, aprenderá a integrar Zscaler Private Access (ZPA) con Azure Active Directory (Azure AD). Al integrar Zscaler Private Access con Azure AD, puede hacer lo siguiente:

* Puede controlar en Azure AD quién tiene acceso a Zscaler Private Access (ZPA).
* Permitir que los usuarios inicien sesión automáticamente en Zscaler Private Access (ZPA) con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único de Zscaler Private Access (ZPA).

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. 

* Zscaler Private Access (ZPA) admite el inicio de sesión único iniciado por **SP**.
* Zscaler Private Access (ZPA) admite el [aprovisionamiento de usuarios **automatizado**](zscaler-private-access-provisioning-tutorial.md).
> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Incorporación de Zscaler Private Access (ZPA) desde la galería

Para configurar la integración de Zscaler Private Access (ZPA) con Azure AD, debe agregar Zscaler Private Access (ZPA) desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zscaler Private Access (ZPA)** en el cuadro de búsqueda.
1. Seleccione **Zscaler Private Access (ZPA)** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-private-access-zpa"></a>Configuración y prueba del inicio de sesión único de Azure AD para Zscaler Private Access (ZPA)

Configure y pruebe el inicio de sesión único de Azure AD con Zscaler Private Access (ZPA) con un usuario de prueba llamado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Zscaler Private Access (ZPA).

Para configurar y probar el inicio de sesión único de Azure AD con Zscaler Private Access (ZPA), siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Zscaler Private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** : para tener un homólogo de B.Simon en Zscaler Private Access (ZPA) vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Zscaler Private Access (ZPA)** , busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configuración básica de SAML**, siga estos pasos:

    1. En el cuadro de texto **Identificador (id. de entidad)** , escriba la dirección URL: `https://samlsp.private.zscaler.com/auth/metadata`.

    1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://samlsp.private.zscaler.com/auth/login?domain=<DOMAIN_NAME>`

    > [!NOTE]
    > El valor de la **dirección URL de inicio de sesión** no es real. Actualice el valor con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico para clientes de Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

   ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Zscaler Private Access (ZPA)** , copie las direcciones URL que necesite.

   ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado Britta Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `Britta Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `BrittaSimon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección va a permitir que B.Simon acceda a Zscaler Private Access (ZPA) mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Zscaler Private Access (ZPA)** .
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-zscaler-private-access-zpa-sso"></a>Configuración del inicio de sesión único de Zscaler Private Access (ZPA)

1. Para automatizar la configuración en Zscaler Private Access (ZPA), debe instalar la **extensión de explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión** .

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Zscaler Private Access (ZPA)** para ir a la aplicación Zscaler Private Access (ZPA). Desde allí, proporcione las credenciales del administrador para iniciar sesión en Zscaler Private Access (ZPA). La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Zscaler Private Access (ZPA) manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Zscaler Private Access (ZPA) como administrador y realice los pasos siguientes:

4. En la parte izquierda del menú, haga clic en **Administration** (Administración) y vaya a la sección **AUTHENTICATION** (Autenticación) y haga clic en **IdP Configuration** (Configuración de IdP).

    ![Administrador de Zscaler Private Access Administrator](./media/zscalerprivateaccess-tutorial/administration.png)

5. En la esquina superior derecha, haga clic en **Add IdP Configuration** (Agregar configuración de IdP). 

    ![IDP de Administrador de Zscaler Private Access](./media/zscalerprivateaccess-tutorial/metadata.png)

6. En la página **Add IdP Configuration** (Agregar configuración de IdP), realice estos pasos:
 
    ![Selección de Administrador de Zscaler Private Access](./media/zscalerprivateaccess-tutorial/select.png)

    a. Haga clic en **Select File** (Seleccionar archivo) para cargar el archivo de metadatos descargado de Azure AD en el campo **IdP Metadata File Upload** (Carga de archivo de metadatos de IdP).

    b. Lee los **metadatos de IdP** de Azure AD y rellena la información de todos los campos como se indica a continuación.

    ![Configuración de Administrador de Zscaler Private Access](./media/zscalerprivateaccess-tutorial/configure.png)

    c. Seleccione el dominio en el campo **Domains** (Dominios).
    
    d. Haga clic en **Save**(Guardar).

### <a name="create-zscaler-private-access-zpa-test-user"></a>Creación de un usuario de prueba en Zscaler Private Access (ZPA)

En esta sección, creará un usuario llamado Britta Simon en Zscaler Private Access (ZPA). Trabaje con el [equipo de soporte técnico de Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para agregar los usuarios a la plataforma de Zscaler Private Access (ZPA).

Zscaler Private Access (ZPA) también admite el aprovisionamiento automático de usuarios. [Aquí](zscaler-private-access-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Zscaler Private Access (ZPA), donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Zscaler Private Access (ZPA) e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Zscaler Private Access (ZPA) en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de dicha aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Zscaler Private Access (ZPA), puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).