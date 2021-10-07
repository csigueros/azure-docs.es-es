---
title: 'Tutorial: Integración del inicio de sesión único de Azure AD con SAP Cloud Platform'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 939bc772a46a35a1d702bf972d42467be8c86dee
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547259"
---
# <a name="tutorial-azure-ad-sso-integration-with-sap-cloud-platform"></a>Tutorial: Integración del inicio de sesión único de Azure AD con SAP Cloud Platform

En este tutorial aprenderá a integrar SAP Cloud Platform con Azure Active Directory (Azure AD). Al integrar SAP Cloud Platform con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a SAP Cloud Platform.
* Permitir que los usuarios inicien sesión automáticamente en SAP Cloud Platform con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para inicio de sesión único (SSO) en SAP Cloud Platform.

>[!IMPORTANT]
>Deberá implementar su propia aplicación o suscribirse a una aplicación en su cuenta de SAP Cloud Platform para probar el inicio de sesión único. En este tutorial, se implementa una aplicación en la cuenta.
> 

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* SAP Cloud Platform admite el inicio de sesión único iniciado por **SP**.

## <a name="add-sap-cloud-platform-from-the-gallery"></a>Adición de SAP Cloud Platform desde la galería

Para configurar la integración de SAP Cloud Platform en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **SAP Cloud Platform** en el cuadro de búsqueda.
1. Seleccione **SAP Cloud Platform** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Configuración y prueba del inicio de sesión único de Azure AD para SAP Cloud Platform

