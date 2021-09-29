---
title: 'Tutorial: Integración de Azure Active Directory con JFrog Artifactory | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: ff9e84ecef92c14a620908e67a93f672a0d13adc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124790612"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Tutorial: Integración de JFrog Artifactory con Azure Active Directory

En este tutorial, obtendrá información sobre cómo integrar JFrog Artifactory con Azure Active Directory (Azure AD). Al integrar JFrog Artifactory con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a JFrog Artifactory.
* Permitir que los usuarios puedan iniciar sesión automáticamente en JFrog Artifactory con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para inicio de sesión único (SSO) en JFrog Artifactory.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* JFrog Artifactory admite el inicio de sesión único iniciado por **SP e IDP**.
* JFrog Artifactory admite el aprovisionamiento de usuarios **Just-In-Time**.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Adición de JFrog Artifactory desde la galería

Para configurar la integración de JFrog Artifactory en Azure AD, deberá agregar JFrog Artifactory desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **JFrog Artifactory** en el cuadro de búsqueda.
1. Seleccione **JFrog Artifactory** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Configuración y prueba del inicio de sesión único de Azure AD para JFrog Artifactory

Configure y pruebe el inicio de sesión único de Azure AD con JFrog Artifactory mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de JFrog Artifactory.

Para configurar y probar el inicio de sesión único de Azure AD con JFrog Artifactory, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en JFrog Artifactory](#configure-jfrog-artifactory-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de JFrog Artifactory](#create-jfrog-artifactory-test-user)** : para tener un homólogo de B.Simon en JFrog Artifactory vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **JFrog Artifactory**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL que refleje la URL de Artifactory.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón:
    
    - En el caso de Artifactory autohospedado: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - En el caso de SaaS Artifactory: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **Dirección URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    - En el caso de Artifactory autohospedado: `https://<servername>.jfrog.io/<servername>/webapp/`
    - En el caso de SaaS de Artifactory: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de cliente de JFrog Artifactory](https://support.jfrog.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación JFrog Artifactory espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos y notificaciones de usuario.

    ![Captura de pantalla que muestra User Attributes (Atributos de usuario) con el control de edición seleccionado.](common/edit-attribute.png)

1. Además de lo anterior, la aplicación JFrog Artifactory espera que se usen algunos atributos más en la respuesta de SAML. En la sección **Atributos y notificaciones de usuario**, haga clic en **Agregar una notificación de grupo** y siga estos pasos:

    a. Haga clic en **Abrir** junto a **Groups returned in claim** (Grupos devueltos en la notificación).

    ![Captura de pantalla que muestra los atributos y las notificaciones del usuario con el icono de edición seleccionado.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Captura de pantalla que muestra la sección Group Claims (Notificaciones de grupo) con la opción All groups (Todos los grupos) seleccionada.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Seleccione **Todos los grupos** en la lista de selección.

    c. Haga clic en **Save**(Guardar).

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configure Artifactory (nombre del proveedor de servicios SAML) con el campo "identificador" (consulte el paso 4). En la sección **Configurar JFrog Artifactory**, copie las direcciones URL adecuadas según sus necesidades.

   - En el caso de Artifactory autohospedado: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - En el caso de SaaS Artifactory: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

En esta sección, va a permitir que B.Simon acceda a JFrog Artifactory mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **JFrog Artifactory**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-jfrog-artifactory-sso"></a>Configuración del inicio de sesión único en JFrog Artifactory

Para configurar el inicio de sesión único en **JFrog Artifactory**, es preciso enviar el **certificado (sin procesar)** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de JFrog Artifactory](https://support.jfrog.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-jfrog-artifactory-test-user"></a>Creación de un usuario de prueba de JFrog Artifactory

En esta sección se crea un usuario llamado B.Simon en JFrog Artifactory. JFrog Artifactory admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario deja de existir en JFrog Artifactory, se crea otro después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de JFrog Artifactory, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de JFrog Artifactory e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de JFrog Artifactory para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de JFrog Artifactory en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de JFrog Artifactory para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado JFrog Artifactory, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).