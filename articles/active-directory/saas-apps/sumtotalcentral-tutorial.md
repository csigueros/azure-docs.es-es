---
title: 'Tutorial: integración de Azure Active Directory con SumTotalCentral | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SumTotalCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 2ff2ea6b4c230a2156a7a63287e8d940d1cb6f98
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124752076"
---
# <a name="tutorial-azure-active-directory-integration-with-sumtotalcentral"></a>Tutorial: integración de Azure Active Directory con SumTotalCentral

En este tutorial, obtendrá información sobre cómo integrar SumTotalCentral con Azure Active Directory (Azure AD).
La integración de SumTotalCentral con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a SumTotalCentral.
* Puede permitir que los usuarios inicien sesión automáticamente en SumTotalCentral (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Prerequisites

Para configurar la integración de Azure AD con SumTotalCentral, se necesitan los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/)
* Una suscripción habilitada para el inicio de sesión único en SumTotalCentral

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SumTotalCentral admite el inicio de sesión único iniciado por **SP**.

* Una vez que haya configurado SumTotalCentral, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
    
> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="adding-sumtotalcentral-from-the-gallery"></a>Agregar SumTotalCentral desde la galería

Para configurar la integración de SumTotalCentral en Azure AD, será preciso que agregue SumTotalCentral desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SumTotalCentral** en el cuadro de búsqueda.
1. Seleccione **SumTotalCentral** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SumTotalCentral utilizando un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SumTotalCentral.

Para configurar y probar el inicio de sesión único de Azure AD con SumTotalCentral, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en SumTotalCentral](#configure-sumtotalcentral-sso)** , para definir los valores de Inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba de SumTotalCentral](#create-sumtotalcentral-test-user)**: para tener un homólogo de Britta Simon en SumTotalCentral vinculado a la representación del usuario de Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de la aplicación **SumTotalCentral**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    ![Información sobre dominio y direcciones URL de inicio de sesión único de SumTotalCentral](common/sp-identifier.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.sumtotalsystems.com/sites/default`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba el valor: `SumTotalFederationGateway`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón:    
    `https://<subdomain>.sumtotalsystems.com/Broker/Token/CUSTOM_URL`

    > [!NOTE]
    > Estos valores no son reales. Actualice el valor con la dirección URL de inicio de sesión y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico para clientes de SumTotalCentral](http://www.sumtotalsystems.com/support/) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

6. En la sección **Set up SumTotalCentral** (Configurar SumTotalCentral), copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

    a. URL de inicio de sesión

    b. Identificador de Azure AD

    c. URL de cierre de sesión

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD 

En esta sección, creará un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. En la parte superior de la pantalla, seleccione **Nuevo usuario**.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba **B.Simon**.  
   1. En el campo **Nombre de usuario**, escriba `<username>@<companydomain>.<extension>`. Por ejemplo: `B.Simon@contoso.com`.
   1. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a SumTotalCentral mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SumTotalCentral**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sumtotalcentral-sso"></a>Configuración del inicio de sesión único de SumTotalCentral

Para configurar el inicio de sesión único en **SumTotalCentral**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de SumTotalCentral](http://www.sumtotalsystems.com/support/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-sumtotalcentral-test-user"></a>Creación de un usuario de prueba de SumTotalCentral

En esta sección, creará un usuario llamado Britta Simon en SumTotalCentral. Trabaje con el [equipo de soporte técnico de SumTotalCentral](http://www.sumtotalsystems.com/support/) para agregar los usuarios en la plataforma de SumTotalCentral. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SumTotalCentral en el panel de acceso, debería iniciar sesión automáticamente en la versión de SumTotalCentral para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](./tutorial-list.md)

- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [¿Qué es el acceso condicional en Azure Active Directory?](../conditional-access/overview.md)