Configure y pruebe el inicio de sesión único de Azure AD con SAP Cloud Platform mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP Cloud Platform.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Cloud Platform, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en SAP Cloud Platform](#configure-sap-cloud-platform-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de SAP Cloud Platform](#create-sap-cloud-platform-test-user)** : para tener un homólogo de Britta Simon en SAP Cloud Platform vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **SAP Cloud Platform**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador** proporcionará el de su aplicación SAP Cloud Platforn, escriba una dirección URL conforme a uno de los siguientes patrones: 

    | **Identificador** |
    |-----------|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con uno de los siguientes patrones:

    | **URL de respuesta** |
    |----------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    c. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación **SAP Cloud Platform**. Se trata de la URL específica de la cuenta de un recurso protegido en su aplicación SAP Cloud Platform. La dirección URL se parece al siguiente patrón: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Se trata de la URL de la aplicación SAP Cloud Platform que requiere la autenticación del usuario.
    > 

    | **Dirección URL de inicio de sesión** |
    |------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obtener la dirección URL de inicio de sesión y el identificador. La dirección URL de respuesta se obtiene de la sección de administración de confianza que se explica más adelante en el tutorial.
    > 
4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **XML de metadatos de federación** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

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

En esta sección va a permitir que B.Simon acceda a SAP Cloud Platform mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **SAP Cloud Platform**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-sap-cloud-platform-sso"></a>Configuración del inicio de sesión único en SAP Cloud Platform

1. En otra ventana del explorador web, inicie sesión en SAP Cloud Platform Cockpit en `https://account.<landscape host>.ondemand.com/cockpit` (por ejemplo: https://account.hanatrial.ondemand.com/cockpit) ).

2. Haga clic en la pestaña **Trust** (Confianza).
   
    ![Trust (Confianza)](./media/sap-hana-cloud-platform-tutorial/account.png "Trust")

3. En la sección Trust Management (Administración de confianza), en **Local Service Provider** (Proveedor de servicios local), realice los pasos siguientes:

    ![Captura de pantalla que muestra la sección "Trust Management" (Administración de confianza) con la pestaña "Local Service Provider" (Proveedor de servicios local) seleccionada y todos los cuadros de texto resaltados.](./media/sap-hana-cloud-platform-tutorial/service.png "Administración de confianza")
   
    a. Haga clic en **Editar**.

    b. En **Configuration Type** (Tipo de configuración), seleccione **Custom** (Personalizado).

    c. Como **Local Provider Name**(Nombre de proveedor local), deje el valor predeterminado. Copie este valor y péguelo en el campo **Identificador** de la configuración de Azure AD para SAP Cloud Platform.

    d. Para generar una **Signing Key** (Clave de firma) y un par de claves **Signing Certificate** (Certificado de firma), haga clic en **Generate Key Pair** (Generar par de claves).

    e. En **Principal Propagation** (Propagación de entidad de seguridad), seleccione **Disabled** (Deshabilitada).

    f. En **Force Authentication** (Forzar autenticación), seleccione **Disabled** (Deshabilitado).

    g. Haga clic en **Save**(Guardar).

4. Después de guardar la configuración de **Local Service Provider** (Proveedor de servicios local), realice lo siguiente para obtener la dirección URL de respuesta:
   
    ![Get Metadata (Obtener metadatos)](./media/sap-hana-cloud-platform-tutorial/certificate.png "Get Metadata")

    a. Para descargar el archivo de metadatos de SAP Cloud Platform, haga clic en **Get Metadata** (Obtener metadatos).

    b. Abra el archivo de metadatos de SAP Cloud Platform descargado y busque la etiqueta **ns3:AssertionConsumerService**.
 
    c. Copie el valor del atributo **Location** y péguelo en el campo **URL de respuesta** de la configuración de Azure AD para SAP Cloud Platform.

5. Haga clic en la pestaña **Trusted Identity Provider** (Proveedor de identidades de confianza) y en **Add Trusted Identity Provider** (Agregar proveedor de identidad de confianza).
   
    ![Captura de pantalla que muestra la página "Trust Management" (Administración de confianza) con la pestaña "Trusted Identity Provider" (Proveedor de identidades de confianza) seleccionada.](./media/sap-hana-cloud-platform-tutorial/add-service.png "Administración de confianza")
   
    >[!NOTE]
    >Para administrar la lista de proveedores de identidades de confianza, deberá haber elegido el tipo de configuración personalizada en la sección del proveedor de servicios local. Para el tipo de configuración predeterminado, tendrá una confianza implícita y no editable para el servicio de id. de SAP. Para Ninguno, no tiene ninguna configuración de confianza.
    > 
    > 

6. Haga clic en la pestaña **General** y en **Browse** (Examinar) para cargar el archivo de metadatos descargados.
    
    ![Trust Management (Administración de confianza)](./media/sap-hana-cloud-platform-tutorial/general.png "Administración de confianza")
    
    >[!NOTE]
    >Después de cargar el archivo de metadatos, los valores de **Dirección URL de inicio de sesión único**, **Dirección URL de cierre de sesión único** y **Certificado de firma** se rellenan automáticamente.
    > 
     
7. Haga clic en la pestaña **Attributes** (Atributos).

8. En la pestaña **Attributes** (Atributos), realice los pasos siguientes:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/principal-attribute.png "Atributos") 

    a. Haga clic en **Add Assertion-Based Attribute**(Agregar atributo basado en la aserción) y agregue los siguientes atributos basados en aserción:
       
    | Atributo de aserción | Atributo de entidad de seguridad |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
    >[!NOTE]
    >La configuración de los atributos depende de cómo se desarrollen las aplicaciones en SCP, es decir, qué atributos se esperan en la respuesta de SAML y con qué nombre (atributo de la entidad de seguridad) se accede a este atributo en el código.
    > 
    
    b. El **atributo predeterminado** de la captura de pantalla solo es para fines ilustrativos. No es necesario para que el escenario funcione.  
 
    c. Los nombres y valores para el **Principal Attribute** (Atributo principal) que se muestran en la captura de pantalla dependen de cómo se desarrolle la aplicación. Es posible que la aplicación requiera diferentes asignaciones.

### <a name="assertion-based-groups"></a>Grupos basados en aserción

Como paso opcional, puede configurar grupos basados en aserciones para su proveedor de identidades de Azure Active Directory.

El uso de grupos en SAP Cloud Platform permite asignar dinámicamente uno o más usuarios a uno o más roles en las aplicaciones SAP Cloud Platform, en función de los valores de los atributos de la aserción de SAML 2.0. 

Por ejemplo, si la aserción contiene el atributo "*contrato=temporal*", puede que desee que se agreguen todos los usuarios afectados al grupo "*TEMPORAL*". El grupo "*TEMPORAL*" puede contener uno o varios roles de una o más de las aplicaciones implementadas en la cuenta de SAP Cloud Platform.
 
Use grupos basados en aserciones si quiere asignar de manera simultánea muchos usuarios a uno o más roles de las aplicaciones en su cuenta de SAP Cloud Platform. Si quiere asignar un número único o pequeño de usuarios a roles específicos, se recomienda asignarlos directamente en la pestaña "**Autorizaciones**" de SAP Cloud Platform Cockpit.

### <a name="create-sap-cloud-platform-test-user"></a>Creación de un usuario de prueba de SAP Cloud Platform

Para permitir que los usuarios de Azure AD inicien sesión en SAP Cloud Platform, debe asignarles roles en SAP Cloud Platform.

**Para asignar un rol a un usuario, lleve a cabo los siguientes pasos:**

1. Inicie sesión en de **SAP Cloud Platform Cockpit**.

2. Lleve a cabo los siguientes pasos:
   
    ![Authorizations (Autorizaciones)](./media/sap-hana-cloud-platform-tutorial/roles.png "Autorizaciones")
   
    a. Haga clic en **Authorization**(Autorización).

    b. Haga clic en la pestaña **Usuarios**.

    c. En el cuadro de texto **User** (Usuario), escriba la dirección de correo electrónico del usuario.

    d. Haga clic en **Assign** (Asignar) para asignar el usuario a un rol.

    e. Haga clic en **Save**(Guardar).

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la URL de inicio de sesión de SAP Cloud Platform, donde podrá poner en marcha el flujo de inicio de sesión. 

* Acceda directamente a la URL de inicio de sesión de SAP Cloud Platform y ponga en marcha el flujo de inicio de sesión desde ahí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de SAP Cloud Platform de Aplicaciones, debería iniciar sesión automáticamente en la aplicación SAP Cloud Platform para la que configurara el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado SAP Cloud Platform, puede aplicar el control de sesión que protege la información confidencial de la organización de la filtración y la infiltración en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).