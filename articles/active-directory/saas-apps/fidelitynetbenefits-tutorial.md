---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Fidelity NetBenefits'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Fidelity NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: 304bc6d4d3100478ba4fa542c76cd1d2e4ef4aca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285686"
---
# <a name="tutorial-azure-ad-sso-integration-with-fidelity-netbenefits"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Fidelity NetBenefits

En este tutorial, aprenderá a integrar Fidelity NetBenefits con Azure Active Directory (Azure AD). Al integrar Fidelity NetBenefits con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Fidelity NetBenefits.
* Permitir que los usuarios inicien sesión automáticamente en Fidelity NetBenefits con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción que permita el inicio de sesión único (SSO) en Fidelity NetBenefits.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Fidelity NetBenefits admite SSO iniciado por **IDP**.

* Fidelity NetBenefits admite el aprovisionamiento de usuarios **Just-In-Time**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-fidelity-netbenefits-from-the-gallery"></a>Incorporación de Fidelity NetBenefits desde la galería

Para configurar la integración de Fidelity NetBenefits en Azure AD, será preciso que agregue Fidelity NetBenefits desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Fidelity NetBenefits** en el cuadro de búsqueda.
1. Seleccione **Fidelity NetBenefits** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-fidelity-netbenefits"></a>Configuración y prueba del inicio de sesión único de Azure AD para Fidelity NetBenefits

Configure y pruebe el inicio de sesión único de Azure AD con Fidelity NetBenefits mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Fidelity NetBenefits.

Para configurar y probar el inicio de sesión único de Azure AD con Fidelity NetBenefits, es preciso completar los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Fidelity NetBenefits](#configure-fidelity-netbenefits-sso)** : para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Crear un usuario de prueba de Fidelity NetBenefits](#create-fidelity-netbenefits-test-user)** : para tener un homólogo de B.Simon en Fidelity NetBenefits que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Fidelity NetBenefits**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, escriba uno de los valores siguientes:

    Para un entorno de pruebas: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Para un entorno de producción: `urn:sp:fidelity:geninbndnbparts20`

    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL que va a proporcionar Fidelity en el momento de la implementación, o bien póngase en contacto con un responsable del servicio de atención al cliente de Fidelity.

5. La aplicación Fidelity NetBenefits espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación Fidelity NetBenefits espera que el valor de **nameidentifier** se corresponda con **employeeid** o con cualquier otro recurso que sea aplicable a su organización, como **nameidentifier**, por lo que deberá modificar la asignación de atributos. Para ello, debe hacer clic en el icono **Editar** y cambiarla.

    ![imagen](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits admite la federación estática y dinámica. La opción estática significa que no usará el aprovisionamiento de usuarios Just-In-Time basado en SAML y la opción dinámica significa que admite el aprovisionamiento de usuarios Just-In-Time. Para utilizar el aprovisionamiento basado en JIT, los clientes tienen que agregar algunas notificaciones más en Azure AD, como la fecha de nacimiento del usuario, etc. Estos detalles los proporciona el **responsable del servicio de atención al cliente de Fidelity** asignado, quien debe habilitar esta federación dinámica para su instancia.

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

7. En la sección **Set up Fidelity NetBenefits** (Configurar Fidelity NetBenefits), copie las direcciones URL copie las direcciones URL que necesite.

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

En esta sección, va a permitir que B.Simon acceda a Fidelity NetBenefits mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Fidelity NetBenefits**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-fidelity-netbenefits-sso"></a>Configuración del inicio de sesión único de Fidelity NetBenefits

Para configurar el inicio de sesión único en **Fidelity NetBenefits**, es preciso enviar el **XML de metadatos de federación** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-fidelity-netbenefits-test-user"></a>Creación de un usuario de prueba de Fidelity NetBenefits

En esta sección, creará un usuario llamado Britta Simon en Fidelity NetBenefits. Si va a crear la federación estática, trabaje con el **responsable del servicio de atención al cliente de Fidelity** para crear usuarios en la plataforma Fidelity NetBenefits. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

Para la federación dinámica, los usuarios se crean mediante el aprovisionamiento de usuarios Just-In-Time. Para utilizar el aprovisionamiento basado en JIT, los clientes tienen que agregar algunas notificaciones más en Azure AD, como la fecha de nacimiento del usuario, etc. Estos detalles los proporciona el **responsable del servicio de atención al cliente de Fidelity** asignado, quien debe habilitar esta federación dinámica para su instancia.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal; debería iniciar sesión automáticamente en la instancia de Fidelity NetBenefits para la que configuró el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Fidelity NetBenefits en Aplicaciones, debería iniciar sesión automáticamente en la aplicación Fidelity NetBenefits para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Fidelity NetBenefits, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con las aplicaciones de Microsoft Defender for Cloud](/cloud-app-security/proxy-deployment-aad).
