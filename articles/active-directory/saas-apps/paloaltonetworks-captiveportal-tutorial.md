---
title: 'Tutorial: Integración de Azure Active Directory con Captive Portal de Palo Alto Networks | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Captive Portal de Palo Alto Networks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1258688ae17e7ad14fbe4c2516e9b44550785fed
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731943"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integración de Azure Active Directory con Captive Portal de Palo Alto Networks

En este tutorial aprenderá a integrar Captive Portal de Palo Alto Networks con Azure Active Directory (Azure AD).
La integración de Captive Portal de Palo Alto Networks con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Captive Portal de Palo Alto Networks.
* Puede permitir que los usuarios inicien sesión automáticamente en Captive Portal de Palo Alto Networks (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: Azure Portal.

## <a name="prerequisites"></a>Prerrequisitos

Para integrar Azure AD con Captive Portal de Palo Alto Networks, se necesita lo siguiente:

* Una suscripción a Azure Active Directory. Si no tiene Azure AD, puede [obtener una versión de evaluación durante un mes](https://azure.microsoft.com/pricing/free-trial/).
* Una suscripción con inicio de sesión único (SSO) de Captive Portal de Palo Alto Networks.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Captive Portal de Palo Alto Networks admite el inicio de sesión único iniciado por **IDP**.
* Captive Portal de Palo Alto Networks admite el aprovisionamiento de usuarios **Just-in-time**.

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Adición de Captive Portal de Palo Alto Networks desde la galería

Para configurar la integración de Captive Portal de Palo Alto Networks en Azure AD, es preciso agregar Captive Portal de Palo Alto Networks desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Captive Portal de Palo Alto Networks** en el cuadro de búsqueda.
1. Seleccione **Captive Portal de Palo Alto Networks** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Captive Portal de Palo Alto Networks con un usuario de prueba llamado **B.Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Captive Portal de Palo Alto Networks.

Para configurar y probar el inicio de sesión único de Azure AD con Captive Portal de Palo Alto Networks, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** : para permitir al usuario usar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con el usuario B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** : para que B.Simon pueda usar el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único de Captive Portal de Palo Alto Networks](#configure-palo-alto-networks-captive-portal-sso)** : para configurar las opciones de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba de Captive Portal de Palo Alto Networks](#create-a-palo-alto-networks-captive-portal-test-user)** : para tener un homólogo de B.Simon en Captive Portal de Palo Alto Networks que esté vinculado a la representación del usuario en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar que la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En la página de integración de la aplicación **Captive Portal de Palo Alto Networks** de Azure Portal, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En el panel **Configuración básica de SAML**, siga estos pasos:

   1. En **Identificador**, escriba una dirección URL con el patrón `https://<customer_firewall_host_name>/SAML20/SP`.

   2. En **URL de respuesta** , escriba una dirección URL con el patrón `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > En este paso, actualice los valores de los marcadores de posición con las direcciones URL del identificador y de respuesta reales. Para obtener los valores reales, póngase en contacto con el [equipo de soporte técnico del cliente de Captive Portal de Palo Alto Networks](https://support.paloaltonetworks.com/support).

5. En la sección **Certificado de firma de SAML**, al lado de **XML de metadatos de federación**, seleccione **Descargar**. Guarde el archivo descargado en el equipo.

    ![El vínculo de descarga del XML de metadatos de federación](common/metadataxml.png)

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

En esta sección, habilitará a B.Simon para usar el inicio de sesión único de Azure al concederle acceso a Captive Portal de Palo Alto Networks.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Captive Portal de Palo Alto Networks**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Configuración del inicio de sesión único de Captive Portal de Palo Alto Networks

A continuación, configure el inicio de sesión único en Captive Portal de Palo Alto Networks:

1. En otra ventana del explorador web, inicie sesión en el sitio web de Palo Alto Networks como administrador.

2. Seleccione la pestaña **Device** (Dispositivo).

    ![La pestaña Device (Dispositivo) del sitio web de Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. En el menú, seleccione **SAML Identity Provider** (Proveedor de identidades SAML) y, después, seleccione **Import** (Importar).

    ![El botón Import (Importar)](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. En el cuadro de diálogo **SAML Identity Provider Server Profile Import** (Importación de perfil de servidor del proveedor de identidades SAML), siga estos pasos:

    ![Configurar el inicio de sesión único en Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. En **Profile Name** (Nombre de perfil), escriba un nombre, como **AzureAD-CaptivePortal**.
    
    2. Al lado de **Identity Provider Metadata** (Metadatos del proveedor de identidades), seleccione **Browse** (Examinar). Seleccione el archivo metadata.xml que descargó en Azure Portal.
    
    3. Seleccione **Aceptar**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Creación de un usuario de prueba de Captive Portal de Palo Alto Networks

A continuación, cree un usuario llamado *Britta Simon* en Captive Portal de Palo Alto Networks. Captive Portal de Palo Alto Networks admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. En esta sección no es preciso realizar ninguna tarea. Si un usuario no existe aún en Captive Portal de Palo Alto Networks, se crea después de la autenticación.

> [!NOTE]
> Si desea crear un usuario manualmente, póngase en contacto con el [equipo de soporte técnico del cliente de Captive Portal de Palo Alto Networks](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Captive Portal de Palo Alto Networks para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Captive Portal de Palo Alto Networks en Aplicaciones, debería iniciar sesión automáticamente en la instancia de Captive Portal de Palo Alto Networks para la que configurara el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Captive Portal de Palo Alto Networks, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).