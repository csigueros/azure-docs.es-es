---
title: 'Tutorial: Integración del inicio de sesión único de Azure Active Directory con AWS Single Sign-on | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AWS Single Sign-on.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 91a66240941ce18b4d898327abeac695b09d65e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732349"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con AWS Single Sign-on

En este tutorial, aprenderá a integrar AWS Single Sign-on con Azure Active Directory (Azure AD). Al integrar AWS Single Sign-on con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a AWS Single Sign-on.
* Permitir que los usuarios inicien sesión automáticamente en AWS Single Sign-on con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AWS Single Sign-on.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* AWS Single Sign-on admite el inicio de sesión único iniciado por **SP e IDP**.

* AWS Single Sign-on admite el [**aprovisionamiento automático de usuarios**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Incorporación de AWS Single Sign-on desde la galería

Para configurar la integración de AWS Single Sign-on en Azure AD, será preciso que agregue AWS Single Sign-on desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AWS Single Sign-on** en el cuadro de búsqueda.
1. Seleccione **AWS Single Sign-on** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Configuración y prueba del inicio de sesión único de Azure AD para AWS Single Sign-on

Configure y pruebe el inicio de sesión único de Azure AD con AWS Single Sign-on mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de AWS Single Sign-on.

Para configurar y probar el inicio de sesión único de Azure AD con AWS Single Sign-on, siga estos siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de AWS Single Sign-on](#configure-aws-single-sign-on-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de AWS Single Sign-on](#create-aws-single-sign-on-test-user)** , para tener un homólogo de B. Simon en AWS Single Sign-on que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **AWS Single Sign-on**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. Si tiene un **archivo de metadatos del proveedor de servicios**, en la sección **Configuración básica de SAML**, siga estos pasos:

    a. Haga clic en **Cargar el archivo de metadatos**.

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos que ha descargado en la sección **Configuración del inicio de sesión único de AWS Single Sign-on** (punto 8) y haga clic en **Agregar**.

    ![imagen2](common/browse-upload-metadata.png)

    c. Una vez que se haya cargado correctamente el archivo de metadatos, los valores de **Identificador** y **URL de respuesta** se rellenan automáticamente en la sección Configuración básica de SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Si los valores de **Identificador** y **URL de respuesta** no se rellenan automáticamente, hágalo manualmente a medida que lo necesite.

1. Si no tiene el **archivo de metadatos del proveedor de servicios** realice los pasos siguientes en la sección **Configuración básica de SAML** y desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Para obtener dichos valores, póngase en contacto con el [equipo de soporte técnico al cliente de AWS Single Sign-on](mailto:aws-sso-partners@amazon.com). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación AWS Single Sign-on espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/edit-attribute.png)


    > [!NOTE]
    > Si ABAC está habilitado en el inicio de sesión único de AWS, los atributos adicionales se pueden pasar como etiquetas de sesión directamente a las cuentas de AWS.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up AWS Single Sign-on** (Configurar AWS Single Sign-on), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a conceder a B. Simon acceso a AWS Single Sign-on utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AWS Single Sign-on**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-aws-single-sign-on-sso"></a>Configuración del inicio de sesión único de AWS Single Sign-on

1. Para automatizar la configuración en AWS Single Sign-On, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Set up AWS Single Sign-On** (Configurar AWS Single Sign-On) para ir a esta aplicación. Desde allí, proporcione las credenciales de administrador para iniciar sesión en AWS Single Sign-on. La extensión del navegador configurará automáticamente la aplicación y automatizará los pasos del 3 al 10.

    ![Configuración](common/setup-sso.png)

3. Si desea configurar manualmente AWS Single Sign-on, en otra ventana del explorador web, inicie sesión en el sitio de la empresa como administrador.

1. Vaya a **Services -> Security, Identity, & Compliance -> AWS Single Sign-On** (Servicios -> Seguridad, identidad y complimiento -> AWS Single Sign-On).
2. En el panel de navegación izquierdo, elija **Configuración**.
3. En la página **Settings** (Configuración), busque **Identity source** (Origen de la identidad), elija **Change** (Cambiar).

    ![Captura de pantalla del servicio de cambio de origen de la identidad.](./media/aws-single-sign-on-tutorial/settings.png)

