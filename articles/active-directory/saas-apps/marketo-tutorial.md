---
title: 'Tutorial: Integración de Azure Active Directory con Marketo | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 6e87f8b6f11ffc86d8e4f63facb2433b9f40b7c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808695"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: integración de Azure Active Directory con Marketo

En este tutorial, aprenderá a integrar Marketo con Azure Active Directory (Azure AD).
Integrar Marketo con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Marketo.
* Puede permitir que los usuarios inicien sesión automáticamente en Marketo (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Marketo, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/)
* Una suscripción habilitada para inicio de sesión único en Marketo

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Marketo admite SSO iniciado por **IDP**

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-marketo-from-the-gallery"></a>Incorporación de Marketo desde la galería

Para configurar la integración de Marketo en Azure AD, deberá agregar Marketo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Marketo** en el cuadro de búsqueda.
1. Seleccione **Marketo** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Configuración y prueba del inicio de sesión único de Azure AD para Marketo

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Marketo con un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Marketo.

Para configurar y probar el inicio de sesión único de Azure AD con Marketo, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con el usuario B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** : para permitir que B.Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en Marketo](#configure-marketo-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Marketo](#create-marketo-test-user)** : el objetivo es tener un homólogo de Britta Simon en Marketo que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Marketo**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://saml.marketo.com/sp`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://login.marketo.com/saml/assertion/<munchkinid>`

    c. En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL de respuesta y el estado de la retransmisión. Póngase en contacto con el [equipo de soporte técnico para clientes de Marketo](https://investors.marketo.com/contactus.cfm) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Azure Portal,**  (Configurar Azure Portal, ), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a Marketo mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Marketo**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-marketo-sso"></a>Configuración del inicio de sesión único en Marketo

1. Para automatizar la configuración en Marketo, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Set up Marketo** (Configurar Marketo) para ir a la aplicación. Ahí, escriba las credenciales de administrador para iniciar sesión en Marketo. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 a 6.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Marketo manualmente, en otra ventana del explorador web, inicie sesión en su sitio empresarial de Marketo como administrador.

1. Para obtener el identificador de Munchkin de la aplicación, realice las siguientes acciones:
   
    a. Inicie sesión en la aplicación Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configuración del inicio de sesión único1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Vaya al menú Integración y haga clic en el **vínculo de Munchkin**.
   
    ![Configuración de inicio de sesión único2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie el identificador de Munchkin mostrado en la pantalla y complete la dirección URL de respuesta en el asistente para configuración de Azure AD.
   
    ![Configuración de inicio de sesión único3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Para configurar el inicio de sesión único en la aplicación, siga estos pasos:
   
    a. Inicie sesión en la aplicación Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configuración de inicio de sesión único4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Vaya al menú Integración y haga clic en **Inicio de sesión único**.
   
    ![Configuración de inicio de sesión único5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para habilitar la configuración de SAML, haga clic en el botón **Editar**.
   
    ![Configurar inicio de sesión único6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Ya está la configuración de inicio de sesión único **habilitada**.
   
    f. Pegue el valor de **Identificador de Azure AD**  en el cuadro de texto **Issuer ID** (Identificador del emisor).
   
    g. En el cuadro de texto **Entity ID** (Id. de entidad), escriba la dirección URL como `http://saml.marketo.com/sp`.
   
    h. Seleccione la ubicación del identificador de usuario como **elemento Identificador de nombre**.
   
    ![Configuración de inicio de sesión único7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Si su identificador de usuario no es el valor UPN, cambie el valor en la pestaña Atributo.
   
    i. Cargue el certificado que ha descargado del Asistente para la configuración de Azure AD. **Guarde** la configuración.
   
    j. Edite la configuración delas páginas de redireccionamiento.
   
    k. Pegue el valor de **Dirección URL de inicio de sesión** en el cuadro de texto **Login URL** (URL de inicio de sesión).
   
    l. Pegue el valor de **URL de cierre de sesión** en el cuadro de texto **Logout URL** (URL de cierre de sesión).
   
    m. En **URL del error**, copie la **dirección URL de la instancia de Marketo** y haga clic en el botón **Guardar** para guardar la configuración.
   
    ![Configuración de inicio de sesión único8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Para habilitar el SSO para los usuarios, complete las siguientes acciones:
   
    a. Inicie sesión en la aplicación Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configuración de inicio de sesión único9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Desplácese hasta el menú **Seguridad** y haga clic en **Login Settings** (Configuración de inicio de sesión).
   
    ![Configuración de inicio de sesión único10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Active la opción **Require SSO** (Requerir SSO) y **guarde** la configuración.
   
    ![Configuración de inicio de sesión único11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Creación de un usuario de prueba en Marketo

En esta sección, creará un usuario llamado Britta Simon en Marketo. Siga estos pasos para crear un usuario en la plataforma Marketo.

1. Inicie sesión en la aplicación Marketo con credenciales de administrador.

2. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![usuario de prueba1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Desplácese hasta el menú **Security** (Seguridad) y haga clic en **Users & Roles** (Usuarios y roles).
   
    ![usuario de prueba2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Haga clic en el vínculo **Invite New User** (Invitar a un usuario nuevo) en la pestaña Users (Usuarios).
   
    ![usuario de prueba3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. En el asistente Invitar a un usuario nuevo, rellene la siguiente información.
   
    a. Especifique la dirección de **Correo electrónico** del usuario en el cuadro de texto.
   
    ![usuario de prueba4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Escriba el **Nombre** en el cuadro de texto.
   
    c. Escriba los **Apellidos** en el cuadro de texto.
   
    d. Haga clic en **Siguiente**.

6. En la pestaña **Permissions** (Permisos) seleccione los **roles de usuario** y haga clic en **Next** (Siguiente).
   
    ![usuario de prueba5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Haga clic en el botón **Send** (Enviar) para enviar la invitación del usuario.
   
    ![usuario de prueba6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. El usuario recibe la notificación de correo electrónico y tiene que hacer clic en el vínculo y cambiar la contraseña para activar la cuenta. 

### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Marketo para la que configurara el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Marketo en Mis aplicaciones, debería iniciar sesión automáticamente en la versión de Marketo para la que configurara el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la aplicación Marketo, podrá aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).