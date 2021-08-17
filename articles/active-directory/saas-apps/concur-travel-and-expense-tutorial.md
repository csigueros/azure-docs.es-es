---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SAP Concur Travel and Expense | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Concur Travel and Expense.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 0e1676836cce03cef2d47f466a497937b1a8ae4e
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288183"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-concur-travel-and-expense"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SAP Concur Travel and Expense

En este tutorial va a aprender a integrar SAP Concur Travel and Expense con Azure Active Directory (Azure AD). Al integrar SAP Concur Travel and Expense con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a SAP Concur Travel and Expense.
* Permitir que los usuarios inicien sesión automáticamente en SAP Concur Travel and Expense con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción de SAP Concur Travel and Expense.
* Un rol de "administrador de empresa" en su cuenta de usuario de Concur. Puede probar si tiene el acceso correcto; para ello, vaya a [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin), la herramienta de autoservicio de inicio de sesión único de Concur. Si no tiene acceso, póngase en contacto con el servicio de soporte técnico de Concur o con el administrador de proyectos de implementación. 

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD.

* SAP Concur Travel and Expense admite el SSO iniciado por **IDP** y **SP**
* SAP Concur Travel and Expense permite probar el inicio de sesión único tanto en entornos de producción como de implementación 

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo para cada una de las tres regiones: Estados Unidos, EMEA y China. Por lo tanto, solo se puede configurar una instancia para cada región de un inquilino. 

## <a name="adding-sap-concur-travel-and-expense-from-the-gallery"></a>Incorporación de SAP Concur Travel and Expense desde la galería

Para configurar la integración de SAP Concur Travel and Expense en Azure AD, debe agregar SAP Concur Travel and Expense desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAP Concur Travel and Expense** en el cuadro de búsqueda.
1. Seleccione **SAP Concur Travel and Expense** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-concur-travel-and-expense"></a>Configuración y prueba del inicio de sesión único de Azure AD para SAP Concur Travel and Expense

Configure y pruebe el SSO de Azure AD con SAP Concur Travel and Expense mediante un usuario de prueba de nombre **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de SAP Concur Travel and Expense.

Para configurar y probar el SSO de Azure AD con SAP Concur Travel and Expense, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de SAP Concur Travel and Expense](#configure-sap-concur-travel-and-expense-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SAP Concur Travel and Expense](#create-sap-concur-travel-and-expense-test-user)** , para tener un homólogo de B.Simon en SAP Concur Travel and Expense que esté vinculado a la representación de Azure AD del usuario.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SAP Concur Travel and Expense**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada en el modo iniciado por **IDP** y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

    > [!NOTE]
    > El identificador (Id. de entidad) y la dirección URL de respuesta (	URL del Servicio de consumidor de aserciones) son específicos de la región. Seleccione lo que corresponda en función del centro de datos de la entidad Concur. Si no conoce el centro de datos de la entidad Concur, póngase en contacto con el soporte técnico de Concur. 

5. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Atributo de usuario** y modificar la configuración. El identificador de usuario único debe coincidir con el valor de login_id del usuario de Concur. Normalmente, debe cambiar **user.userPrincipalName** a **user.mail**.

    ![Editar atributo de usuario](common/edit-attribute.png)

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el archivo de metadatos y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

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

En esta sección va a permitir que B.Simon use el inicio de sesión único de Azure al concederle acceso a SAP Concur Travel and Expense.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAP Concur Travel and Expense**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sap-concur-travel-and-expense-sso"></a>Configuración del inicio de sesión único de SAP Concur Travel and Expense

1. Para automatizar la configuración en SAP Concur Travel and Expense, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar SAP Concur Travel and Expense** para ir directamente a la aplicación SAP Concur Travel and Expense. Desde ahí, escriba las credenciales de administrador para iniciar sesión en SAP Concur Travel and Expense. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 7.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar SAP Concur Travel and Expense manualmente en otra ventana del explorador web, debe cargar el archivo **XML de metadatos de federación** descargado en la [herramienta de autoservicio de SSO de Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin) e iniciar sesión en el sitio de empresa de SAP Concur Travel and Expense como administrador.

1. Haga clic en **Agregar**.
1. Escriba un nombre personalizado para el IdP, por ejemplo "Azure AD (EE. UU.)". 
1. Haga clic en **Cargar archivo XML** y adjunte el **XML de metadatos de federación** que descargó anteriormente.
1. Haga clic en **Agregar metadatos** para guardar el cambio.

    ![Captura de pantalla de la herramienta de autoservicio de SSO de Concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-sap-concur-travel-and-expense-test-user"></a>Creación de un usuario de prueba de SAP Concur Travel and Expense

En esta sección va a crear un usuario de nombre B.Simon en SAP Concur Travel and Expense. Trabaje con el equipo de soporte técnico de Concur para agregar los usuarios a la plataforma SAP Concur Travel and Expense. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

> [!NOTE]
> El identificador de inicio de sesión de Concur de B.Simon debe coincidir con el identificador único de B. Simon en Azure AD. Por ejemplo, si el identificador único de Azure AD de B.Simon es `B.Simon@contoso.com`. El identificador de inicio de sesión de Concur de B.Simon tiene que ser `B.Simon@contoso.com` también. 

## <a name="configure-concur-mobile-sso"></a>Configuración del inicio de sesión único para dispositivos móviles en Concur
Para habilitar el inicio de sesión único para dispositivos móviles en Concur, debe proporcionar al equipo de soporte técnico de Concur la **dirección URL de acceso del usuario**. Siga los pasos que se indican a continuación para obtener la **dirección URL de acceso del usuario** desde Azure AD:
1. Vaya a **Aplicaciones empresariales**.
1. Haga clic en **SAP Concur Travel and Expense**
1. Haga clic en **Propiedades**.
1. Copie la **dirección URL de acceso del usuario** y proporcione esta dirección URL al soporte técnico de Concur.

> [!NOTE]
> La opción de autoservicio para configurar el inicio de sesión único no está disponible, por lo que debe trabajar con el equipo de soporte técnico de Concur si desea habilitar el inicio de sesión único para dispositivos móviles. 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirige a la dirección URL de inicio de sesión de SAP Concur Travel and Expense, donde puede iniciar el flujo de inicio de sesión.

* Vaya directamente a la dirección URL de inicio de sesión de SAP Concur Travel and Expense e inicie el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de SAP Concur Travel and Expense para la que ha configurado el SSO.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de SAP Concur Travel and Expense en Aplicaciones, si se ha configurado en modo SP, se le redirige a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión; si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de SAP Concur Travel and Expense para la que ha configurado el SSO. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado SAP Concur Travel and Expense, puede aplicar el control de sesión, que protege a la organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).