---
title: 'Tutorial: Integración de Azure Active Directory con iLMS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 285fea57252c23776c9d7bc3b172889311356909
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768484"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutorial: Integración de iLMS con Azure Active Directory

En este tutorial, obtendrá información sobre cómo integrar iLMS con Azure Active Directory (Azure AD). Al integrar iLMS con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a iLMS.
* Permitir que los usuarios puedan iniciar sesión automáticamente en iLMS con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en iLMS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* iLMS admite el inicio de sesión único iniciado por **SP e IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-ilms-from-the-gallery"></a>Adición de iLMS desde la galería

Para configurar la integración de iLMS en Azure AD, debe agregar iLMS desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **iLMS** en el cuadro de búsqueda.
1. Seleccione **iLMS** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ilms"></a>Configuración y prueba del inicio de sesión único de Azure AD para iLMS

Configure y pruebe el inicio de sesión único de Azure AD con iLMS mediante un usuario de prueba llamado **B.Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de iLMS.

Para configurar y probar el inicio de sesión único de Azure AD con iLMS, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en iLMS](#configure-ilms-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de iLMS](#create-ilms-test-user)** : para tener un homólogo de B.Simon en iLMS que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **iLMS**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configuración básica de SAML**, si quiere configurar la aplicación en modo iniciado por **IDP**, siga estos pasos:

    a. En el cuadro de texto **Identificador**, pegue el valor del **identificador** que ha copiado de la sección **Proveedor de servicios** de la configuración de SAML en el portal de administración de iLMS.

    b. En el cuadro de texto **URL de respuesta**, pegue el valor de **Endpoint (URL)** (Punto de conexión [URL]) que ha copiado de la sección **Proveedor de servicios** de la configuración de SAML en el portal de administración de iLMS, que tiene el siguiente patrón: `https://www.inspiredlms.com/Login/<INSTANCE_NAME>/consumer.aspx`.

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, pegue el valor de **Endpoint (URL)** (Punto de conexión [URL]) que ha copiado de la sección **Proveedor de servicios** de la configuración de SAML en el portal de administración de iLMS, como `https://www.inspiredlms.com/Login/<INSTANCE_NAME>/consumer.aspx`.

1. Para habilitar el aprovisionamiento Just-In-Time, la aplicación iLMS espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos de usuario.

    > [!NOTE]
    > Debe habilitar **Create Un-recognized User Account** (Crear una cuenta de usuario no reconocida) en iLMS para asignar estos atributos. Siga las instrucciones [aquí](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) para hacerse una idea de la configuración de los atributos.

1. Además de lo anterior, la aplicación iLMS espera que se usen algunos atributos más en la respuesta de SAML. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, realice los siguientes pasos para agregar el atributo Token SAML como se muestra en la tabla siguientes:

    | Nombre | Atributo de origen|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Deje **Espacio de nombres** en blanco.

    d. Seleccione **Atributo** como origen.

    e. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    f. Haga clic en **Aceptar**.

    g. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up iLMS** (Configurar iLMS), copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado Britta Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `Britta Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `BrittaSimon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a iLMS mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **iLMS**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-ilms-sso"></a>Configuración del inicio de sesión único de iLMS

1. En otra ventana del explorador web, inicie sesión en el **Portal de administración de iLMS** como administrador.

2. Haga clic en **SSO:SAML** en **Settings** (Configuración) para abrir la configuración de SAML y realice los siguientes pasos:

    ![Captura de pantalla que muestra la pestaña de configuración de I L M S, donde puede seleccionar S S O: SAML.](./media/ilms-tutorial/settings.png)

3. Expanda la sección **Service Provider** (Proveedor de servicios) y copie los valores **Identifier** (Identificador) y **Endpoint (URL)** (Punto de conexión [URL]).

    ![Captura de pantalla que muestra la configuración de SAML, donde puede obtener los valores.](./media/ilms-tutorial/values.png) 

4. En la sección **Identity Provider** (Proveedor de identidades), haga clic en **Import Metadata** (Importar metadatos).

5. Seleccione el archivo de **metadatos de federación** descargado de Azure Portal en la sección **Certificado de firma de SAML**.

    ![Captura de pantalla que muestra la configuración de SAML, donde puede seleccionar el archivo de metadatos.](./media/ilms-tutorial/certificate.png)

6. Si desea habilitar el aprovisionamiento JIT para crear cuentas de iLMS para usuarios no reconocidos, realice los pasos siguientes:

    a. Active **Create Un-recognized User Account** (Crear cuenta de usuario no reconocido).

    ![Captura de pantalla que muestra la opción Create Un-recognized User Account (Crear cuenta de usuario no reconocido).](./media/ilms-tutorial/accounts.png)

    b. Asigne los atributos de Azure AD a los atributos de iLMS. En la columna de atributos, especifique el nombre de los atributos o el valor predeterminado.

    c. Vaya a la pestaña **Business Rules** (Reglas empresariales) y realice los pasos siguientes:

    ![Captura de pantalla que muestra la configuración Business Rules (Reglas empresariales), donde puede especificar la información de este paso.](./media/ilms-tutorial/rules.png)

    d. Active **Create Un-recognized Regions, Divisions and Departments** (Crear regiones, divisiones y departamentos no reconocidos) para crear regiones, divisiones y departamentos que todavía no existen en el momento del inicio de sesión único.

    e. Active **Update User Profile During Sign-in** (Actualizar perfil de usuario durante el inicio de sesión) para especificar si es necesario actualizar el perfil del usuario con cada inicio de sesión único.

    f. Si la opción **Update Blank Values for Non Mandatory Fields in User Profile** (Actualizar valores en blanco en campos no obligatorios del perfil de usuario) está seleccionada, los campos opcionales del perfil que estén en blanco también provocarán que el perfil de iLMS del usuario contengan valores en blanco.

    g. Active **Send Error Notification Email** (Enviar correo electrónico de notificación de errores) y escriba la dirección de correo electrónico del usuario en la que desea recibir el correo electrónico de notificación de errores.

7. Haga clic en el botón **Save** (Guardar) para guardar la configuración.

    ![Captura de pantalla que muestra el botón Save (Guardar).](./media/ilms-tutorial/save.png)

### <a name="create-ilms-test-user"></a>Creación de un usuario de prueba en iLMS

La aplicación admite aprovisionamiento de usuarios Just-In-Time y, tras la autenticación, los usuarios se crean automáticamente en la aplicación. JIT funcionará si ha marcado la casilla **Create Un-recognized User Account** (Crear cuenta de usuario no reconocido) durante la configuración de SAML en el portal de administración de iLMS.

Si debe crear un usuario manualmente, siga estos pasos:

1. Inicie sesión en el sitio de la compañía de iLMS como administrador.

2. Haga clic en **Register User** (Registrar usuario) en la pestaña **Users** (Usuarios) para abrir la página **Register User** (Registrar usuario).

   ![Captura de pantalla que muestra la pestaña de configuración de I L M S, donde puede Register User (Registrar usuario).](./media/ilms-tutorial/user.png)

3. En la página **Register User** (Registrar usuario), siga estos pasos.

    ![Captura de pantalla que muestra la página Register User (Registrar usuario) en la que se escribe la información especificada.](./media/ilms-tutorial/add-user.png)

    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre, por ejemplo, Britta.

    b. En el cuadro de texto **Last Name** (Apellido), escriba el apellido, por ejemplo, Simon.

    c. En el cuadro de texto **Email ID** (Identificador de correo electrónico), escriba esta dirección de correo electrónico del usuario: BrittaSimon@contoso.com.

    d. En el menú desplegable **Region** (Región), seleccione el valor de la región.

    e. En el menú desplegable **Division** (División), seleccione el valor de la división.

    f. En el menú desplegable **Department** (Departamento), seleccione el valor del departamento.

    g. Haga clic en **Save**(Guardar).

    > [!NOTE]
    > Puede enviar un correo electrónico de registro al usuario si selecciona la casilla **Send Registration Mail** (Enviar correo electrónico de registro).

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de iLMS, donde puede iniciar el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de iLMS e inicie el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en la instancia de iLMS para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de iLMS en Mis aplicaciones, si ha realizado la configuración en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión. Si ha realizado la configuración en modo IDP, debería iniciar sesión automáticamente en la instancia de iLMS para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado iLMS, podrá aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).