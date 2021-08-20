---
title: 'Tutorial: Integración de Azure Active Directory con ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ON24 Virtual Environment SAML Connection.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/14/2021
ms.author: jeedes
ms.openlocfilehash: aad8e571d477fe0cf50abccaa167ada8b64cc02c
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112200301"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Integración de Azure Active Directory con ON24 Virtual Environment SAML Connection

En este tutorial, aprenderá integrar ON24 Virtual Environment SAML Connection con Azure Active Directory (Azure AD). Al integrar ON24 Virtual Environment SAML Connection con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a ON24 Virtual Environment SAML Connection.
* Permitir que los usuarios inicien sesión automáticamente en ON24 Virtual Environment SAML Connection con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en ON24 Virtual Environment SAML Connection.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* ON24 Virtual Environment SAML Connection admite el inicio de sesión único iniciado por **SP** e **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-on24-virtual-environment-saml-connection-from-the-gallery"></a>Adición de ON24 Virtual Environment SAML Connection desde la galería

Para configurar la integración de ON24 Virtual Environment SAML Connection en Azure AD, debe agregar ON24 Virtual Environment SAML Connection desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **ON24 Virtual Environment SAML Connection** en el cuadro de búsqueda.
1. Seleccione **ON24 Virtual Environment SAML Connection** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-on24-virtual-environment-saml-connection"></a>Configuración y prueba del inicio de sesión único de Azure AD en ON24 Virtual Environment SAML Connection

Configure y pruebe el inicio de sesión único de Azure AD con ON24 Virtual Environment SAML Connection mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de ON24 Virtual Environment SAML Connection.

Para configurar y probar el inicio de sesión único de Azure AD con ON24 Virtual Environment SAML Connection, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de ON24 Virtual Environment SAML Connection](#configure-on24-virtual-environment-saml-connection-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de ON24 Virtual Environment SAML Connection](#create-on24-virtual-environment-saml-connection-test-user)** : para tener un homólogo de B.Simon en ON24 Virtual Environment SAML Connection que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **ON24 Virtual Environment SAML Connection**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en el modo iniciado por **IDP** siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba uno de los valores siguientes:

    | **Dirección URL del entorno de producción** |
    |------|
    | `SAML-VSHOW.on24.com` |
    | `SAML-Gateway.on24.com` |
    | `SAP PROD SAML-EliteAudience.on24.com` |
    |
                
    | **Dirección URL del entorno de control de calidad** |
    |-----|
    | `SAMLQA-VSHOW.on24.com` |
    | `SAMLQA-Gateway.on24.com` |
    | `SAMLQA-EliteAudience.on24.com` |
    |

    b. En el cuadro de texto **URL de respuesta**, escriba una de las siguientes direcciones URL:

    | **Dirección URL del entorno de producción** |
    |-----|
    | `https://federation.on24.com/sp/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2` |
    |

    | **Dirección URL del entorno de control de calidad** |
    |-------|
    | `https://qafederation.on24.com/sp/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` |
    |

    c. Haga clic en **Establecer direcciones URL adicionales**. 

    d. En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Si desea configurar la aplicación en modo iniciado por **SP**, realice el siguientes paso:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://vshow.on24.com/vshow/<INSTANCE_NAME>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el estado de la retransmisión y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico de ON24 Virtual Environment SAML Connection](https://www.on24.com/contact-us/) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up ON24 Virtual Environment SAML Connection** (Configurar ON24 Virtual Environment SAML Connection), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a ON24 Virtual Environment SAML Connection mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **ON24 Virtual Environment SAML Connection**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-on24-virtual-environment-saml-connection-sso"></a>Configuración del inicio de sesión único de ON24 Virtual Environment SAML Connection

Para configurar el inicio de sesión único en **ON24 Virtual Environment SAML Connection**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL adecuadas que copió de Azure Portal al [equipo de soporte técnico de ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Creación de un usuario de prueba de ON24 Virtual Environment SAML Connection

En esta sección, creará un usuario llamado Britta Simon en ON24 Virtual Environment SAML Connection. Póngase en contacto con el [equipo de soporte técnico de conexión de ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) para agregar los usuarios en la plataforma de ON24 Virtual Environment SAML Connection. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de ON24 Virtual Environment SAML, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de ON24 Virtual Environment SAML Connection e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de ON24 Virtual Environment SAML Connection para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de ON24 Virtual Environment SAML Connection en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de ON24 Virtual Environment SAML Connection para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado ON24 Virtual Environment SAML Connection, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
