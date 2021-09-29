---
title: 'Tutorial: Integración de Azure Active Directory con YouEarnedIt | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y YouEarnedIt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: jeedes
ms.openlocfilehash: 4d8cafde96cd3c2c45e0c00a6a951d42abd3ae62
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789149"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>Tutorial: Integración de Azure Active Directory con YouEarnedIt

En este tutorial, obtendrá información sobre cómo integrar YouEarnedIt con Azure Active Directory (Azure AD). Al integrar YouEarnedIt con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a YouEarnedIt.
* Permitir que los usuarios inicien sesión automáticamente en YouEarnedIt con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) en YouEarnedIt

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* YouEarnedIt admite el inicio de sesión único iniciado por **SP**.

## <a name="add-youearnedit-from-the-gallery"></a>Incorporación de YouEarnedIt desde la galería

Para configurar la integración de YouEarnedIt en Azure AD, es preciso agregar YouEarnedIt desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **YouEarnedIt** en el cuadro de búsqueda.
1. Seleccione **YouEarnedIt** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-youearnedit"></a>Configuración y prueba del SSO de Azure AD para YouEarnedIt

Configure y pruebe el inicio de sesión único de Azure AD con YouEarnedIt mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de YouEarnedIt.

Para configurar y probar el inicio de sesión único de Azure AD con YouEarnedIt, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de YouEarnedIt](#configure-youearnedit-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de YouEarnedIt](#create-youearnedit-test-user)** , para tener un homólogo de B.Simon en YouEarnedIt que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **YouEarnedIt**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba un valor con los siguientes patrones:

    | Entorno  | Patrón  |
    |:--- |:--- |
    | Producción | `<company name>.youearnedit.com` |
    | Espacio aislado  |`<company name>.sandbox.youearnedit.com` |

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con uno de los siguientes patrones:

    | Entorno  | Patrón  |
    |:--- |:--- |
    | Producción | `https://<company name>.youearnedit.com/users/sign_in` |
    | Espacio aislado  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el administrador del éxito de los clientes de YouEarnedIt que se le ha asignado para obtener estos valores.

5. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Set up YouEarnedIt** (Configurar YouEarnedIt), copie las direcciones URL adecuada según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a YouEarnedIt mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **YouEarnedIt**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-youearnedit-sso"></a>Configuración del inicio de sesión único de YouEarnedIt

Para configurar el inicio de sesión único en **YouEarnedIt**, es preciso enviar el **certificado (Base64)** descargado y las direcciones URL apropiadas copiadas desde Azure Portal en el administrador de éxito de los clientes de YouEarnedIt. Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-youearnedit-test-user"></a>Creación de un usuario de prueba de YouEarnedIt

En esta sección, creará una usuaria llamada Britta Simon en YouEarnedIt. Trabaje con el administrador de éxito de los clientes de YouEarnedIt que se le ha asignado para añadir a los usuarios en la plataforma de YouEarnedIt.

> [!NOTE]
> YouEarnedIt espera que el proveedor de identidades proporcione una dirección de correo electrónico o un nombre de usuario en el atributo NameID. Si no se encuentra el nombre de usuario o dirección de correo electrónico correspondiente dentro de la base de datos o estos no coinciden exactamente, se producirá un error en la autenticación. Para que esto se realice correctamente es necesario que las cuentas se importen en el sistema YouEarnedIt antes de la integración de inicio de sesión único (normalmente mediante la importación CSV o API).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de YouEarnedIt, donde puede comenzar el flujo de inicio de sesión. 

* Vaya a directamente a la dirección URL de inicio de sesión de YouEarnedIt y comience el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de YouEarnedIt en Aplicaciones, se le redirigirá a la dirección URL de inicio de sesión de la aplicación. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado YouEarnedIt, puede aplicar el control de sesión, que protege la filtración y la infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).