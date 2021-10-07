---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con ZenQMS'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ZenQMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2021
ms.author: jeedes
ms.openlocfilehash: 82a2d5f9554a90f514a3ec4f2d76c8b4608703ea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661392"
---
# <a name="tutorial-azure-ad-sso-integration-with-zenqms"></a>Tutorial: Integración del inicio de sesión único de Azure AD con ZenQMS

En este tutorial, aprenderá a integrar ZenQMS con Azure Active Directory (Azure AD). Al integrar ZenQMS con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ZenQMS.
* Permitir que los usuarios puedan iniciar sesión automáticamente en ZenQMS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ZenQMS, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único de ZenQMS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* ZenQMS admite el inicio de sesión único iniciado por **SP** e **IDP**.

## <a name="add-zenqms-from-the-gallery"></a>Adición de ZenQMS desde la galería

Para configurar la integración de ZenQMS en Azure AD, deberá agregar ZenQMS desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ZenQMS** en el cuadro de búsqueda.
1. Seleccione **ZenQMS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zenqms"></a>Configuración y prueba del SSO de Azure AD para ZenQMS

Configure y pruebe el inicio de sesión único de Azure AD con ZenQMS mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de ZenQMS.

Para configurar y probar el inicio de sesión único de Azure AD con ZenQMS, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de ZenQMS](#configure-zenqms-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de ZenQMS](#create-zenqms-test-user)** : para tener un homólogo de B.Simon en ZenQMS que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **ZenQMS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba un valor con el siguiente patrón: `urn:zenqms:<INSTANCE>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<INSTANCE>.zenqms.com/SAML/AssertionConsumerService`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

    | **URL de inicio de sesión** |
    |----------|
    | `https://<INSTANCE>.zenqms.com/<ID>` |
    | `https://<INSTANCE>.zenqms.com/<EMAIL DOMAIN>/` |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico para clientes de ZenQMS](mailto:help@zenqms.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

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

En esta sección, va a permitir que B.Simon acceda a ZenQMS mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **ZenQMS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-zenqms-sso"></a>Configuración del inicio de sesión único de ZenQMS

Para configurar el inicio de sesión único en **ZenQMS**, debe enviar la **dirección URL de metadatos de federación de la aplicación** al [equipo de soporte técnico de ZenQMS](mailto:help@zenqms.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-zenqms-test-user"></a>Creación de un usuario de prueba de ZenQMS

En esta sección, creará un usuario llamado Britta Simon en ZenQMS. Trabaje con el [equipo de soporte técnico de ZenQMS](mailto:help@zenqms.com) para agregar los usuarios a la plataforma de ZenQMS. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de ZenQMS, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de ZenQMS e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de ZenQMS para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de ZenQMS en Aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de ZenQMS para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado ZenQMS, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).