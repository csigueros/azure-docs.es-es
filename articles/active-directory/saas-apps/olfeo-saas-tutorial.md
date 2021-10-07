---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Olfeo SAAS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Olfeo SAAS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 45ec57871be8160c2bfde34c20137bd6c7afd6c7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124738582"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-olfeo-saas"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Olfeo SAAS

En este tutorial, aprenderá a integrar Olfeo SAAS con Azure Active Directory (Azure AD). Si integra Olfeo SAAS con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Olfeo SAAS.
* Permitir que los usuarios inicien sesión automáticamente en Olfeo SAAS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita el inicio de sesión único (SSO) en Olfeo SAAS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Olfeo SAAS admite el inicio de sesión único iniciado por **SP**.

* Olfeo SAAS admite el [aprovisionamiento automatizado de usuarios](olfeo-saas-provisioning-tutorial.md).

## <a name="adding-olfeo-saas-from-the-gallery"></a>Adición de Olfeo SAAS desde la galería

Para poder configurar la integración de Olfeo SAAS en Azure AD, debe agregar esta aplicación a la lista de aplicaciones SaaS administradas desde la galería.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Olfeo SAAS** en el cuadro de búsqueda.
1. Seleccione **Olfeo SAAS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-olfeo-saas"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Olfeo SAAS

Configure y pruebe el inicio de sesión único de Azure AD con Olfeo SAAS utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso vincular un usuario de Azure AD y el usuario correspondiente de Olfeo SAAS.

Para configurar y probar el inicio de sesión único de Azure AD con Olfeo SAAS, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Olfeo SAAS](#configure-olfeo-saas-sso)** : para configurar el inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Olfeo SAAS](#create-olfeo-saas-test-user)** : para tener un homólogo de B.Simon en Olfeo SAAS que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Olfeo SAAS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.olfeo.com/api/sso/saml/<ID>/login`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.olfeo.com/api/sso/saml/<ID>/login`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.olfeo.com/api/sso/saml/<ID>/acs`

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de respuesta, el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de Olfeo SAAS](mailto:equipe-rd@olfeo.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a Olfeo SAAS.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Olfeo SAAS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-olfeo-saas-sso"></a>Configuración del inicio de sesión único de Olfeo SAAS

Para configurar el inicio de sesión único en **Olfeo SAAS**, debe enviar la **dirección URL de metadatos de federación de la aplicación** al [equipo de soporte técnico de Olfeo SAAS](mailto:equipe-rd@olfeo.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-olfeo-saas-test-user"></a>Creación de un usuario de prueba de Olfeo SAAS

En esta sección, va a crear un usuario llamado Britta Simon en Olfeo SAAS. Colabore con el [equipo de soporte técnico de Olfeo SAAS](mailto:equipe-rd@olfeo.com) para agregar los usuarios a esta plataforma. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

Olfeo SAAS también admite el aprovisionamiento automático de usuarios. [Aquí](./olfeo-saas-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurarlo.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Al hacerlo, accederá automáticamente a la dirección URL de Olfeo SAAS, donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de Olfeo SAAS y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Si en Aplicaciones hace clic en el icono de Olfeo SAAS, debería acceder automáticamente a la dirección URL de inicio de sesión de esta aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Olfeo SAAS, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).