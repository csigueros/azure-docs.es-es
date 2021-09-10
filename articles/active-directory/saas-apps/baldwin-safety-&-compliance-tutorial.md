---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Baldwin Safety and Compliance | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Baldwin Safety and Compliance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: jeedes
ms.openlocfilehash: 9fda314b5cd25f4e806d1ed552fbef24ae54ffa6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033122"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-baldwin-safety-and-compliance"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Baldwin Safety and Compliance

En este tutorial aprenderá a integrar Baldwin Safety and Compliance con Azure Active Directory (Azure AD). Al integrar Baldwin Safety and Compliance con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Baldwin Safety and Compliance.
* Permitir que los usuarios inicien sesión automáticamente en Baldwin Safety and Compliance con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Baldwin Safety and Compliance.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Baldwin Safety and Compliance admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-baldwin-safety-and-compliance-from-the-gallery"></a>Adición de Baldwin Safety and Compliance desde la galería

Para configurar la integración de Baldwin Safety and Compliance en Azure AD, es preciso agregar Baldwin Safety and Compliance desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Baldwin Safety and Compliance** en el cuadro de búsqueda.
1. Seleccione **Baldwin Safety and Compliance** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-baldwin-safety-and-compliance"></a>Configuración y prueba del inicio de sesión único de Azure AD para Baldwin Safety and Compliance

Configure y pruebe el inicio de sesión único de Azure AD con Baldwin Safety and Compliance mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Baldwin Safety and Compliance.

Para configurar y probar el inicio de sesión único de Azure AD con Baldwin Safety and Compliance, realice los pasos siguientes:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Baldwin Safety and Compliance](#configure-baldwin-safety-and-compliance-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Baldwin Safety and Compliance](#create-baldwin-safety-and-compliance-test-user)** : para tener un homólogo de B. Simon en Baldwin Safety and Compliance que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Baldwin Safety and Compliance**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada en el modo iniciado por **IDP** y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

1. En la sección **Certificado de firma de SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Edición del certificado de firma de SAML](common/edit-certificate.png)

1. En la sección **Certificado de firma de SAML**, copie el valor de **Huella digital** y guárdelo en el equipo.

    ![Copia del valor de la huella digital](common/copy-thumbprint.png)

1. En la sección **Configurar Baldwin Safety and Compliance**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, para permitir que B. Simon use el inicio de sesión único de Azure, le concederá acceso a Baldwin Safety and Compliance.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Baldwin Safety and Compliance**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-baldwin-safety-and-compliance-sso"></a>Configuración del inicio de sesión único de Baldwin Safety and Compliance

Para configurar el inicio de sesión único en **Baldwin Safety and Compliance**, es preciso enviar el **valor de la huella digital** y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de Baldwin Safety and Compliance](mailto:support@baldwinaviation.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-baldwin-safety-and-compliance-test-user"></a>Creación de un usuario de prueba de Baldwin Safety and Compliance

En esta sección, creará un usuario llamado Britta Simon en Baldwin Safety and Compliance. Trabaje con el [equipo de soporte técnico de Baldwin Safety and Compliance](mailto:support@baldwinaviation.com) para agregar los usuarios a la plataforma Baldwin Safety and Compliance. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* En Azure Portal, haga clic en Probar esta aplicación. Al hacerlo, debería iniciar sesión automáticamente en la instancia de Baldwin Safety and Compliance para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Baldwin Safety and Compliance en Aplicaciones, debería iniciar sesión automáticamente en la instancia de Baldwin Safety and Compliance para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Baldwin Safety and Compliance, puede aplicar el control de sesión que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).