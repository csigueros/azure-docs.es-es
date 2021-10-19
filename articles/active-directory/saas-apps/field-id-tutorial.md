---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Field iD'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Field iD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2021
ms.author: jeedes
ms.openlocfilehash: ac235f95bfb88b8bab0733b1dbbc6c0f3e09af90
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808823"
---
# <a name="tutorial-azure-ad-sso-integration-with-field-id"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Field iD

En este tutorial aprenderá a integrar Field iD con Azure Active Directory (Azure AD). Al integrar Field iD con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Field iD.
* Permitir que los usuarios inicien sesión automáticamente en Field iD con sus cuentas de Azure AD.
* Administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Field iD.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Field iD admite el inicio de sesión único iniciado por IDP.

## <a name="add-field-id-from-the-gallery"></a>Adición de Field iD desde la galería

Para configurar la integración de Field iD en Azure AD, tiene que agregar Field iD desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el menú de navegación de la izquierda, seleccione **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una aplicación nueva, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Field iD** en el cuadro de búsqueda.
1. Seleccione **Field iD** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-field-id"></a>Configuración y prueba del SSO de Azure AD para Field iD

Configure y pruebe el inicio de sesión único de Azure AD con Field iD mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una vinculación entre un usuario de Azure AD y el usuario relacionado de Field iD.

Para configurar y probar el inicio de sesión único de Azure AD con Field iD, es preciso completar los siguientes pasos:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso), para permitir que los usuarios puedan utilizar esta característica.
   1. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con B. Simon.
   1. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para que B. Simon pueda usar el inicio de sesión único de Azure AD.
1. [Configuración del inicio de sesión único en Field iD](#configure-field-id-sso), para configurar los valores de inicio de sesión único en la aplicación.
   1. [Creación de un usuario de prueba de Field iD](#create-a-field-id-test-user), para tener un usuario equivalente a B.Simon en Field iD que esté vinculado a la representación del usuario en Azure AD.
1. [Comprobación del inicio de sesión único](#test-sso), para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Field iD**, busque la sección **Administrar**. Después, seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Captura de pantalla de la página Configurar el inicio de sesión único con SAML con el icono de lápiz resaltado](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

   a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.fieldid.com/fieldid`.

   b. En el cuadro de texto **Dirección URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`.

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Field iD](mailto:support@ecompliance.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione el icono de copia para copiar la **dirección URL de metadatos de federación de aplicación**. Guárdelo en el equipo.

    ![Captura de pantalla de la sección Certificado de firma de SAML, con el icono de copia resaltado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En **Nombre**, escriba `B.Simon`.  
   1. En **Nombre de usuario**, escriba el username@companydomain.extension (por ejemplo, `B.Simon@contoso.com`).
   1. Seleccione la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a Field iD mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Field iD**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario** y, después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista **Usuarios** y, después, elija **Seleccionar** en la parte inferior de la pantalla.
1. Si espera algún valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione el rol adecuado para el usuario en la lista. A continuación, elija **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

## <a name="configure-field-id-sso"></a>Configuración del inicio de sesión único de Field iD

Para configurar el inicio de sesión único en Field iD, debe enviar la **Dirección URL de metadatos de federación de aplicación** al [equipo de soporte técnico de Field iD](mailto:support@ecompliance.com). Estas garantizan que la conexión de inicio de sesión único de SAML está establecida correctamente en ambos lados.

### <a name="create-a-field-id-test-user"></a>Creación de un usuario de prueba de Field iD

En esta sección, creará un usuario llamado B.Simon en Field iD. Colabore con el [equipo de soporte técnico de Field iD](mailto:support@ecompliance.com) para agregar los usuarios a la plataforma de Field iD. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Field iD para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Field iD en Aplicaciones, debería iniciar sesión automáticamente en la aplicación Field iD para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado Field iD, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración y la infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).