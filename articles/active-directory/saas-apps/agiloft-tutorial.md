---
title: 'Tutorial: Integración de Azure Active Directory con Agiloft Contract Management Suite | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Agiloft Contract Management Suite.
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
ms.openlocfilehash: 25bea4a80a33d520683ec56ee4b40dd54b9652c9
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527035"
---
# <a name="tutorial-azure-active-directory-integration-with-agiloft-contract-management-suite"></a>Tutorial: Integración de Azure Active Directory con Agiloft Contract Management Suite

En este tutorial, aprenderá a integrar Agiloft Contract Management Suite con Azure Active Directory (Azure AD). Al integrar Agiloft Contract Management Suite con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Agiloft Contract Management Suite.
* Permitir que los usuarios inicien sesión automáticamente en Agiloft Contract Management Suite con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Agiloft Contract Management Suite.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Agiloft Contract Management Suite admite el inicio de sesión único habilitado por **SP e IDP**.
* Agiloft Contract Management Suite admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-agiloft-contract-management-suite-from-the-gallery"></a>Adición de Agiloft Contract Management Suite desde la galería

Para configurar la integración de Agiloft Contract Management Suite en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Agiloft Contract Management Suite** en el cuadro de búsqueda.
1. Seleccione **Agiloft Contract Management Suite** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-agiloft-contract-management-suite"></a>Configuración y prueba del inicio de sesión único de Azure AD para Agiloft Contract Management Suite

Configure y pruebe el inicio de sesión único de Azure AD con Agiloft Contract Management Suite mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Agiloft Contract Management Suite.

Para configurar y probar el inicio de sesión único de Azure AD con Agiloft Contract Management Suite, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Agiloft Contract Management Suite](#configure-agiloft-contract-management-suite-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Agiloft Contract Management Suite](#create-agiloft-contract-management-suite-test-user)** : para tener un homólogo de B.Simon en Agiloft Contract Management Suite que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Agiloft Contract Management Suite**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en el modo iniciado por **IDP** siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<SUBDOMAIN>.agiloft.com/<KB_NAME>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.agiloft.com:443/gui2/spsamlsso?project=<KB_NAME>`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.agiloft.com/gui2/samlssologin.jsp?project=<KB_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de clientes de Agiloft Contract Management Suite](https://www.agiloft.com/support-login.htm). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Configurar Agiloft Contract Management Suite**, copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Agiloft Contract Management Suite mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Agiloft Contract Management Suite**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-agiloft-contract-management-suite-sso"></a>Configuración del inicio de sesión único de Agiloft Contract Management Suite

1. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la empresa en Agiloft Contract Management Suite.

2. Haga clic en **Setup** (Configuración) (en el panel izquierdo) y, luego, seleccione **Access** (Acceso).

    ![Captura de pantalla que resalta la sección Access (Acceso).](./media/agiloft-tutorial/access.png)

3. Haga clic en el botón **Configure SAML 2.0 Single Sign-On** (Configurar inicio de sesión único de SAML 2.0).

    ![Captura de pantalla que resalta el botón Configure SAML 2.0 Single Sign-On (Configurar inicio de sesión único de SAML 2.0).](./media/agiloft-tutorial/setup.png)

4. Aparece un cuadro de diálogo del asistente. En el cuadro de diálogo, haga clic en la pestaña **Identity Provider Details** (Detalles del proveedor de identidades) y rellene los campos siguientes:  

    ![Configuración de Agiloft Contract Management Suite](./media/agiloft-tutorial/details.png)

    a. En el cuadro de texto **IdP Entity Id / Issuer** (Identificador de la entidad de IdP/Emisor), pegue el valor de **Identificador de Azure AD** que copió de Azure Portal.

    b. En el cuadro de texto **IdP Login URL** (URL de inicio de sesión del IdP), pegue el valor de la **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **IdP logout URL** (Dirección URL de cierre de sesión de IdP), pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.

    d. Abra el **certificado codificado en base 64** descargado de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego pegue el contenido en el cuadro de texto **IdP Provided X.509 certificate contents** (Contenido del certificado X.509 proporcionado por el IdP).

    e. Haga clic en **Finalizar**

### <a name="create-agiloft-contract-management-suite-test-user"></a>Creación de un usuario de prueba de Agiloft Contract Management Suite

En esta sección se creará un usuario llamado Britta Simon en Agiloft Contract Management Suite. Agiloft Contract Management Suite admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si todavía no existe ningún usuario en Agiloft Contract Management Suite, se crea uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Agiloft Contract Management Suite, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Agiloft Contract Management Suite e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Agiloft Contract Management Suite para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Agiloft Contract Management Suite en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Agiloft Contract Management Suite para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Agiloft Contract Management Suite, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).