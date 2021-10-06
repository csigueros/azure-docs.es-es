---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Appaegis Isolation Access Cloud'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Appaegis Isolation Access Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: a9f5a22c8a76af8f57845d8f0e1ba2ebfb0c28f6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839111"
---
# <a name="tutorial-azure-ad-sso-integration-with-appaegis-isolation-access-cloud"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Appaegis Isolation Access Cloud

En este tutorial aprenderá a integrar ABBYY Appaegis Isolation Access Cloud con Azure Active Directory (Azure AD). Al integrar Appaegis Isolation Access Cloud con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Appaegis Isolation Access Cloud.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Appaegis Isolation Access Cloud con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Appaegis Isolation Access Cloud.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Appaegis Isolation Access Cloud admite el inicio de sesión único iniciado por **SP e IDP**.
* Appaegis Isolation Access Cloud admite el aprovisionamiento de usuarios **Just in Time**.

## <a name="adding-appaegis-isolation-access-cloud-from-the-gallery"></a>Adición de Appaegis Isolation Access Cloud desde la galería

Para configurar la integración de Appaegis Isolation Access Cloud en Azure AD, necesita agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Appaegis Isolation Access Cloud** en el cuadro de búsqueda.
1. Seleccione **Appaegis Isolation Access Cloud** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-appaegis-isolation-access-cloud"></a>Configuración y prueba del inicio de sesión único de Azure AD para Appaegis Isolation Access Cloud

Configure y pruebe el inicio de sesión único de Azure AD con Appaegis Isolation Access Cloud mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Appaegis Isolation Access Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Appaegis Isolation Access Cloud, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Appaegis Isolation Access Cloud](#configure-appaegis-isolation-access-cloud-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Appaegis Isolation Access Cloud](#create-appaegis-isolation-access-cloud-test-user)** : para tener un homólogo de B. Simon en Appaegis Isolation Access Cloud que esté vinculado a la representación de Azure AD del usuario.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Appaegis Isolation Access Cloud**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.appaegis.net`

    b. En el cuadro de texto **Estado de la retransmisión**, escriba un valor con el siguiente patrón:`<RelayState>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de la dirección URL de respuesta y el estado de la retransmisión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico al cliente de Appaegis Isolation Access Cloud](mailto:support@appaegis.com). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Appaegis Isolation Access Cloud espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Appaegis Isolation Access Cloud espera que pasen a la respuesta de SAML algunos atributos más, que se muestran a continuación. Estos atributos también se han rellenado previamente, pero los puede revisar según sus requisitos.
    
    | Nombre | Atributo de origen |
    | ---------| --------- |
    | email | user.userprincipalname |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up Appaegis Isolation Access Cloud** (Configurar Appaegis Isolation Access Cloud), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B. Simon acceda a Appaegis Isolation Access Cloud mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Appaegis Isolation Access Cloud**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-appaegis-isolation-access-cloud-sso"></a>Configuración del inicio de sesión único de Appaegis Isolation Access Cloud

1. Inicie sesión como administrador en su sitio de la compañía de Appaegis Isolation Access Cloud.

1. Vaya a **Configuración** > **Personalización**, escriba una **dirección URL del dominio del inquilino** en el cuadro de texto y haga clic en el botón **+IdP**.

    ![Captura de pantalla que muestra la personalización de la cuenta.](./media/appaegis-isolation-access-cloud-tutorial/account.png "Personalización de la cuenta")

1. En la página **Identity Provider Details** (Detalles del proveedor de identidades), siga estos pasos.

    ![Captura de pantalla que muestra los detalles del proveedor de identidades.](./media/appaegis-isolation-access-cloud-tutorial/details.png "Proveedor de identidades") 

    1. Seleccione **Azure AD** en la lista desplegable de **Proveedor de identidades**.

    1. Copie el valor de **ACS URL** (URL de ACS), péguelo en el cuadro de texto **Dirección URL de respuesta**, en la sección **Configuración básica de SAML** de Azure Portal.

    1. Copie el valor de **Entity ID** (Identificador de entidad) y péguelo en el cuadro de texto **Identificador** de la sección **Configuración básica de SAML** de Azure Portal.

    1. Abra el archivo **XML de metadatos de federación** descargado de Azure Portal en el Bloc de notas y cargue el archivo en **Carga de archivos de SAML**.

    1. Active la casilla **Estado** y haga clic en **Guardar**.

### <a name="create-appaegis-isolation-access-cloud-test-user"></a>Creación de un usuario de prueba en Appaegis Isolation Access Cloud

En esta sección se crea un usuario llamado B. Simon en Appaegis Isolation Access Cloud. Appaegis Isolation Access Cloud admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario no existe en Appaegis Isolation Access Cloud, se creará uno después de la autenticación.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Appaegis Isolation Access Cloud, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Appaegis Isolation Access Cloud e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal, y debería iniciarse sesión automáticamente en la instancia de Appaegis Isolation Access Cloud para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Appaegis Isolation Access Cloud en Aplicaciones, si se ha configurado en el modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión. Si se ha configurado en modo IDP, debería iniciarse sesión automáticamente en la instancia de Appaegis Isolation Access Cloud para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Appaegis Isolation Access Cloud, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).