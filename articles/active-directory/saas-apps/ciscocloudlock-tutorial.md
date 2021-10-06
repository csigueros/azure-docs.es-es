---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con The Cloud Security Fabric'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y The Cloud Security Fabric.
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
ms.openlocfilehash: a3b787b3ed320c5c5c1381831c7b0a24a33c1186
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619749"
---
# <a name="tutorial-azure-ad-sso-integration-with-the-cloud-security-fabric"></a>Tutorial: Integración del inicio de sesión único de Azure AD con The Cloud Security Fabric

En este tutorial, aprenderá a integrar The Cloud Security Fabric con Azure Active Directory (Azure AD). Al integrar The Cloud Security Fabric con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a The Cloud Security Fabric.
* Permitir que los usuarios inicien sesión automáticamente en The Cloud Security Fabric con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción de The Cloud Security Fabric con el inicio de sesión único habilitado.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* The Cloud Security Fabric admite el inicio de sesión único iniciado por **SP**.

## <a name="add-the-cloud-security-fabric-from-the-gallery"></a>Incorporación de The Cloud Security Fabric desde la galería

Para configurar la integración de The Cloud Security Fabric en Azure AD, debe agregar The Cloud Security Fabric desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **The Cloud Security Fabric** en el cuadro de búsqueda.
1. Seleccione **The Cloud Security Fabric** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-the-cloud-security-fabric"></a>Configuración y prueba del inicio de sesión único de Azure AD para The Cloud Security Fabric

Configure y pruebe el inicio de sesión único de Azure AD con The Cloud Security Fabric utilizando un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de The Cloud Security Fabric.

Para configurar y probar el inicio de sesión único de Azure AD con The Cloud Security Fabric, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de The Cloud Security Fabric](#configure-the-cloud-security-fabric-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de The Cloud Security Fabric](#create-the-cloud-security-fabric-test-user)**: para tener un homólogo de B.Simon en The Cloud Security Fabric que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **The Cloud Security Fabric**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:

      | **Dirección URL de inicio de sesión** |
      |--------|
      | `https://platform.cloudlock.com` |
      | `https://app.cloudlock.com` |
      
   b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con uno de los patrones siguientes:

      | **Identificador** |
      |---------|
      | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
      | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |
     
    > [!NOTE]
    > El valor del identificador no es real. Actualícelo con el identificador real. Póngase en contacto con el [equipo de soporte técnico de The Cloud Security Fabric ](mailto:support@cloudlock.com) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

5. Para modificar las opciones de **Firma** para ajustarlas a sus requisitos, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Respuesta SAML](./media/ciscocloudlock-tutorial/saml.png)

    a. Seleccione **Firmar respuesta y aserción SAML** en **Opción de firma**.

    b. Seleccione la opción **SHA-256** para **Algoritmo de firma**.

    c. Haga clic en **Save**(Guardar).  

6. En la sección **Configurar The Cloud Security Fabric**, copie las direcciones URL adecuadas según sus requisitos.

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

En esta sección, va a conceder acceso a B.Simon a The Cloud Security Fabric para que use el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **The Cloud Security Fabric**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-the-cloud-security-fabric-sso"></a>Configuración del inicio de sesión único de The Cloud Security Fabric

Para configurar el inicio de sesión único en **The Cloud Security Fabric**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL correspondientes copiadas de Azure Portal al [equipo de soporte técnico de The Cloud Security Fabric](mailto:support@cloudlock.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-the-cloud-security-fabric-test-user"></a>Creación de un usuario de prueba de The Cloud Security Fabric

En esta sección, creará el usuario B.Simon en The Cloud Security Fabric. Póngase en contacto con el [equipo de soporte técnico de The Cloud Security Fabric](mailto:support@cloudlock.com) para agregar usuario a la plataforma de The Cloud Security Fabric. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de The Cloud Security Fabric, donde puede iniciar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de The Cloud Security Fabric y comience el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de The Cloud Security Fabric en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de The Cloud Security Fabric. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado The Cloud Security Fabric, puede aplicar el control de sesión, que protege la información confidencial de su organización en tiempo real de la filtración y la infiltración. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).