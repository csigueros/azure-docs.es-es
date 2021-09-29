---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Paylocity | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Paylocity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: a381ae4ec8decba83d16b367d4dd8f621192449c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124783147"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Paylocity

En este tutorial aprenderá a integrar Paylocity con Azure Active Directory (Azure AD). Al integrar Paylocity con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Paylocity.
* Permitir que los usuarios inicien sesión automáticamente en Paylocity con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Paylocity.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Paylocity admite el inicio de sesión único iniciado por **SP e IDP**.

## <a name="add-paylocity-from-the-gallery"></a>Incorporación de Paylocity desde la galería

Para configurar la integración de Paylocity en Azure AD, deberá agregar Paylocity desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Paylocity** en el cuadro de búsqueda.
1. Seleccione **Paylocity** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-paylocity"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Paylocity

Configure y pruebe el inicio de sesión único de Azure AD con Paylocity mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Paylocity.

Para configurar y probar el inicio de sesión único de Azure AD con Paylocity, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Paylocity](#configure-paylocity-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en Paylocity](#create-paylocity-test-user)** , para tener un homólogo de B.Simon en Paylocity que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones **Paylocity**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, el usuario no tiene que realizar ningún paso porque la aplicación ya se ha integrado previamente con Azure.

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://access.paylocity.com/`

1. Haga clic en **Save**(Guardar).

1. La aplicación Paylocity espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Paylocity espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero tiene que actualizarlos con los valores reales.

    | Nombre |  Atributo de origen|
    | ---------------| --------------- |
    | PartnerID | `P8000010` |
    | PaylocityUser | `user.mail`|
    | PaylocityEntity | < `PaylocityEntity` > |

    > [!NOTE]
    > PaylocityEntity es el identificador de compañía de Paylocity.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Editar icono**.

    ![Captura de pantalla que muestra la sección "Certificado de firma de S A M L" con la acción "Descargar" para "X M L de metadatos de federación" seleccionada.](./media/paylocity-tutorial/edit-samlassertion.png)

1. Seleccione **Opción de firma** como **Firmar respuesta y aserción SAML** y haga clic en **Guardar**.

    ![Edición del certificado de firma SAML](./media/paylocity-tutorial/saml-assertion.png)

1. En la sección **Configurar Paylocity**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, habilitará a B.Simon para que use el inicio de sesión único de Azure concediéndole acceso a Paylocity.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Paylocity**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-paylocity-sso"></a>Configuración del inicio de sesión único de Paylocity

Para configurar el inicio de sesión único en el **Paylocity**, siga estos pasos:

1. Descargue el archivo **XML de metadatos de federación**.
1. En Paylocity, vaya a **HR & Payroll** > **User Access** > **SSO Configuration** (RR. HH. y nómina > Acceso de usuario > Configuración de SSO).
1. Seleccione **Add SSO Integration** (Agregar integración de SSO) en **SSO Integrations**  (Integraciones de SSO). Se abrirá un nuevo cajón.
1. Seleccione **Microsoft Azure** como el proveedor de SSO en la lista desplegable.
1. Seleccione **Status** (Estado) en la lista desplegable.
1. Arrastre y coloque el archivo de metadatos en el área de colocación. Paylocity intentará analizar las direcciones URL de emisor, redireccionamiento posterior y enlace, y los certificados de seguridad.
1. Seleccione **Guardar** para confirmar los cambios. La integración se debe mostrar en **SSO Integrations** (Integraciones de SSO).

### <a name="create-paylocity-test-user"></a>Creación de un usuario de prueba de Paylocity

En esta sección, va a crear un usuario llamado B.Simon en Paylocity. Trabaje con el [equipo de soporte técnico de Paylocity](mailto:service@paylocity.com) para agregar los usuarios a la plataforma de Paylocity. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. De este modo, accederá automáticamente a la dirección URL de inicio de sesión de Paylocity, donde podrá comenzar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de Paylocity y comience el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* En Azure Portal, haga clic en **Probar esta aplicación**. Al hacerlo, debería iniciar sesión automáticamente en la instancia de Paylocity en la que haya configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Cuando en Aplicaciones haga clic en el icono de Paylocity, si seleccionó el modo SP en la configuración, debería acceder automáticamente a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Por el contrario, si seleccionó el modo IDP en la configuración, debería iniciar sesión automáticamente en la instancia de Paylocity en la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Paylocity, podrá aplicar el control de sesión, que protege la información confidencial de la organización en tiempo real de posibles filtraciones. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).