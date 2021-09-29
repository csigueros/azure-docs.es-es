---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Broadcom DX SaaS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: c4915175cc87cbda12e31758c198628807d3411c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773557"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Broadcom DX SaaS

En este tutorial, aprenderá a integrar Broadcom DX SaaS con Azure Active Directory (Azure AD). Al integrar Broadcom DX SaaS con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Broadcom DX SaaS.
* Permitir que los usuarios inicien sesión automáticamente en Broadcom DX SaaS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Broadcom DX SaaS.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Broadcom DX SaaS admite el inicio de sesión mediante **IDP**.

* Broadcom DX SaaS admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Adición de Broadcom DX SaaS desde la galería

Para poder configurar la integración de Broadcom DX SaaS en Azure AD, debe agregar esta aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Broadcom DX SaaS** en el cuadro de búsqueda.
1. Seleccione **Broadcom DX SaaS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Configuración y prueba del inicio de sesión único de Azure AD en Broadcom DX SaaS

Configure y pruebe el inicio de sesión único de Azure AD con Broadcom DX SaaS mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Broadcom DX SaaS.

Para configurar y probar el inicio de sesión único de Azure AD con Broadcom DX SaaS, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Broadcom DX SaaS](#configure-broadcom-dx-saas-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Broadcom DX SaaS](#create-broadcom-dx-saas-test-user)** : para tener un homólogo de B.Simon en Broadcom DX SaaS que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Broadcom DX SaaS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar inicio de sesión único con SAML** realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba un valor con el siguiente patrón: `DXI_<TENANT_NAME>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Para obtener estos valores, póngase en contacto con el [equipo de atención al cliente de Broadcom DX SaaS](mailto:dxi-na1@saas.broadcom.com). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Broadcom DX SaaS espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos de token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además, la aplicación Broadcom DX SaaS espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre |  Atributo de origen|
    | --------------- | --------- |
    | Grupo | user.groups |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Broadcom DX SaaS**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Broadcom DX SaaS mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Broadcom DX SaaS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-broadcom-dx-saas-sso"></a>Configuración del inicio de sesión único en Broadcom DX SaaS

1. Inicie sesión en el sitio de la empresa de Broadcom DX SaaS como administrador.

2. Vaya a **Settings** (Configuración) y haga clic en la pestaña **Users** (Usuarios).

    ![Usuarios](./media/broadcom-dx-saas-tutorial/settings.png "Usuarios")

3. En la sección **User management** (Administración de usuarios), haga clic en los puntos suspensivos y seleccione **SAML**.

    ![Administración de usuarios](./media/broadcom-dx-saas-tutorial/local.png "Administración de usuarios")

4. En la sección **Identify SAML account** (Identificar cuenta de SAML), haga lo siguiente:
   
    ![Cuenta](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Cuenta") 

    a. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **Identificador de Azure AD** que ha copiado de Azure Portal.

    b. En el cuadro de texto **Identity Provider (IDP) Login URL** (Dirección URL de inicio de sesión del proveedor de identidades [IDP]), pegue el valor de **Login URL** (Dirección URL de inicio de sesión) que ha copiado de Azure Portal.

    c. En el cuadro de texto **Identity Provider (IDP) Logout URL** (Dirección URL de cierre de sesión del proveedor de identidades [IDP]), pegue el valor de **Logout URL** (Dirección URL de cierre de sesión) que ha copiado de Azure Portal.

    d. Abra en el Bloc de notas el archivo del **certificado (Base 64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Identity provider certificate** (Certificado del proveedor de identidades).

    e. Haga clic en **SIGUIENTE**.

5. En la sección **Map attributes** (Asignar atributos), rellene los atributos SAML necesarios en la página siguiente y haga clic en **NEXT** (SIGUIENTE).

    ![Atributos](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Atributos") 


6. En la sección **Identify user group** (Identificar grupo de usuarios), escriba el identificador de objeto de ese grupo y haga clic en **NEXT** (SIGUIENTE).

    ![Agregar grupo](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Agregar grupo") 

7. En la sección **Populate SAML Account** (Rellenar cuenta de SAML), compruebe los valores rellenados y haga clic en **SAVE** (GUARDAR).

    ![Cuenta de SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Cuenta de SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Creación de un usuario de prueba de Broadcom DX SaaS

En esta sección, se crea un usuario llamado Britta Simon en Broadcom DX SaaS. Broadcom DX SaaS admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si aún no existe ningún usuario en Broadcom DX SaaS, se crea uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Broadcom DX SaaS para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Broadcom DX SaaS en Aplicaciones, debería iniciar sesión automáticamente en la instancia de Broadcom DX SaaS para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Broadcom DX SaaS, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).