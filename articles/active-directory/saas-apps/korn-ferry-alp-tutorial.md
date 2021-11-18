---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Korn Ferry ALP'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Korn Ferry ALP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2021
ms.author: jeedes
ms.openlocfilehash: ce4ba44c1c3096bad6b936f293b47ac5ff9a30c7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285203"
---
# <a name="tutorial-azure-ad-sso-integration-with-korn-ferry-alp"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Korn Ferry ALP

En este tutorial, aprenderá a integrar Korn Ferry ALP con Azure Active Directory (Azure AD). Al integrar Korn Ferry ALP con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Korn Ferry ALP.
* Permitir que los usuarios inicien sesión automáticamente en Korn Ferry ALP con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Korn Ferry ALP.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Korn Ferry ALP admite el inicio de sesión único iniciado por **SP**.

## <a name="add-korn-ferry-alp-from-the-gallery"></a>Incorporación de Korn Ferry ALP desde la galería

Para configurar la integración de Korn Ferry ALP en Azure AD, será preciso que agregue Korn Ferry ALP desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Korn Ferry ALP** en el cuadro de búsqueda.
1. Seleccione **Korn Ferry ALP** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-korn-ferry-alp"></a>Configuración y prueba del inicio de sesión único en Korn Ferry ALP

Configure y pruebe el inicio de sesión único de Azure AD con Korn Ferry ALP mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Korn Ferry ALP.

Para configurar y probar el inicio de sesión único de Azure AD con Korn Ferry ALP, realice los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Korn Ferry ALP](#configure-korn-ferry-alp-sso)** , para configurar los valores de inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba de Korn Ferry ALP](#create-korn-ferry-alp-test-user)** : para tener un homólogo de B.Simon en Korn Ferry ALP que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Korn Ferry ALP**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:
   
    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con uno de los patrones siguientes:
    
   | **Identificador** |
   |-------|
   | `https://intappextin01/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://qaassessment.kfnaqa.com/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://assessments.kornferry.com/portalweb/sso/client/audience?guid=<customerguid>` |
    
    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

   | **Dirección URL de inicio de sesión** |
   |------|
   | `https://intappextin01/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://qaassessment.kfnaqa.com/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://assessments.kornferry.com/portalweb/sso/client/audience?guid=<customerguid>` |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte de cliente de Korn Ferry ALP](mailto:noreply@kornferry.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

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

En esta sección, va a permitir que B.Simon acceda a Korn Ferry ALP mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Korn Ferry ALP**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-korn-ferry-alp-sso"></a>Configuración del inicio de sesión único de Korn Ferry ALP

Para configurar el inicio de sesión único en **Korn Ferry ALP**, hay que enviar la **dirección URL de metadatos de federación de la aplicación** al [equipo de soporte técnico de Korn Ferry ALP](mailto:noreply@kornferry.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-korn-ferry-alp-test-user"></a>Creación de un usuario de prueba de Korn Ferry ALP

En esta sección, creará un usuario llamado Britta Simon en Korn Ferry ALP. Trabaje con el [equipo de soporte técnico de Korn Ferry ALP](mailto:noreply@kornferry.com) para agregar los usuarios a la plataforma de Korn Ferry ALP. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Korn Ferry ALP, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Korn Ferry ALP e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Korn Ferry ALP en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de dicha aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Korn Ferry ALP, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
