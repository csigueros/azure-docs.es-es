---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con IBM Kenexa Survey Enterprise'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory e IBM Kenexa Survey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: 994dd9ce458bf57145c0ac73e975af43cae82292
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005831"
---
# <a name="tutorial-azure-ad-sso-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integración del inicio de sesión único de Azure AD con IBM Kenexa Survey Enterprise

En este tutorial, aprenderá a integrar IBM Kenexa Survey Enterprise con Azure Active Directory (Azure AD). Al integrar IBM Kenexa Survey Enterprise con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a IBM Kenexa Survey Enterprise.
* Permitir que los usuarios inicien sesión automáticamente en IBM Kenexa Survey Enterprise con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción a IBM Kenexa Survey Enterprise habilitada para el inicio de sesión único.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* IBM Kenexa Survey Enterprise admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Agregar IBM Kenexa Survey Enterprise desde la galería

Para configurar la integración de IBM Kenexa Survey Enterprise en Azure AD, deberá agregar IBM Kenexa Survey Enterprise desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **IBM Kenexa Survey Enterprise** en el cuadro de búsqueda.
1. Seleccione **IBM Kenexa Survey Enterprise** en el panel de resultados y, luego, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ibm-kenexa-survey-enterprise"></a>Configuración y prueba del inicio de sesión único de Azure AD para IBM Kenexa Survey Enterprise

Configure y pruebe el inicio de sesión único (SSO) de Azure AD con IBM Kenexa Survey Enterprise mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de IBM Kenexa Survey Enterprise.

Para configurar el inicio de sesión único de Azure AD con IBM Kenexa Survey Enterprise, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de IBM Kenexa Survey Enterprise](#configure-ibm-kenexa-survey-enterprise-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de IBM Kenexa Survey Enterprise](#create-ibm-kenexa-survey-enterprise-test-user)** : para tener un homólogo de B.Simon en IBM Kenexa Survey Enterprise que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **IBM Kenexa Survey Enterprise**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la página **Configurar inicio de sesión único con SAML** realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://surveys.kenexa.com/<companycode>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico al cliente de IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. La aplicación IBM Kenexa Survey Enterprise espera recibir las aserciones del Lenguaje de marcado de aserción de seguridad (SAML) en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos de token de SAML. El valor de la notificación de identificador de usuario en la respuesta debe coincidir con el Id. de inicio de sesión único configurado en el sistema Kenexa. Para asignar el identificador de usuario adecuado en su organización como Protocolo de datagramas de Internet (IDP) de inicio de sesión único, trabaje con el [equipo de soporte técnico de IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    De forma predeterminada, Azure AD establece el identificador de usuario como el valor de nombre principal de usuario (UPN). Puede cambiar este valor en la pestaña **Atributos de usuario**, como se muestra en la captura de pantalla siguiente. La integración solo funciona después de haber completado correctamente la asignación.

    ![imagen](common/edit-attribute.png)

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Set up IBM Kenexa Survey Enterprise** (Configuración de IBM Kenexa Survey Enterprise), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, concederá a B.Simon acceso a IBM Kenexa Survey Enterprise para que use el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **IBM Kenexa Survey Enterprise**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-ibm-kenexa-survey-enterprise-sso"></a>Configuración del inicio de sesión único de IBM Kenexa Survey Enterprise

Para configurar el inicio de sesión único en **IBM Kenexa Survey Enterprise**, es preciso enviar el valor descargado de **Certificado (Base64)** y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Creación de un usuario de prueba de IBM Kenexa Survey Enterprise

En esta sección, creará un usuario llamado Britta Simon en IBM Kenexa Survey Enterprise.

Para crear usuarios en el sistema IBM Kenexa Survey Enterprise y asignarles el identificador de SSO, puede trabajar con el [equipo de soporte técnico de IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Este valor de identificador de SSO también debe asignarse al valor user identifier (identificador de usuario) de Azure AD. Puede cambiar esta configuración predeterminada en la pestaña **Atributo**.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de IBM Kenexa Survey Enterprise para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de IBM Kenexa Survey Enterprise en Aplicaciones, debería iniciar sesión automáticamente en la instancia de IBM Kenexa Survey Enterprise para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Cuando haya configurado IBM Kenexa Survey, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).