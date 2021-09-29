---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con F5 | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: fbc7df55d9c52b65a0f09569f8e65ff2db5efdd1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796236"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Tutorial: Configuración del inicio de sesión único entre Azure Active Directory y F5

En este tutorial, obtendrá información sobre cómo integrar F5 con Azure Active Directory (Azure AD). Al integrar F5 con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a F5.
* Permitir que los usuarios inicien sesión automáticamente en F5 con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

> [!NOTE]
> BIG-IP APM de F5 [Comprar ahora](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="prerequisites"></a>Prerrequisitos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

* Una suscripción habilitada para el inicio de sesión único (SSO) en F5.

* La implementación de la solución conjunta requiere la licencia siguiente:

    * F5 BIG-IP® Best bundle (o) 

    * Licencia independiente de F5 BIG-IP Access Policy Manager™ (APM). 

    * Licencia del complemento F5 BIG-IP Access Policy Manager™ (APM) en una instalación de F5 BIG-IP® Local Traffic Manager™ (LTM) ya existente.

    * Además de la licencia anterior, el sistema F5 también puede tener licencia para: 

        * Una suscripción de filtrado de direcciones URL para usar la base de datos de categorías de URL.

        * Una suscripción a F5 IP Intelligence para detectar y bloquear a atacantes conocidos y tráfico malintencionado.
     
        * Un módulo de seguridad de hardware (HSM) de red para proteger y administrar claves digitales para la autenticación segura.

* El sistema F5 BIG-IP se aprovisiona con módulos de APM (LTM es opcional).

* Aunque es opcional, es muy recomendable implementar los sistemas F5 en un [grupo de dispositivos de sincronización/conmutación por error](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que incluye el par activo en espera, con una dirección IP flotante para la alta disponibilidad (HA). Se puede lograr una mayor redundancia de la interfaz mediante el protocolo de control de agregación de vínculos (LACP). LACP administra las interfaces físicas conectadas como una sola interfaz virtual (grupo agregado) y detecta cualquier error de interfaz dentro del grupo.

* En el caso de las aplicaciones Kerberos, una cuenta de servicio de AD local para la delegación restringida.  Consulte la [Documentación de F5](https://support.f5.com/csp/article/K43063049) para crear una cuenta de delegación de AD.

## <a name="access-guided-configuration"></a>Configuración de acceso guiado

* La configuración de acceso guiado es compatible con F5 TMOS versión 13.1.0.8 y posteriores. Si el sistema BIG-IP ejecuta una versión anterior a la 13.1.0.8, consulte la sección **Configuración avanzada**.

* La configuración de acceso guiado presenta una experiencia de usuario completamente nueva y simplificada. Esta arquitectura basada en el flujo de trabajo proporciona pasos de configuración intuitivos y reentrantes adaptados a la topología seleccionada.

* Antes de continuar con la configuración, actualice la configuración guiada; para ello, descargue el último paquete de casos de uso desde [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para actualizar, siga el procedimiento siguiente.

    >[!NOTE]
    >Las capturas de pantalla siguientes corresponden a la versión más reciente (BIG-IP 15.0 con AGC versión 5.0). Los pasos de configuración siguientes son válidos para este caso de uso desde la versión 13.1.0.8 hasta la última versión de BIG-IP.

1. En la interfaz de usuario web de F5 BIG-IP, haga clic en **Access >> Guided Configuration** (Acceso >> Configuración guiada).

1. En la página **Guided Configuration** (Configuración guiada), haga clic en **Upgrade Guided Configuration** (Actualizar configuración guiada) en la esquina superior izquierda.

    ![Captura de pantalla que muestra la página Guided Configuration (Configuración guiada) con el vínculo Update Guided Configuration (Actualizar configuración guiada).](./media/headerf5-tutorial/configure14.png) 

1. En la pantalla emergente Upgrade Guided Configuration (Actualizar configuración guiada), seleccione **Choose File** (Elegir archivo) para cargar el paquete de casos de uso descargado y haga clic en el botón **Upload and Install** (Cargar e instalar).

    ![Captura de pantalla que muestra el cuadro de diálogo Upgrade Guided Configuration (Actualizar configuración guiada) con Choose File (Elegir archivo) seleccionado.](./media/headerf5-tutorial/configure15.png) 

1. Cuando se complete la actualización, haga clic en el botón **Continue** (Continuar).

    ![Captura de pantalla que muestra el cuadro de diálogo Upgrade Guided Configuration (Actualizar configuración guiada) con un mensaje de finalización.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* El inicio de sesión único de F5 se puede configurar de tres maneras diferentes:

- [Configuración del inicio de sesión único de F5 para la aplicación basada en encabezados](#configure-f5-single-sign-on-for-header-based-application)

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos](kerbf5-tutorial.md)

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos avanzada](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Escenarios de autenticación de claves

* Además de la compatibilidad de la integración nativa de Azure Active Directory con protocolos de autenticación modernos, como Open ID Connect, SAML y WS-Fed, F5 amplía el acceso seguro para aplicaciones de autenticación heredadas para el acceso interno y externo con Azure AD, lo que permite escenarios modernos (por ejemplo, el acceso sin contraseña) para estas aplicaciones. Esto incluye:

* Aplicaciones de autenticación basadas en encabezados

* Aplicaciones de autenticación Kerberos

* Autenticación anónima o aplicaciones sin autenticación integrada

* Aplicaciones de autenticación NTLM (protección con dos preguntas para el usuario)

* Aplicaciones basadas en formularios (protección con dos preguntas para el usuario)

## <a name="adding-f5-from-the-gallery"></a>Adición de F5 desde la galería

Para configurar la integración de F5 en Azure AD, será preciso agregar F5 desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **F5** en el cuadro de búsqueda.
1. Seleccione **F5** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-f5"></a>Configuración y prueba del inicio de sesión único de Azure AD para F5

Configure y pruebe el inicio de sesión único de Azure AD con F5 mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de F5.

Para configurar y probar el inicio de sesión único de Azure AD con F5, lleve a cabo los siguientes pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de F5](#configure-f5-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de F5](#create-f5-test-user)** , para tener un homólogo de B.Simon en F5 que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de aplicaciones de **F5**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en modo iniciado por **IDP**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://<YourCustomFQDN>.f5.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<YourCustomFQDN>.f5.com/`

1. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y **Certificado (Base 64)** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Set up F5** (Configurar F5), copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B.Simon acceda a F5 mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **F5**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-f5-sso"></a>Configuración del inicio de sesión único de F5

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos](kerbf5-tutorial.md)

- [Configuración del inicio de sesión único de F5 para la aplicación Kerberos avanzada](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configuración del inicio de sesión único de F5 para la aplicación basada en encabezados

### <a name="guided-configuration"></a>Configuración guiada

1. Abra una nueva ventana del explorador web e inicie sesión en el sitio de empresa de F5 (basada en encabezados) como administrador. Luego, realice los pasos siguientes:

1. Vaya a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Administración de certificados > Administración de certificados de tráfico > Lista de certificados SSL). Seleccione **Import** (Importar) en la esquina derecha. Especifique un valor en **Certificate Name** (Nombre de certificado) (se hará referencia a él más adelante en la configuración). En **Certificate Source** (Origen del certificado), seleccione Upload file (Cargar archivo) y especifique el certificado descargado de Azure al configurar el inicio de sesión único de SAML. Haga clic en **Import**.

    ![Captura de pantalla que muestra la lista de certificados SSL en la que se seleccionan el nombre y el origen del certificado.](./media/headerf5-tutorial/configure12.png)
 
1. Además, necesitará el **Certificado SSL para el nombre de host de la aplicación. Vaya a System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Administración de certificados > Administración de certificados de tráfico > Lista de certificados SSL). Seleccione **Import** (Importar) en la esquina derecha. El campo **Import Type** (Tipo de importación) será **PKCS 12 (IIS)** . Especifique un valor para **Key Name** (Nombre de clave) (se hará referencia a él más adelante en la configuración) y especifique el archivo PFX. Especifique el campo **Password** (Contraseña) del archivo PFX. Haga clic en **Import**.

    >[!NOTE]
    >En el ejemplo, el nombre de la aplicación es `Headerapp.superdemo.live` y se usa un certificado comodín; el nombre de clave es `WildCard-SuperDemo.live`.

    ![Captura de pantalla que muestra la página SSL Certificate/Key Source (Certificado SSL/ Origen de la clave).](./media/headerf5-tutorial/configure13.png)

1. Usaremos la experiencia guiada para configurar la federación de Azure AD y el acceso a las aplicaciones. Vaya a la página **Main** (Principal) de F5 BIG-IP y seleccione **Access > Guided Configuration > Federation > SAML Service Provider** (Acceso > Configuración guiada > Federación > Proveedor de servicios SAML). Haga clic en **Next** (Siguiente) y, a continuación, en **Next** (Siguiente) para comenzar la configuración.

    ![Captura de pantalla que muestra la página Guided Configuration (Configuración guiada) con Federation (Federación) seleccionado.](./media/headerf5-tutorial/configure01.png)

    ![Captura de pantalla que muestra la página SAML Service Provider (Proveedor de servicios de SAML).](./media/headerf5-tutorial/configure02.png)
 
1. Proporcione un valor para **Configuration Name** (Nombre de configuración). Especifique el valor de **Entity ID** (Identificador de entidad) (el mismo que configuró en la configuración de la aplicación de Azure AD). Especifique el campo **Host name** (Nombre de host). Agregue un valor para **Description** (Descripción) como referencia. Acepte las entradas predeterminadas restantes y seleccione y, a continuación, haga clic en **Save & Next** (Guardar y siguiente).

    ![Captura de pantalla que muestra la página Service Provider Properties (Propiedades del proveedor de servicios).](./media/headerf5-tutorial/configure03.png) 

1. En este ejemplo, vamos a crear un nuevo servidor virtual como 192.168.30.20 con el puerto 443. Especifique la dirección IP del servidor virtual en el campo **Destination Address** (Dirección de destino). Seleccione el campo **SSL Profile** (Perfil SSL) del cliente y seleccione Create new (Crear nuevo). Especifique el certificado de aplicación cargado previamente (el certificado comodín en este ejemplo) y la clave asociada y, a continuación, haga clic en **Save & Next** (Guardar y siguiente).

    >[!NOTE]
    >En este ejemplo, el servidor web interno se ejecuta en el puerto 888 y queremos publicarlo en el 443.

    ![Captura de pantalla que muestra la página Virtual Server Properties (Propiedades de servidor virtual).](./media/headerf5-tutorial/configure04.png) 

1. En **Select method to configure your IdP connector** (Seleccionar el método para configurar el conector del proveedor de identidades), especifique Metadata (Metadatos), haga clic en Choose File (Elegir archivo) y cargue el archivo XML de metadatos que descargó antes desde Azure AD. Especifique un campo **Name** (Nombre) único para el conector del proveedor de identidades de SAML. Elija el **Certificado de firma de metadatos** que cargó anteriormente. Haga clic en **Save & Next** (Guardar y siguiente).

    ![Captura de pantalla que muestra la página de configuración del conector del proveedor de identidades externo.](./media/headerf5-tutorial/configure05.png)
 
1. En **Select a Pool** (Seleccionar un grupo), especifique **Create New** (Crear nuevo) (también puede seleccionar un grupo que ya existe). Deje los otros valores como predeterminados. En Pool Servers (Servidores de grupo), escriba la dirección IP en **IP Address/Node Name** (Dirección IP/Nombre de nodo). Especifique el campo **Port** (Puerto). Haga clic en **Save & Next** (Guardar y siguiente).

    ![Captura de pantalla que muestra la página Pool Properties (Propiedades del grupo).](./media/headerf5-tutorial/configure06.png)

1. En la pantalla Single Sign-On Settings (Configuración de inicio de sesión único), seleccione **Enable Single Sign-On** (Habilitar inicio de sesión único). En Selected Single Sign-On Type (Tipo de inicio de sesión único seleccionado), elija **HTTP header-based** (Basado en encabezado HTTP). Reemplace **session.saml.last.Identity** por **session.saml.last.attr.name.Identity** en Username Source (esta variable se establece mediante la asignación de notificaciones en Azure AD). En SSO Headers (Encabezados de SSO).

    * **Header Name (Nombre de encabezado): MyAuthorization**

    * **Header Value (Valor de encabezado): %{session.saml.last.attr.name.Identity}**

    * Haga clic en **Save & Next** (Guardar y siguiente).

    Consulte el Apéndice para obtener una lista completa de las variables y los valores. Puede agregar más encabezados según sea necesario.

    >[!NOTE]
    >Account Name (Nombre de cuenta) es la cuenta de delegación de F5 creada (consulte la documentación de F5).

    ![Captura de pantalla que muestra la página de configuración del inicio de sesión único.](./media/headerf5-tutorial/configure07.png) 

1. Para los fines de esta guía, se omitirán las comprobaciones del punto de conexión.  Consulte la documentación de F5 para más información. Seleccione **Save & Next** (Guardar y siguiente).

    ![Captura de pantalla que muestra la página Endpoint Checks Properties (Propiedades de comprobaciones de puntos de conexión).](./media/headerf5-tutorial/configure08.png)

1. Acepte los valores predeterminados y haga clic en **Save & Next** (Guardar y siguiente). Consulte la documentación de F5 para más información sobre la configuración de la administración de sesiones de SAML.

    ![Captura de pantalla que muestra la pestaña Timeout Settings (Configuración de tiempo de espera).](./media/headerf5-tutorial/configure09.png)

1. Revise la pantalla de resumen y seleccione **Deploy** (Implementar) para configurar BIG-IP. Haga clic en **Finish** (Finalizar).

    ![Captura de pantalla que muestra la página Your application is ready to be deployed (Aplicación lista para la implementación).](./media/headerf5-tutorial/configure10.png)

    ![Captura de pantalla que muestra la página Your application is deployed (Aplicación implementada).](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Configuración avanzada

Esta sección está pensada para emplearse si no puede usar la Configuración guiada o si desea agregar o modificar parámetros adicionales. Necesitará el Certificado TLS/SSL para el nombre de host de la aplicación.

1. Vaya a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Administración de certificados > Administración de certificados de tráfico > Lista de certificados SSL). Seleccione **Import** (Importar) en la esquina derecha. El campo **Import Type** (Tipo de importación) será **PKCS 12 (IIS)** . Especifique un valor para **Key Name** (Nombre de clave) (se hará referencia a él más adelante en la configuración) y especifique el archivo PFX. Especifique el campo **Password** (Contraseña) del archivo PFX. Haga clic en **Import**.

    >[!NOTE]
    >En el ejemplo, el nombre de la aplicación es `Headerapp.superdemo.live` y se usa un certificado comodín; el nombre de clave es `WildCard-SuperDemo.live`.
  
    ![Captura de pantalla que muestra la página SSL Certificate/Key Source (Certificado SSL/ Origen de la clave) para la configuración avanzada.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Adición de un nuevo servidor web a BigIP-F5

1. Haga clic en **Main > IApps > Application Services > Application > Create** (Principal > IApps > Servicios de aplicación > Aplicación > Crear).

1. Proporcione un valor para **Name** (Nombre) y, en **Template** (Plantilla), elija **f5.http**.
 
    ![Captura de pantalla que muestra la página Application Services (Servicios de aplicación) con Template (Plantilla) seleccionado.](./media/headerf5-tutorial/configure18.png)

1. En este caso, publicaremos HeaderApp2 externamente como HTTPS, para **how should the BIG-IP system handle SSL Traffic** (¿Cómo debe controlar el sistema de BIG-IP el tráfico SSL?) se especifica **Terminate SSL from Client, Plaintext to servers (SSL Offload)** (Finalizar SSL del cliente y texto no cifrado en los servidores [descarga SSL]). Especifique el certificado y la clave en los campos **Which SSL certificate do you want to use?** (¿Qué certificado SSL desea usar?) y **Which SSL private key do you want to use?** (¿Qué clave privada SSL desea usar?). Especifique la dirección IP del servidor virtual en **What IP Address do you want to use for the Virtual Server?** (¿Qué dirección IP desea usar para el servidor virtual?). 

    * **Especificar otros detalles**

        * FQDN  

        * Especifique un grupo de aplicaciones existente o cree uno nuevo.

        * Si va a crear un nuevo servidor de aplicaciones, especifique los campos **internal IP Address** (Dirección IP interna) y **port number** (Número de puerto).

        ![Captura de pantalla que muestra el panel para especificar estos detalles.](./media/headerf5-tutorial/configure19.png) 

1. Haga clic en **Finished** (Finalizado).

    ![Captura de pantalla que muestra la página tras la finalización.](./media/headerf5-tutorial/configure20.png) 

1. Asegúrese de que se pueden modificar las propiedades de la aplicación. Haga clic en **Main > IApps > Application Services: Applications >> HeaderApp2** (Principal > IApps > Servicios de aplicación: Aplicaciones > HeaderApp2). Desactive **Strict Updates** (Actualizaciones estrictas) (modificaremos algunos valores fuera de la GUI). Haga clic en el botón **Update** (Actualizar).

    ![Captura de pantalla que muestra la página Application Services (Servicios de aplicación) con la pestaña Properties (Propiedades) seleccionada.](./media/headerf5-tutorial/configure21.png) 

1. En este momento, debería poder examinar el servidor virtual.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Configuración de F5 como SP y Azure como IDP

1.  Haga clic en **Access > Federation> SAML Service Provider > Local SP Service > (Acceso > Federación > Proveedor de servicios de SAML > Servicio SP local) y haga clic en Create (Crear) o el signo +** .

    ![Captura de pantalla que muestra la página About this BIG-IP (Acerca de este BIG I P). ](./media/headerf5-tutorial/configure22.png)

1. Especifique los detalles del servicio del proveedor de servicios. Especifique el campo **Name** (Nombre) que representa la configuración de SP de F5. Especifique el campo **Entity ID** (Identificador de entidad) (suele ser la dirección URL de la aplicación).

    ![Captura de pantalla que muestra la página SAML Service Provider (Proveedor de servicios de SAML) con el cuadro de diálogo Create New SAML SP Service (Crear servicio del proveedor de servicios de SAML).](./media/headerf5-tutorial/configure23.png)

    ![Captura de pantalla que muestra el cuadro de diálogo Create New SAML SP Service (Crear servicio del proveedor de servicios de SAML) con Endpoint Settings (Configuración del punto de conexión) seleccionado.](./media/headerf5-tutorial/configure24.png)

    ![Captura de pantalla que muestra el cuadro de diálogo Create New SAML SP Service (Crear servicio del proveedor de servicios de SAML) con Security Settings (Configuración de seguridad) seleccionado.](./media/headerf5-tutorial/configure25.png)

    ![Captura de pantalla que muestra el cuadro de diálogo Create New SAML SP Service (Crear servicio del proveedor de servicios de SAML) con Authentication Context (Contexto de autenticación) seleccionado.](./media/headerf5-tutorial/configure26.png)

    ![Captura de pantalla que muestra el cuadro de diálogo Create New SAML SP Service (Crear servicio del proveedor de servicios de SAML) con Requested Attributes (Atributos solicitados) seleccionado.](./media/headerf5-tutorial/configure27.png)

    ![Captura de pantalla que muestra el cuadro de diálogo Edit SAML SP Service (Modificar servicio del proveedor de servicios de SAML) con Advanced Settings (Configuración avanzada) seleccionado.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Creación del conector del proveedor de identidades

1. Haga clic en el botón **Bind/Unbind IdP Connectors** (Enlazar/desenlazar conectores del IdP), seleccione **Create New IdP Connector** (Crear nuevo conector del IdP), elija la opción **Metadata** (Metadatos) y, a continuación, realice los pasos siguientes:
 
    ![Captura de pantalla que muestra Edit SAML IdPs that use this SP (Editar IdP que usan este proveedor de servicios) con Create new IdP Connector (Crear conector de IdP) seleccionado.](./media/headerf5-tutorial/configure29.png)

    a. Vaya al archivo metadata.xml descargado de Azure AD y especifique un valor para **Identity Provider Name** (Nombre del proveedor de identidades).

    b. Haga clic en **Ok** (Aceptar).

    c. Se crea el conector y el certificado está listo automáticamente desde el archivo XML de metadatos.
    
    ![Captura de pantalla que muestra el cuadro de diálogo Create New SAML IdP Connector (Crear conector de IdP de SAML).](./media/headerf5-tutorial/configure30.png)

    d. Configure F5BIG-IP para que envíe todas las solicitudes a Azure AD.

    e. Haga clic en **Add New Row** (Agregar nueva fila), elija **AzureIDP** (tal y como se creó en los pasos anteriores), especifique 

    f. **Matching Source   =  %{session.server.landinguri}** 

    g. **Matching Value     = /** *

    h. Haga clic en **Update** (Actualizar).

    i. Haga clic en **Aceptar**

    j. **Se completó la configuración del IDP de SAML**
    
    ![Captura de pantalla que muestra el cuadro de diálogo Edit SAML IdPs that use this SP (Editar IdP de SAML que usan este proveedor de servicios).](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Configuración de la directiva de F5 para redirigir a los usuarios al IDP de SAML de Azure

1. Para configurar la directiva de F5 para redirigir a los usuarios al IDP de SAML de Azure, realice los pasos siguientes:

    a. Haga clic en **Main > Access > Profile/Policies > Access Profiles** (Principal > Acceso > Perfiles/Directivas > Perfiles de acceso).

    b. Haga clic en el botón **Crear**.

    ![Captura de pantalla que muestra la página Access Profiles (Perfiles de acceso).](./media/headerf5-tutorial/configure32.png)
 
    c. Especifique un valor para **Name** (Nombre) (en el ejemplo, HeaderAppAzureSAMLPolicy).

    d. Puede personalizar otras opciones de configuración. Consulte la documentación de F5.

    ![Captura de pantalla que muestra la página General Properties (Propiedades generales).](./media/headerf5-tutorial/configure33.png)

    ![Captura de pantalla que muestra la página General Properties (Propiedades generales) (continuación).](./media/headerf5-tutorial/configure34.png) 

    e. Haga clic en **Finished** (Finalizado).

    f. Una vez completada la creación de la directiva, haga clic en la directiva y vaya a la pestaña **Access Policy** (Directiva de acceso).

    ![Captura de pantalla que muestra la pestaña Access Policy (Directiva de acceso) con General Properties (Propiedades generales).](./media/headerf5-tutorial/configure35.png)
 
    g. Haga clic en **Visual Policy editor** (Editor visual de directivas) y edite el vínculo **Access Policy for Profile** (Directiva de acceso del perfil).

    h. Haga clic en el signo + en el editor visual de directivas y elija **SAML Auth** (Autenticación de SAML).

    ![Captura de pantalla que muestra una directiva de acceso.](./media/headerf5-tutorial/configure36.png)

    ![Captura de pantalla que muestra un cuadro de diálogo de búsqueda con la autenticación SAML seleccionada.](./media/headerf5-tutorial/configure37.png)
 
    i. Haga clic en **Add Item** (Agregar elemento).

    j. En **Properties** (Propiedades), especifique un valor para **Name** (Nombre) y en **AAA Server** (Servidor de AAA), seleccione el SP configurado previamente y haga clic en **Save** (Guardar).
 
    ![Captura de pantalla que muestra las propiedades del elemento, incluido su servidor AAA.](./media/headerf5-tutorial/configure38.png)

    k. La directiva básica está lista y puede personalizar la directiva para incorporar almacenes de orígenes y atributos adicionales.

    ![Captura de pantalla que muestra la directiva personalizada.](./media/headerf5-tutorial/configure39.png)
 
    l. Asegúrese de hacer clic en el vínculo **Apply Access Policy** (Aplicar directiva de acceso) en la parte superior.

### <a name="apply-access-profile-to-the-virtual-server"></a>Aplicación del perfil de acceso al servidor virtual

1. Asigne el perfil de acceso al servidor virtual con el fin de que F5 BIG-IP APM aplique la configuración del perfil al tráfico entrante y ejecute la directiva de acceso definida previamente.

    a. Haga clic en **Main** > **Local Traffic** > **Virtual Servers** (Principal > Tráfico local > Servidores virtuales).

    ![Captura de pantalla que muestra la página Virtual Server List (Lista de servidores virtuales).](./media/headerf5-tutorial/configure40.png)
 
    b. Haga clic en el servidor virtual, desplácese a la sección **Access Policy** (Directiva de acceso) en la lista desplegable **Access Profile** (Perfil de acceso) y seleccione la directiva de SAML creada (en el ejemplo, HeaderAppAzureSAMLPolicy).

    c. Haga clic en **Update** (Actualizar).
 
    ![Captura de pantalla del panel Access Policy (Directiva de acceso).](./media/headerf5-tutorial/configure41.png)

    d. Cree una regla iRule® de F5 BIG-IP para extraer los atributos de SAML personalizados de la aserción entrante y pasarlos como encabezados HTTP a la aplicación de prueba de back-end. Haga clic en **Main > Local Traffic > iRules > iRule List (Principal > Tráfico local > iRules > Lista de iRules) y haga clic en Create (Crear)** .

    ![Captura de pantalla que muestra la lista de iRule del tráfico local.](./media/headerf5-tutorial/configure42.png)
 
    e. Pegue el texto siguiente de la regla iRule de F5 BIG-IP en la ventana de definición.

    ![Captura de pantalla que muestra la página New iRule (Nueva iRule).](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Salida de ejemplo a continuación**

    ![Captura de pantalla que muestra la salida de ejemplo.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Creación de un usuario de prueba de F5

En esta sección, creará un usuario llamado B.Simon en F5. Trabaje con el [equipo de soporte técnico del cliente de F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para agregar los usuarios a la plataforma de F5. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Haga clic en **Probar esta aplicación** en Azure Portal. Esto le redirigirá a la dirección URL de inicio de sesión de F5, donde puede poner en marcha el flujo de inicio de sesión.  

* Vaya directamente a la dirección URL de inicio de sesión de F5 y ponga en marcha el flujo de inicio de sesión desde allí.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Haga clic en **Probar esta aplicación** en Azure Portal; debería iniciar sesión automáticamente en F5 para la que ha configurado el inicio de sesión único. 

También puede usar Aplicaciones de Microsoft para probar la aplicación en cualquier modo. Al hacer clic en el icono de F5 en Mis aplicaciones, si se ha configurado en modo SP, se le redirigirá a la página de inicio de sesión de la aplicación para comenzar el flujo de inicio de sesión; y si se ha configurado en modo IDP, se debería iniciar sesión automáticamente en la instancia de F5 para la que ha configurado el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

> [!NOTE]
> BIG-IP APM de F5 [Comprar ahora](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado F5, puede aplicar el control de sesión, que protege a su organización, en tiempo real, frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).