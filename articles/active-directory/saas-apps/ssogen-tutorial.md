---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE.
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
ms.openlocfilehash: d968b0417b65d2297a057968dc7ea64f96b39783
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800866"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE

En este tutorial, aprenderá a integrar SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE con Azure Active Directory (Azure AD). Al integrar SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE.
* Permitir que los usuarios inicien sesión automáticamente en SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE admite el inicio de sesión único iniciado por **SP e IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Adición de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE desde la galería

Para configurar la integración de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE desde la galería, tiene que agregar esta aplicación a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE** en el cuadro de búsqueda.
1. Seleccione **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE** en el panel de resultados y después agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Configuración y prueba del inicio de sesión único de Azure AD para SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE

Configure y pruebe el inicio de sesión único de Azure AD con SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE.

Para configurar y probar el inicio de sesión único de Azure AD con SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** : para tener un homólogo de B.Simon en SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE Client](mailto:support@ssogen.com) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE application espera las aserciones de SAML en un formato específico que requiere que agregue asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE application espera que el valor de **nameidentifier** (identificador) se asigne a **user.onpremisessamaccountname**, por lo que debe hacer clic en el icono **Editar** y cambiar la asignación de atributos.

    ![imagen](common/edit-attribute.png)

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

En esta sección, va a permitir que B.Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>Configuración del inicio de sesión único de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE

Para configurar el inicio de sesión único en **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE**, busque la documentación del registro SSO específico de la aplicación a continuación:

* Oracle EBS. Integración de SSO de Azure AD: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft. Integración de SSO de Azure AD: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards. integración de SSO de Azure AD: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache. Integración de SSO de Azure AD: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>Creación de un usuario de prueba de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE

Azure AD envía un identificador de usuario único (id. de nombre) a la aplicación de usuario, una vez que la autenticación se realiza correctamente.  Asegúrese de que el identificador de usuario único (id. de nombre) coincida con el registro del usuario en la aplicación, por ejemplo, FND_USER.USER_NAME en Oracle EBS.

Póngase en contacto con [info@ssogen.com](mailto:info@ssogen.com) y [support@ssogen.com](mailto:support@ssogen.com) para obtener soporte técnico.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE para la que configuró el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE en la página Aplicaciones, si está configurado en el modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para iniciar el flujo de inicio de sesión y, si está configurado en el modo IDP, debe iniciar sesión automáticamente en la instancia de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE para la que se ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE, puede aplicar controles de sesión, que protegen la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).