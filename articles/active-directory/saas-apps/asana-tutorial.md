---
title: 'Tutorial: Integración de Azure Active Directory con Asana | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 7ef73c7890b0b1394936f2d20b6cd7adbe826e38
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761322"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integración de Azure Active Directory con Asana

En este tutorial, aprenderá a integrar Asana con Azure Active Directory (Azure AD). Al integrar Asana con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Asana.
* Permitir que los usuarios inicien sesión automáticamente en Asana con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en Asana.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Asana admite el inicio de sesión único iniciado por **SP**

* Asana admite el [aprovisionamiento **automático** de usuarios](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Adición de Asana desde la galería

Para configurar la integración de Asana en Azure AD, es preciso agregar Asana desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Asana** en el cuadro de búsqueda.
1. Seleccione **Asana** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Configuración y prueba del inicio de sesión único de Azure AD para Asana

Configure y pruebe el inicio de sesión único de Azure AD con Asana con un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Asana.

Para configurar y probar el inicio de sesión único de Azure AD con Asana, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Asana](#configure-asana-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Asana](#create-asana-test-user)** : para tener un homólogo de B.Simon en Asana que esté vinculado a la representación de este usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Asana**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Asana](common/sp-identifier.png)

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://app.asana.com/`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba la dirección URL: `https://app.asana.com/`.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Asana** (Configurar Asana), copie las direcciones URL adecuada según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Asana mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Asana**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-asana-sso"></a>Configuración del inicio de sesión único de Asana

1. En una ventana de explorador diferente, inicie sesión en su aplicación de Asana como administrador. Para configurar SSO en Asana, acceda a la configuración del área de trabajo haciendo clic en el nombre del área de trabajo en la esquina superior derecha de la pantalla. A continuación, haga clic en **Configuración de \<your workspace name\>** .

    ![Configuración de SSO de Asana](./media/asana-tutorial/settings.png)

2. En la ventana **Organization settings** (Configuración de la organización), haga clic en **Administration** (Administración). Después, haga clic en **Members must log in via SAML** (Los miembros deben iniciar sesión mediante SAML) para habilitar la configuración de SSO. Lleve a cabo los siguiente pasos:

    ![Definición de la configuración de la organización de inicio de sesión único](./media/asana-tutorial/save.png)  

    a. En el cuadro de texto **Dirección URL de la página de inicio de sesión**, pegue la **Dirección URL de inicio de sesión**.

    b. Haga clic con el botón derecho en el certificado descargado de Azure Portal y después abra el archivo de certificado con el Bloc de notas o el editor de texto que prefiera. Copie el contenido entre el título inicial y final del certificado y péguelo en el cuadro de texto **Certificado X.509**.

3. Haga clic en **Save**(Guardar). Vaya a la [guía de Asana para configurar el SSO](https://asana.com/guide/help/premium/authentication#gl-saml) si necesita más ayuda.

### <a name="create-asana-test-user"></a>Creación de un usuario de prueba de Asana

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Asana. Asana admite el aprovisionamiento automático de usuarios, que está habilitado de forma predeterminada. [Aquí](asana-provisioning-tutorial.md) puede encontrar más información sobre cómo configurar el aprovisionamiento automático de usuarios.

**Para crear un usuario manualmente, siga los pasos siguientes:**

En esta sección, creará un usuario llamado Britta Simon en Asana.

1. En **Asana**, vaya a la sección **Teams** (Equipos) en el panel izquierdo. Haga clic en el botón de signo más.

    ![Creación de un usuario de prueba de Azure AD](./media/asana-tutorial/teams.png)

2. Escriba el correo electrónico del usuario como **britta.simon\@contoso.com** en el cuadro de texto y, luego, seleccione **Invitar**.

3. Haga clic en **Enviar invitación**. El nuevo usuario recibirá un correo electrónico en su cuenta de correo. El usuario tendrá que crear y validar la cuenta.

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Asana, desde donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de Asana y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Asana en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de esta aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada Asana, puede aplicar el control de sesión, que protege la exfiltración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).