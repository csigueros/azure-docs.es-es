---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con la plataforma de administración de permisos de CloudKnox | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y la plataforma de administración de permisos de CloudKnox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 4cd19d038ada2a4d2fcbab473f56ee04f3be0b3d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817345"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con la plataforma de administración de permisos de CloudKnox

En este tutorial, aprenderá a integrar la plataforma de administración de permisos de CloudKnox con Azure Active Directory (Azure AD). Si integra la plataforma de administración de permisos de CloudKnox con Azure AD, podrá:

* Controlar desde Azure AD quién tiene acceso a la plataforma de administración de permisos de CloudKnox.
* Permitir que los usuarios inicien sesión automáticamente en la plataforma de administración de permiso de CloudKnox con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita el inicio de sesión único (SSO) en la plataforma de administración de permisos de CloudKnox.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* La plataforma de administración de permisos de CloudKnox admite los inicios de sesión únicos con **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>Adición de la plataforma de administración de permisos de CloudKnox desde la galería

Para poder configurar la integración de la plataforma de administración de permisos de CloudKnox en Azure AD, debe agregar dicha plataforma a la lista de aplicaciones SaaS administradas desde la galería.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **CloudKnox Permissions Management Platform** (Plataforma de administración de permisos de CloudKnox) en el cuadro de búsqueda.
1. Seleccione **CloudKnox Permissions Management Platform** (Plataforma de administración de permisos de CloudKnox) en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Configuración y comprobación del inicio de sesión único de Azure AD con la plataforma de administración de permisos de CloudKnox

Configure y pruebe el inicio de sesión único de Azure AD con la plataforma de administración de permisos de CloudKnox utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario vincular un usuario de Azure AD y el usuario correspondiente de la plataforma de administración de permisos de CloudKnox.

Para configurar y probar el inicio de sesión único de Azure AD con la plataforma de administración de permisos de CloudKnox, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en la plataforma de administración de permisos de CloudKnox](#configure-cloudknox-permissions-management-platform-sso)** : para configurar el inicio de sesión único en el lado de la aplicación.
    1. **[Creación de un usuario de prueba en la plataforma de administración de permisos de CloudKnox](#create-cloudknox-permissions-management-platform-test-user)** : para tener un homólogo de B.Simon en la plataforma de administración de permisos de CloudKnox vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **CloudKnox Permissions Management Platform** (Plataforma de administración de permisos de CloudKnox), busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar el inicio de sesión único con SAML**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de la plataforma de administración de permisos de CloudKnox](mailto:support@cloudknox.io) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La plataforma de administración de permisos de CloudKnox espera las aserciones de SAML en un formato concreto, lo que requiere la incorporación de asignaciones de atributos personalizados en la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la plataforma de administración de permisos de CloudKnox espera que, en la respuesta de SAML, se devuelvan algunos atributos más, que se muestran a continuación. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre |  Atributo de origen|
    | --------------- | --------- |
    | First_Name | user.givenname |
    | Grupos | user.groups |
    | Last_Name | user.surname |
    | Email_Address | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up CloudKnox Permissions Management Platform** (Configuración de la plataforma de administración de permisos de CloudKnox), copie las direcciones URL apropiadas conforme a sus necesidades.

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

En esta sección, va a permitir que B.Simon utilice el inicio de sesión único de Azure concediéndole acceso a la plataforma de administración de permisos de CloudKnox.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **CloudKnox Permissions Management Platform** (Plataforma de administración de permisos de CloudKnox).
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>Configuración del inicio de sesión único en la plataforma de administración de permisos de CloudKnox

Para configurar el inicio de sesión único en la **plataforma de administración de permisos de CloudKnox**, tiene que enviar el **XML de metadatos de federación** descargado y las direcciones URL apropiadas que haya copiado de Azure Portal al [equipo de soporte técnico de la plataforma de administración de permisos de CloudKnox](mailto:support@cloudknox.io). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>Creación de un usuario de prueba en la plataforma de administración de permisos de CloudKnox

En esta sección, va a crear un usuario llamado Britta Simon en la plataforma de administración de permisos de CloudKnox. Colabore con el [equipo de soporte técnico de la plataforma de administración de permisos de CloudKnox](mailto:support@cloudknox.io) para agregar usuarios a esta plataforma. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* En Azure Portal, haga clic en "Probar esta aplicación". Al hacerlo, debería iniciar sesión automáticamente en la plataforma de administración de permisos de CloudKnox en la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Si en Aplicaciones hace clic en el icono de la plataforma de administración de permisos de CloudKnox, debería iniciar sesión automáticamente en la plataforma en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la plataforma de administración de permisos de CloudKnox, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).