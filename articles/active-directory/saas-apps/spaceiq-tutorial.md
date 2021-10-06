---
title: 'Tutorial: Integración de Azure Active Directory con SpaceIQ | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SpaceIQ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 22206e91842a71e4dd41102a4ba9a86e11649201
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785655"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Tutorial: Integración de Azure Active Directory con SpaceIQ

En este tutorial aprenderá a integrar SpaceIQ con Azure Active Directory (Azure AD). Al integrar SpaceIQ con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SpaceIQ.
* Permitir que los usuarios inicien sesión automáticamente en SpaceIQ con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SpaceIQ, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción de SpaceIQ con el inicio de sesión único habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SpaceIQ admite el inicio de sesión único iniciado por **IDP**.
* SpaceIQ admite el [aprovisionamiento automatizado de usuarios](spaceiq-provisioning-tutorial.md).

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-spaceiq-from-the-gallery"></a>Incorporación de SpaceIQ desde la galería

Para configurar la integración de SpaceIQ en Azure AD, tendrá que agregar SpaceIQ desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SpaceIQ** en el cuadro de búsqueda.
1. Seleccione **SpaceIQ** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-spaceiq"></a>Configuración y prueba del inicio de sesión único de Azure AD para SpaceIQ

Configure y pruebe el inicio de sesión único de Azure AD con SpaceIQ mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SpaceIQ.

Para configurar y probar el inicio de sesión único de Azure AD con SpaceIQ, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en SpaceIQ](#configure-spaceiq-sso)** : para definir los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SpaceIQ](#create-spaceiq-test-user)** : para tener un homólogo de B.Simon en SpaceIQ que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SpaceIQ**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la página **Configurar inicio de sesión único con SAML** realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://api.spaceiq.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://api.spaceiq.com/saml/<INSTANCE_ID>/callback`

    > [!NOTE]
    > Actualice estos valores con la dirección URL de respuesta y el identificador reales, que se explican más adelante en el tutorial.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up SpaceIQ** (Configurar SpaceIQ), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a SpaceIQ mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SpaceIQ**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-spaceiq-sso"></a>Configuración del inicio de sesión único de SpaceIQ

1. Abra una nueva ventana del explorador e inicie sesión en el entorno de SpaceIQ como administrador.

1. Una vez que haya iniciado sesión, haga clic en la pieza de puzzle en la esquina superior derecha, y luego haga clic en **Integraciones**.

    ![Configuración de la cuenta](./media/spaceiq-tutorial/setting.png) 

1. En **All PROVISIONING & SSO** (Todos los aprovisionamientos y SSO), haga clic en el icono de **Azure** para agregar una instancia de Azure como IDP.

    ![Icono de SAML](./media/spaceiq-tutorial/azure.png)

1. En el cuadro de diálogo **SSO**, realice los pasos siguientes:

    ![Configuración de la autenticación SAML](./media/spaceiq-tutorial/configuration.png)

    a. En el cuadro **SAML Issuer URL** (Dirección URL del emisor de SAML), pegue el valor del **Identificador de Azure AD** que copió de la ventana de configuración de la aplicación Azure AD.

    b. Copie el valor de **dirección URL de punto de conexión de devolución de llamada SAML (solo lectura)** y péguelo en el cuadro **URL de respuesta** en la sección **Configuración básica de SAML** de Azure Portal.

    c. Copie el valor de **URI de audiencia de SAML (solo lectura)** y péguelo en el cuadro **Identificador** de la sección **Configuración básica de SAML** de Azure Portal.

    d. Abra el certificado descargado en el Bloc de notas, copie el contenido y péguelo en el cuadro **Certificado X.509**.

    e. Haga clic en **Save**(Guardar).

### <a name="create-spaceiq-test-user"></a>Creación de un usuario de prueba de SpaceIQ

En esta sección, creará un usuario llamado Britta Simon en SpaceIQ. Trabaje con el [equipo de soporte técnico de SpaceIQ](mailto:eng@spaceiq.com) para agregar los usuarios en la plataforma de SpaceIQ. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

SpaceIQ también admite el aprovisionamiento automático de usuarios. [Aquí](./spaceiq-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de SpaceIQ para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de SpaceIQ en Mis aplicaciones, debería iniciar sesión automáticamente en la versión de SpaceIQ para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado SpaceIQ, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).