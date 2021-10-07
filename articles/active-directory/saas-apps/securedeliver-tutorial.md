---
title: 'Tutorial: integración de Azure Active Directory con SECURE DELIVER | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SECURE DELIVER.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 95c47807b64e5433aef109c8cf803075cc92b0fd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124801683"
---
# <a name="tutorial-azure-active-directory-integration-with-secure-deliver"></a>Tutorial: Integración de Azure Active Directory con SECURE DELIVER

En este tutorial, obtendrá información sobre cómo integrar SECURE DELIVER con Azure Active Directory (Azure AD). Al integrar SECURE DELIVER con Azure AD, podrá hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SECURE DELIVER.
* Permitir que los usuarios inicien sesión automáticamente en SECURE DELIVER con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SECURE DELIVER, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en SECURE DELIVER.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SECURE DELIVER admite el inicio de sesión único iniciado por **SP**.

* SECURE DELIVER admite el [aprovisionamiento automatizado de usuarios](secure-deliver-provisioning-tutorial.md).

## <a name="add-secure-deliver-from-the-gallery"></a>Incorporación de SECURE DELIVER desde la galería

Para configurar la integración de SECURE DELIVER en Azure AD, es preciso agregar SECURE DELIVER desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SECURE DELIVER** en el cuadro de búsqueda.
1. Seleccione **SECURE DELIVER** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-secure-deliver"></a>Configuración y prueba del inicio de sesión único de Azure AD para SECURE DELIVER

Configure y pruebe el inicio de sesión único de Azure AD con SECURE DELIVER mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de SECURE DELIVER.

Para configurar y probar el inicio de sesión único de Azure AD con SECURE DELIVER, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de SECURE DELIVER](#configure-secure-deliver-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SECURE DELIVER](#create-secure-deliver-test-user)** : para tener un homólogo de B.Simon en SECURE DELIVER que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SECURE DELIVER**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.i-securedeliver.jp/sd/<tenantname>/jsf/login/sso`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<companyname>.i-securedeliver.jp/sd/<tenantname>/postResponse`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de SECURE DELIVER](mailto:iw-sd-support@fujifilm.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar SECURE DELIVER**, copie las direcciones URL apropiadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a SECURE DELIVER mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SECURE DELIVER**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-secure-deliver-sso"></a>Configuración del inicio de sesión único de SECURE DELIVER

Para configurar el inicio de sesión único en **SECURE DELIVER**, es preciso enviar el **certificado (Base64)** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de SECURE DELIVER](mailto:iw-sd-support@fujifilm.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-secure-deliver-test-user"></a>Creación de un usuario de prueba de SECURE DELIVER

En esta sección creará un usuario llamado Britta Simon en SECURE DELIVER. Colabore con el [equipo de soporte técnico de SECURE DELIVER](mailto:iw-sd-support@fujifilm.com) para agregar los usuarios a la plataforma SECURE DELIVER. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

SECURE DELIVER también admite el aprovisionamiento automático de usuarios. [Aquí](./secure-deliver-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de SECURE DELIVER, donde podrá iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de SECURE DELIVER y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de SECURE DELIVER en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado SECURE DELIVER, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).