---
title: 'Tutorial: Integración de Azure Active Directory con UNIFI | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y UNIFI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: c6a91d392b3a48c925ada9c3b758d458698b2ca3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808156"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Tutorial: Integración de Azure Active Directory con UNIFI

En este tutorial, aprenderá a integrar UNIFI con Azure Active Directory (Azure AD). Al integrar UNIFI con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a UNIFI.
* Permitir que los usuarios inicien sesión automáticamente en UNIFI con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en UNIFI.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* UNIFI admite el inicio de sesión único iniciado por **SP e IDP**.
* UNIFI admite el aprovisionamiento **automático** de usuarios.

## <a name="add-unifi-from-the-gallery"></a>Adición de UNIFI desde la galería

Para configurar la integración de UNIFI en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **UNIFI** en el cuadro de búsqueda.
1. Seleccione **UNIFI** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Configuración y prueba del inicio de sesión único de Azure AD para UNIFI

Configure y pruebe el inicio de sesión único de Azure AD con UNIFI mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de UNIFI.

Para configurar y probar el inicio de sesión único de Azure AD con UNIFI, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en UNIFI](#configure-unifi-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en UNIFI](#create-unifi-test-user)** : para tener un homólogo de B.Simon en UNIFI vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **UNIFI**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en el modo iniciado por **IDP** siga estos pasos:

    En el cuadro de texto **Identificador**, escriba la dirección URL: `INVIEWlabs`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.discoverunifi.com/login`

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Set up UNIFI** (Configurar UNIFI), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a UNIFI mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **UNIFI**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-unifi-sso"></a>Configuración del inicio de sesión único de UNIFI

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **UNIFI** como administrador.

2. Haga clic en la pestaña **Users** (Usuarios).

    ![Captura de pantalla que muestra la opción Users (Usuarios) seleccionada en el sitio de UNIFI.](./media/unifi-tutorial/app-1.png)

3. Haga clic en **Add New Identity Provider** (Agregar nuevo proveedor de identidades).

    ![Captura de pantalla que muestra la opción Add New Identity Provider (Agregar nuevo proveedor de identidades) seleccionada.](./media/unifi-tutorial/app-2.png)

4. En la sección **Add Identity Provider** (Agregar proveedor de identidades), realice los pasos siguientes:

    ![Captura de pantalla que muestra la sección para agregar un proveedor de identidades, en la que puede especificar los valores descritos.](./media/unifi-tutorial/app-3.png) 

    a. En el cuadro de texto **Provider Name** (Nombre del proveedor, escriba el nombre del proveedor de identidades.

    b. En el cuadro de texto **Dirección URL del proveedor**, pegue el valor de la **URL de inicio de sesión** que ha copiado de Azure Portal.

    c. Abra el certificado que ha descargado de Azure Portal en el Bloc de notas, quite las etiquetas **---BEGIN CERTIFICATE---** y **---END CERTIFICATE---** y pegue el resto del contenido en el cuadro de texto **Certificate** (Certificado).

    d. Active la casilla **is Default Provider** (Es el proveedor predeterminado).

### <a name="create-unifi-test-user"></a>Creación de un usuario de prueba de UNIFI

En esta sección, creará un usuario llamado Britta Simon. **UNIFI** admite el aprovisionamiento automático de usuarios, por lo que se requiere ningún paso manual. Los usuarios se crean automáticamente después de autenticarse correctamente en Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de UNIFI, donde podrá comenzar el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de UNIFI y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de UNIFI para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de UNIFI en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de UNIFI para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado UNIFI, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).