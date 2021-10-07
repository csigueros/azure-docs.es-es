---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con RStudio Connect SAML Authentication'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y RStudio Connect SAML Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 76550ad5f3a37058d37015e8e2d71ad727b2d5b8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665822"
---
# <a name="tutorial-azure-ad-sso-integration-with-rstudio-connect-saml-authentication"></a>Tutorial: Integración del inicio de sesión único de Azure AD con RStudio Connect SAML Authentication

En este tutorial, aprenderá a integrar RStudio Connect SAML Authentication con Azure Active Directory (Azure AD). Al integrar RStudio Connect SAML Authentication con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a RStudio Connect SAML Authentication.
* Permitir que los usuarios inicien sesión automáticamente en RStudio Connect SAML Authentication con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con RStudio Connect SAML Authentication, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* RStudio Connect SAML Authentication. Hay una [evaluación gratuita de 45 días](https://www.rstudio.com/products/connect/).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* RStudio Connect SAML Authentication admite el inicio de sesión único iniciado por **SP e IDP**.

* RStudio Connect SAML Authentication admite el aprovisionamiento de usuarios **Just In Time**.

## <a name="add-rstudio-connect-saml-authentication-from-the-gallery"></a>Adición de RStudio Connect SAML Authentication desde la galería

Para configurar la integración de RStudio Connect SAML Authentication con Azure AD, debe agregar RStudio Connect SAML Authentication desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **RStudio Connect SAML Authentication** en el cuadro de búsqueda.
1. Seleccione **RStudio Connect SAML Authentication** del panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Configuración y prueba del inicio de sesión único de Azure AD para RStudio Connect SAML Authentication

Configure y pruebe el inicio de sesión único de Azure AD con RStudio Connect SAML Authentication usando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de RStudio Connect SAML Authentication.

Para configurar y probar el inicio de sesión único de Azure AD con RStudio Connect SAML Authentication, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en RStudio Connect SAML Authentication](#configure-rstudio-connect-saml-authentication-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en RStudio Connect SAML Authentication](#create-rstudio-connect-saml-authentication-test-user)** : para tener un homólogo de B.Simon en RStudio Connect SAML Authentication vinculado a su representación en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **RStudio Connect SAML Authentication**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos, y sustituya `<example.com>` por el puerto y la dirección del servidor de RStudio Connect SAML Authentication:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<example.com>/__login__/saml`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<example.com>/__login__/saml/acs`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<example.com>/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Se determinan a partir de la dirección del servidor de RStudio Connect SAML Authentication (`https://example.com` en los ejemplos anteriores). Póngase en contacto con el [equipo de soporte técnico de RStudio Connect SAML Authentication](mailto:support@rstudio.com) si tiene algún problema. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. La aplicación RStudio Connect SAML Authentication espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación RStudio Connect SAML Authentication espera que **nameidentifier** se corresponda con **user.mail**, por lo que debe editar la asignación de atributos; para ello, haga clic en el icono **Edit** (Editar) y cambie la asignación.

    ![imagen](common/edit-attribute.png)

7. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

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

En esta sección va a permitir que B.Simon acceda a RStudio Connect SAML Authentication mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **RStudio Connect SAML Authentication**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Configuración del inicio de sesión único en RStudio Connect SAML Authentication

Para configurar el inicio de sesión único en **RStudio Connect SAML Authentication**, necesita usar la **dirección URL de metadatos de federación de aplicación** y la **dirección del servidor** utilizadas anteriormente. Esto se hace en el archivo de configuración de RStudio Connect SAML Authentication de `/etc/rstudio-connect.rstudio-connect.gcfg`.

Este es un archivo de configuración de ejemplo:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Almacene la **Dirección del servidor** en el valor `Server.Address`, y la **Dirección URL de metadatos de federación de aplicación** en el valor `SAML.IdPMetaData`. Tenga en cuenta que esta configuración de ejemplo usa una conexión HTTP sin cifrar, en tanto que Azure AD requiere el uso de una conexión HTTPS cifrada. Puede usar un [proxy inverso](https://docs.rstudio.com/connect/admin/proxy/) delante de RStudio Connect SAML Authentication o configurar RStudio Connect para que [use HTTPS directamente](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Si tiene problemas con la configuración, puede leer la [guía del administrador de RStudio Connect SAML Authentication](https://docs.rstudio.com/connect/admin/authentication/saml/) o enviar un correo electrónico al [equipo de soporte técnico de RStudio](mailto:support@rstudio.com) para obtener ayuda.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Creación de un usuario de prueba en RStudio Connect SAML Authentication

En esta sección se crea un usuario llamado B.Simon en RStudio Connect SAML Authentication. RStudio Connect SAML Authentication admite el aprovisionamiento Just-In-Time, habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en la aplicación RStudio Connect SAML Authentication, se crea al intentar acceder a ella.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de RStudio Connect SAML Authentication, donde podrá iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de RStudio Connect SAML Authentication e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de RStudio Connect SAML Authentication para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de RStudio Connect SAML Authentication en Aplicaciones, si tiene la configuración del modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si tiene la configuración del modo IDP, debería iniciar sesión automáticamente en la instancia de RStudio Connect SAML Authentication para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la aplicación RStudio Connect SAML Authentication, podrá aplicar el control de sesión, que protege a su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).