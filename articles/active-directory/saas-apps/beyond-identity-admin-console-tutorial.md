---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Beyond Identity Admin Console | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Beyond Identity Admin Console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2021
ms.author: jeedes
ms.openlocfilehash: 057e0bfdd37beebb86d9292acaa6e40175b12526
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839223"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyond-identity-admin-console"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Beyond Identity Admin Console

En este tutorial, aprenderá a integrar Beyond Identity Admin Console con Azure Active Directory (Azure AD). Al integrar Beyond Identity Admin Console con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Beyond Identity Admin Console.
* Permitir que los usuarios inicien sesión automáticamente en Beyond Identity Admin Console con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Beyond Identity Admin Console.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Beyond Identity Admin Console admite el inicio de sesión único iniciado por **SP**.

## <a name="add-beyond-identity-admin-console-from-the-gallery"></a>Incorporación de Beyond Identity Admin Console desde la galería

Para configurar la integración de Beyond Identity Admin Console en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Beyond Identity Admin Console** en el cuadro de búsqueda.
1. Seleccione **Beyond Identity Admin Console** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-beyond-identity-admin-console"></a>Configuración y prueba del inicio de sesión único de Azure AD para Beyond Identity Admin Console

Configure y pruebe el inicio de sesión único de Azure AD con Beyond Identity Admin Console mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una vinculación entre un usuario de Azure AD y el usuario correspondiente de Beyond Identity Admin Console.

Para configurar y probar el inicio de sesión único de Azure AD con Beyond Identity Admin Console, realice los pasos siguientes:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Beyond Identity Admin Console](#configure-beyond-identity-admin-console-sso)** , para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Beyond Identity Admin Console](#create-beyond-identity-admin-console-test-user)** , para tener un usuario correspondiente a B.Simon en Beyond Identity Admin Console que esté vinculado a la representación del usuario de Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Beyond Identity Admin Console**, busque la sección **Manage** (Administrar) y seleccione el **inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://admin.byndid.com/auth/saml/<azure-tenant-id>/sso/metadata.xml`

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://admin.byndid.com/auth/?org_id=<bi-tenant-id>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de Beyond Identity Admin Console](mailto:support@beyondidentity.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Beyond Identity Admin Console espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Beyond Identity Admin Console espera que se pasen algunos atributos más en la respuesta de SAML que se muestran a continuación. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre | Espacio de nombres  |  Atributo de origen|
    | ---------------| --------------- | --------- |
    | immutableId | externalId | user.immutableId |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Beyond Identity Admin Console** (Configurar Beyond Identity Admin Console), copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Beyond Identity Admin Console mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Beyond Identity Admin Console**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-beyond-identity-admin-console-sso"></a>Configuración del inicio de sesión único de Beyond Identity Admin Console

Para configurar el inicio de sesión único en **Beyond Identity Admin Console**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de Beyond Identity Admin Console](mailto:support@beyondidentity.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-beyond-identity-admin-console-test-user"></a>Creación de un usuario de prueba de Beyond Identity Admin Console

En esta sección, creará un usuario llamado Britta Simon en Beyond Identity Admin Console. Trabaje con el [equipo de soporte técnico de Beyond Identity Admin Console](mailto:support@beyondidentity.com) para agregar los usuarios a la plataforma correspondiente. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Beyond Identity Admin Console, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Beyond Identity Admin Console y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Si hace clic en el icono de Beyond Identity Admin Console en Aplicaciones, se le redirigirá a la dirección URL del inicio de sesión correspondiente. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Beyond Identity Admin Console, puede aplicar el control de sesión, que protege en tiempo real su organización frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).