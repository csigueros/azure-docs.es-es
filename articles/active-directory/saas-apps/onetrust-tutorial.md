---
title: 'Tutorial: Integración de Azure Active Directory con OneTrust Privacy Management Software | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el software OneTrust Privacy Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: a23f0b1389323ffbbe27b27cf9011c0a16b7f7d2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124738428"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Tutorial: Integración de Azure Active Directory con OneTrust Privacy Management Software

En este tutorial, aprenderá a integrar OneTrust Privacy Management Software con Azure Active Directory (Azure AD). Al integrar OneTrust Privacy Management Software con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a OneTrust Privacy Management Software.
* Permitir que los usuarios inicien sesión automáticamente en OneTrust Privacy Management Software con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con OneTrust Privacy Management Software, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener una versión de evaluación de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).
* Una suscripción habilitada para el inicio de sesión único en OneTrust Privacy Management Software.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* OneTrust Privacy Management Software admite el inicio de sesión único iniciado por **SP** e **IDP**.

* OneTrust Privacy Management Software admite aprovisionamiento de usuarios **Just-In Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Incorporación de OneTrust Privacy Management Software desde la galería

Para configurar la integración de OneTrust Privacy Management Software en Azure AD, deberá agregar OneTrust Privacy Management Software desde la galería hasta la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **OneTrust Privacy Management Software** en el cuadro de búsqueda.
1. Seleccione **OneTrust Privacy Management Software** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Configuración y prueba del inicio de sesión único de Azure AD SSO para OneTrust Privacy Management Software

Configure y pruebe el inicio de sesión único de Azure AD con OneTrust Privacy Management mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de OneTrust Privacy Management Software.

Para configurar el inicio de sesión único de Azure AD con OneTrust Privacy Management Software, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de OneTrust Privacy Management Software](#configure-onetrust-privacy-management-software-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de OneTrust Privacy Management Software](#create-onetrust-privacy-management-software-test-user)** , para tener un usuario equivalente a B.Simon en OneTrust Privacy Management Software que esté vinculado a su representación de usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección va a habilitar el inicio de sesión único de Azure AD en Azure Portal.
 
1. En Azure Portal, en la página de integración de la aplicación **OneTrust Privacy Management Software**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en el modo iniciado por **IDP** siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://www.onetrust.com/saml2`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

     En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de atención al cliente de Trust Privacy Management Software](mailto:support@onetrust.com). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

7. En la sección **Set up OneTrust Privacy Management Software** (Configurar OneTrust Privacy Management Software), copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD 

En esta sección va a crear un usuario de prueba llamado B.Simon en Azure Portal.
1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Seleccione la casilla **Mostrar contraseña** y, a continuación, anote la contraseña.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a OneTrust Privacy Management Software mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **OneTrust Privacy Management Software**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista de usuarios. A continuación, elija **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Configuración del inicio de sesión único de OneTrust Privacy Management Software

Para configurar el inicio de sesión único en **OneTrust Privacy Management Software**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de OneTrust Privacy Management Software](mailto:support@onetrust.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Creación de un usuario de prueba de OneTrust Privacy Management Software

En esta sección, se crea un usuario llamado Britta Simon en OneTrust Privacy Management Software. OneTrust Privacy Management Software admite aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario ya no existe en OneTrust Privacy Management Software, se crea uno después de la autenticación.

>[!Note]
>Si tiene que crear manualmente un usuario, póngase en contacto con el [equipo de atención al cliente de OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de OneTrust Privacy Management Software, desde donde puede poner en marcha el flujo de inicio de sesión.  
 
* Vaya directamente a la URL de inicio de sesión de OneTrust Privacy Management Software y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de OneTrust Privacy Management para la configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de OneTrust Privacy Management Software en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de OneTrust Privacy Management Software para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado OneTrust Privacy Management, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).