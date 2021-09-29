---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con NetDocuments | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y NetDocuments.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 7e6fc17510186ebae3ec736f2385baf39c22eda0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802120"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con NetDocuments

En este tutorial, aprenderá a integrar NetDocuments con Azure Active Directory (Azure AD). Al integrar NetDocuments con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a NetDocuments.
* Permitir que los usuarios inicien sesión automáticamente en NetDocuments con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en NetDocuments.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* NetDocuments admite el inicio de sesión único iniciado por **SP**.

## <a name="adding-netdocuments-from-the-gallery"></a>Adición de NetDocuments desde la galería

Para configurar la integración de NetDocuments en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **NetDocuments** en el cuadro de búsqueda.
1. Seleccione **NetDocuments** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>Configuración y prueba del inicio de sesión único de Azure AD para NetDocuments

Configure y pruebe el inicio de sesión único de Azure AD con NetDocuments utilizando un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de NetDocuments.

Para configurar y probar el inicio de sesión único de Azure AD con NetDocuments, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en NetDocuments](#configure-netdocuments-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de NetDocuments](#create-netdocuments-test-user)** , para tener un homólogo de B. Simon en NetDocuments que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **NetDocuments**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba uno de los siguientes patrones de dirección URL:

    |Dirección URL de inicio de sesión|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. En el cuadro de texto **Identifier (Entity ID)** (Identificador [id. de entidad]), escriba una de las direcciones URL:

    |Identificador|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. En el cuadro de texto **URL de respuesta**, escriba uno de los siguientes s patrones de dirección URL:

    |URL de respuesta|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. El identificador del repositorio es un valor que comienza por **CA-** seguido por un código de ocho caracteres asociado al repositorio de NetDocuments. Para más información puede consultar el [documento de soporte técnico sobre identidad federada de NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login). También tiene la alternativa de ponerse en contacto con el [equipo de soporte técnico de NetDocuments](https://support.netdocuments.com/hc/) para obtener estos valores si tiene dificultades para configurar utilizando la información anterior. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación NetDocuments espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla muestra la lista de atributos predeterminados, donde **nameidentifier** se asigna con **user.userprincipalname**. La aplicación NetDocuments espera que el valor de **nameidentifier** se corresponda con **ObjectID** o con cualquier otro recurso aplicable a su organización, como **nameidentifier**, por lo que se debe modificar la asignación de atributos haciendo clic en el icono **Editar** para cambiarla.

    ![imagen](common/edit-attribute.png)

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma SAML**, busque **Dirección URL de metadatos de federación de aplicación** y copie la URL.

    ![Vínculo de descarga del certificado](common/copy-metadataurl.png)

1. En la sección **Set up NetDocuments** (Configurar NetDocuments), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B. Simon acceda a NetDocuments mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **NetDocuments**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-netdocuments-sso"></a>Configuración del inicio de sesión único de NetDocuments

1. En otra ventana del explorador web, inicie sesión en como administrador en el sitio de la compañía de NetDocuments.

2. En la esquina superior derecha, seleccione su nombre>**Admin**.

3. Seleccione **Centro de seguridad**.
   
    ![Repositorio](./media/netdocuments-tutorial/security-center.png "Security Center")

4. Seleccione **Autenticación avanzada**.
    
    ![Configurar opciones de autenticación avanzadas](./media/netdocuments-tutorial/advance-authentication.png "Configurar opciones de autenticación avanzadas")

5.  En la pestaña **Id. federado**, realice los pasos siguientes:   
   
    [ ![Identidad federada](./media/netdocuments-tutorial/federated-id.png "Identidad federada")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. En **Tipo de servidor de identidad federada**, seleccione **Windows Azure Active Directory**.
    
    b.  Seleccione **Choose file** (Elegir archivo) para cargar el archivo de metadatos que descargó de Azure Portal.
    
    c.  Seleccione **SAVE** (GUARDAR).

### <a name="create-netdocuments-test-user"></a>Creación de un usuario de prueba de NetDocuments

Para permitir que los usuarios de Azure AD inicien sesión en NetDocuments, deben aprovisionarse en dicha aplicación. En el caso de NetDocuments, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía **NetDocuments**.

2. En la esquina superior derecha, seleccione su nombre>**Admin**.
   
    ![Administrador](./media/netdocuments-tutorial/user-admin.png "Administración")

3. Seleccione **Usuarios y grupos**.
   
    ![Usuarios y grupos](./media/netdocuments-tutorial/users-groups.png "Repositorio")

4. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico de la cuenta válida de Azure Active Directory que quiera aprovisionar y haga clic en **Agregar usuario**.
   
    ![Dirección de correo electrónico](./media/netdocuments-tutorial/user-mail.png "Dirección de correo electrónico")
   
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de NetDocuments ofrecida por NetDocuments para aprovisionar cuentas de usuario de Azure Active Directory.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de NetDocuments, desde donde puede poner en marcha el flujo de inicio de sesión. 

* Vaya directamente a la URL de inicio de sesión de NetDocuments y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de NetDocuments en Mis aplicaciones, debería iniciar sesión automáticamente en la instancia de NetDocuments para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha configurado NetDocuments, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).