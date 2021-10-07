---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y KnowBe4 Security Awareness Training.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 5b4ffdc24a4a1e41d0db0c5e816b67909b125dc4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676701"
---
# <a name="tutorial-azure-ad-sso-integration-with-knowbe4-security-awareness-training"></a>Tutorial: Integración del inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training

En este tutorial, aprenderá a integrar KnowBe4 Security Awareness Training con Azure Active Directory (Azure AD). Al integrar KnowBe4 Security Awareness Training con Azure AD, puede hacer lo siguiente:

* Controle en Azure AD quién tendrá acceso a KnowBe4 Security Awareness Training.
* Permita que los usuarios inicien sesión automáticamente en KnowBe4 Security Awareness Training con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción de KnowBe4 Security Awareness Training con el inicio de sesión único (SSO) habilitado

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* KnowBe4 Security Awareness Training admite el inicio de sesión único iniciado por **SP**

* KnowBe4 Security Awareness Training admite el aprovisionamiento de usuarios **Just-In-Time**

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-knowbe4-from-the-gallery"></a>Incorporación de KnowBe4 desde la galería

Para configurar la integración de KnowBe4 en Azure AD, es preciso agregar KnowBe4 desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **KnowBe4** en el cuadro de búsqueda.
1. Seleccione **KnowBe4** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-knowbe4-security-awareness-training"></a>Configuración y prueba del inicio de sesión único de Azure AD para KnowBe4 Security Awareness Training

En esta sección podrá configurar y probar el inicio de sesión único de Azure AD con KnowBe4 con un usuario de prueba llamado **B.Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de KnowBe4.

Para configurar el inicio de sesión único de Azure AD con KnowBe4, realice los pasos siguientes:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con el usuario B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** : para permitir que B.Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en KnowBe4 Security Awareness Training](#configure-knowbe4-security-awareness-training-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de KnowBe4 Security Awareness Training](#create-knowbe4-security-awareness-training-test-user)**: para tener un homólogo de Britta Simon en KnowBe4 Security Awareness Training que esté vinculado a la representación de ella en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **KnowBe4**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com) para obtener dicho valor. También puede ver el patrón que se muestra en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (sin procesar)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificateraw.png)

6. En la sección **Set up KnowBe4 Security Awareness Training** (Configurar KnowBe4 Security Awareness Training), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a KnowBe4 mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **KnowBe4**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-knowbe4-security-awareness-training-sso"></a>Configuración del inicio de sesión único en KnowBe4 Security Awareness Training

Para configurar el inicio de sesión único en **KnowBe4 Security Awareness Training**, es preciso enviar el **certificado (sin procesar)** descargado y las direcciones URL copiadas pertinentes de Azure Portal al [equipo de soporte técnico de KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Creación de un usuario de prueba en KnowBe4 Security Awareness Training

En esta sección se crea un usuario llamado a B.Simon en KnowBe4. KnowBe4 admite el aprovisionamiento de usuarios Just-In-Time, habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si aún no existe aún un usuario en KnowBe4, se crea uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de KnowBe4 Security Awareness Training, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de KnowBe4 Security Awareness Training e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono KnowBe4 Security Awareness Training en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de dicha aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado KnowBe4 Security Awareness Training, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).