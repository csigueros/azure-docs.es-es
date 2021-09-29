---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con BeyondTrust Remote Support | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y BeyondTrust Remote Support.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: d4f00d555c8834f21ca8492401d5101c80b527c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739818"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con BeyondTrust Remote Support

En este tutorial aprenderá a integrar BeyondTrust Remote Support con Azure Active Directory (Azure AD). Al integrar BeyondTrust Remote Support con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a BeyondTrust Remote Support.
* Permitir que los usuarios inicien sesión automáticamente en BeyondTrust Remote Support con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en BeyondTrust Remote Support.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* BeyondTrust Remote Support admite el inicio de sesión único iniciado por **SP**
* BeyondTrust Remote Support admite el aprovisionamiento de usuarios **Just-In-Time**

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Incorporación de BeyondTrust Remote Support desde la galería

Para configurar la integración de BeyondTrust Remote Support en Azure AD, es preciso agregarla desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **BeyondTrust Remote Support** en el cuadro de búsqueda.
1. Seleccione **BeyondTrust Remote Support** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-beyondtrust-remote-support"></a>Configuración y prueba del inicio de sesión único de Azure AD para BeyondTrust Remote Support

Configure y pruebe el inicio de sesión único de Azure AD con BeyondTrust Remote Support mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de BeyondTrust Remote Support.

