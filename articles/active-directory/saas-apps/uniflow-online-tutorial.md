---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con uniFLOW Online | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y uniFLOW Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jeedes
ms.openlocfilehash: aff05774e08efb5e0e1e1a0e9d7a2c104bd427e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808110"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con uniFLOW Online

En este tutorial, aprenderá a integrar uniFLOW Online con Azure Active Directory (Azure AD). Cuando integre uniFLOW Online con Azure AD podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a uniFLOW Online.
* Permitir que los usuarios inicien sesión automáticamente en uniFLOW Online con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Un inquilino de uniFLOW Online.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* uniFLOW Online admite el inicio de sesión único iniciado por **SP**.

## <a name="add-uniflow-online-from-the-gallery"></a>Incorporación de uniFLOW Online desde la galería

Para configurar la integración de uniFLOW Online en Azure AD, debe agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **uniFLOW Online** en el cuadro de búsqueda.
1. Seleccione **uniFLOW Online** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Configuración y prueba del inicio de sesión único de Azure AD para uniFLOW

Configure y pruebe el inicio de sesión único de Azure AD con uniFLOW Online mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de uniFLOW Online.

Para configurar el inicio de sesión único de Azure AD con uniFLOW Online, realice los pasos siguientes:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
   1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
   1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de uniFLOW Online](#configure-uniflow-online-sso)** : para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Inicio de sesión en UniFLOW Online con el usuario de prueba creado](#sign-in-to-uniflow-online-using-the-created-test-user)** , para probar el inicio de sesión del usuario en la aplicación.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **uniFLOW Online**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con uno de los patrones siguientes:

    | **Identificador** |
    |---------|
    | `https://<tenant_domain_name>.eu.uniflowonline.com` |
    | `https://<tenant_domain_name>.us.uniflowonline.com` |
    | `https://<tenant_domain_name>.sg.uniflowonline.com` |
    | `https://<tenant_domain_name>.jp.uniflowonline.com` |
    | `https://<tenant_domain_name>.au.uniflowonline.com` |

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    | **Dirección URL de inicio de sesión** |
    |---------|
    | `https://<tenant_domain_name>.eu.uniflowonline.com` |
    | `https://<tenant_domain_name>.us.uniflowonline.com` |
    | `https://<tenant_domain_name>.sg.uniflowonline.com` |
    | `https://<tenant_domain_name>.jp.uniflowonline.com` |
    | `https://<tenant_domain_name>.au.uniflowonline.com` |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico para clientes de uniFLOW Online](mailto:support@nt-ware.com) para obtener estos valores. También puede hacer referencia a los patrones mostrados en la sección **Configuración básica de SAML** en Azure Portal o hacer referencia a la dirección URL de respuesta que se muestra en el inquilino de uniFLOW Online.

1. La aplicación uniFLOW Online espera las aserciones de SAML en un formato específico que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación uniFLOW Online espera que **nameidentifier** se asigne a **user.objectid**, por lo que debe editar la asignación de atributos; para ello, haga clic en el icono de **Editar** y cambie dicha asignación.

    ![Captura de pantalla que muestra el panel User Attributes (Atributos de usuario) con el icono de edición resaltado.](common/edit-attribute.png)

1. Además de lo anterior, la aplicación uniFLOW Online espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre |  Atributo de origen|
    | -----------| --------------- |
    | displayname | user.displayname |
    | nickname | user.onpremisessamaccountname |

   > [!NOTE]
   > El atributo `user.onpremisessamaccountname` contendrá un valor solo si los usuarios de Azure AD se sincronizan desde una instancia local de Windows Active Directory.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

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

En esta sección va a permitir que B.Simon acceda a uniFLOW Online mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **uniFLOW Online**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

> [!NOTE]
> Para permitir que todos los usuarios accedan a la aplicación sin asignación manual, vaya a la sección **Administrar** y seleccione **Propiedades**. A continuación, cambie el parámetro **Asignación de usuarios necesaria** a **NO**.

## <a name="configure-uniflow-online-sso"></a>Configuración del inicio de sesión único de uniFLOW Online

1. En otra ventana del explorador web, inicie sesión en el sitio web de uniFLOW Online como administrador.

1. En el panel de navegación de la izquierda, seleccione la pestaña **Usuario**.

    ![Captura de pantalla que muestra el usuario seleccionado en el sitio de uniFLOW Online.](./media/uniflow-online-tutorial/user.png)

1. Haga clic en **Proveedores de identidades**.

    ![Captura de pantalla que muestra la opción de proveedores de identidades seleccionada.](./media/uniflow-online-tutorial/profile.png)

1. Haga clic en **Agregar proveedor de identidades**.

    ![Captura de pantalla que muestra la opción para agregar un proveedor de identidades seleccionada.](./media/uniflow-online-tutorial/add-profile.png)

1. En la sección **Agregar proveedor de identidades**, realice los pasos siguientes:

    ![Captura de pantalla que muestra la sección AGREGAR PROVEEDOR DE IDENTIDADES, donde puede especificar los valores descritos.](./media/uniflow-online-tutorial/configuration.png)

    a. Indique el nombre para mostrar, por ejemplo: **AzureAD SSO**.

    b. En **Tipo de proveedor**, seleccione la opción **WS-Fed** del menú desplegable.

    c. En **WS-Fed type** (Tipo de WS-Fed), seleccione la opción **Azure Active Directory** del menú desplegable.

    d. Haga clic en **Save**(Guardar).

1. En la pestaña **General**, realice los pasos siguientes:

    ![Captura de pantalla que muestra la pestaña General, en la que puede especificar los valores descritos.](./media/uniflow-online-tutorial/general-tab.png)

    a. Indique el nombre para mostrar, por ejemplo: **AzureAD SSO**.

    b. Seleccione la opción **From URL** (Desde URL) para **ADFS Federation Metadata** (Metadatos de federación de ADFS).

    c. En el cuadro de texto **Dirección URL de metadatos de federación**, pegue el valor de **Dirección URL de metadatos de federación de aplicación** que copió de Azure Portal.

    d. Seleccione **Identity Provider Initiated** (Proveedor de identidades) como **Habilitado**.

    e. Seleccione **Automatic user registration** (Registro de usuarios automático) como **Activado**.

    f. Haga clic en **Save**(Guardar).

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Inicio de sesión en uniFLOW Online con el usuario de prueba creado

1. En otra ventana del explorador web, vaya a la dirección URL de uniFLOW Online para el inquilino.

1. Seleccione el proveedor de identidades creado anteriormente para iniciar sesión a través de la instancia de Azure AD.

1. Inicie sesión con el usuario de prueba.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de uniFLOW Online, donde podrá iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de uniFLOW Online e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de uniFLOW Online en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de uniFLOW Online. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado uniFLOW Online, puede aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).