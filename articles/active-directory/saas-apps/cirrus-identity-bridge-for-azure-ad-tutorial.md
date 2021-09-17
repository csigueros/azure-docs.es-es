---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cirrus Identity Bridge para Azure AD | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cirrus Identity Bridge para Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: jeedes
ms.openlocfilehash: 17a00a0a18039098c83bb7ce178207b67786b947
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cirrus-identity-bridge-for-azure-ad"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Cirrus Identity Bridge para Azure AD

En este tutorial aprenderá a integrar Cirrus Identity Bridge para Azure AD con Azure Active Directory (Azure AD). Al integrar Cirrus Identity Bridge para Azure AD con Azure AD, puede:

* Controlar desde Azure AD quién tiene acceso a Cirrus Identity Bridge para Azure AD.
* Permitir que los usuarios inicien sesión automáticamente en Cirrus Identity Bridge para Azure AD con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) en Cirrus Identity Bridge para Azure AD. Si aún no es suscriptor, visite la [página de registro de Cirrus Identity Bridge para Azure AD](https://info.cirrusidentity.com/cirrus-identity-azure-ad-app-gallery-registration).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Cirrus Identity Bridge para Azure AD admite el inicio de sesión único iniciado por **SP** e **IDP**.

## <a name="add-cirrus-identity-bridge-for-azure-ad-from-the-gallery"></a>Adición de Cirrus Identity Bridge para Azure AD desde la galería

Para configurar la integración de Cirrus Identity Bridge para Azure AD en Azure AD, es preciso que agregue Cirrus Identity Bridge para Azure AD desde la galería a la lista de aplicaciones de SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Cirrus Identity Bridge para Azure AD** en el cuadro de búsqueda.
1. Seleccione **Cirrus Identity Bridge para Azure AD** en el panel de resultados y, a continuación, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cirrus-identity-bridge-for-azure-ad"></a>Configuración y prueba del inicio de sesión único de Azure AD para Cirrus Identity Bridge para Azure AD

Configure y pruebe el inicio de sesión único de Azure AD con Cirrus Identity Bridge para Azure AD utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Cirrus Identity Bridge para Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con Cirrus Identity Bridge para Azure AD, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Cirrus Identity Bridge para Azure AD](#configure-cirrus-identity-bridge-for-azure-ad-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Configuración de pruebas de Cirrus Identity Bridge para Azure AD](#setup-cirrus-identity-bridge-for-azure-ad-testing)** : para tener un homólogo de B.Simon en Cirrus Identity Bridge para Azure AD que esté vinculado a la representación Azure AD del usuario.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Cirrus Identity Bridge para Azure AD**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `https://<SUBDOMAIN>.cirrusidentity.com/bridge`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<NAME>.proxy.cirrusidentity.com/module.php/saml/sp/saml2-acs.php/<NAME>_proxy`

1. Haga clic en Establecer direcciones URL adicionales y siga este paso si desea configurar la aplicación en el modo iniciado por SP:
 
    En el cuadro de texto **URL de inicio de sesión**, escriba un valor con el siguiente patrón: `<CUSTOMER_LOGIN_URL>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Si aún no se ha suscrito a  Cirrus Bridge, visite la [página de registro](https://info.cirrusidentity.com/cirrus-identity-azure-ad-app-gallery-registration). Si ya es cliente de Cirrus Bridge, póngase en contacto con el equipo de soporte técnico de [Cirrus Identity Bridge para Azure AD](https://www.cirrusidentity.com/resources/service-desk) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Cirrus Identity Bridge para Azure AD espera que las aserciones SAML estén en un formato específico, así que es necesario agregar asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Cirrus Identity Bridge para Azure AD espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.
    
    | Nombre |  Atributo de origen|
    | ---------| --------- |
    | displayname | user.displayname |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar la **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

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

En esta sección va a permitir que B.Simon acceda a Cirrus Identity Bridge para Azure AD mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Cirrus Identity Bridge para Azure AD**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-cirrus-identity-bridge-for-azure-ad-sso"></a>Configuración de Cirrus Identity Bridge para Azure AD para el inicio de sesión único

Para configurar el inicio de sesión único en **Cirrus Identity Bridge para Azure AD**, es preciso enviar el valor de **Dirección URL de metadatos de federación de aplicación**  al [equipo de soporte técnico de Cirrus Identity Bridge para Azure AD](https://www.cirrusidentity.com/resources/service-desk). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="setup-cirrus-identity-bridge-for-azure-ad-testing"></a>Configurar pruebas de Cirrus Identity Bridge para Azure AD

En esta sección, comprobará que se puede usar un usuario llamado Britta Simon para las pruebas. El [equipo de soporte técnico de Cirrus Identity Bridge para Azure AD](https://www.cirrusidentity.com/resources/service-desk) proporcionará una dirección URL de prueba para comprobar que Britta Simon está lista para usarse con la plataforma Cirrus Identity Bridge para Azure AD. El usuario de prueba Britta Simon también tendrá que agregarse a cualquier aplicación que use Cirrus Identity Bridge para Azure AD como método para autenticarse (por ejemplo, aplicaciones en metadatos de federación multilateral). 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Cirrus Identity Bridge para Azure AD, donde puede comenzar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Cirrus Identity Bridge para Azure AD y comience el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Cirrus Identity Bridge para Azure AD para la que configurara el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Cirrus Identity Bridge para Azure AD en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Cirrus Identity Bridge para Azure AD para la que se configurara el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Cirrus Identity Bridge para Azure AD, podrá aplicar el control de sesión, que protege la información confidencial de la organización de la exfiltración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).