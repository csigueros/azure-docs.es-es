---
title: 'Tutorial: Integración de Azure Active Directory con Rightscale | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Rightscale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 7dae32f31c30474886bcffff296e513058829b86
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753225"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Tutorial: Integración de Azure Active Directory con Rightscale

En este tutorial, aprenderá a integrar Rightscale con Azure Active Directory (Azure AD).
Integrar Rightscale con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Rightscale.
* Puede permitir que los usuarios inicien sesión automáticamente en Rightscale (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Prerequisites

Para configurar la integración de Azure AD con Rightscale, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/)
* Una suscripción habilitada para el inicio de sesión único en Rightscale

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Rightscale admite el inicio de sesión único iniciado tanto por **SP como por IDP**

## <a name="adding-rightscale-from-the-gallery"></a>Adición de Rightscale desde la galería

Para configurar la integración de Rightscale en Azure AD, deberá agregar Rightscale desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Rightscale desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)** , haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione la opción **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Rightscale**, seleccione **Rightscale** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

     ![Rightscale en la lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Rightscale con un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Rightscale.

Para configurar y probar el inicio de sesión único de Azure AD con Rightscale, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)** : para que los usuarios puedan usar esta característica.
2. **[Configuración del inicio de sesión único de Rightscale](#configure-rightscale-single-sign-on)** : para configurar los valores de Inicio de sesión único en la aplicación.
3. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Creación de un usuario de prueba de Rightscale](#create-rightscale-test-user)** : para tener un homólogo de Britta Simon en Rightscale que esté vinculado a su representación en Azure AD.
6. **[Prueba del inicio de sesión único](#test-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal.

Para configurar el inicio de sesión único de Azure AD con Rightscale, realice los pasos siguientes:

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de la aplicación **Rightscale**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único](common/select-sso.png)

2. En el cuadro de diálogo **Seleccionar un método de inicio de sesión único**, seleccione el modo **SAML/WS-Fed** para habilitar el inicio de sesión único.

    ![Modo de selección de inicio de sesión único](common/select-saml-option.png)

3. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono **Editar** para abrir el cuadro de diálogo **Configuración básica de SAML**.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

    ![Captura de pantalla que muestra la página de configuración básica de SAML.](common/preintegrated.png)

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Captura de pantalla que muestra Establecer direcciones U R L adicionales donde puede escribir una U R L de inicio de sesión.](common/metadata-upload-additional-signon.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://login.rightscale.com/`

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Configurar Rightscale**, copie las direcciones URL adecuadas según sus requisitos.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

    a. URL de inicio de sesión

    b. Identificador de Azure AD

    c. URL de cierre de sesión

### <a name="configure-rightscale-single-sign-on"></a>Configuración del inicio de sesión único de Rightscale

1. Para configurar SSO para la aplicación, debe iniciar sesión en su inquilino de RightScale como administrador.

2. En el menú de la parte superior, haga clic en la pestaña **Configuración** y seleccione **Inicio de sesión único**.

    ![Captura de pantalla que muestra la opción de inicio de sesión único seleccionada en la configuración.](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Haga clic en el botón "**nuevo**" para agregar **sus proveedores de identidades SAML**.

    ![Captura de pantalla que muestra los nuevos botones seleccionados para agregar un proveedor de identidades SAML.](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. En el cuadro de texto **Nombre para mostrar**, escriba el nombre de la compañía.

    ![Captura de pantalla que muestra dónde escribir un nombre para mostrar.](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Seleccione **Permitir SSO iniciado por RightScale con una sugerencia de detección** y escriba el **nombre de dominio** en el cuadro de texto siguiente.

    ![Captura de pantalla que muestra dónde se puede especificar un método de inicio de sesión.](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Pegue el valor del **Dirección URL de inicio de sesión** que copió de Azure Portal en **SAML SSO Endpoint** (Punto de conexión SSO de SAML) en Rightscale.

    ![Captura de pantalla que muestra dónde puede escribir un punto de conexión de inicio de sesión único de SAML.](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Pegue el valor del **identificador de Azure AD** que copió de Azure Portal en **SAML EntityID** (Identificador de entidad de SAML) en Rightscale.

    ![Captura de pantalla que muestra dónde puede escribir un identificador de entidad de SAML.](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Haga clic en el botón **Explorador** para cargar el certificado que descargó de Azure Portal.

    ![Captura de pantalla que muestra dónde puede especificar el certificado de firma de SAML.](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Haga clic en **Save**(Guardar).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](common/users.png)

2. Seleccione **Nuevo usuario** en la parte superior de la pantalla.

    ![Botón Nuevo usuario](common/new-user.png)

3. En las propiedades Usuario, siga estos pasos.

    ![Cuadro de diálogo Usuario](common/user-properties.png)

    a. En el campo **Nombre**, escriba **BrittaSimon**.
  
    b. En el campo **Nombre de usuario**, escriba `brittasimon@yourcompanydomain.extension`.  
    Por ejemplo: BrittaSimon@contoso.com

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro Contraseña.

    d. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Rightscale para que use el inicio de sesión único de Azure.

1. En Azure Portal, seleccione **Aplicaciones empresariales**, **Todas las aplicaciones**, **Rightscale**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Rightscale**.

    ![Vínculo a Rightscale en la lista de aplicaciones](common/all-applications.png)

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

4. Haga clic en el botón **Agregar usuario** y, después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación](common/add-assign-user.png)

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista Usuarios y, luego, haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.

6. Si espera cualquier valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol** seleccione en la lista el rol adecuado para el usuario y, después, haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="create-rightscale-test-user"></a>Creación de un usuario de prueba de Rightscale

En esta sección, creará un usuario denominado Britta Simon en Rightscale. Trabaje con el [equipo de soporte técnico de Rightscale](mailto:support@rightscale.com) para agregar los usuarios a la plataforma de Rightscale. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Rightscale en el panel de acceso y debería iniciar sesión automáticamente en la versión de Rightscale para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](./tutorial-list.md)

- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [¿Qué es el acceso condicional en Azure Active Directory?](../conditional-access/overview.md)