4. En Change identity source (Cambiar origen de la identidad), elija **External identity provider** (Proveedor de identidades externo).

    
    ![Captura de pantalla de la sección de selección del proveedor de identidades externo.](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Realice los pasos siguientes de la sección **Configure external identity provider** (Configurar proveedor de identidades externo):

    ![Captura de pantalla de la sección de carga y descarga de metadatos.](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. En la sección **Service provider metadata** (Metadatos del proveedor de servicios), busque **AWS SSO SAML metadata** (Metadatos de SAML de AWS SSO) y seleccione **Download metadata file** (Descargar archivo de metadatos) para descargar el archivo de metadatos y guardarlo en el ordenador para usarlo para la carga en Azure Portal.

    b. Copie el valor de **AWS SSO Sign.in URL** (URL de inicio de sesión de AWS SSO) y péguelo en el cuadro de texto **URL de inicio de sesión** de la sección **Configuración básica de SAML** de Azure Portal.

    c. En la sección **Metadatos del proveedor de identidades**, elija **Examinar** para cargar el archivo de metadatos que ha descargado desde Azure Portal.

    d. Elija **Siguiente: Revisar**.

8. En el cuadro de texto, escriba **ACCEPT** (ACEPTAR) para cambiar el origen de la identidad.

    ![Captura de pantalla de confirmación de la configuración.](./media/aws-single-sign-on-tutorial/accept.png)

9. Haga clic en **Change identity source** (Cambiar el origen de la identidad).

### <a name="create-aws-single-sign-on-test-user"></a>Creación de un usuario de prueba de AWS Single Sign-on

1. Abra la **consola de inicio de sesión único de AWS**.

2. En el panel de navegación izquierdo, elija **Usuarios**.

3. En la página Usuarios, elija **Agregar usuarios**.

4. En la página Agregar usuario, siga estos pasos:

    a. En el campo **Nombre de usuario**, escriba B. Simon.

    b. En el campo **Dirección de correo electrónico**, escriba `username@companydomain.extension`. Por ejemplo, `B.Simon@contoso.com`.

    c. En el campo **Confirmar dirección de correo electrónico**, vuelva a escribir la dirección de correo electrónico del paso anterior.

    d. En el campo Nombre, escriba `Jane`.

    e. En el campo Nombre, escriba `Doe`.

    f. En el campo Nombre para mostrar, escriba `Jane Doe`.

    g. Elija **Siguiente: Grupos**.

    > [!NOTE]
    > Asegúrese de que el nombre de usuario especificado en AWS SSO coincide con el nombre de inicio de sesión de Azure AD del usuario. Esto le ayudará a evitar problemas de autenticación.

5. Elija **Agregar usuario**.
6. A continuación, asignará el usuario a su cuenta de AWS. Para ello, en el panel de navegación izquierdo de la consola de AWS SSO, elija **Cuentas de AWS**.
7. En la página Cuentas de AWS, seleccione la pestaña Organización de AWS y active la casilla situada junto a la cuenta de AWS que quiere asignar al usuario. A continuación, elija **Asignar usuarios**.
8. En la página Asignar usuarios, busque y active la casilla situada junto al usuario B. Simon. Luego, elija **Siguiente: Conjuntos de permisos**.
9. En la sección Seleccionar conjuntos de permisos, active la casilla situada junto al conjunto de permisos que desea asignar al usuario B. Simon. Si no tiene un conjunto de permisos existente, elija **Crear un conjunto de permisos**.

    > [!NOTE]
    > Los conjuntos de permisos definen el nivel de acceso que los usuarios y los grupos tienen en una cuenta de AWS. Para más información sobre los conjuntos de permisos, consulte la página **Conjuntos de permisos** de AWS SSO.
10. Elija **Finalizar**.

> [!NOTE]
> AWS Single Sign-on también admite el aprovisionamiento automático de usuarios. [Aquí](./aws-single-sign-on-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de AWS Single Sign-on, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de AWS Single Sign-on e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de AWS Single Sign-on para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de AWS Single Sign-on en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de AWS Single Sign-on para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado AWS Single Sign-on, puede aplicar el control de sesión, que protege su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
