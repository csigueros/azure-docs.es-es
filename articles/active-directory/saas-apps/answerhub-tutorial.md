---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con AnswerHub'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y AnswerHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: b4e701cb930a9d65a49be2cd83d2e6bf81c3a18b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985612"
---
# <a name="tutorial-azure-ad-sso-integration-with-answerhub"></a>Tutorial: Integración del inicio de sesión único de Azure AD con AnswerHub

En este tutorial, aprenderá a integrar AnswerHub con Azure Active Directory (Azure AD). Al integrar AnswerHub con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AnswerHub.
* Permitir que los usuarios inicien sesión automáticamente en AnswerHub con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en AnswerHub

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* AnswerHub admite el inicio de sesión único iniciado por SP.

## <a name="add-answerhub-from-the-gallery"></a>Incorporación de AnswerHub desde la galería

Para configurar la integración de AnswerHub en Azure AD, será preciso que agregue AnswerHub desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AnswerHub** en el cuadro de búsqueda.
1. Seleccione **AnswerHub** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="set-up-and-test-azure-ad-sso-for-answerhub"></a>Configuración y prueba del inicio de sesión único de Azure AD para AnswerHub

Configure y pruebe el inicio de sesión único de Azure AD con AnswerHub mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de AnswerHub.

Para configurar y probar el inicio de sesión único de Azure AD con AnswerHub, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en GitHub](#configure-answerhub-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de AnswerHub](#create-answerhub-test-user)** : para tener un homólogo de B.Simon en AnswerHub que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **AnswerHub**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente formato: `https://<company>.answerhub.com`
    
    b. En el cuadro **URL de inicio de sesión**, escriba una dirección URL con el siguiente formato: `https://<company>.answerhub.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de AnswerHub](mailto:success@answerhub.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione el vínculo **Descargar** situado junto a **Certificado (Base64)** , según sus requisitos, y guarde el certificado en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up AnswerHub** (Configurar AnswerHub), copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a AnswerHub mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AnswerHub**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-answerhub-sso"></a>Configuración del inicio de sesión único de AnswerHub

En esta sección puede configurar el inicio de sesión único de AnswerHub.  

**Para configurar el inicio de sesión único de AnswerHub:**

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía AnswerHub como administrador.

    > [!NOTE]
    > Si necesita ayuda para configurar AnswerHub, póngase en contacto con el [equipo de soporte técnico de AnswerHub](mailto:success@answerhub.com.).

2. Vaya a **Administración**.

3. En la pestaña **Usuario y grupos**, en el panel de la izquierda, en la sección **Social Settings** (Configuración social), seleccione **SAML Setup** (Configuración de SAML).

4. En la pestaña **IDP Config**, complete estas tareas:

    ![Captura de pantalla que muestra la página AnswerHub con la pestaña Users & Groups (Usuarios y grupos) seleccionada.](./media/answerhub-tutorial/admin.png "Configuración de SAML")  

    a. En el cuadro **IDP Login URL** (URL de inicio de sesión de IDP), pegue la **dirección URL de inicio de sesión** que copió de Azure Portal.

    b. En el cuadro **IDP Logout URL** (URL de cierre de sesión de IDP), pegue la **dirección URL de cierre de sesión** que copió de Azure Portal.

    c. En el cuadro de texto **IDP Name Identifier Format** (Formato del identificador de nombre de IDP), escriba el valor de **identificador** que seleccionó en Azure Portal en la sección **Atributos de usuario**.

    d. Seleccione **Claves y certificados**.

5. En la sección **Claves y certificados** sección, siga estos pasos:

    ![Sección Keys and Certificates (Claves y certificados)](./media/answerhub-tutorial/users.png "Claves y certificados")  

    a. En el Bloc de notas, abra el certificado codificado en Base 64 que descargó de Azure Portal, copie el contenido y, a continuación, péguelo en el cuadro **IDP Public Key (x509 Format)** [Clave pública de IDP (formato x509)].

    b. Seleccione **Guardar**.

6. En la pestaña **IDP Config** (Configuración de IDP), seleccione **Save** (Guardar) de nuevo.

### <a name="create-answerhub-test-user"></a>Creación de un usuario de prueba de AnswerHub

Para permitir que los usuarios de Azure AD inicien sesión en AnswerHub, deberá agregarlos a este. En AnswerHub, esta tarea se realiza manualmente.

**Para configurar una cuenta de usuario:**

1. Inicie sesión en el sitio de la compañía **AnswerHub** como administrador.

2. Vaya a **Administración**.

3. Seleccione la pestaña **Users & Groups** (Usuarios y grupos).

4. En el panel izquierdo, en la sección **Manage Users** (Administrar usuarios), seleccione **Create or import users** (Crear o importar usuarios) y, a continuación, seleccione **Users & Groups** (Usuarios y grupos).

    ![Captura de pantalla que muestra la página AnswerHub con la pestaña Users & Groups (Usuarios y grupos) seleccionada y el vínculo Create or import users (Crear o importar usuarios) activado.](./media/answerhub-tutorial/groups.png "Usuarios y grupos")

5. En los cuadros correspondientes, escriba la **dirección de correo electrónico**, el **nombre de usuario** y la **contraseña** de una cuenta de Azure AD válida que desee agregar y seleccione **Save** (Guardar).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AnswerHub para configurar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de AnswerHub, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de AnswerHub e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de AnswerHub en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de esta aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado AnswerHub, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).