---
title: 'Tutorial: Integración de Azure Active Directory con TigerConnect Secure Messenger | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 90505ef7ed5219c4eda6e70ee549b1d99e0e7a5b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281043"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Tutorial: Integración de Azure Active Directory con TigerConnect Secure Messenger

En este tutorial, aprenderá a integrar TigerConnect Secure Messenger con Azure Active Directory (Azure AD). Al integrar TigerConnect Secure Messenger con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a TigerConnect Secure Messenger.
* Habilitar a los usuarios para que inicien sesión automáticamente en TigerConnect Secure Messenger con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TigerConnect Secure Messenger, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Una suscripción habilitada para el inicio de sesión único en TigerConnect Secure Messenger.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba e integrar TigerConnect Secure Messenger con Azure AD.

* TigerConnect Secure Messenger admite el inicio de sesión único iniciado por el **proveedor de servicios**.

## <a name="add-tigerconnect-secure-messenger-from-the-gallery"></a>Incorporación de TigerConnect Secure Messenger desde la galería

Para configurar la integración de TigerConnect Secure Messenger en Azure AD, será preciso que lo agregue desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **TigerConnect Secure Messenger**  en el cuadro de búsqueda.
1. Seleccione **TigerConnect Secure Messenger** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-tigerconnect-secure-messenger"></a>Configuración y prueba del inicio de sesión único de Azure AD para TigerConnect Secure Messenger

En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con TigerConnect Secure Messenger con un usuario de prueba llamado **Britta Simon**. Para que el inicio de sesión único funcione, es preciso establecer un vínculo entre un usuario de Azure AD y el usuario correspondiente de TigerConnect Secure Messenger.

Para configurar y probar el inicio de sesión único de Azure AD con TigerConnect Secure Messenger, es preciso realizar los pasos siguientes:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en TigerConnect Secure Messenger](#configure-tigerconnect-secure-messenger-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de TigerConnect Secure Messenger](#create-a-tigerconnect-secure-messenger-test-user)** , para tener un usuario llamado Britta Simon en TigerConnect Secure Messenger que esté vinculado con el usuario de Azure AD llamado Britta Simon.
1. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal.

Para configurar el inicio de sesión único de Azure AD con TigerConnect Secure Messenger, realice los pasos siguientes:

1. En Azure Portal, en la página de integración de aplicaciones de **TigerConnect Secure Messenger**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    1. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL:

       `https://home.tigertext.com`

    1. En el cuadro de texto **Identificador (Id. de entidad)**, escriba una dirección URL con el siguiente patrón:

       `https://saml-lb.tigertext.me/v1/organization/<INSTANCE_ID>`

    > [!NOTE]
    > El valor **Identificador (Id. de entidad)** no es real. Actualícelo con el identificador real. Para obtener este valor póngase en contacto con el [equipo de soporte técnico de TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). También puede hacer referencia a los patrones que se muestran en el panel **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas y guárdelo en el equipo.

    ![Opción de descarga del XML de metadatos de federación](common/metadataxml.png)

1. En la sección **Configurar TigerConnect Secure Messenger**, copie las direcciones URL según sus necesidades.

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

En esta sección, podrá habilitar a B.Simon para que use el inicio de sesión único de Azure concediéndole acceso a TigerConnect Secure Messenger.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **TigerConnect Secure Messenger**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Configuración del inicio de sesión único de TigerConnect Secure Messenger

Para configurar el inicio de sesión único en TigerConnect Secure Messenger, tiene que enviar el XML de metadatos de federación descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). El equipo de TigerConnect Secure Messenger se asegurará de que la conexión de inicio de sesión único de SAML esté establecida correctamente en ambos lados.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Creación de un usuario de prueba de TigerConnect Secure Messenger

En esta sección, creará un usuario llamado Britta Simon en TigerConnect Secure Messenger. Colabore con el [equipo de soporte técnico de TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) para agregar a Britta Simon como usuario en TigerConnect Secure Messenger. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de TigerConnect Secure Messenger, desde donde podrá poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de TigerConnect Secure Messenger y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono TigerConnect Secure Messenger en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de TigerConnect Secure Messenger. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado TigerConnect Secure Messenger, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
