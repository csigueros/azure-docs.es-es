---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Visibly | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Visibly.
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
ms.openlocfilehash: f482e1e35b303cb4dfd355e2a8da796de5296a99
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820898"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visibly"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Visibly

En este tutorial, aprenderá a integrar Visibly con Azure Active Directory (Azure AD). Al integrar Visibly con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Visibly.
* Permitir que los usuarios inicien sesión automáticamente en Visibly con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Visibly.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Visibly admite el inicio de sesión único iniciado por **SP**.
* Visibly admite el [aprovisionamiento automatizado de usuarios](visibly-provisioning-tutorial.md).

## <a name="add-visibly-from-the-gallery"></a>Adición de Visibly desde la galería

Para configurar la integración de Visibly en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Visibly** en el cuadro de búsqueda.
1. Seleccione **Visibly** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-visibly"></a>Configuración y prueba del inicio de sesión único de Azure AD para Visibly

Configure y pruebe el inicio de sesión único de Azure AD con Visibly mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Visibly.

Para configurar y probar el inicio de sesión único de Azure AD con Visibly, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Visibly](#configure-visibly-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Visibly](#create-visibly-test-user)** : para tener un homólogo de B.Simon en Visibly que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Visibly**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://app.visibly.io/`

    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL: `https://api.visibly.io/api/v1/verifyResponse`

1. La aplicación Visibly espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Visibly espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre |  Atributo de origen|
    | ----------- | --------- |
    | city | user.city |
    | lastName | user.surname |
    | state | user.state |
    | department | user.department |
    | email | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Visibly**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Visibly mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Visibly**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-visibly-sso"></a>Configuración del inicio de sesión único de Visibly

1. Inicie sesión en Visibly con sus credenciales.

1. Vaya a la opción **Settings** (Configuración) del menú de navegación.

    ![Captura de pantalla que muestra la opción de configuración seleccionada.](./media/visibly-tutorial/settings.png)

1. En Settings (Configuración), haga clic en **Integraciones** (Integraciones).

    ![Captura de pantalla que muestra la opción Integrations (Integraciones) seleccionada en el menú Settings (Configuración).](./media/visibly-tutorial/integrations.png)

1. En **Integraciones**, seleccione **SSO**.

    ![Captura de pantalla que muestra la opción S S O seleccionada en Integrations (Integraciones).](./media/visibly-tutorial/sso.png)

1. En la página siguiente, realice estos pasos.

    ![Captura de pantalla que muestra la página S S O Integration (Integración de S S O), donde puede especificar los valores descritos.](./media/visibly-tutorial/configuration.png)

    a. En el cuadro de texto **Id. de entidad**, pegue el valor de **Id. de entidad** que ha copiado desde Azure Portal.

    b. En el cuadro de texto **SSO url** (URL de inicio de sesión único), pegue el valor de la **URL de inicio de sesión** que copió de Azure Portal.

    c. En el cuadro de texto **SSO name** (Nombre de SSO), proporcione cualquier nombre válido.

    d. Abra el **certificado (Base64)** descargado desde Azure Portal en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado**. También puede cargar el **Certificado** seleccionando **Cargar certificado**.

    e. Haga clic en **Save**(Guardar).

### <a name="create-visibly-test-user"></a>Creación de un usuario de prueba de Visibly

En esta sección, se crea un usuario llamado B.Simon en Visibly. Visibly admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario no existe aún en Visibly, se crea uno nuevo después de la autenticación.

Visibly también admite el aprovisionamiento automático de usuarios. [Aquí](./visibly-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Visibly, donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Visibly e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Visibly en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Visibly, puede aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