Para configurar y probar el inicio de sesión único de Azure AD con BeyondTrust Remote Support, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en BeyondTrust Remote Support](#configure-beyondtrust-remote-support-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en BeyondTrust Remote Support](#create-beyondtrust-remote-support-test-user)** : para tener un homólogo de B.Simon en BeyondTrust Remote Support vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **BeyondTrust Remote Support**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<HOSTNAME>.bomgar.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<HOSTNAME>.bomgar.com/saml/sso`
    
    c. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<HOSTNAME>.bomgar.com/saml`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Estos valores se explican más adelante en el tutorial.

1. La aplicación BeyondTrust Remote Support espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, BeyondTrust Remote Support espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | Nombre |  Atributo de origen|
    | ---------------| ----------|
    | Nombre de usuario | user.userprincipalname |
    | Nombre | user.givenname |
    | Apellidos | user.surname |
    | Email | user.mail |
    | Grupos | user.groups |

    > [!NOTE]
    > Al asignar grupos de Azure AD para la aplicación BeyondTrust Remote Support, la opción "Grupos devueltos en la notificación" deberá modificarse de Ninguno a SecurityGroup. Los grupos se importarán en la aplicación con sus identificadores de objeto. El identificador de objeto del grupo de Azure AD se encuentra al comprobar las propiedades en la interfaz de Azure Active Directory. Esto será necesario para hacer referencia a los grupos de Azure AD y asignar estos a las directivas de grupo correctas.

1. Al establecer el identificador de usuario único, este valor debe establecerse en NameID-Format: **Persistente**. Es necesario que este sea un identificador persistente para identificar correctamente y asociar al usuario a las directivas de grupo correctas para los permisos. Haga clic en el icono de edición para abrir el cuadro de diálogo **Atributos y reclamaciones del usuario** para editar el valor de identificador de usuario único.

1. En la sección **Administración de la notificación**, haga clic en **Elija el formato del identificador de nombre**, establezca el valor en **Persistente** y haga clic en **Guardar**.

    ![Atributos y reclamaciones del usuario](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up BeyondTrust Remote Support** (Configurar BeyondTrust Remote Support), copie las direcciones URL que necesite.

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

En esta sección va a permitir que B.Simon acceda a BeyondTrust Remote Support mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **BeyondTrust Remote Support**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-beyondtrust-remote-support-sso"></a>Configuración del inicio de sesión único en BeyondTrust Remote Support

1. En otra ventana del explorador web, inicie sesión en el inquilino de BeyondTrust Remote Support como administrador.

1. Vaya a **Users & Security** > **Security Providers** (Usuarios y seguridad > Proveedores de seguridad).

1. Haga clic en el icono **Edit** (Editar) en **SAML Providers** (Proveedores de SAML).

    ![Icono de edición de proveedores de SAML](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Expanda la sección **Service Provider Settings** (Configuración del proveedor de servicios).

1. Haga clic en **Download Service Provider Metadata** (Descargar metadatos del proveedor de servicios) o bien copie los valores de **Entity ID** (Id. de entidad) y **ACS URL** (URL de ACS) y use estos valores en la sección **Configuración básica de SAML** de Azure Portal.

    ![Descarga de metadatos del proveedor de servicios](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. En la sección Identity Provider Settings (Configuración del proveedor de identidad), haga clic en **Upload Identity Provider Metadata** (Cargar metadatos del proveedor de identidades) y busque el archivo XML de metadatos que ha descargado de Azure Portal.

1.  Los valores de **Entity ID** (Id. de entidad), **Single Sign-On Service URL** (URL de servicio de inicio de sesión único) y **Server Certificate** (Certificado de servidor) se cargarán automáticamente; **SSO URL Protocol Binding** (Enlace de protocolo de URL de inicio de sesión único) deberá cambiarse a **HTTP POST**.

    ![Captura de pantalla que muestra la sección Configuración del proveedor de identidades para realizar estas acciones.](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Haga clic en **Guardar**.

### <a name="create-beyondtrust-remote-support-test-user"></a>Creación de un usuario de prueba en BeyondTrust Remote Support

En esta sección se crea un usuario llamado B.Simmon en BeyondTrust Remote Support. BeyondTrust Remote Support admite el aprovisionamiento de usuarios Just-In-Time, habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en BeyondTrust Remote Support, se crea uno después de la autenticación.

Siga el procedimiento siguiente, obligatorio para configurar BeyondTrust Remote Support.

Aquí se configurarán las opciones de aprovisionamiento de usuarios. Se hará referencia a los valores de esta sección desde la sección **Atributos y reclamaciones del usuario** de Azure Portal. Los hemos configurado como los valores predeterminados que ya se han importado en el momento de la creación; no obstante, se pueden personalizar.

![Captura de pantalla que muestra User Provision Settings (Configurar aprovisionamiento de usuarios) para configurar los valores de los usuarios.](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> Los grupos y el atributo e-mail no son necesarios para esta implementación. Si utiliza grupos de Azure AD y los asigna a las directivas de grupo de BeyondTrust Remote Support para los permisos, será necesario hacer referencia al identificador de objeto del grupo a través de sus propiedades en Azure Portal y colocarlo en la sección "Available Groups" (Grupos disponibles). Cuando haya terminado, el identificador de objeto o el grupo de AD estarán ya disponibles para su asignación a una directiva de grupo para los permisos.

![Captura de pantalla que muestra la sección IT (TI) con Membership type (Tipo de pertenencia), Source (Origen), Type (Tipo) y Object ID (Id. de objeto).](./media/bomgarremotesupport-tutorial/config-user-2.png)

![Captura de pantalla muestra la página Basic Settings (Configuración básica) de una directiva de grupo.](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> Como alternativa, se puede establecer una directiva de grupo predeterminada en el proveedor de seguridad SAML2. Al definir esta opción, se asignarán los permisos especificados en la directiva de grupo a todos los usuarios que se autentiquen mediante SAML. La directiva General Members (Miembros generales) está incluida en BeyondTrust Remote Support/Privileged Remote Access (Acceso privilegiado remoto) con permisos limitados y se puede usar para probar la autenticación y asignar usuarios a las directivas correctas. Los usuarios no se rellenarán en la lista de usuarios de SAML2 a través de /login > Users & Security (Usuarios y seguridad) hasta el primer intento de autenticación correcto. Puede encontrar más información sobre las directivas de grupo en el vínculo siguiente: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de BeyondTrust Remote Support, donde puede iniciar el flujo de inicio de sesión. 

* Vaya directamente a la dirección URL de inicio de sesión de BeyondTrust Remote Support e inicie el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de BeyondTrust Remote Support en Mis aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de BeyondTrust Remote Support. Para más información acerca del portal Mis aplicaciones, consulte [Introducción a Mis aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado BeyondTrust Remote Support, puede aplicar controles de sesión, que protegen a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. Los controles de sesión proceden del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).