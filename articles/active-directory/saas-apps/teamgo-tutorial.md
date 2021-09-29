---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Teamgo | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Teamgo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2021
ms.author: jeedes
ms.openlocfilehash: 16cdd3aaa78b1b0fb445887f8cd9f158c79c6970
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760990"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamgo"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Teamgo

En este tutorial, aprenderá a integrar Teamgo con Azure Active Directory (Azure AD). Al integrar Teamgo con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Teamgo.
* Permitir que los usuarios inicien sesión automáticamente en Teamgo con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Teamgo.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Teamgo admite el inicio de sesión único iniciado por **SP e IDP**.
* Teamgo admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="adding-teamgo-from-the-gallery"></a>Adición de Teamgo desde la galería

Para configurar la integración de Teamgo en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Teamgo** en el cuadro de búsqueda.
1. Seleccione **Teamgo** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-teamgo"></a>Configuración y prueba del inicio de sesión único de Azure AD para Teamgo

Configure y pruebe el inicio de sesión único de Azure AD con Teamgo mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Teamgo.

Para configurar y probar el inicio de sesión único de Azure AD con Teamgo, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Teamgo](#configure-teamgo-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Teamgo](#create-teamgo-test-user)** : para tener un homólogo de B.Simon en Teamgo que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Teamgo**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una de las siguientes direcciones URL:

    | Identificador |
    |-----------|
    | `https://my.teamgo.co` |
    | `https://my.teamgo.co/integration/saml/serviceProviderEntityId` |
    |

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con uno de los siguientes patrones:

    | URL de respuesta |
    |-----------|
    | `https://my.teamgo.co` |
    | `https://my.teamgo.co/integration/saml?acs&domain=<DOMAIN NAME>` |
    |

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://my.teamgo.co/integration/saml `

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de Teamgo](mailto:support@teamgo.co) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Teamgo espera las aserciones de SAML en un formato específico, para lo que tendrá que agregar asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Teamgo espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre |  Atributo de origen|
    | --------------- | --------- |
    | displayname | user.displayname |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Teamgo**, copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Teamgo mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Teamgo**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-teamgo-sso"></a>Configuración del inicio de sesión único de Teamgo

1. Inicie sesión en el sitio web de Teamgo como administrador.

1. Haga clic en el botón **Kiosks** (Pantallas completas) y vaya a **Settings** -> **Integrations** (Configuración>Integraciones) el panel de navegación de la izquierda.

1. En **Integrations** (Integraciones), vaya a la pestaña **Single sign-on** (Inicio de sesión único) y haga clic en **Configure** (Configurar).

    ![Integraciones de Teamgo](./media/teamgo-tutorial/integrations.png)

1. Realice los pasos que se indican a continuación en la página **SAML** siguiente. 

    ![Configuración de Teamgo](./media/teamgo-tutorial/configuration.png)

    a. En el cuadro de texto **SSO Domain** (Dominio de SSO), asigne un valor al dominio.

    b. En el cuadro de texto **Issuer URL** (Dirección URL del emisor), pegue el valor del **Identificador** que ha copiado de Azure Portal.

    c. En el cuadro de texto **SAML 2.0 Endpoint(HTTP)** (Punto de conexión SAML 2.0 [HTTP]), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    d. En el Bloc de notas, abra el **Certificado (Base64)** que ha descargado de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Public Certificate/Fingerprint** (Certificado público/huella digital).

    e. Haga clic en **Save**(Guardar).

### <a name="create-teamgo-test-user"></a>Creación del usuario de prueba de Teamgo

En esta sección se creará un usuario llamado B.Simon en Teamgo. Teamgo admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si todavía no existe ningún usuario en Teamgo, se crea uno al intentar acceder a Teamgo.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Teamgo, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Teamgo e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Teamgo para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Teamgo en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Teamgo para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Teamgo, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).