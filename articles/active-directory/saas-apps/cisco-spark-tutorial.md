---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cisco Webex | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: jeedes
ms.openlocfilehash: 851a396fe848306223f2e875ac81fc7b2fcb5da1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cisco Webex

En este tutorial, aprenderá a integrar Cisco Webex con Azure Active Directory (Azure AD). Al integrar Cisco Webex con Azure AD, puede hacer lo siguiente:

* Puede controlar en Azure AD quién tiene acceso a Cisco Webex.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Cisco Webex con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Cisco Webex.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Cisco Webex admite el inicio de sesión único iniciado por **SP**.
* Cisco Webex admite el [**aprovisionamiento automatizado de usuarios**](./cisco-webex-provisioning-tutorial.md).

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adición de Cisco Webex desde la galería

Para configurar la integración de Cisco Webex en Azure AD, será preciso que agregue Cisco Webex desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Cisco Webex** en el cuadro de búsqueda.
1. Seleccione **Cisco Webex** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Configuración y prueba del inicio de sesión único de Azure AD para Cisco Webex

Configure y pruebe el inicio de sesión único de Azure AD con Cisco Webex utilizando un usuario de prueba llamado **B. Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Cisco Webex.

Para configurar y probar el inicio de sesión único de Azure AD con Cisco Webex, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en Cisco Webex](#configure-cisco-webex-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación del usuario de prueba de Cisco Webex](#create-cisco-webex-test-user)** : para tener un homólogo de B. Simon en Cisco Webex vinculado a la representación del usuario en Azure AD.
3. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Cisco Webex**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, cargue el **archivo de metadatos del proveedor de servicios** que descargó y configure la aplicación realizando estos pasos:

    >[!Note]
    >Obtendrá el archivo de metadatos del proveedor de servicios en la sección **Configure Cisco Webex** (Configuración de Cisco Webex), como se explica más adelante en el tutorial. 

    a. Haga clic en **Cargar el archivo de metadatos**.

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.

    c. Cuando el archivo de metadatos del proveedor de servicios se haya cargado correctamente, los valores **Identificador** y **URL de respuesta** se rellenan automáticamente en la sección **Configuración básica de SAML**:

    d. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://web.ciscospark.com/idb/Consumer/metaAlias/<ID>/sp`
    
    > [!NOTE]
    > Este valor no es real. Copie el valor lateral de Dirección URL de respuesta y agréguelo a `https://web.ciscospark.com/`para formular el valor real de la dirección URL de inicio de sesión.

1. La aplicación Cisco Webex espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Cisco Webex espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
  
    | Nombre |  Atributo de origen|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  El valor del atributo de origen se asigna de forma predeterminada a userprincipalname. Esto se puede cambiar a user.mail, user.onpremiseuserprincipalname o cualquier otro valor según la configuración de WebEx.


1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

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

En esta sección, va a permitir que B. Simon acceda a Cisco Webex mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Cisco Webex**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cisco-webex-sso"></a>Configuración del inicio de sesión único en Cisco Webex

1. Inicie sesión en Cisco WebEx con sus credenciales de administrador.

1. Seleccione **Organization Settings** (Configuración) y. en la sección **Authentication** (Autenticación), haga clic en **Modify** (Modificar).

    ![Captura de pantalla que muestra Authentication Settings (Configuración de autenticación) donde puede seleccionar Modify (Modificar).](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Seleccione **Integrate a 3rd-party identity provider. (Advanced)** (Integrar un proveedor de identidades de terceros [Avanzado]) y haga clic en **Next** (Siguiente).

    ![Captura de pantalla que muestra la integración de un proveedor de identidades de terceros.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Haga clic en **Descargar archivo de metadatos** (Descargar archivo de metadatos) para descargar el **archivo de metadatos del proveedor de servicios** y guárdelo en el equipo; haga clic en **Next** (Siguiente).

    ![Captura de pantalla que muestra el archivo de metadatos del proveedor de servicios.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Haga clic en la opción del **explorador de archivos** para buscar y cargar el archivo de metadatos de Azure AD. Después, seleccione **Require certificate signed by a certificate authority in Metadata (more secure)** (Requerir certificado firmado por una entidad de certificación en metadatos [más seguro]) y haga clic en **Next** (Siguiente).

    ![Captura de pantalla que muestra la página Import IdP Metadata (Importar metadatos del IdP).](./media/cisco-spark-tutorial/idp-metadata.png)

1. Seleccione **Test SSO Connection** (Probar SSO de conexión) y cuando se abra una nueva pestaña de explorador, autentíquese con Azure AD mediante el inicio de sesión.

1. Vuelva a la pestaña del explorador de **Cisco Cloud Collaboration Management**. Si la prueba se realizó correctamente, seleccione **This test was successful. Enable Single Sign-On option** (Esta prueba se realizó correctamente. Habilite la opción de inicio de sesión único) y haga clic en **Next** (Siguiente).

1. Haga clic en **Save**(Guardar).

> [!NOTE]
> Para más información sobre cómo configurar Cisco WebEx, consulte [esta](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) página.

### <a name="create-cisco-webex-test-user"></a>Creación del usuario de prueba de Cisco Webex

En esta sección, se crea un usuario llamado B. Simon en Cisco WebEx. Esta aplicación admite el aprovisionamiento automático de usuarios, que permite el aprovisionamiento y desaprovisionamiento automáticos en función de las reglas del negocio.  Microsoft recomienda usar el aprovisionamiento automático siempre que sea posible. Consulte cómo habilitar el aprovisionamiento automático para [Cisco WebEx](./cisco-webex-provisioning-tutorial.md).

Si tiene que crear manualmente un usuario, siga los pasos siguientes:

1. Inicie sesión en Cisco WebEx con sus credenciales de administrador.

2. Haga clic en **Usuarios** y después en **Administrar usuarios**.
   
    ![Captura de pantalla que muestra la página Users (Usuarios) donde se administran los usuarios.](./media/cisco-spark-tutorial/user-1.png) 

3. En la ventana **Manage Users** (Administrar usuarios), seleccione **Manually add or modify users** (Agregar o modificar usuarios manualmente).

    ![Captura de pantalla muestra la página de usuarios, donde puede administrar usuarios y seleccionar agregar o modificar usuarios manualmente.](./media/cisco-spark-tutorial/user-2.png)

4. Seleccione **Names and Email address** (Nombres y direcciones de correo electrónico). Después, rellene el cuadro de texto de la forma siguiente:

    ![Captura de pantalla que muestra el cuadro de diálogo Manage Users (Administrar usuarios) agregar o modificar usuarios manualmente.](./media/cisco-spark-tutorial/user-3.png) 

    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre del usuario, en este caso, **B**.

    b. En el cuadro de texto **Last Name** (Apellidos), escriba los apellidos del usuario, en este caso, **Simon**.

    c. En el cuadro de texto **Email address** (Dirección de correo electrónico), escriba la dirección de correo electrónico del usuario, por ejemplo, b.simon@contoso.com.

5. Haga clic en el signo más para agregar a B. Simon. A continuación, haga clic en **Siguiente**.

6. En la ventana **Add Services for Users** (Agregar servicios para los usuarios), haga clic en **Add Users** (Agregar usuarios) y después en **Finish** (Finalizar).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Cisco Webex, donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de Cisco Webex y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Cisco Webex en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Cisco Webex, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).