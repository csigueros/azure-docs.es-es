---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con InfinityQS ProFicient on Demand'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory e InfinityQS ProFicient on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2021
ms.author: jeedes
ms.openlocfilehash: c0e2296fab5ccf6c046fb292fe6de84ba47817c2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478220"
---
# <a name="tutorial-azure-ad-sso-integration-with-infinityqs-proficient-on-demand"></a>Tutorial: Integración del inicio de sesión único de Azure AD con InfinityQS ProFicient on Demand

En este tutorial, aprenderá a integrar InfinityQS ProFicient on Demand con Azure Active Directory (Azure AD). Al integrar InfinityQS ProFicient on Demand con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a InfinityQS ProFicient on Demand.
* Permitir que los usuarios inicien sesión automáticamente en InfinityQS ProFicient on Demand con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Prerrequisitos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en InfinityQS ProFicient on Demand.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* InfinityQS ProFicient on Demand admite el inicio de sesión único iniciado por **IDP**.

## <a name="add-infinityqs-proficient-on-demand-from-the-gallery"></a>Adición de InfinityQS ProFicient on Demand desde la galería

Para configurar la integración de InfinityQS ProFicient on Demand en Azure AD, es necesario agregar InfinityQS ProFicient on Demand desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **InfinityQS ProFicient on Demand** en el cuadro de búsqueda.
1. Seleccione **InfinityQS ProFicient on Demand** en el panel de resultados y, luego, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-infinityqs-proficient-on-demand"></a>Configuración y prueba del inicio de sesión único de Azure AD en InfinityQS ProFicient on Demand

Configure y pruebe el inicio de sesión único de Azure AD con InfinityQS ProFicient on Demand mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de InfinityQS ProFicient on Demand.

Para configurar y probar el inicio de sesión único de Azure AD con InfinityQS ProFicient on Demand, haga lo siguiente:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)**, para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de InfinityQS ProFicient on Demand](#configure-infinityqs-proficient-on-demand-sso)** : para configurar los valores de inicio de sesión único de la aplicación.
    1. **[Creación de un usuario de prueba de InfinityQS ProFicient on Demand](#create-infinityqs-proficient-on-demand-test-user)** : para tener un homólogo de B.Simon en InfinityQS ProFicient on Demand que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)**, para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **InfinityQS ProFicient on Demand**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada en el modo iniciado por **IDP** y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe hacer clic en el botón **Guardar**.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar InfinityQS ProFicient on Demand**, copie las direcciones URL adecuadas en función de sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a InfinityQS ProFicient on Demand mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **InfinityQS ProFicient on Demand**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-infinityqs-proficient-on-demand-sso"></a>Configuración del inicio de sesión único de InfinityQS ProFicient on Demand

Para configurar el inicio de sesión único en **InfinityQS ProFicient on Demand**, es preciso enviar el **certificado (Base64)** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de InfinityQS ProFicient on Demand](mailto:support@infinityqs.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-infinityqs-proficient-on-demand-test-user"></a>Creación de un usuario de prueba de InfinityQS ProFicient on Demand

En esta sección, creará un usuario llamado Britta Simon en InfinityQS ProFicient on Demand. Trabaje con el [equipo de soporte técnico de InfinityQS ProFicient on Demand](mailto:support@infinityqs.com) para agregar los usuarios a la plataforma de InfinityQS ProFicient on Demand. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la versión de InfinityQS ProFicient on Demand para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de InfinityQS ProFicient on Demand en Aplicaciones, debería iniciar sesión automáticamente en la versión de InfinityQS ProFicient on Demand para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Cuando haya configurado InfinityQS ProFicient on Demand, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).