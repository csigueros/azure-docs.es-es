---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con CrowdStrike Falcon Platform'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y CrowdStrike Falcon Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 35b1317b8ca049c9ddb4df7865c2fc936ee4826a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839159"
---
# <a name="tutorial-azure-ad-sso-integration-with-crowdstrike-falcon-platform"></a>Tutorial: Integración del inicio de sesión único de Azure AD con CrowdStrike Falcon Platform

En este tutorial, aprenderá a integrar CrowdStrike Falcon Platform con Azure Active Directory (Azure AD). Al integrar CrowdStrike Falcon Platform con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a CrowdStrike Falcon Platform.
* Permitir que los usuarios puedan iniciar sesión automáticamente en CrowdStrike Falcon Platform con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción válida a CrowdStrike Falcon.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* CrowdStrike Falcon Platform admite el inicio de sesión único activado habilitado **SP e IDP**.

## <a name="adding-crowdstrike-falcon-platform-from-the-gallery"></a>Adición de CrowdStrike Falcon Platform desde la galería

Para configurar la integración de CrowdStrike Falcon Platform en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **CrowdStrike Falcon Platform** en el cuadro de búsqueda.
1. Seleccione **CrowdStrike Falcon Platform** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-crowdstrike-falcon-platform"></a>Configuración y prueba del inicio de sesión único de Azure AD para CrowdStrike Falcon Platform

Configure y pruebe el inicio de sesión único de Azure AD con CrowdStrike Falcon Platform mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de CrowdStrike Falcon Platform.

Para configurar y probar el inicio de sesión único de Azure AD con CrowdStrike Falcon Platform, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en CrowdStrike Falcon Platform](#configure-crowdstrike-falcon-platform-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de CrowdStrike Falcon Platform](#create-crowdstrike-falcon-platform-test-user)** , para tener un homólogo de B. Simon en CrowdStrike Falcon Platform que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **CrowdStrike Falcon Platform**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una de las siguientes direcciones URL:

    | Identificador |
    | -------------- |
    | `https://falcon.crowdstrike.com/saml/metadata` |
    | `https://falcon.us-2.crowdstrike.com/saml/metadata` |
    | `https://falcon.eu-1.crowdstrike.com/saml/metadata` |
    | `https://falcon.laggar.gcw.crowdstrike.com/saml/metadata` |
    |

    b. En el cuadro de texto **URL de respuesta**, escriba una de las siguientes direcciones URL:

    | URL de respuesta |
    | -------------- |
    | `https://falcon.crowdstrike.com/saml/acs` |
    | `https://falcon.us-2.crowdstrike.com/saml/acs` |
    | `https://falcon.eu-1.crowdstrike.com/saml/acs` |
    | `https://falcon.laggar.gcw.crowdstrike.com/saml/acs` |
    |

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una de las siguientes direcciones URL:

    | URL de inicio de sesión |
    | -------------- |
    | `https://falcon.crowdstrike.com/login` |
    | `https://falcon.us-2.crowdstrike.com/login` |
    | `https://falcon.eu-1.crowdstrike.com/login` |
    | `https://falcon.laggar.gcw.crowdstrike.com/login` |
    |

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

En esta sección, va a permitir que B. Simon acceda a CrowdStrike Falcon Platform mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **CrowdStrike Falcon Platform**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-crowdstrike-falcon-platform-sso"></a>Configuración del inicio de sesión único de CrowdStrike Falcon Platform

Para configurar el inicio de sesión único en **CrowdStrike Falcon Platform**, es preciso enviar la **dirección URL de metadatos de federación de la aplicación** al [equipo de soporte técnico de CrowdStrike Falcon Platform](mailto:support@crowdstrike.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-crowdstrike-falcon-platform-test-user"></a>Creación de un usuario de prueba en CrowdStrike Falcon Platform

En esta sección, creará un usuario llamado Britta Simon en CrowdStrike Falcon Platform. Trabaje con el [equipo de soporte técnico de CrowdStrike Falcon Platform](mailto:support@crowdstrike.com) para agregar los usuarios a la plataforma de CrowdStrike Falcon Platform. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de CrowdStrike Falcon Platform, donde podrá poner en marcha el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de CrowdStrike Falcon Platform y ponga en marcha el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Debería iniciarse la sesión automáticamente en la aplicación CrowdStrike Falcon Platform para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de CrowdStrike Falcon Platform en Aplicaciones, si se ha configurado el modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para poner en marcha el flujo de inicio de sesión y, si ha configurado el modo IDP, se debería iniciar sesión automáticamente en la aplicación CrowdStrike Falcon Platform para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado CrowdStrike Falcon Platform, puede aplicar controles de sesión, que protegen contra la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


