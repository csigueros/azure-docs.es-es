---
title: 'Tutorial: Integración de Azure Active Directory con SAP Business ByDesign | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 54bf74a0d20945d7af27b86b80e1ac50397d7425
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808479"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integración de Azure Active Directory con SAP Business ByDesign

En este tutorial, descubrirá cómo SAP Business ByDesign se integra con Azure Active Directory (Azure AD). Si integra SAP Business ByDesign con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a SAP Business ByDesign.
* Permitir que los usuarios inicien sesión automáticamente en SAP Business ByDesign con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción a SAP Business ByDesign con el inicio de sesión único (SSO) habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAP Business ByDesign admite el inicio de sesión único iniciado por **SP**

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Adición de SAP Business ByDesign desde la galería

Para configurar la integración de SAP Business ByDesign en Azure AD, es preciso agregar SAP Business ByDesign desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAP Business ByDesign** en el cuadro de búsqueda.
1. Seleccione **SAP Business ByDesign** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD

Configure y pruebe el inicio de sesión único de Azure AD con SAP Business ByDesign utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de SAP Business ByDesign.

Para configurar el inicio de sesión único de Azure AD con SAP Business ByDesign, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en SAP Business ByDesign](#configure-sap-business-bydesign-sso)** : para configurar los valores de inicio de sesión único de la aplicación.
    1. **[Creación de un usuario de prueba de SAP Business ByDesign](#create-sap-business-bydesign-test-user)** : para tener un homólogo de Britta Simon en SAP Business ByDesign que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **SAP Business ByDesign**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<servername>.sapbydesign.com`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. La aplicación SAP Business ByDesign espera las aserciones SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Atributos de usuario**.

    ![image1](common/edit-attribute.png)

6. Haga clic en el icono **Editar** para editar el **Valor de identificador de nombre**.

    ![imagen2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. En la sección **Administrar las notificaciones del usuario**, realice los siguientes pasos:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Seleccione **Transformación** como **Origen**.

    b. En la lista desplegable **Transformación**, seleccione **ExtractMailPrefix()** .

    > [!NOTE]
    > De forma predeterminada, ByD usa el formato **unspecified** (sin especificar) de NameID para asignar usuarios. ByD asigna el NameID de las aserciones de SAML en el alias de usuario de ByD. ByD admite también el formato **emailAddress** (dirección de correo electrónico). En este caso, ByD asigna el NameID de la aserción de SAM en la dirección de correo electrónico del usuario de ByD que aparece en los datos de contacto del empleado de ByD. Para más información, puede consultar [este blog de SAP](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

9. En la sección **Configurar SAP Business ByDesign**, copie las direcciones URL apropiadas según sus requisitos.

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

En esta sección, va a dar permiso a B.Simon para que pueda utilizar el inicio de sesión único de Azure concediéndole acceso a SAP Business ByDesign.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAP Business ByDesign**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".

1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sap-business-bydesign-sso"></a>Configuración del inicio de sesión único de SAP Business ByDesign

1. Inicie sesión en el portal de SAP Business ByDesign con derechos de administrador.

2. Navegue hasta **Application and User Management Common Task** (Tarea común de administración de usuarios y aplicaciones) y haga clic en la pestaña **Proveedor de identidades**.

3. Haga clic en **New Identity Provider** (Nuevo proveedor de identidades) y seleccione el archivo XML de metadatos que ha descargado de Azure Portal. Al importar los metadatos, el sistema carga automáticamente el certificado de firma y el certificado de cifrado necesarios.

    ![Configuración del inicio de sesión único1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir la **URL del Servicio de consumidor de aserciones** en la petición SAML, seleccione **Include Assertion Consumer Service URL** (Incluir URL del Servicio de consumidor de aserciones).

5. Haga clic en **Activate Single Sign-On**(Activar inicio de sesión único).

6. Guarde los cambios.

7. Haga clic en la pestaña **My System** (Mi sistema).

    ![Configuración de inicio de sesión único2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. En el cuadro de texto **Azure AD Sign On URL** (Dirección URL de inicio de sesión de Azure AD), pegue el valor de la **URL de inicio de sesión** que ha copiado de Azure Portal.

    ![Configuración de inicio de sesión único3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especifique si el empleado puede elegir manualmente entre iniciar sesión con el id. de usuario y una contraseña o con SSO, para lo que necesita seleccionar **Manual Identity Provider Selection** (Selección manual de proveedor de identidades).

10. En la sección **SSO URL** (URL del inicio de sesión único), especifique la dirección URL que debe utilizar el empleado para iniciar sesión en el sistema.
    En la lista desplegable URL Sent to Employee (URL que se envía a empleado), puede elegir una de las siguientes opciones:

    **Non-SSO URL**

    El sistema solo envía la dirección URL del sistema normal al empleado. El empleado no puede iniciar sesión mediante el inicio de sesión único y tiene que usar una contraseña o un certificado en su lugar.

    **SSO URL**

    El sistema solo envía la dirección URL de SSO al empleado. El empleado puede iniciar sesión mediante el inicio de sesión único. La solicitud de autenticación se redirige a través de IdP.

    **Automatic Selection**

    SI SSO no está activo, el sistema envía la dirección URL del sistema normal al empleado. Si SSO está activo, el sistema comprueba si el empleado tiene contraseña. Si existe una contraseña, se envían al empleado tanto la URL de SSO como la URL no de SSO. Sin embargo, si el empleado no tiene contraseña, solo se envía la primera al empleado.

11. Guarde los cambios.

### <a name="create-sap-business-bydesign-test-user"></a>Creación de un usuario de prueba de SAP Business ByDesign

En esta sección, creará un usuario llamado Britta Simon en SAP Business ByDesign. Trabaje con el [equipo de soporte técnico de SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para agregar usuarios en la plataforma SAP Business ByDesign. 

> [!NOTE]
> Asegúrese de que el valor de NameID coincide con el campo de nombre de usuario de la plataforma SAP Business ByDesign.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

1. Haga clic en **Probar esta aplicación** en Azure Portal. De este modo, accederá a la dirección URL de inicio de sesión único de SAP Business ByDesign, donde podrá iniciar el flujo de inicio de sesión. 

2. Vaya directamente a la dirección URL de inicio de sesión de SAP Business ByDesign e inicie el flujo de inicio de sesión desde allí.

3. Puede usar Mis aplicaciones de Microsoft. Si hace clic en el icono de SAP Business ByDesign de Aplicaciones, accederá a la dirección URL de inicio de sesión único de SAP Business ByDesign. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

* Una vez configurado SAP Business ByDesign, puede aplicar controles de sesión para proteger en tiempo real los datos confidenciales de la organización e impedir que se filtren dentro y fuera de esta. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).