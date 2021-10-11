---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Cloud Management Portal para Microsoft Azure'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el Portal de administración en la nube de Microsoft Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: 732c06c044564081a5554510dd65de2b584128cf
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400655"
---
# <a name="tutorial-azure-ad-sso-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Cloud Management Portal para Microsoft Azure

En este tutorial, aprenderá a integrar Cloud Management Portal para Microsoft Azure con Azure Active Directory (Azure AD). Al integrar Cloud Management Portal para Microsoft Azure con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Cloud Management Portal para Microsoft Azure.
* Permitir que los usuarios inicien sesión automáticamente en Cloud Management Portal para Microsoft Azure con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) de Cloud Management Portal para Microsoft Azure.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Cloud Management Portal para Microsoft Azure admite inicio de sesión único iniciado por **SP**.

## <a name="add-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Incorporación de Cloud Management Portal para Microsoft Azure desde la galería

Para configurar la integración del Portal de administración en la nube de Microsoft Azure en Azure AD, debe agregar el Portal de administración en la nube de Microsoft Azure desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Cloud Management Portal para Microsoft Azure** en el cuadro de búsqueda.
1. Seleccione **Cloud Management Portal para Microsoft Azure** en panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cloud-management-portal-for-microsoft-azure"></a>Configuración y prueba del inicio de sesión único de Azure AD para Cloud Management Portal para Microsoft Azure

Configure y pruebe el inicio de sesión único de Azure AD con Cloud Management Portal para Microsoft Azure utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario vincular un usuario de Azure AD y el usuario correspondiente de Cloud Management Portal para Microsoft Azure.

Para configurar y probar el inicio de sesión único de Azure AD con Cloud Management Portal para Microsoft Azure, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Cloud Management Portal para Microsoft Azure](#configure-cloud-management-portal-for-microsoft-azure-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Cloud Management Portal para Microsoft Azure](#create-cloud-management-portal-for-microsoft-azure-test-user)** : para tener un homólogo de B.Simon en Cloud Management Portal para Microsoft Azure que esté vinculado a la representación del usuario de Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Cloud Management Portal para Microsoft Azure**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:
  
    a. En el cuadro **Identificador**, escriba una dirección URL con uno de los siguientes patrones:

    | **Identificador** |
    |------|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con uno de los siguientes patrones:

    | **URL de respuesta** |
    |----|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |
    
    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    | **URL de inicio de sesión** |
    |------|
    | `https://portal.newsignature.com/<instancename>` |
    | `https://portal.igcm.com/<instancename>` |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y las direcciones URL de inicio de sesión y de respuesta reales. Póngase en contacto con el [equipo de atención al cliente de Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección de **Configuración del Portal de administración de nube para Microsoft Azure**, copie las direcciones URL que necesite.

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

En esta sección, habilitará a B.Simon para que use el inicio de sesión único de Azure, para lo que se le concederá acceso a Cloud Management Portal para Microsoft Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Portal de administración en la nube de Microsoft Azure**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cloud-management-portal-for-microsoft-azure-sso"></a>Configuración del inicio de sesión único de Cloud Management Portal para Microsoft Azure

Para configurar el inicio de sesión único en el lado del **Portal de administración en la nube para Microsoft Azure**, tiene que enviar el **certificado (Base64)** descargado, y las direcciones URL copiadas correctamente desde Azure Portal al [equipo de soporte técnico del Portal de administración en la nube para Microsoft Azure](mailto:jczernuszka@newsignature.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Creación de un usuario de prueba para el Portal de administración en la nube para Microsoft Azure

En esta sección va a crear un usuario llamado Britta Simon en el Portal de administración en la nube para Microsoft Azure. Trabaje con el [equipo de soporte técnico del Portal de administración en la nube para Microsoft Azure](mailto:jczernuszka@newsignature.com) para agregar los usuarios a la cuenta de Portal de administración en la nube para Microsoft Azure. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Cloud Management Portal para Microsoft Azure, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Cloud Management Portal para Microsoft Azure e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Cloud Management Portal para Microsoft Azure en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de Cloud Management Portal para Microsoft Azure. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Cloud Management Portal para Microsoft Azure, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).