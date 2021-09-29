---
title: 'Tutorial: Integración de Azure Active Directory con Secret Server (On-Premises) | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Secret Server (On-Premises).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 929ca0786ab25cbbcd219510970c3ce66709b6f5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780231"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Tutorial: Integración de Secret Server (On-Premises) con Azure Active Directory

En este tutorial, aprenderá a integrar Secret Server (On-Premises) con Azure Active Directory (Azure AD). Al integrar Secret Server (On-Premises) con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Secret Server (On-Premises)
* Permitir que los usuarios inicien sesión automáticamente en Secret Server (On-Premises) con sus cuentas de Azure AD
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Secret Server (On-Premises).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Secret Server (On-Premises) admite el inicio de sesión único iniciado por **SP y IDP**.

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Adición de Secret Server (On-Premises) desde la galería

Para configurar la integración de Secret Server (On-Premises) en Azure AD, es necesario que agregue Secret Server (On-Premises) desde la galería hasta la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Secret Server (On-Premises)** en el cuadro de búsqueda.
1. Seleccione **Secret Server (On-Premises)** en el panel de resultados y, luego, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Configuración y prueba del inicio de sesión único de Azure AD para Secret Server (On-Premises)

Configure y pruebe el inicio de sesión único de Azure AD con Secret Server (On-Premises) mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Secret Server (On-Premises).

Para configurar y probar el inicio de sesión único de Azure AD con Secret Server (On-Premises), siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Secret Server (On-Premises)](#configure-secret-server-on-premises-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Secret Server (On-Premises)](#create-secret-server-on-premises-test-user)** , para tener un homólogo de B.Simon en Secret Server (On-Premises) que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Secret Server (On-Premises)** , busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://secretserveronpremises.azure`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > El identificador de entidad mostrado anteriormente es solo un ejemplo; es libre de elegir cualquier valor único que identifique la instancia de Secret Server en Azure AD. Este identificador de entidad se deberá enviar al [equipo de atención al cliente de Secret Server (On-Premises)](https://thycotic.force.com/support/s/) para que se lo configuren. Para obtener más información, lea [este artículo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Secret Server (On-Premises)](https://thycotic.force.com/support/s/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Captura de pantalla que muestra la sección "Certificado de firma de S A M L" con la acción "Descargar" para "Certificado (Base64)" seleccionada.](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Seleccione **Opción de firma** como **Firmar respuesta y aserción SAML**.

    ![Opciones de firma](./media/secretserver-on-premises-tutorial/signing-option.png)

1. En la sección **Configurar Secret Server (On-Premises)** , copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, permitirá que B.Simon acceda a Secret Server (On-Premises) para usar el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Secret Server (On-Premises)** .
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-secret-server-on-premises-sso"></a>Configuración del inicio de sesión único de Secret Server (On-Premises)

Para configurar el inicio de sesión único en **Secret Server (On-Premises)** , debe enviar el **certificado (Base64)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-secret-server-on-premises-test-user"></a>Creación de un usuario de prueba de Secret Server (On-Premises)

En esta sección, creará un usuario llamado Britta Simon en Secret Server (On-Premises). Trabaje con el [equipo de soporte técnico de Secret Server (On-Premises)](https://thycotic.force.com/support/s/) para agregar a los usuarios a la plataforma de Secret Server (On-Premises). Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Secret Server (On-Premises), donde podrá iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Secret Server (On-Premises) e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Secret Server (On-Premises) para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Secret Server (On-Premises) en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Secret Server (On-Premises) para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado la instancia de Secret Server (On-Premises), puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).