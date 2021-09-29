---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con TrendMiner | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TrendMiner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: 96e8d433b7c69ab3b21a6f97aa8d236287841a7e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800467"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trendminer"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con TrendMiner

En este tutorial aprenderá a integrar TrendMiner con Azure Active Directory (Azure AD). Al integrar TrendMiner con Azure AD, se puede realizar lo siguiente:

* Controlar en Azure AD quién tiene acceso a TrendMiner.
* Permitir que los usuarios inicien sesión automáticamente en TrendMiner con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en TrendMiner.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* TrendMiner admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="adding-trendminer-from-the-gallery"></a>Incorporación de TrendMiner desde la galería

Para configurar la integración de TrendMiner con Azure AD, deberá agregar TrendMiner desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **TrendMiner** en el cuadro de búsqueda.
1. Seleccione **TrendMiner** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-trendminer"></a>Configuración y prueba del inicio de sesión único de Azure AD para TrendMiner

Configure y pruebe el inicio de sesión único de Azure AD con TrendMiner mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de TrendMiner.

Para configurar y probar el inicio de sesión único de Azure AD con TrendMiner, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en TrendMiner](#configure-trendminer-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en TrendMiner](#create-trendminer-test-user)** : para tener un homólogo de B.Simon en TrendMiner vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **TrendMiner**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<CUSTOMER>.trendminer.cloud/security/saml/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMER>.trendminer.cloud/security/saml/SSO`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMER>.trendminer.cloud/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico al cliente de TrendMiner](mailto:support@trendminer.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up TrendMiner** (Configurar TrendMiner), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a TrendMiner mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **TrendMiner**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-trendminer-sso"></a>Configuración del inicio de sesión único en TrendMiner

1. Abra una nueva ventana del explorador web e inicie sesión en el sitio de la empresa TrendMiner como administrador.

1. En el menú de la izquierda, seleccione **SECURITY > Identity Provider** (SEGURIDAD > Proveedor de identidades)

1. En la página **Identity Provider** (Proveedor de identidades), haga clic en **SAML** y, después, en **Next step** (Siguiente paso).

    ![selección de SAML](./media/trendminer-tutorial/saml.png)

1. Haga clic en **Next step** (Siguiente paso) en la página **SAML Backup** (Copia de seguridad de SAML).

    ![selección de SAML Backup (Copia de seguridad de SAML)](./media/trendminer-tutorial/saml-backup.png)

1. Haga clic en **Next step** (Siguiente paso) en **Self-Signed certificates** (Certificados autofirmados).

    ![Página Self-Signed certificates (Certificados autofirmados)](./media/trendminer-tutorial/self-signed-certificate.png)

1. Puede elegir **Skip** (Omitir) para no cargar una clave de firma.

    ![carga de una clave de firma](./media/trendminer-tutorial/signing-key.png)

1. En la pantalla **SAML Configuration** (Configuración de SAML) de **Entity base URL** (Dirección URL de base de entidad), escriba una dirección URL de tipo `https://trendminer.domain.com/`.

1. En **Identity provider metadata** (Metadatos del proveedor de identidades), cargue el **archivo de metadatos de Azure** que copió de Azure Portal y haga clic en **Next step** (Siguiente paso).

    ![Configuración de SAML](./media/trendminer-tutorial/saml-configuration.png)

1. En la sección **SAML User mapping** (Asignación de usuarios de SAML), escriba los nombres de usuario que usará para iniciar sesión y haga clic en **Finish** (Finalizar).

    ![SAML User mapping (Asignación de usuarios de SAML)](./media/trendminer-tutorial/user-mapping.png)

### <a name="create-trendminer-test-user"></a>Creación de un usuario de prueba en TrendMiner

En esta sección creará un usuario llamado B.Simon en TrendMiner. Trabaje con el [equipo de soporte técnico de TrendMiner](mailto:support@trendminer.com) para agregar los usuarios en la plataforma de TrendMiner. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de TrendMiner, donde podrá iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de TrendMiner e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de TrendMiner para la que configurara el inicio de sesión único. 

También puede usar el Panel de acceso de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de TrendMiner en el panel de acceso, si tiene la configuración del modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si tiene la configuración del modo IDP, debería iniciar sesión automáticamente en la instancia de TrendMiner para la que configurara el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la aplicación TrendMiner, podrá aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).