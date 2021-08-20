---
title: 'Tutorial: integración de Azure Active Directory con Expensify | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 9bba6c33dafb04be0cec0eb74be3b7211bc1b0ed
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466481"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: Integración de Expensify con Azure Active Directory

En este tutorial aprenderá a integrar Expensify con Azure Active Directory (Azure AD). Al integrar Expensify con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Expensify.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Expensify con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Expensify.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Expensify permite utilizar el inicio de sesión único con **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-expensify-from-the-gallery"></a>Incorporación de Expensify desde la galería

Para configurar la integración de Expensify en Azure AD, deberá agregar Expensify desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Expensify** en el cuadro de búsqueda.
1. Seleccione **Expensify** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Expensify

Configure y pruebe el inicio de sesión único de Azure AD con Expensify mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Expensify.

Para configurar el inicio de sesión único de Azure AD con Expensify, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en Expensify](#configure-expensify-sso)**, para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Expensify](#create-expensify-test-user)**: para tener un homólogo de B.Simon en Expensify vinculado a la representación del usuario en Azure AD.
6. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Expensify**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba la dirección URL: `https://www.expensify.com`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`
    
    c. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://www.expensify.com/authentication/saml/login`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Actualice este valor con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de cliente de Expensify](mailto:help@expensify.com) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos** y seleccione **Descargar** para descargar el certificado y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Expensify** (Configurar Expensify) copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Expensify mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Expensify**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-expensify-sso"></a>Configuración del inicio de sesión único en Expensify

Para habilitar SSO en Expensify, primero deberá habilitar el **control de dominio** en la aplicación. Se puede habilitar el control de dominio de la aplicación mediante los pasos enumerados [aquí](https://help.expensify.com/domain-control). Para más información, trabaje con el [equipo de soporte técnico de cliente de Expensify](mailto:help@expensify.com). Una vez habilitado el control de dominio, siga estos pasos:

![Configurar inicio de sesión único](./media/expensify-tutorial/domain-control.png)

1. Inicie sesión en la aplicación Expensify.

2. En el panel izquierdo, mantenga el puntero sobre Settings (Configuración), haga clic en Domains (Dominios) y vaya a **SAML**.

3. Cambie la opción **Inicio de sesión SAML** a **Habilitado**.

4. Abra el documento de metadatos de federación descargado desde Azure AD en el Bloc de notas, copie el contenido y péguelo en el cuadro de texto **Metadatos del proveedor de identidades** que se proporciona.

### <a name="create-expensify-test-user"></a>Creación de un usuario de prueba de Expensify

En esta sección creará el mismo usuario llamado B. Simon (por ejemplo, B.Simon@contoso.com) en Expensify. Consulte [aquí](https://community.expensify.com/discussion/4869/how-to-manage-domain-members) la guía para invitar a miembros, o bien póngase en contacto con el [equipo de soporte técnico a los clientes de Expensify](mailto:help@expensify.com) para agregar los usuarios a la plataforma de Expensify.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. De este modo, accederá automáticamente a la dirección URL de inicio de sesión de Expensify, donde podrá comenzar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Expensify y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Si hace clic en el icono de Expensify de Aplicaciones, accederá automáticamente a la dirección URL de inicio de sesión de Expensify. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Expensify, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).