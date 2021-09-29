---
title: 'Tutorial: integración de Azure Active Directory con UltiPro | Microsoft Docs'
description: Obtenga información acerca de cómo configurar un inicio de sesión único entre Azure Active Directory y UltiPro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 6cc5570344033951aacfd1558e7c23091bc74678
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808190"
---
# <a name="tutorial-azure-active-directory-integration-with-ultipro"></a>Tutorial: integración de Azure Active Directory con UltiPro

En este tutorial, obtendrá información sobre cómo integrar UltiPro con Azure Active Directory (Azure AD). Al integrar UltiPro con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a UltiPro.
* Permitir que los usuarios inicien sesión automáticamente en UltiPro con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).


## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en UltiPro.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* UltiPro admite el inicio de sesión único iniciado por **SP**.

## <a name="adding-ultipro-from-the-gallery"></a>Incorporación de UltiPro desde la galería

Para configurar la integración de UltiPro en Azure AD, deberá agregar UltiPro desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **UltiPro** en el cuadro de búsqueda.
1. Seleccione **UltiPro** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-ultipro"></a>Configuración y prueba del inicio de sesión único de Azure AD para UltiPro

Configure y pruebe el inicio de sesión único de Azure AD con UltiPro mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de UltiPro.

Para configurar y probar el inicio de sesión único de Azure AD con UltiPro, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en UltiPro](#configure-ultipro-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de UltiPro](#create-ultipro-test-user)** : para tener un homólogo de B.Simon en UltiPro vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **UltiPro**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con uno de los siguientes patrones:

    | URL de inicio de sesión |
    |-------------|
    | `https://<companyname>.ultipro.com/` |
    | `https://<companyname>.ultiproworkplace.com?cpi=AZUREADISSSUERURL` |
    | `https://<companyname>.ultipro.ca` |
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con uno de los siguientes patrones:

    | Identificador |
    |-------------|
    | `https://<companyname>.ultipro.com/adfs/services/trust` |
    | `https://<companyname>.ultiproworkplace.com/adfs/services/trust` |
    | `https://<companyname>.ultipro.ca/adfs/services/trust` |
    
    c. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con uno de los siguientes patrones:

    | URL de respuesta |
    |-------------|
    | `https://<companyname>.ultipro.com/<instancename>` |
    | `https://<companyname>.ultiproworkplace.com/<instancename>` |
    | `https://<companyname>.ultipro.ca/<instancename>` |

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte al cliente de UltiPro](https://www.ultimatesoftware.com/ContactUs) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up UltiPro** (Configurar UltiPro), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a UltiPro mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **UltiPro**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.


## <a name="configure-ultipro-sso"></a>Configuración del inicio de sesión único de UltiPro

Para configurar el inicio de sesión único en **UltiPro**, es preciso enviar el **certificado (Base64)** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de UltiPro](https://www.ultimatesoftware.com/ContactUs). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-ultipro-test-user"></a>Creación de un usuario de prueba de UltiPro

En esta sección, creará un usuario llamado Britta Simon en UltiPro. Trabaje con el [equipo de soporte técnico de Ultipro](https://www.ultimatesoftware.com/ContactUs) para agregar usuarios a la plataforma de UltiPro. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de UltiPro, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de UltiPro y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de UltiPro en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de UltiPro. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado UltiPro, puede aplicar controles de sesión, que protegen contra la filtración y la infiltración de la información confidencial de la organización en tiempo real. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).