---
title: 'Tutorial: Integración de Azure Active Directory con Azure AD SSO for Splunk Enterprise and Splunk Cloud | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Azure AD SSO for Splunk Enterprise and Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: jeedes
ms.openlocfilehash: bc1d0ec9b5aa74493a0ee43d94303b0a02278198
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466105"
---
# <a name="tutorial-azure-active-directory-integration-with-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integración de Azure Active Directory con Azure AD SSO for Splunk Enterprise and Splunk Cloud

En este tutorial aprenderá a integrar Azure AD SSO for Splunk Enterprise and Splunk Cloud con Azure Active Directory (Azure AD). Al integrar Azure AD SSO for Splunk Enterprise and Splunk Cloud con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Azure AD SSO for Splunk Enterprise and Splunk Cloud.
* Permitir que los usuarios inicien sesión automáticamente en Azure AD SSO for Splunk Enterprise and Splunk Cloud con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) en Azure AD SSO for Splunk Enterprise and Splunk Cloud.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Azure AD SSO for Splunk Enterprise and Splunk Cloud admite el inicio de sesión único iniciado por **SP**.

## <a name="add-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Incorporación de Azure AD SSO for Splunk Enterprise and Splunk Cloud desde la galería

Para configurar la integración de Azure AD SSO for Splunk Enterprise and Splunk Cloud en Azure AD, deberá agregar Azure AD SSO for Splunk Enterprise and Splunk Cloud desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Azure AD SSO for Splunk Enterprise and Splunk Cloud** en el cuadro de búsqueda.
1. Seleccione **Azure AD SSO for Splunk Enterprise and Splunk Cloud** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Configuración y prueba del inicio de sesión único de Azure AD para Azure AD SSO for Splunk Enterprise and Splunk Cloud

Configure y pruebe el inicio de sesión único de Azure AD con Azure AD SSO for Splunk Enterprise and Splunk Cloud mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Azure AD SSO for Splunk Enterprise and Splunk Cloud.

Para configurar el inicio de sesión único de Azure AD con Azure AD SSO for Splunk Enterprise and Splunk Cloud, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Azure AD SSO for Splunk Enterprise and Splunk Cloud](#configure-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Azure AD SSO for Splunk Enterprise and Splunk Cloud](#create-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-test-user)** : para tener un homólogo de B.Simon en Azure AD SSO for Splunk Enterprise and Splunk Cloud vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Azure AD SSO for Splunk Enterprise and Splunk Cloud**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<splunkserverUrl>/app/launcher/home`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `<splunkserverUrl>`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte de cliente de Azure AD SSO for Splunk Enterprise and Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

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

En esta sección va a permitir que B.Simon acceda a Azure AD SSO for Splunk Enterprise and Splunk Cloud mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Azure AD SSO for Splunk Enterprise and Splunk Cloud**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-sso"></a>Configuración del inicio de sesión único de Azure AD para Azure AD SSO for Splunk Enterprise and Splunk Cloud

  Para configurar el inicio de sesión único en **Azure AD SSO for Splunk Enterprise and Splunk Cloud**, tiene que enviar el archivo **XML de metadatos** descargado y las correspondientes direcciones URL copiadas de Azure Portal al [equipo de soporte técnico de Azure AD SSO for Splunk Enterprise and Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud-test-user"></a>Creación de un usuario de prueba en Azure AD SSO for Splunk Enterprise and Splunk Cloud

En esta sección creará un usuario llamado B.Simon en Azure AD SSO for Splunk Enterprise and Splunk Cloud. Trabaje con el [equipo de soporte técnico de Azure AD SSO for Splunk Enterprise and Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) para agregar los usuarios a la plataforma de la aplicación. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Azure AD SSO for Splunk Enterprise and Splunk Cloud, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de Azure AD SSO for Splunk Enterprise and Splunk Cloud y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Azure AD SSO for Splunk Enterprise and Splunk Cloud en Aplicaciones, se le redirigirá a la URL de inicio de sesión de Azure AD SSO for Splunk Enterprise and Splunk Cloud. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la aplicación Azure AD SSO for Splunk Enterprise and Splunk Cloud, podrá aplicar el control de sesión, que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).