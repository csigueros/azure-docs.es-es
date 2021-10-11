---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Civic Platform'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Civic Platform.
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
ms.openlocfilehash: 1a966c99cc167180cd47406c8909bfbd48a11422
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402518"
---
# <a name="tutorial-azure-ad-sso-integration-with-civic-platform"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Civic Platform

En este tutorial, aprenderá a integrar Civic Platform con Azure Active Directory (Azure AD). Al integrar Civic Platform con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Civic Platform.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Civic Platform con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Un suscripción habilitada para el inicio de sesión único (SSO) en Civic Platform.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Civic Platform admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-civic-platform-from-the-gallery"></a>Adición de Civic Platform desde la galería

Para configurar la integración de Civic Platform en Azure AD, deberá agregar Civic Platform desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Civic Platform** en el cuadro de búsqueda.
1. Seleccione **Civic Platform** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-civic-platform"></a>Configuración y prueba del inicio de sesión único de Azure AD para Civic Platform

Configure y pruebe el inicio de sesión único de Azure AD con Civic Platform mediante un usuario de prueba llamado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Civic Platform.

Para configurar y probar el inicio de sesión único de Azure AD con Civic Platform, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Civic Platform](#configure-civic-platform-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Civic Platform](#create-civic-platform-test-user)** , para tener un homólogo de B.Simon en Civic Platform que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Civic Platform**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba el valor: `civicplatform.accela.com`

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.accela.com`

    > [!NOTE]
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de cliente de Civic Platform](mailto:skale@accela.com) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Captura de pantalla que muestra la página Certificado de firma de SAML, donde puede copiar la dirección U R L de metadatos de federación de aplicaciones.](common/copy-metadataurl.png)

1. Vaya a **Azure Active Directory** > **Registros de aplicaciones** in Azure AD y seleccione la aplicación.

1. Copie el **identificador de directorio (inquilino)** y almacénelo en el Bloc de notas.

    ![Copie directorio (identificador del inquilino) y almacénelo en el código de la aplicación](media/civic-platform-tutorial/directory.png)

1. Copie el **identificador de la aplicación** y almacénelo en el Bloc de notas.

   ![Copie el identificador de la aplicación (cliente)](media/civic-platform-tutorial/application.png)

1. Vaya a **Azure Active Directory** > **Registros de aplicaciones** in Azure AD y seleccione la aplicación. Seleccione **Certificados y secretos**.

1. Seleccione **Secretos de cliente -> Nuevo secreto de cliente**.

1. Proporcione una descripción y duración del secreto. Cuando haya terminado, seleccione **Agregar**.

   > [!NOTE]
   > Después de guardar el secreto de cliente, se muestra el valor del mismo. Copie este valor porque no podrá recuperar la clave más adelante.

   ![Copie el valor del secreto porque no podrá recuperarlo más adelante](media/civic-platform-tutorial/secret-key.png)

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

En esta sección, va a permitir que B.Simon acceda a Civic Platform mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Civic Platform**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-civic-platform-sso"></a>Configuración del inicio de sesión único de Civic Platform

1. Abra una ventana del explorador web e inicie sesión como administrador en el sitio Atlassian Cloud de su compañía.

1. Haga clic en **Standard Choices** (Opciones estándar).

    ![Captura de pantalla que muestra el sitio de Atlassian Cloud con Standard Choices (Opciones estándar) seleccionada en Administrator Tools (Herramientas de administrador).](media/civic-platform-tutorial/standard-choices.png)

1. Cree un **ssoconfig** de la opción estándar.

1. Busque **ssoconfig** y envíelo.

    ![Captura de pantalla que muestra Standard Choices - Search (Opciones estándar - Buscar) con el nombre de la c o n f i g u r a c i ó n d e i n i c i o d e s e s i ó n ú n i c o especificada.](media/civic-platform-tutorial/item.png)

1. Expanda SSOCONFIG y haga clic en el punto rojo.

    ![Captura de pantalla que muestra Standard Choices - Browse (Opciones estándar - Examinar) con la c o n f i g u r a c i ó n d e i n i c i o d e s e s i ó n ú n i c o disponible.](media/civic-platform-tutorial/details.png)

1. Proporcione información de la configuración relacionada con el inicio de sesión único en el paso siguiente:

    ![Captura de pantalla que muestra Standard Choices Item - Edit (Elemento Opciones estándar - Editar) para la c o n f i g u r a c i ó n d e i n i c i o d e s e s i ó n ú n i c o.](media/civic-platform-tutorial/values.png)

    1. En el campo **applicationid**, escriba el valor del **identificador de la aplicación** que ha copiado de Azure Portal.

    1. En el campo **clientSecret**, escriba el valor del **secreto** que ha copiado de Azure Portal.

    1. En el campo **directoryId**, escriba el valor del **identificador del directorio (inquilino)** que ha copiado de Azure Portal.

    1. Escriba el valor de idpName. Por ejemplo: `Azure`.

### <a name="create-civic-platform-test-user"></a>Creación de un usuario de prueba de Civic Platform

En esta sección, va a crear un usuario llamado B.Simon en Civic Platform. Trabaje con el equipo de soporte técnico de Civic Platform para agregar los usuarios en el [equipo de soporte técnico de cliente de Civic Platform](mailto:skale@accela.com). Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Civic Platform, donde podrá poner en marcha el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de Civic Platform y ponga en marcha el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Civic Platform en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de dicha aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Civic Platform, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).