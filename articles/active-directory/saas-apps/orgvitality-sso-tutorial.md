---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con OrgVitality SSO | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y OrgVitality SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: jeedes
ms.openlocfilehash: 8e192249aeb5bb85d02a616950fe74b0cd239f22
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602861"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-orgvitality-sso"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con OrgVitality SSO

En este tutorial, obtendrá información sobre cómo integrar OrgVitality SSO con Azure Active Directory (Azure AD). Al integrar OrgVitality SSO con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a OrgVitality SSO.
* Permitir que los usuarios puedan iniciar sesión automáticamente en OrgVitality SSO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en OrgVitality SSO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* OrgVitality SSO admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-orgvitality-sso-from-the-gallery"></a>Adición de OrgVitality SSO desde la galería

Para configurar la integración de OrgVitality SSO en Azure AD, debe agregar OrgVitality SSO desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **OrgVitality SSO** en el cuadro de búsqueda.
1. Seleccione **OrgVitality SSO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-orgvitality-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD para OrgVitality SSO

Configure y pruebe el inicio de sesión único de Azure AD con OrgVitality SSO con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de OrgVitality SSO.

Para configurar y probar el inicio de sesión único de Azure AD con OrgVitality SSO, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración de OrgVitality SSO](#configure-orgvitality-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de OrgVitality SSO](#create-orgvitality-sso-test-user)** : para tener un homólogo de B.Simon en OrgVitality SSO que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **OrgVitality SSO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://rpt.orgvitality.com/<COMPANY_NAME>/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://rpt.orgvitality.com/<COMPANY_NAME>Auth/default.aspx`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de OrgVitality](https://orgvitality.com/contact-us/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación OrgVitality SSO espera que las aserciones de SAML estén en un formato específico, así que es necesario agregar asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación OrgVitality SSO espera que **nameidentifier** se asigne a **user.employeeid**, por lo que necesita editar la asignación de atributos; para ello, haga clic en el icono de **Editar** y cambie esa asignación.

    ![imagen](common/default-attributes.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar OrgVitality SSO**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon use el inicio de sesión único de Azure concediéndole acceso a OrgVitality SSO.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **OrgVitality SSO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-orgvitality-sso"></a>Configuración de OrgVitality SSO

Para configurar el inicio de sesión único en **OrgVitality SSO**, es preciso enviar el **Certificado (Base64)** descargado y las direcciones URL correspondientes copiadas en Azure Portal al [equipo de soporte técnico de OrgVitality SSO](https://orgvitality.com/contact-us/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-orgvitality-sso-test-user"></a>Creación de un usuario de prueba de OrgVitality SSO

En esta sección, creará un usuario llamado Britta Simon en OrgVitality SSO. Trabaje con el [equipo de soporte técnico de OrgVitality SSO](https://orgvitality.com/contact-us/) para agregar los usuarios a la plataforma de OrgVitality SSO. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de OrgVitality SSO para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de OrgVitality SSO en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de OrgVitality SSO para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado OrgVitality SSO, puede aplicar el control de sesión, que protege la exfiltración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).