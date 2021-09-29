---
title: 'Tutorial: Integración de Azure Active Directory con Costpoint | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: dc4297e677dd94db6e3464d20a65784dff441b38
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779186"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integración de Costpoint con Azure Active Directory

En este tutorial, aprenderá a integrar Costpoint con Azure Active Directory (Azure AD). Al integrar Costpoint con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Costpoint.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Costpoint con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Costpoint.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. 

* Costpoint admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="generate-costpoint-metadata"></a>Generación de metadatos de Costpoint

La configuración del inicio de sesión único de SAML de Costpoint se explica en la guía **DeltekCostpoint711Security.pdf**. Descargue esta guía desde el sitio de soporte técnico de Deltek Costpoint y consulte la sección **SAML Single Sign-on Setup** > **Configure SAML Single Sign-on between Costpoint and Microsoft Azure** (Configuración del inicio de sesión único de SAML -> Configuración del inicio de sesión único de SAML entre Costpoint y Microsoft Azure). Siga las instrucciones y genere un archivo **XML de metadatos de federación del proveedor de servicios de Costpoint**. 

![Captura de pantalla que muestra la utilidad de configuración del producto con la pestaña "Weblogic - Security" (Weblogic - Seguridad) seleccionada.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adición de Costpoint desde la galería

Para configurar la integración de Costpoint en Azure AD, debe agregar Costpoint desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Costpoint** en el cuadro de búsqueda.
1. Seleccione **Costpoint** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Configuración y prueba del inicio de sesión único de Azure AD para Costpoint

Configure y pruebe el inicio de sesión único de Azure AD con Costpoint mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Costpoint.

Para configurar y probar el inicio de sesión único de Azure AD con Costpoint, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Costpoint](#configure-costpoint-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Costpoint](#create-costpoint-test-user)** : para tener un homólogo de B.Simon en Costpoint que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal:

1. En la página de integración de la aplicación **Costpoint**, seleccione **Inicio de sesión único**.

1. En la sección **Configuración básica de SAML**, si tiene el **archivo de metadatos del proveedor de servicios**, lleve a cabo estos pasos:

   > [!NOTE]
   > Puede obtener el archivo de metadatos del proveedor de servicios en [Generación de metadatos de Costpoint](#generate-costpoint-metadata). El uso del archivo se explica posteriormente en este tutorial.
 
   1. Seleccione el botón **Cargar el archivo de metadatos** y, a continuación, seleccione el archivo **XML de metadatos de federación del proveedor de servicios de Costpoint** generado anteriormente por Costpoint y, finalmente, seleccione el botón **Agregar** para cargar el archivo.

      ![Carga del archivo de metadatos](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Cuando se haya cargado correctamente el archivo de metadatos, los valores **Identificador** y **URL de respuesta** se rellenarán automáticamente en la sección de Costpoint.

      > [!NOTE]
      > Si los valores **Identificador** y **Dirección URL de respuesta** no se rellenan automáticamente, escríbalos manualmente según sus necesidades. Compruebe que los valores de **Identificador (id. de entidad)** y **Dirección URL de respuesta (URL del Servicio de consumidor de aserciones)** estén establecidos correctamente y que la **dirección URL de ACS** sea una dirección URL válida de Costpoint que finalice con **/LoginServlet.cps**.

   1. Seleccione **Establecer direcciones URL adicionales**. En **Estado de la retransmisión**, escriba un valor que utilice el siguiente patrón:`system=[your system]` (por ejemplo, **system=DELTEKCP**).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione el icono **Copiar** para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en el Bloc de notas.

   ![Certificado de firma SAML](common/copy-metadataurl.png)

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

En esta sección, va a permitir que B. Simon acceda a Costpoint mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Costpoint**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-costpoint-sso"></a>Configuración del inicio de sesión único de Costpoint

1. Vuelva a la utilidad de configuración de Costpoint. En el cuadro de texto **IdP Federation Metadata XML** (XML de metadatos de federación de IdP), pegue el contenido del archivo de la *dirección URL de metadatos de federación de aplicación*. 

   ![Utilidad de configuración de Costpoint](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Siga las instrucciones de la guía **DeltekCostpoint711Security.pdf** para finalizar la configuración de SAML de Costpoint.

### <a name="create-costpoint-test-user"></a>Creación de un usuario de prueba de Costpoint

En esta sección, creará un usuario en Costpoint. Suponga que el identificador de usuario es **B.SIMON** y que el nombre de usuario es **B.Simon**. Trabaje con el [equipo de atención al cliente de Costpoint](https://www.deltek.com/about/contact-us) para agregar el usuario a la plataforma de Costpoint. El usuario se debe crear y activar antes de poder usar el inicio de sesión único.

Una vez creado, la selección de **Método de autenticación** del usuario debe ser **Active Directory**, la casilla **Inicio de sesión único de SAML** debe estar activada y el nombre de usuario de Azure Active Directory debe ser **Active Directory or Certificate ID** (Active Directory o id. de certificado) (como se muestra en la captura de pantalla siguiente).

![Usuario de Costpoint](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Costpoint, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Costpoint e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal y debería iniciar sesión automáticamente en la instancia de Costpoint para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Costpoint en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, debería iniciar sesión automáticamente en la instancia de Costpoint para la que haya configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Costpoint, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).