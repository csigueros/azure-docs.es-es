---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Splan Visitor | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: de00b869aef2d1429740d75829da9a5c3e79de15
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112465954"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Splan Visitor

En este tutorial aprenderá a integrar Splan Visitor con Azure Active Directory (Azure AD). Al integrar Splan Visitor con Azure AD, puede:

* Use Azure AD para controlar quién tiene acceso a Splan Visitor.
* Permitir que los usuarios inicien sesión automáticamente en Splan Visitor con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Splan Visitor.

> [!NOTE]
> Esta integración también está disponible para usarse desde el entorno de la nube del gobierno de EE. UU de Azure AD. Puede encontrar esta aplicación en la galería de aplicaciones de la nube del gobierno de EE. UU. de Azure AD y configurarla de la misma manera que en la nube pública.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Splash Visitor admite el inicio de sesión único iniciado por IDP.

## <a name="add-splan-visitor-from-the-gallery"></a>Incorporación de Splan Visitor desde la galería

Para configurar la integración de Splan Visitor en Azure AD, agregue la aplicación desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Splan Visitor** en el cuadro de búsqueda.
1. Seleccione **Splash Visitor** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Configuración y prueba del inicio de sesión único de Azure AD para Splan Visitor

Configure y pruebe el inicio de sesión único de Azure AD con Splan Visitor mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Splan Visitor.

Para configurar y probar el inicio de sesión único de Azure AD con Splan Visitor, complete los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** para probar el inicio de sesión único de Azure AD con el usuario de prueba B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Splan Visitor](#configure-splan-visitor-sso)** , para configurar los valores de inicio de sesión único en Splan Visitor.
    1. **[Creación de un usuario de prueba de Splan Visitor](#create-a-splan-visitor-test-user)** , para tener un homólogo de B.Simon en Splan Visitor que esté vinculado a la representación del usuario en Azure AD.
1. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal:

1. En Azure Portal, en la página de integración de aplicaciones de **Splan Visitor**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, seleccione el icono con forma de **lápiz** para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Captura de pantalla que resalta el icono de edición (con forma de lápiz) de Configuración básica de SAML.](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada y las direcciones URL necesarias se han rellenado previamente con Azure. Seleccione el botón de **guardar** para guardar la configuración.

1. En la página **Configuración del inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación**. Seleccione **Descargar** para descargar el certificado y guárdelo en el equipo.

    ![Captura de pantalla que resalta el vínculo de descarga del XML de metadatos de federación.](common/metadataxml.png)

1. En la sección **Configurar Splan Visitor**, copie las direcciones URL adecuadas según sus necesidades.

    ![Captura de pantalla que resalta la sección de direcciones URL de configuración.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba **B.Simon**.  
   1. En el campo **Nombre de usuario**, escriba su nombre de usuario en formato _username@companydomain.extension_ . Por ejemplo, escriba: **B.Simon@contoso.com** .
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a Splan Visitor mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Splan Visitor** para que se abra la información general de la aplicación.
1. Busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario** y, después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** en la lista **Usuarios** y haga clic en **Seleccionar** en la parte inferior de la pantalla.
1. Si al usuario se le asigna un rol, selecciónelo en el menú desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, deje seleccionado el rol **Acceso predeterminado**.
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

## <a name="configure-splan-visitor-sso"></a>Configuración del inicio de sesión único de Splan Visitor

Para configurar el inicio de sesión único con Splan Visitor, envíe el **XML de metadatos de federación** que descargó y las URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de Splan Visitor](mailto:support@splan.com). Este se asegurará de que la conexión de inicio de sesión único de SAML esté establecida correctamente en ambos lados.

### <a name="create-a-splan-visitor-test-user"></a>Creación de un usuario de prueba de Splan Visitor

Cree un usuario de prueba llamado **Britta Simon** en Splan Visitor. Trabaje con el [equipo de soporte técnico de Splan Visitor](mailto:support@splan.com) para agregar al usuario a la plataforma de Splan Visitor. Debe crear y activar el usuario antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

Pruebe la configuración de inicio de sesión único de Azure AD con alguna de las siguientes opciones:

* **Portal de Azure**: Seleccione **Probar esta aplicación** para iniciar sesión automáticamente en la instancia de Splan Visitor para la que configuró el inicio de sesión único.
* **Portal Aplicaciones de Microsoft**: Seleccione el icono **Splan Visitor** para iniciar sesión automáticamente en la instancia de Splan Visitor para la que configuró el inicio de sesión único. Para más información acerca del portal Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Después de configurar Splan Visitor, puede [aprender a aplicar controles de sesión en Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app). Los controles de sesión protegen la filtración e infiltración de la información confidencial de la organización en tiempo real. Los controles de sesión proceden del acceso condicional.