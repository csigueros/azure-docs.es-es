---
title: 'Tutorial: integración de Azure Active Directory con Kudos | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Kudos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: 407f4d6cd896c45194e48d637eb8b7baa745b03a
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111571721"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integración de Azure Active Directory con Kudos

En este tutorial, aprenderá a integrar Kudos con Azure Active Directory (Azure AD). Al integrar Kudos con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Kudos.
* Permitir que los usuarios inicien sesión automáticamente en Kudos con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Kudos, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en Kudos.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Kudos admite el inicio de sesión único iniciado por **SP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-kudos-from-the-gallery"></a>Adición de Kudos desde la galería

Para configurar la integración de Kudos en Azure AD, deberá agregar Kudos desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Kudos** en el cuadro de búsqueda.
1. Seleccione **Kudos** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-kudos"></a>Configuración y prueba del inicio de sesión único de Azure AD para Kudos

Configure y pruebe el inicio de sesión único de Azure AD con Kudos mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Kudos.

Para configurar y probar el inicio de sesión único de Azure AD con Kudos, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Kudos](#configure-kudos-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Kudos](#create-kudos-test-user)** : para tener un homólogo de B.Simon en Kudos que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Kudos**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<COMPANY>.kudosnow.com`

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte al cliente de Kudos](http://success.kudosnow.com/home) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up Kudos** (Configurar Kudos), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a Kudos mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Kudos**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-kudos-sso"></a>Configuración del inicio de sesión único en Kudos

1. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Kudos.

1. En el menú de la parte superior, haga clic en el **icono de configuración**.

    ![Configuración](./media/kudos-tutorial/menu.png "Configuración")

1. Desplácese hasta la opción **Integrations > SSO** (Integraciones > Inicio de sesión único) y realice los pasos siguientes:

    ![SSO](./media/kudos-tutorial/account.png "SSO")

    a. En el cuadro de texto **Sign-on URL** (Dirección URL de inicio de sesión), pegue el valor de la **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    b. Abra el certificado codificado en Base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado X.509**.

    c. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.

    d. En el cuadro de texto **Su dirección URL de Kudos**, escriba el nombre de la empresa.

    e. Haga clic en **Save**(Guardar).

### <a name="create-kudos-test-user"></a>Creación de un usuario de prueba de Kudos

Para permitir que los usuarios de Azure AD inicien sesión en Kudos, deben aprovisionarse en Kudos. En el caso de Kudos, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de **Kudos**.

1. En el menú de la parte superior, haga clic en el **icono de configuración**.

   ![Configuración](./media/kudos-tutorial/menu.png "Configuración")

1. Haga clic en **Administración de usuarios**.

1. Haga clic en la pestaña **Users** (Usuarios) y, a continuación, haga clic en **Add user** (Agregar un usuario).

   ![Administrador de usuarios](./media/kudos-tutorial/users.png "Administrador de usuarios")

1. En la sección **Agregar un usuario**, lleve a cabo los siguientes pasos:

    ![Adición de un usuario](./media/kudos-tutorial/create-users.png "Agregar un usuario")

    a. Escriba el **Nombre**, **Apellido**, **Correo electrónico** y otros detalles de una cuenta válida de Azure Active Directory que desea aprovisionar en los cuadros de texto relacionados.

    b. Haga clic en **Create User**(Crear usuario).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Kudos ofrecida por Kudos para aprovisionar cuentas de usuario de Azure AD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de Kudos, donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de Kudos e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Kudos en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de esta aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que ha configurado Kudos, puede aplicar el control de sesión, que protege a la organización en tiempo real contra la filtración y la infiltración de la información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
