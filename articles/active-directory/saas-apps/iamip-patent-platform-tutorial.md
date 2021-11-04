---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con IamIP Platform'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory e IamIP Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2021
ms.author: jeedes
ms.openlocfilehash: 331d870472e328e0e1f5be4e24763327fe054162
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057066"
---
# <a name="tutorial-azure-ad-sso-integration-with-iamip-platform"></a>Tutorial: Integración del inicio de sesión único de Azure AD con IamIP Platform

En este tutorial, aprenderá a integrar IamIP Platform con Azure Active Directory (Azure AD). Al integrar IamIP Platform con Azure AD, puede realizar lo siguiente:

* Controlar en Azure AD quién tiene acceso a IamIP Platform.
* Permitir que los usuarios puedan iniciar sesión automáticamente en IamIP Platform con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Prerrequisitos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en IamIP Platform.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* IamIP Platform admite el inicio de sesión único iniciado por SP e IDP.
* IamIP Platform admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-iamip-platform-from-the-gallery"></a>Adición de IamIP Platform desde la galería

Para configurar la integración de IamIP Platform en Azure AD, deberá agregar IamIP Platform desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta profesional o educativa o con una cuenta Microsoft personal.
1. En el panel izquierdo, seleccione **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **IamIP Platform** en el cuadro de búsqueda.
1. Seleccione **IamIP Platform** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-iamip-platform"></a>Configuración y prueba del inicio de sesión único de Azure AD para IamIP Platform

Configure y pruebe el inicio de sesión único de Azure AD con IamIP Platform mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de IamIP Platform.

Para configurar y probar el inicio de sesión único de Azure AD con IamIP Platform, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)**, para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en IamIP Platform](#configure-iamip-platform-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en IamIP Platform](#create-iamip-platform-test-user)** : para tener un homólogo de B.Simon en IamIP Platform vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)**, para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **IamIP Platform**, busque la sección **Administrar** y seleccione **inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **SP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://accounts.iamip.com/`
    
    b. En el cuadro de texto **URL de respuesta**, escriba una de las siguientes direcciones URL:

    | **URL de respuesta** |
    |---------|
    | `https://accounts.iamip.com/sso-callback` |
    | `https://accounts.iamip.com/sso-login` |
    | `https://accounts.iamip.com/sso-logout` |

    c. En el cuadro de texto **URL de inicio de sesión**, escriba una de las siguientes direcciones URL:

    | **URL de inicio de sesión** |
    |------|
    | `https://accounts.iamip.com/login` |
    | `https://patents.iamip.com/login-user` |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar IamIP Platform**, copie las direcciones URL adecuadas en función de sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**. Seleccione **Usuarios** y, a continuación, seleccione **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades de **usuario**, realice estos pasos:
   1. En el cuadro **Nombre**, escriba **B.Simon**.  
   1. En el cuadro **Nombre de usuario**, escriba \<username>@\<companydomain>.\<extension>. Por ejemplo, `B.Simon@contoso.com`.
   1. Seleccione **Mostrar contraseña** y, a continuación, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección va a permitir que B.Simon acceda a IamIP Platform mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **IamIP Platform**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-iamip-platform-sso"></a>Configuración del inicio de sesión único de IamIP Platform

Para configurar el inicio de sesión único en **IamIP Platform**, es preciso enviar el **certificado (Base64)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de IamIP Platform](mailto:info@iamip.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-iamip-platform-test-user"></a>Creación de un usuario de prueba de IamIP Platform

En esta sección se crea un usuario llamado Britta Simon en IamIP Platform. IamIP Platform admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario no existe aún en IamIP Platform, se crea uno nuevo después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de IamIP Platform, donde podrá poner en marcha el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de IamIP Platform y ponga en marcha el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Debería iniciarse la sesión automáticamente en la aplicación IamIP Platform para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de IamIP Platform en Aplicaciones, si se ha configurado el modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para poner en marcha el flujo de inicio de sesión y, si ha configurado el modo IDP, se debería iniciar sesión automáticamente en la aplicación IamIP Platform para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado IamIP Platform, puede aplicar el control de sesión que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).