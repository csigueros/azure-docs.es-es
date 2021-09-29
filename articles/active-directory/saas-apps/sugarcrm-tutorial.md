---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Sugar CRM | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Sugar CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 5af4f18d3fd5e91ed48f8bdd603906b2f700c358
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Sugar CRM

En este tutorial, aprenderá a integrar Sugar CRM con Azure Active Directory (Azure AD). Al integrar Sugar CRM con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Sugar CRM.
* Permitir a los usuarios iniciar sesión automáticamente en Sugar CRM con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Sugar CRM.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Sugar CRM admite el SSO iniciado por **SP**

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-sugar-crm-from-the-gallery"></a>Adición de Sugar CRM desde la galería

Para configurar la integración de Sugar CRM en Azure AD, deberá agregar Sugar CRM desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Sugar CRM** en el cuadro de búsqueda.
1. Seleccione **Sugar CRM** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>Configuración y prueba del inicio de sesión único de Azure AD para Sugar CRM

Configure y pruebe el inicio de sesión único de Azure AD con Sugar CRM utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Sugar CRM.

Para configurar y probar el inicio de sesión único de Azure AD con Sugar CRM, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Sugar CRM](#configure-sugar-crm-sso)** : para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Sugar CRM](#create-sugar-crm-test-user)** : para tener un homólogo de B.Simon en Sugar CRM que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación de **Sugar CRM**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Sugar CRM](https://support.sugarcrm.com/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Sugar CRM** (Configurar Sugar CRM), copie las direcciones URL adecuadas en función de sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Sugar CRM mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Sugar CRM**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sugar-crm-sso"></a>Configuración del inicio de sesión único en Sugar CRM

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Sugar CRM como administrador.

1. Vaya a **Administración**.

    ![Administrador](./media/sugarcrm-tutorial/ic795888.png "Administración")

1. En la sección **Administración**, haga clic en **Administración de contraseñas**.

    ![Captura de pantalla que muestra la sección Administración donde puede seleccionar Administración de contraseñas.](./media/sugarcrm-tutorial/ic795889.png "Administración")

1. Seleccione **Habilitar autenticación SAML**.

    ![Captura de pantalla que muestra la opción para seleccionar la autenticación SAML.](./media/sugarcrm-tutorial/ic795890.png "Administración")

1. En la sección **Autenticación SAML** , realice los pasos siguientes:

    ![SAML Authentication (Autenticación SAML)](./media/sugarcrm-tutorial/save.png "Autenticación SAML")  

    a. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.
  
    b. En el cuadro de texto **SLO URL** (Dirección URL de SLO), pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.
  
    c. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego pegue todo el certificado en el cuadro de texto **Certificado X.509** .
  
    d. Haga clic en **Save**(Guardar).

### <a name="create-sugar-crm-test-user"></a>Creación de un usuario de prueba en Sugar CRM

Para permitir que los usuarios de Azure AD inicien sesión en Sugar CRM, deben aprovisionarse en Sugar CRM. En el caso de SugarCRM, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de compañía de **Sugar CRM** como administrador.

1. Vaya a **Administración**.

    ![Administrador](./media/sugarcrm-tutorial/ic795888.png "Administración")

1. En la sección **Administración**, haga clic en **Administración de usuarios**.

    ![Captura de pantalla que muestra la sección Administración, en la que puede seleccionar Administración de usuarios.](./media/sugarcrm-tutorial/ic795893.png "Administración")

1. Vaya a **Usuarios \> Crear nuevo usuario**.

    ![Create New User (Crear nuevo usuario)](./media/sugarcrm-tutorial/ic795894.png "Crear nuevo usuario")

1. En la pestaña **Perfil de usuario** , realice los siguientes pasos:

    ![Captura de pantalla que muestra la ficha Perfil de usuario, en la que puede especificar los valores descritos.](./media/sugarcrm-tutorial/ic795895.png "Nuevo usuario")

    * Escriba el **nombre de usuario**, **apellidos** y la **dirección de correo electrónico** de un usuario de Azure Active Directory válido en los cuadros de texto relacionados.
  
1. Como **Estado**, seleccione **Activo**.

1. En la pestaña Contraseña, realice los pasos siguientes:

    ![Captura de pantalla que muestra la pestaña Contraseña, en la que puede escribir los valores descritos](./media/sugarcrm-tutorial/ic795896.png "Nuevo usuario").

    a. Escriba la contraseña en el cuadro de texto relacionado.

    b. Haga clic en **Save**(Guardar).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SugarCRM ofrecida por SugarCRM para aprovisionar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Sugar CRM, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de Sugar CRM y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Sugar CRM en Mis aplicaciones, se le redirigirá a la URL de inicio de sesión de esa aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Sugar CRM, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).