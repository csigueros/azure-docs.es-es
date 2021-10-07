---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Foodee | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Foodee.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: ce15d098f1713a1483f4a5c054f2c348d92eb228
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604957"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-foodee"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Foodee

En este tutorial, obtendrá información sobre cómo integrar Foodee con Azure Active Directory (Azure AD). Al integrar Foodee con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Foodee.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Foodee con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Foodee.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Foodee admite el inicio de sesión único iniciado por **SP e IDP**.
* Foodee admite el aprovisionamiento de usuarios **Just-In-Time**.
* Foodee admite el [aprovisionamiento automatizado de usuarios](foodee-provisioning-tutorial.md).

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-foodee-from-the-gallery"></a>Adición de Foodee desde la galería

Para configurar la integración de Foodee en Azure AD, deberá agregar Foodee desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Foodee** en el cuadro de búsqueda.
1. Seleccione **Foodee** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-foodee"></a>Configuración y prueba del SSO de Azure AD para Foodee

Configure y pruebe el inicio de sesión único de Azure AD con Foodee mediante un usuario de prueba llamado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Foodee.

Para configurar y probar el inicio de sesión único de Azure AD con Foodee, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Foodee](#configure-foodee-sso)**, para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Foodee](#create-foodee-test-user)**, para tener un homólogo de B.Simon en Foodee que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Foodee**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://concierge.food.ee/sso/saml/<INSTANCENAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico para clientes de Foodee](mailto:dev@food.ee) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Foodee**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Foodee utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Foodee**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-foodee-sso"></a>Configuración del inicio de sesión único de Foodee

1. Para automatizar la configuración en Foodee, debe instalar la **extensión del explorador de inicio de sesión seguro de Mis aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Foodee** para ir a la aplicación del mismo nombre. En ella, escriba las credenciales de administrador para iniciar sesión en Foodee. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 y 4.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Foodee manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Foodee como administrador y lleve a cabo los siguientes pasos:

4. Haga clic en el **logotipo del perfil** en la esquina superior derecha de la página, a continuación, vaya a **Single Sign On** (Inicio de sesión único) y realice los pasos siguientes:

   ![Configuración de Foodee](./media/foodee-tutorial/profile-logo.png)

   1. En el cuadro de texto **IDP NAME** (NOMBRE DE IDP), escriba el nombre (por ejemplo, Azure).
   1. Abra el XML de metadatos de federación en el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **IDP METADATA XML** (XML de metadatos de IDP).
   1. Haga clic en **Save**(Guardar).

### <a name="create-foodee-test-user"></a>Creación de usuario de prueba de Foodee

En esta sección, se crea un usuario llamado B.Simon en Foodee. Foodee admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Foodee, se crea uno cuando se intenta acceder a Foodee.

Foodee también admite el aprovisionamiento automático de usuarios. [Aquí](./foodee-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

Iniciado por SP:

* Haga clic en Probar esta aplicación en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Foodee, donde puede poner en marcha el flujo de inicio de sesión.

* Vaya directamente a la dirección URL de inicio de sesión de Foodee e inicie el flujo de inicio de sesión desde allí.

Iniciado por IDP:

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Foodee para la que configuró el inicio de sesión único.

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Foodee en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Foodee para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Foodee, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).