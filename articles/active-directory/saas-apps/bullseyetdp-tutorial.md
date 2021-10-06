---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con BullseyeTDP'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y BullseyeTDP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: f07bb144bf32262d135af6ef23496ab544bdf0db
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091509"
---
# <a name="tutorial-azure-ad-sso-integration-with-bullseyetdp"></a>Tutorial: Integración del inicio de sesión único de Azure AD con BullseyeTDP

En este tutorial aprenderá a integrar BullseyeTDP con Azure Active Directory (Azure AD). Al integrar BullseyeTDP con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a BullseyeTDP.
* Permitir que los usuarios inicien sesión automáticamente en BullseyeTDP con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en BullseyeTDP.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* BullseyeTDP admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-bullseyetdp-from-the-gallery"></a>Adición de BullseyeTDP desde la galería

Para configurar la integración de BullseyeTDP en Azure AD, deberá agregar BullseyeTDP desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **BullseyeTDP** en el cuadro de búsqueda.
1. Seleccione **BullseyeTDP** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-bullseyetdp"></a>Configuración y prueba del inicio de sesión único de Azure AD en BullseyeTDP

Configure y pruebe el inicio de sesión único de Azure AD con BullseyeTDP con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de BullseyeTDP.

Para configurar y probar el inicio de sesión único de Azure AD con BullseyeTDP, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
   1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
   1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en BullseyeTDP](#configure-bullseyetdp-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
   1. **[Creación de un usuario de prueba en BullseyeTDP](#create-bullseyetdp-test-user)** : para tener un homólogo de B.Simon en BullseyeTDP vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **BullseyeTDP**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

1. La aplicación BullseyeTDP espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación BullseyeTDP espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre | Atributo de origen|
    | ---- | --------------- |
    | AccessToken | < AccessTokenValue > |
    | ApplicationKey | < ApplicationKeyValue >|
    | EmployeeID | user.employeeid |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar BullseyeTDP**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a BullseyeTDP mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **BullseyeTDP**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-bullseyetdp-sso"></a>Configuración del inicio de sesión único de BullseyeTDP

Para configurar el inicio de sesión único en **BullseyeTDP**, es preciso enviar el **certificado (Base 64)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de BullseyeTDP](mailto:hello@bullseyetdp.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-bullseyetdp-test-user"></a>Creación de un usuario de prueba de BullseyeTDP

En esta sección, creará un usuario llamado Britta Simon en BullseyeTDP. Trabaje con el [equipo de soporte técnico de BullseyeTDP](mailto:hello@bullseyetdp.com) para agregar los usuarios a la plataforma de BullseyeTDP. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de BullseyeTDP para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de BullseyeTDP en Aplicaciones, iniciará sesión automáticamente en la instancia de BullseyeTDP para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que ha configurado BullseyeTDP, puede aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).