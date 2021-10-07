---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Clever Nelly'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Clever Nelly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 48751c06ab573f70943389c959bce2bea2f5a65a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566622"
---
# <a name="tutorial-azure-ad-sso-integration-with-clever-nelly"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Clever Nelly

En este tutorial, aprenderá a integrar Clever Nelly con Azure Active Directory (Azure AD). Al integrar Clever Nelly con Azure AD, puede:

* Controlar en Azure AD quién tiene acceso a Clever Nelly.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Clever Nelly con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Clever Nelly.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Clever Nelly admite el inicio de sesión único iniciado por **SP e IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-clever-nelly-from-the-gallery"></a>Adición de Clever Nelly desde la galería

Para configurar la integración de Clever Nelly en Azure AD, tendrá que agregar Clever Nelly desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Clever Nelly** en el cuadro de búsqueda.
1. Seleccione **Clever Nelly** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-clever-nelly"></a>Configuración y prueba del SSO de Azure AD para Clever Nelly

Configure y pruebe el inicio de sesión único de Azure AD con Clever Nelly mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Clever Nelly.

Para configurar y probar el SSO de Azure AD con Clever Nelly, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Clever Nelly](#configure-clever-nelly-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Clever Nelly](#create-clever-nelly-test-user)** , para tener un homólogo de Britta Simon en Clever Nelly que esté vinculado a la representación de este usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Clever Nelly**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    a. En el cuadro de texto **Identificador**, escriba una de las siguientes direcciones URL:

    | Entorno | Patrón de dirección URL |
    | - | - |
    | Prueba | `https://test.elephantsdontforget.com/plato`|
    | Producción | `https://secure.elephantsdontforget.com/plato` |
    | | |

    b. En el cuadro de texto **URL de respuesta**, escriba una de las siguientes direcciones URL:

    | Entorno | Patrón de dirección URL |
    | - | - |
    | Prueba | `https://test.elephantsdontforget.com/plato/callback?client_name=SAML2Client`|
    | Producción | `https://secure.elephantsdontforget.com/plato/callback?client_name=SAML2Client` |
    | | |

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una de las siguientes direcciones URL:

    | Entorno | Patrón de dirección URL |
    | - | - |
    | Prueba | `https://test.elephantsdontforget.com/plato/sso/microsoft/index.xhtml`|
    | Producción | `https://secure.elephantsdontforget.com/plato/sso/microsoft/index.xhtml` |
    | | |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón de copia para copiar **Dirección URL de metadatos de federación de aplicación** y guárdela en su equipo.

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

En esta sección, va a conceder acceso a B.Simon a Clever Nelly para permitirle usar el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Clever Nelly**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-clever-nelly-sso"></a>Configuración del inicio de sesión único de Clever Nelly

Para configurar el inicio de sesión único en **Clever Nelly**, tiene que enviar la **dirección URL de metadatos de federación de la aplicación** generada al [equipo de soporte técnico de Clever Nelly](mailto:support@elephantsdontforget.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-clever-nelly-test-user"></a>Creación de un usuario de prueba de Clever Nelly

En esta sección, creará un usuario llamado Britta Simon en Clever Nelly. Trabaje con el [equipo de soporte técnico de Clever Nelly](mailto:support@elephantsdontforget.com) para agregar los usuarios a la plataforma de Clever Nelly. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de Clever Nelly, desde donde puede comenzar el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de Clever Nelly y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de Clever Nelly para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Clever Nelly en Aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de Clever Nelly para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Clever Nelly, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).