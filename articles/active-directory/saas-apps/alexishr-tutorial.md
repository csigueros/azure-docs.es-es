---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con AlexisHR'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AlexisHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 194c58d72ab23c58bd4736a929b91e5f1a749697
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839071"
---
# <a name="tutorial-azure-ad-sso-integration-with-alexishr"></a>Tutorial: Integración del inicio de sesión único de Azure AD con AlexisHR

En este tutorial, aprenderá a integrar AlexisHR con Azure Active Directory (Azure AD). Al integrar AlexisHR con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AlexisHR.
* Permitir que los usuarios inicien sesión automáticamente en AlexisHR con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AlexisHR.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* AlexisHR admite inicio de sesión único iniciado por **IDP**.

## <a name="add-alexishr-from-the-gallery"></a>Adición de AlexisHR desde la galería

Para configurar la integración de AlexisHR en Azure AD, será preciso que añada AlexisHR desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AlexisHR**  en el cuadro de búsqueda.
1. Seleccione **AlexisHR** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-alexishr"></a>Configuración y prueba del inicio de sesión único de Azure AD para AlexisHR

Configure y pruebe el inicio de sesión único de Azure AD con AlexisHR utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de AlexisHR.

Para configurar y probar el inicio de sesión único de Azure AD con AlexisHR, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en AlexisHR](#configure-alexishr-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de AlexisHR](#create-alexishr-test-user)** : para tener un homólogo de B.Simon en AlexisHR que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **AlexisHR**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba un valor con el siguiente patrón: `urn:auth0:alexishr:<YOUR_CONNECTION_NAME>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://auth.alexishr.com/login/callback?connection=<YOUR_CONNECTION_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte al cliente de AlexisHR](mailto:support@alexishr.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación AlexisHR espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación AlexisHR espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se han rellenado previamente, pero los puede revisar según sus requisitos.
    
    | Nombre | Atributo de origen |
    | ------------ | --------- |
    | email | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar AlexisHR**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a AlexisHR mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AlexisHR**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-alexishr-sso"></a>Configuración del inicio de sesión único de AlexisHR

1. Inicie sesión en su sitio de la empresa AlexisHR como administrador.

1. Vaya a **Configuración** > **Inicio de sesión único de SAML** y haga clic en **Nuevo proveedor de identidades**.

1. En la sección **Agregar proveedor de identidades**, realice los pasos siguientes:

    ![Captura de pantalla que muestra la Configuración de cuenta.](./media/alexishr-tutorial/account.png "Configuración")

    1. En el cuadro de texto **Dirección URL de inicio de sesión único del proveedor de identidades**, pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    1. En el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades**, pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.

    1. Abra en el Bloc de notas el archivo del **certificado (Base 64)** que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **Certificado público x509**.

    1. Haga clic en **Crear proveedor de identidades**.

1. Después de crear el proveedor de identidades, recibirá la siguiente información.

    ![Captura de pantalla que muestra la configuración de inicio de sesión único.](./media/alexishr-tutorial/certificate.png "Configuración de SSO")

    1. Copie el valor de **URI de audiencia** y péguelo en el cuadro de texto **Identificador** de la sección **Configuración básica de SAML** en Azure Portal.

    1. Copie el valor de **URL del Servicio de consumidor de aserciones** y péguelo en el cuadro de texto **URL de respuesta** en la sección **Configuración básica de SAML** de Azure Portal.

### <a name="create-alexishr-test-user"></a>Creación de usuario de prueba de AlexisHR

En esta sección, creará un usuario llamado Britta Simon en AlexisHR. Trabaje con el [equipo de soporte técnico de AlexisHR](mailto:support@alexishr.com) para agregar los usuarios a la plataforma de AlexisHR. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de AlexisHR para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de AlexisHR en Aplicaciones, iniciará sesión automáticamente en la versión de AlexisHR para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado AlexisHR, puede aplicar el control de sesión, que protege tanto la filtración como la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).