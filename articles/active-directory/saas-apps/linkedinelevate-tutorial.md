---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con LinkedIn Elevate | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y LinkedIn Elevate.
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
ms.openlocfilehash: c055984ed1f50ed0b8511d0aceb611ef863e1625
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111569184"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con LinkedIn Elevate

En este tutorial, aprenderá a integrar LinkedIn Elevate con Azure Active Directory (Azure AD). Al integrar LinkedIn Elevate con Azure AD, puede realizar lo siguiente:

* Controlar en Azure AD quién tiene acceso a LinkedIn Elevate.
* Permitir que los usuarios inicien sesión automáticamente en LinkedIn Elevate con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en LinkedIn Elevate.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* LinkedIn Elevate admite el inicio de sesión único iniciado por **SP e IDP**.
* LinkedIn Elevate admite el aprovisionamiento de usuarios **Just In Time**.
* LinkedIn Elevate admite el [**aprovisionamiento automático** de usuarios](linkedinelevate-provisioning-tutorial.md).

## <a name="add-linkedin-elevate-from-the-gallery"></a>Incorporación de LinkedIn Elevate desde la galería

Para configurar la integración de LinkedIn Elevate en Azure AD, deberá agregar LinkedIn Elevate desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **LinkedIn Elevate** en el cuadro de búsqueda.
1. Seleccione **LinkedIn Elevate** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-linkedin-elevate"></a>Configuración y prueba del inicio de sesión único de Azure AD para LinkedIn Elevate

Configure y pruebe el inicio de sesión único de Azure AD con LinkedIn Elevate mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de LinkedIn Elevate.

Para configurar y probar el inicio de sesión único de Azure AD con LinkedIn Elevate, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en LinkedIn Elevate](#configure-linkedin-elevate-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de LinkedIn Elevate](#create-linkedin-elevate-test-user)** , para tener un homólogo de B.Simon en LinkedIn Elevate que esté vinculado a la representación de este usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **LinkedIn Elevate**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba el valor de **Id. de entidad**; este valor lo copiará de Linkedin Portal, que se explica más adelante en este tutorial.

    b. En el cuadro de texto **URL de respuesta**, escriba el valor de **Assertion Consumer Access (ACS) Url** (Dirección URL de Assertion Consumer Access [ACS]); este valor lo copiará de Linkedin Portal, que se explica más adelante en este tutorial.

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.linkedin.com/checkpoint/enterprise/login/<ACCOUNT_ID>?application=elevate&applicationInstanceId=<INSTANCE_ID>`

1. La aplicación LinkedIn Elevate espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación LinkedIn Elevate espera que nameidentifier se asigne a **user.mail**, por lo que debe editar la asignación de atributos haciendo clic en el icono Editar y cambiar dicha asignación.

    ![imagen](common/edit-attribute.png)

1. Además de lo anterior, la aplicación LinkedIn Elevate espera que se devuelvan algunos atributos más (se muestran a continuación) en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre | Atributo de origen|
    | -------| -------------|
    | department | user.department |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar LinkedIn Elevate**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a LinkedIn Elevate mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **LinkedIn Elevate**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-linkedin-elevate-sso"></a>Configuración del inicio de sesión único en LinkedIn Elevate

1. En otra ventana del explorador web, inicie sesión como administrador en el inquilino LinkedIn Learning.

1. En **Account Center** (Centro de cuentas), haga clic en **Global Settings** (Configuración global) en **Settings** (Configuración). Además, seleccione **Elevate - Elevate AAD Test** en la lista desplegable.

    ![Captura de pantalla que muestra Global Settings (Configuración global), donde puede seleccionar Elevate A A D Test (Elevar prueba de A A D).](./media/linkedinelevate-tutorial/admin.png)

1. Haga clic en **OR Click Here to load and copy individual fields from the form** (O haga clic aquí para cargar y copiar campos individuales del formulario) y haga lo siguiente:

    ![Captura de pantalla que muestra Inicio de sesión único donde puede escribir los valores descritos.](./media/linkedinelevate-tutorial/test.png)

    a. Copie el valor **Entity Id** (Id. de entidad) y péguelo en el cuadro de texto **Identificador** de la sección **Basic SAML Configuration** (Configuración básica de SAML) de Azure Portal.

    b. Copie el valor de **Assertion Consumer Access (ACS) Url** (Dirección URL de Assertion Consumer Access [ACS]) y péguelo en el cuadro de texto **URL de respuesta** de **Basic SAML Configuration** (Configuración básica de SAML) de Azure Portal.

1. Vaya a la sección **LinkedIn Admin Settings** (Configuración de administrador de LinkedIn). Cargue el archivo XML que ha descargado de Azure Portal haciendo clic en la opción Upload XML file (Cargar archivo XML).

    ![Captura de pantalla que muestra la sección Configure the LinkedIn service provider S S O settings (Configurar valores de inicio de sesión único del proveedor de servicios de LinkedIn) donde puede cargar un archivo X M L.](./media/linkedinelevate-tutorial/metadata.png)

1. Haga clic en **On** (Activar) para habilitar SSO. El estado de SSO cambiará de **Not Connected** (No conectado) a **Connected** (Conectado).

    ![Captura de pantalla que muestra el inicio de sesión único, donde puede seleccionar Automatically Assign Licenses (Asignar licencias automáticamente).](./media/linkedinelevate-tutorial/connected.png)

### <a name="create-linkedin-elevate-test-user"></a>Creación de un usuario de prueba de LinkedIn Elevate

La aplicación LinkedIn Elevate admite aprovisionamiento de usuarios Just-In-Time y, tras la autenticación, los usuarios se crearán automáticamente en la aplicación. En la página de configuración del administrador del portal de LinkedIn Elevate, invierta el conmutador **Automatically Assign licenses** (Asignar licencias automáticamente) a aprovisionamiento Just-In-Time activo, y este también asignará una licencia al usuario. LinkedIn Elevate también admite el aprovisionamiento automático de usuarios. [Aquí](linkedinelevate-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

   ![Creación de un usuario de prueba de Azure AD](./media/linkedinelevate-tutorial/switch.png)

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de LinkedIn Elevate, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de LinkedIn Elevate e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; se debería iniciar sesión automáticamente en la instancia de LinkedIn Elevate para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de LinkedIn Elevate en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de LinkedIn Elevate para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado LinkedIn Elevate, puede aplicar el control de sesión, que protege la filtración e infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
