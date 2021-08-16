---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Bizagi for Digital Process Automation | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Bizagi for Digital Process Automation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 390f5c60ec016ab87ae62cfbc8373fc1b485adc8
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112199527"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Bizagi for Digital Process Automation

En este tutorial, aprenderá a integrar Bizagi for Digital Process Automation Services/Server con Azure Active Directory (Azure AD). Al integrar Bizagi for Digital Process Automation con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a un proyecto de Bizagi for Digital Process Automation Services/Server.
* Permitir que los usuarios inicien sesión automáticamente en un proyecto de Bizagi for Digital Process Automation Services/Server con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Un proyecto de Bizagi con Automation Services/Server. 
* Disponer de sus propios certificados para las firmas de aserción de SAML. Estos certificados se deben generar en formato p12 o pfx.
* Disponer de un archivo de metadatos en formato XML generado a partir del proyecto de Bizagi. 

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un proyecto de Bizagi con Automation Services/Server.

* Bizagi for Digital Process Automation admite el SSO iniciado por **SP**.

## <a name="add-bizagi-for-digital-process-automation-from-the-gallery"></a>Incorporación de Bizagi for Digital Process Automation desde la galería

Para configurar la integración de Bizagi for Digital Process Automation en Azure AD, es preciso agregar Bizagi for Digital Process Automation desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Bizagi for Digital Process Automation** en el cuadro de búsqueda.
1. Seleccione **Bizagi for Digital Process Automation** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-bizagi-for-digital-process-automation"></a>Configuración y prueba del inicio de sesión único de Azure AD para Bizagi for Digital Process Automation

Configure y pruebe el inicio de sesión único de Azure AD con Bizagi for Digital Process Automation mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente del proyecto de Bizagi.

Para configurar y probar el SSO de Azure AD con Bizagi for Digital Process Automation, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Bizagi for Digital Process Automation](#configure-bizagi-for-digital-process-automation-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Bizagi for Digital Process Automation](#create-bizagi-for-digital-process-automation-test-user)** : para tener un homólogo de B.Simon en Bizagi for Digital Process Automation vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Bizagi for Digital Process Automation**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<COMPANY_NAME>.bizagi.com/<PROJECT_NAME>`

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<COMPANY_NAME>.bizagi.com/<PROJECT_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)
    
    Esta dirección URL de metadatos debe estar registrada en las opciones de autenticación del proyecto de Bizagi.
    
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Atributos y notificaciones de usuario** para editar el identificador de usuario único.
    
    Establezca el identificador de usuario único como user.mail.

### <a name="create-an-azure-ad-test"></a>Creación de un usuario de prueba de Azure AD 

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a Bizagi for Digital Process Automation mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Bizagi for Digital Process Automation**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Configuración del inicio de sesión único de Bizagi for Digital Process Automation

Para configurar el inicio de sesión único en **Bizagi for Digital Process Automation**, debe enviar la **dirección URL de metadatos de federación de aplicación** al [equipo de soporte técnico de Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Creación de un usuario de prueba de Bizagi for Digital Process Automation

En esta sección, creará un usuario llamado Britta Simon en Bizagi for Digital Process Automation. Colabore con el [equipo de soporte técnico de Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) para agregar los usuarios a la plataforma de Bizagi for Digital Process Automation. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirige a la dirección URL de inicio de sesión de Bizagi for Digital Process Automation, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Bizagi for Digital Process Automation e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Bizagi for Digital Process Automation en Aplicaciones, se le redirige a la dirección URL de inicio de sesión de Bizagi for Digital Process Automation. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Bizagi for Digital Process Automation, puede aplicar el control de sesión, que protege frente a la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
