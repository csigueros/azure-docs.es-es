---
title: 'Tutorial: Integración de Azure Active Directory con Panorama9 | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Panorama9.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 252bf6479819eb41c857a2f402dddf13fb7b8abc
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111748799"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integración de Azure Active Directory con Panorama9

En este tutorial, aprenderá a integrar Panorama9 con Azure Active Directory (Azure AD). Al integrar Panorama9c con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Panorama9.
* Permitir que los usuarios inicien sesión automáticamente en Panorama9 con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Panorama9.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Panorama9 admite SSO iniciado por **SP**.

## <a name="add-panorama9-from-the-gallery"></a>Agregar Panorama9 desde la galería

Para configurar la integración de Panorama9 en Azure AD, será preciso que agregue Panorama9 desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Panorama9** en el cuadro de búsqueda.
1. Seleccione **Panorama9** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-panorama9"></a>Configuración y prueba del inicio de sesión único de Azure AD para Panorama9

Configure y pruebe el inicio de sesión único de Azure AD con Panorama9 mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Panorama9.

Para configurar y probar el inicio de sesión único de Azure AD con Panorama9, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Panorama9](#configure-panorama9-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Panorama9](#create-panorama9-test-user)** : el objetivo es tener un homólogo de B.Simon en Panorama9 que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Panorama9**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL: `https://dashboard.panorama9.com/saml/access/3262`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://www.panorama9.com/saml20/<TENANT_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Panorama9](https://support.panorama9.com/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la sección **Certificado de firma de SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Edición del certificado de firma de SAML](common/edit-certificate.png)

6. En la sección **Certificado de firma de SAML**, copie el valor de **Huella digital** en el equipo.

    ![Copia del valor de la huella digital](common/copy-thumbprint.png)

7. En la sección **Set up Panorama9** (Configurar Panorama9), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Panorama9 mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Panorama9**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-panorama9-sso"></a>Configuración del inicio de sesión único de Panorama9

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía Panorama9 como administrador.

2. En la barra de herramientas de la parte superior, haga clic en **Administrar** y luego en **Extensiones**.
   
    ![Extensiones](./media/panorama9-tutorial/toolbar.png "Extensiones")

3. En el diálogo **Extensiones**, haga clic en **Inicio de sesión único**.
   
    ![Inicio de sesión único](./media/panorama9-tutorial/extension.png "Inicio de sesión único")

4. En la sección **Configuración** , lleve a cabo estos pasos:
   
    ![Configuración](./media/panorama9-tutorial/configuration.png "Configuración")
   
    a. En el cuadro de texto **Identity provider URL** (Dirección URL del proveedor de identidades), pegue el valor de **Dirección URL de inicio de sesión**  que ha copiado de Azure Portal.
   
    b. En el cuadro de texto **Certificate Fingerprint** (Huella digital de certificado), pegue el valor de **Huella digital** del certificado que haya copiado de Azure Portal.    
         
5. Haga clic en **Save**(Guardar).

### <a name="create-panorama9-test-user"></a>Creación de un usuario de prueba de Panorama9

Para permitir que los usuarios de Azure AD se conecten a Panorama9, deben aprovisionarse en Panorama9.  

En el caso de Panorama9, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Conéctese al sitio de la compañía **Panorama9** como administrador.

2. En el menú en la parte superior, haga clic en **Administrar** y luego haga clic en **Usuarios**.
   
    ![Captura de pantalla con las pestañas "Manage" (Administrar) y "Users" (Usuarios) seleccionadas.](./media/panorama9-tutorial/user.png "Usuarios")

3. En la sección Usuarios, haga clic en **+** para agregar un nuevo usuario.

    ![Usuarios](./media/panorama9-tutorial/new-user.png "Usuarios")

4. Vaya a la sección Datos de usuario y escriba la dirección de correo electrónico de un usuario válido de Azure Active Directory que desee aprovisionar en el cuadro de texto **Correo electrónico**.

5. En la sección Usuarios, haga clic en **Guardar**.
   
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirige a la dirección URL de inicio de sesión de Panorama9, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Panorama9 e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Panorama9 en Aplicaciones, se le redirigirá a la URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Panorama9, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
