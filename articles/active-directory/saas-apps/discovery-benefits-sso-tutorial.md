---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Discovery Benefits SSO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Discovery Benefits SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 25ab7f79fdc278ed78bc166b33bd6646ab762464
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734368"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Discovery Benefits SSO

En este tutorial aprenderá a integrar Discovery Benefits SSO con Azure Active Directory (Azure AD). Al integrar Discovery Benefits SSO con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Discovery Benefits SSO.
* Permitir que los usuarios inicien sesión automáticamente en Discovery Benefits SSO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Discovery Benefits SSO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Discovery Benefits SSO admite el inicio de sesión único iniciado por **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-discovery-benefits-sso-from-the-gallery"></a>Adición de Discovery Benefits SSO desde la galería

Para configurar la integración de Discovery Benefits SSO en Azure AD, tiene que agregar Discovery Benefits SSO desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Discovery Benefits SSO** en el cuadro de búsqueda.
1. Seleccione **Discovery Benefits SSO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-discovery-benefits-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD para Discovery Benefits SSO

Configure y pruebe el inicio de sesión único de Azure AD con Discovery Benefits SSO mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Discovery Benefits SSO.

Para configurar y probar el inicio de sesión único de Azure AD con Discovery Benefits SSO, complete los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Discovery Benefits SSO](#configure-discovery-benefits-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Discovery Benefits SSO](#create-discovery-benefits-sso-test-user)** , para tener un homólogo de B.Simon en Discovery Benefits SSO que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Discovery Benefits SSO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada en el modo iniciado por **IDP** y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

1. La aplicación Discovery Benefits SSO espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos de usuario.

    ![imagen](common/edit-attribute.png)

    a. Haga clic en el icono **Editar** para abrir el cuadro de diálogo **Identificador de usuario único (id. de nombre)** .

    ![Captura de pantalla que muestra la sección "User Attributes & Claims" (Atributos y reclamaciones del usuario) con los puntos suspensivos de "Required claim" (Notificación requerida) en el lado derecho seleccionados.](./media/discovery-benefits-sso-tutorial/user-attribute.png)

    ![Configuración del inicio de sesión único de Discovery Benefits SSO](./media/discovery-benefits-sso-tutorial/add-attribute.png)

    b. Haga clic en el icono **Editar** para abrir el cuadro de diálogo **Administración de la transformación**.

    c. En el cuadro de texto **Transformación**, escriba el valor de **ToUppercase()** que se muestra para esa fila.

    d. En el cuadro de texto **Parámetro 1**, escriba el parámetro como `<Name Identifier value>`.

    e. Haga clic en **Agregar**.

    > [!NOTE]
    > Discovery Benefits SSO requiere que se pase un valor de cadena fijo en el campo **Identificador de usuario único (id. de nombre)** para que esta integración funcione. Actualmente, Azure AD no es compatible con esta característica por lo que, como solución alternativa, puede usar transformaciones **ToUpper** o **ToLower** de NameID para establecer un valor de cadena fijo tal como se mostró antes en la captura de pantalla.

    f. Hemos rellenado automáticamente las notificaciones adicionales que se requieren para la configuración del inicio de sesión único (`SSOInstance` y `SSOID`). Use el icono de **lápiz** para asignar los valores correspondientes a la organización.

    ![Captura de pantalla que muestra "User Attributes & Claims" (Atributos y reclamaciones del usuario) con los valores "SSO Instance" (Instancia de SSO) y "SSO ID" (Id. de SSO) resaltados.](./media/discovery-benefits-sso-tutorial/new-attribute.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Discovery Benefits SSO**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Discovery Benefits SSO mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Discovery Benefits SSO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-discovery-benefits-sso"></a>Configuración de Discovery Benefits SSO

Para configurar el inicio de sesión único en **Discovery Benefits SSO**, es preciso enviar el **certificado (Base64)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-discovery-benefits-sso-test-user"></a>Creación de un usuario de prueba de Discovery Benefits SSO

En esta sección, creará un usuario llamado Britta Simon en Discovery Benefits SSO. Trabaje con el [equipo de soporte técnico de Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com) para agregar los usuarios a la plataforma de Discovery Benefits SSO. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de Discovery Benefits SSO para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Discovery Benefits SSO en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de Discovery Benefits SSO para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Discovery Benefits SSO, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).