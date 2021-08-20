---
title: 'Tutorial: Integración de Azure Active Directory con Learning Pool LMS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Learning Pool LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: jeedes
ms.openlocfilehash: 8829da481abbddc61c1cc90da0984742775059ae
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556539"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-pool-lms"></a>Tutorial: Integración de Azure Active Directory con Learning Pool LMS

En este tutorial, obtendrá información sobre cómo integrar Learning Pool LMS con Azure Active Directory (Azure AD). Al integrar Learning Pool LMS con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Learning Pool LMS.
* Permitir que los usuarios inicien sesión automáticamente en Learning Pool LMS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción activa a Learning Pool LMS con inicio de sesión único.

> [!NOTE]
> Al iniciar un proyecto de inicio de sesión único, un miembro del equipo de entrega de Learning Pool LMS le guiará por este proceso. Si no está en contacto con un miembro del equipo de entrega de Learning Pool LMS, hable con el administrador de cuentas de Learning Pool LMS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Learning Pool LMS admite el inicio de sesión único iniciado por **SP**.

## <a name="adding-learning-pool-lms-from-the-gallery"></a>Adición de Learning Pool LMS desde la galería

Para configurar la integración de Learning Pool LMS en Azure AD, tendrá que agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Learning Pool LMS** en el cuadro de búsqueda.
1. Seleccione **Learning Pool LMS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-learning-pool-lms"></a>Configuración y comprobación del inicio de sesión único de Azure AD con Learning Pool LMS

Configure y pruebe el inicio de sesión único de Azure AD con Learning Pool LMS con un usuario de Azure existente. Para que el inicio de sesión único funcione, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Learning Pool LMS.

Para configurar y probar el inicio de sesión único de Azure AD con Learning Pool LMS, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
1. **[Asignación de un usuario de prueba de Azure AD](#assign-an-azure-ad-user)** : para que el usuario pueda utilizar el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Learning LMS SSO](#configure-learning-pool-lms-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Learning Pool LMS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si tiene el **archivo de metadatos del proveedor de servicios**, lleve a cabo los siguientes pasos:

    a. Haga clic en **Cargar el archivo de metadatos**.

    ![Carga del archivo de metadatos](common/upload-metadata.png)

    b. Haga clic en el **logotipo de la carpeta** para seleccionar el archivo de metadatos y luego en **Cargar**.

    ![Elección del archivo de metadatos](common/browse-upload-metadata.png)

    c. Una vez que se haya cargado correctamente el archivo de metadatos, el valor de **Identificador** se rellena automáticamente en la sección Configuración básica de SAML.

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`

    > [!Note]
    > Si el valor **Identificador** no se rellena automáticamente, hágalo manualmente según sus necesidades.

5. Tendrá que enviar al menos un atributo que se usa para hacer coincidir los usuarios de Azure con los de Learning Pool LMS. Normalmente, los atributos predeterminados son suficientes, pero en algunos casos es posible que tenga que enviar algunos atributos personalizados. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos de usuario y agregue más atributos si es necesario.

    ![Captura de pantalla que muestra User Attributes (Atributos de usuario) con el icono de edición seleccionado.](common/edit-attribute.png)

6. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, edite las notificaciones mediante el **icono Editar** o agregue notificaciones mediante **Agregar nueva notificación** para configurar el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos: 

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![Captura de pantalla que muestra User claims (Reclamaciones de usuario) con la opción Add new claim (Agregar nueva reclamación).](common/new-save-attribute.png)

    ![Captura de pantalla que muestra Manage user claims (Administrar reclamaciones de usuario) para escribir los valores que se describen.](common/new-attribute-details.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Deje **Espacio de nombres** en blanco.

    d. Seleccione **Atributo** como origen.

    e. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    f. Haga clic en **Aceptar**.

    g. Haga clic en **Save**(Guardar).

7. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en el botón Copiar de **Dirección URL de metadatos de federación de aplicación** y proporcione esa dirección URL al equipo de entrega de Learning Pool.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

### <a name="assign-an-azure-ad-user"></a>Asignación de un usuario de Azure AD

En esta sección, permitirá que un usuario de Azure AD existente use el inicio de sesión único de Azure concediendo acceso a Learning Pool LMS.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Learning Pool LMS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione un usuario adecuado en la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-learning-pool-lms-sso"></a>Configuración del inicio de sesión único de Learning Pool LMS

El equipo de entrega de Learning Pool usará la **Dirección URL de metadatos de federación de aplicación** a fin de configurar LMS para aceptar conexiones SAML2. Se le pedirá que realice algunos pasos de prueba para comprobar que la conexión está configurada correctamente y el equipo de entrega de Learning Pool le guiará por este proceso.

### <a name="test-sso"></a>Prueba de SSO

El equipo de entrega de Learning Pool le guiará por el proceso de prueba.

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado Learning Pool LMS, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
