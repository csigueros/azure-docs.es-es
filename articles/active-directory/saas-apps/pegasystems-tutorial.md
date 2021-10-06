---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con Pega Systems'
description: En este tutorial, aprenderá a configurar el inicio de sesión único entre Azure Active Directory y Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: a0c88213583105a1076f6606f7265025ec445560
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610017"
---
# <a name="tutorial-azure-ad-sso-integration-with-pega-systems"></a>Tutorial: Integración del inicio de sesión único de Azure AD con Pega Systems

En este tutorial, aprenderá a integrar Pega Systems con Azure Active Directory (Azure AD). Al integrar Pega Systems con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Pega Systems.
* Permitir que los usuarios inicien sesión automáticamente en Pega Systems con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único en Pega Systems.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial configurará y probará el inicio de sesión único de Azure AD en un entorno de prueba.

* Pega Systems admite el inicio de sesión único iniciado por SP e IDP.

## <a name="add-pega-systems-from-the-gallery"></a>Adición de Pega Systems desde la galería

Para configurar la integración de Pega Systems en Azure AD, será preciso que agregue Pega Systems desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Pega Systems** en el cuadro de búsqueda.
1. Seleccione **Pega Systems** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Configuración y prueba del inicio de sesión único de Azure AD para Pega Systems

Configure y pruebe el inicio de sesión único de Azure AD con Pega Systems mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Pega Systems.

Para configurar y probar el inicio de sesión único de Azure AD con Pega Systems, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Pega Systems](#configure-pega-systems-sso)** , para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba en Pega Systems](#create-pega-systems-test-user)** , para tener un homólogo de B.Simon en Pega Systems que esté vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **Pega Systems**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En el cuadro de diálogo **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por IdP, realice estos pasos.

    1. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: 

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Si quiere configurar la aplicación en modo iniciado por SP, seleccione **Establecer direcciones URL adicionales** y haga lo siguiente.

    1. En **Dirección URL de inicio de sesión**, escriba el valor de URL de inicio de sesión.

    1. En el cuadro **Estado de la retransmisión**, escriba una dirección URL con este patrón: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Los valores proporcionados aquí son marcadores de posición. Necesita usar los valores de Identificador, URL de respuesta, URL de inicio de sesión y URL de estado de retransmisión. Puede obtener los valores de identificador y dirección URL de respuesta de una aplicación de Pega, tal como se explica más adelante en este tutorial. Para obtener el valor de estado de la retransmisión, póngase en contacto con el [equipo de soporte técnico de Pega Systems](https://www.pega.com/contact-us). También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

6. La aplicación de Pega Systems necesita las aserciones de SAML en un formato específico. Para obtenerlas en el formato correcto, deberá agregar asignaciones de atributo personalizadas a la configuración de atributos del token SAML. En la siguiente captura se muestra la lista de atributos predeterminados. Seleccione el icono **Editar** para abrir el cuadro de diálogo **Atributos de usuario**.

    ![Atributos de usuario](common/edit-attribute.png)

7. Además de los atributos que se muestran en la captura de pantalla anterior, la aplicación Pega Systems requiere que se pasen algunos atributos más en la respuesta de SAML. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, haga lo siguiente para agregar estos atributos del token SAML:
    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Estos valores son específicos de su organización. Proporcione los valores adecuados.

    1. Seleccione **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**:

    ![Selección de Agregar nueva notificación](common/new-save-attribute.png)

    ![Cuadro de diálogo Administrar las notificaciones del usuario](common/new-attribute-details.png)

    1. En el cuadro **Nombre**, escriba el nombre de atributo que se muestra para la fila.

    1. Deje **Espacio de nombres** vacío.

    1. En **Origen**, seleccione **Atributo**.

    1. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    1. Seleccione **Aceptar**.

    1. Seleccione **Guardar**.

8. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, seleccione el vínculo **Descargar** situado junto a **XML de metadatos de federación**, según sus requisitos, y guarde el certificado en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

9. En la sección **Configurar Pega Systems**, copie las direcciones URL adecuadas según sus necesidades.

    ![Copia de las direcciones URL de configuración](common/copy-configuration-urls.png)

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

En esta sección, va a permitir que B.Simon acceda a Pega Systems mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Pega Systems**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-pega-systems-sso"></a>Configuración del inicio de sesión único de Pega Systems

1. Para configurar el inicio de sesión único en **Pega Systems**, inicie sesión en el portal de Pega con cuenta de administrador en otra ventana del explorador.

1. Seleccione **Create** > **SysAdmin** > **Authentication Service** (Crear > SysAdmin > Servicio de autenticación):

    ![Selección del servicio de autenticación](./media/pegasystems-tutorial/admin.png)
    
1. Realice los siguientes pasos en la pantalla **Create Authentication Service** (Crear servicio de autenticación).

    ![Pantalla de creación del servicio de autenticación](./media/pegasystems-tutorial/service.png)

    1. En la lista **Type** (Tipo), seleccione **SAML 2.0**.

    1. En el cuadro **Name** (Nombre), escriba cualquier nombre (por ejemplo, **Inicio de sesión único de Azure AD**).

    1. En el cuadro **Short description** (Descripción corta) escriba una descripción.  

    1. Seleccione **Create and open** (Crear y abrir).
    
1. En la sección **Identity Provider (IdP) information** (Información de proveedor de identidades [IdP]), haga clic en **Import IdP metadata** (Importar metadatos de IdP) y busque el archivo de metadatos que descargó de Azure Portal. Haga clic en **Submit** (Enviar) para cargar los metadatos:

    ![Sección de información del proveedor de identidades (IdP)](./media/pegasystems-tutorial/metadata.png)
    
    La importación rellenará los datos del IdP, como se muestra a continuación:

    ![Datos del IdP importados](./media/pegasystems-tutorial/data.png)
    
1. Siga estos pasos en la sección **Service Provider (SP) settings** (Configuración del proveedor de servicios [SP]).

    ![Configuración del proveedor de servicio](./media/pegasystems-tutorial/settings.png)

    1. Copie el valor de **Entity Identification** (Identificación de la entidad) y péguelo en el cuadro **Identificador** de **Configuración básica de SAML** en Azure Portal.

    1. Copie el valor de **Assertion Consumer Service (ACS) location** (Ubicación del servicio de consumidor de aserciones [ACS]) y péguelo en el cuadro **Dirección URL de respuesta** de **Configuración básica de SAML** en Azure Portal.

    1. Seleccione **Disable request signing** (Deshabilitar firma de solicitudes).

1. Seleccione **Guardar**.

### <a name="create-pega-systems-test-user"></a>Creación de un usuario de prueba de Pega Systems

A continuación, deberá crear un usuario llamado a Britta Simon en Pega Systems. Trabaje con el [equipo de soporte técnico de Pega Systems](https://www.pega.com/contact-us) para crear usuarios.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Pega Systems, desde donde puede poner en marcha el flujo de inicio de sesión.  

* Acceda directamente a la URL de inicio de sesión de Pega Systems y ponga en marcha el flujo de inicio de sesión desde ahí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Se debería iniciar sesión automáticamente en la instancia de Pega Systems para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de Pega Systems en Aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de Pega Systems para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Pega Systems, puede aplicar el control de sesión, que protege su organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).