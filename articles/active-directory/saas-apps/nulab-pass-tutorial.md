---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Nulab Pass (Backlog,Cacoo,Typetalk) | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Nulab Pass (Backlog,Cacoo,Typetalk).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2021
ms.author: jeedes
ms.openlocfilehash: fcdf4e44e8d8ce8fa585b6c6294071629399edb6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731886"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nulab-pass-backlogcacootypetalk"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Nulab Pass (Backlog,Cacoo,Typetalk)

En este tutorial, aprenderá a integrar Nulab Pass (Backlog,Cacoo,Typetalk) con Azure Active Directory (Azure AD). Si integra Nulab Pass (Backlog,Cacoo,Typetalk) con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Nulab Pass (Backlog,Cacoo,Typetalk).
* Permitir que los usuarios inicien sesión automáticamente en Nulab Pass (Backlog,Cacoo,Typetalk) con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita utilizar el inicio de sesión único (SSO) en Nulab Pass (Backlog,Cacoo,Typetalk).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Nulab Pass (Backlog,Cacoo,Typetalk) admite el inicio de sesión único con SP e **IDP.**

## <a name="add-nulab-pass-backlogcacootypetalk-from-the-gallery"></a>Adición de Nulab Pass (Backlog,Cacoo,Typetalk) desde la galería

Para configurar la integración de Nulab Pass (Backlog,Cacoo,Typetalk) en Azure AD, debe agregar Nulab Pass (Backlog,Cacoo,Typetalk) a la lista de aplicaciones SaaS administradas desde la galería.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Nulab Pass (Backlog,Cacoo,Typetalk)** en el cuadro de búsqueda.
1. Seleccione **Nulab Pass (Backlog,Cacoo,Typetalk)** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-nulab-pass-backlogcacootypetalk"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Nulab Pass (Backlog,Cacoo,Typetalk)

Configure y pruebe el inicio de sesión único de Azure AD con Nulab Pass (Backlog,Cacoo,Typetalk) utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Nulab Pass (Backlog,Cacoo,Typetalk).

Para configurar y probar el inicio de sesión único de Azure AD con Nulab Pass (Backlog,Cacoo,Typetalk), siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Nulab Pass](#configure-nulab-pass-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Nulab Pass](#create-nulab-pass-test-user)** : para tener un homólogo de B.Simon en Nulab Pass (Backlog,Cacoo,Typetalk) que esté vinculado a la representación del usuario de Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Nulab Pass (Backlog,Cacoo,Typetalk)** , busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://apps.nulab.com/signin/spaces/<Space Key>/saml`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://apps.nulab.com/signin/spaces/<Space Key>/saml/callback`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://apps.nulab.com/signin/spaces/<INSTANCE_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Nulab Pass (Backlog,Cacoo,Typetalk)](mailto:support@apps.nulab.com) para conseguir estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Nulab Pass (Backlog,Cacoo,Typetalk) espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados en la configuración de los atributos del token SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. El valor predeterminado de **Identificador de usuario único** es **user.userprincipalname**, pero Nulab Pass (Backlog,Cacoo,Typetalk) espera que este valor se corresponda con la dirección de correo electrónico del usuario. Para ello, puede usar el atributo **user.mail** de la lista o usar el valor de atributo correspondiente en función de la configuración de su organización.

    ![imagen](common/default-attributes.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Nulab Pass (Backlog,Cacoo,Typetalk)** , copie las direcciones URL que correspondan.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a Nulab Pass (Backlog,Cacoo,Typetalk).

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Nulab Pass (Backlog,Cacoo,Typetalk)** .
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-nulab-pass-sso"></a>Configuración del inicio de sesión único de Nulab Pass

Para configurar el inicio de sesión único en **Nulab Pass (Backlog,Cacoo,Typetalk)** , es necesario enviar el **certificado (Base64)** descargado y las direcciones URL correspondiente copiadas de Azure Portal al [equipo de soporte técnico de Nulab Pass (Backlog,Cacoo,Typetalk)](mailto:support@apps.nulab.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-nulab-pass-test-user"></a>Creación de un usuario de prueba en Nulab Pass

En esta sección, va a crear una usuaria llamado Britta Simon en Nulab Pass (Backlog,Cacoo,Typetalk). Colabore con el [equipo de soporte técnico de Nulab Pass (Backlog,Cacoo,Typetalk)](mailto:support@apps.nulab.com) para agregar los usuarios a la plataforma de Nulab Pass (Backlog,Cacoo,Typetalk). Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Al hacerlo, accederá automáticamente a la dirección URL de inicio de sesión de Nulab Pass (Backlog,Cacoo,Typetalk), donde podrá comenzar el flujo de inicio de sesión.  

* Acceda directamente a la dirección URL de inicio de sesión de Nulab Pass (Backlog,Cacoo,Typetalk) y comience el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* En Azure Portal, haga clic en **Probar esta aplicación**. Al hacerlo, debería iniciar sesión automáticamente en la instancia de Nulab Pass (Backlog,Cacoo,Typetalk) en la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Cuando en Aplicaciones haga clic en el icono de Nulab Pass (Backlog,Cacoo,Typetalk), si seleccionó el modo SP en la configuración, debería acceder automáticamente a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Por el contrario, si seleccionó el modo IDP, debería iniciar sesión automáticamente en la instancia de Nulab Pass (Backlog,Cacoo,Typetalk) en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Nulab Pass (Backlog,Cacoo,Typetalk), podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).