---
title: Integración de Azure Active Directory con F5 BIG-IP para el inicio de sesión único con autenticación basada en formularios
description: Obtenga información sobre cómo integrar el administrador de directivas de acceso BIG-IP (APM) de F5 y Azure Active Directory para obtener acceso híbrido seguro a las aplicaciones basadas en formularios.
author: gargi-sinha
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51fb95b6f79bd306a1e936fa99da7a55fe2eea2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039937"
---
# <a name="tutorial-integrate-azure-active-directory-with-f5-big-ip-for-forms-based-authentication-single-sign-on"></a>Tutorial: Integración de Azure Active Directory con F5 BIG-IP para el inicio de sesión único con autenticación basada en formularios

En este tutorial, aprenderá a integrar Access Policy Manager (APM) BIG-IP de F5 y Azure Active Directory (Azure AD) para el acceso híbrido seguro a las aplicaciones basadas en formularios.

La integración de las aplicaciones publicadas de BIG-IP en Azure AD proporciona numerosas ventajas, incluidas las siguientes:

- Gobernanza mejorada de la confianza cero mediante la autenticación previa y la autorización de Azure AD.

- Inicio de sesión único (SSO) completo entre Azure AD y los servicios publicados de BIG-IP

- Administración de identidades y acceso desde un solo plano de control: [Azure Portal](https://portal.azure.com)

Para obtener información sobre todas las ventajas, consulte el artículo sobre la [integración de F5 BIG-IP y Azure AD](f5-aad-integration.md). y [qué es el acceso a aplicaciones y el inicio de sesión único con Azure AD](../active-directory-appssoaccess-whatis.md).

## <a name="scenario-description"></a>Descripción del escenario

En este escenario, tenemos una aplicación heredada interna que se ha configurado para la autenticación basada en formularios (FBA).

El escenario ideal es que la aplicación se administre y se controle directamente a través de Azure AD, pero al carecer de cualquier forma de interoperabilidad con protocolos modernos, requeriría un tiempo y esfuerzo considerables para modernizarse; esto conllevaría costos inevitables y riesgos de posibles tiempos de inactividad.

En su lugar, la implementación de BIG-IP Virtual Edition (VE) entre Internet y la red virtual interna de Azure a la que está conectada la aplicación se usará como puerta de acceso de entrada y se contará con Azure AD para su amplia selección de funcionalidades de autenticación y autorización.

Tener una instancia de BIG-IP delante de la aplicación nos permite superponer el servicio con la autenticación previa de Azure AD y el inicio de sesión único basado en formularios, lo que mejora significativamente la posición de seguridad general de la aplicación y permite que la empresa siga funcionando a buen ritmo, sin interrupción.

La solución de acceso híbrido seguro para este escenario está integrada por los componentes siguientes:

**Aplicación**: servicio back-end que se va a proteger con Azure AD y el acceso híbrido seguro de BIG-IP. Esta aplicación concreta valida las credenciales de usuario con un código abierto, pero podría ser cualquier directorio, incluidos Active Directory o LDS, entre otros.

**Azure AD**: proveedor de identidades SAML (IdP), responsable de la comprobación de las credenciales de usuario, el acceso condicional (CA) y el inicio de sesión único en BIG-IP APM.

**BIG-IP**: proxy inverso y proveedor de servicios SAML (SP) en la aplicación, que delega la autenticación al IdP de SAML, antes de realizar el inicio de sesión único basado en formularios en la aplicación back-end.

![Captura de pantalla que muestra el diagrama de flujo](./media/f5-big-ip-forms-advanced/flow-diagram.png)

| Pasos | Descripción|
|:-------|:----------|
| 1. | El usuario se conecta al punto de conexión del proveedor de servicios SAML de la aplicación (BIG-IP APM).|
|2. | La directiva de acceso de APM redirige al usuario al IdP de SAML (Azure AD) para la autenticación previa.|
| 3. | El IdP de SAML autentica al usuario y aplica las directivas de CA exigidas.|
| 4. | Azure AD redirige al usuario al proveedor de servicios de SAML con el token y las notificaciones que se han emitido. |
| 5. | APM solicita la contraseña de la aplicación y la almacena en caché. |
| 6. |  La solicitud de BIG-IP para la aplicación recibe el formulario de inicio de sesión.|
| 7. | El scripting de APM responde mediante el relleno del nombre de usuario y la contraseña antes de enviar el formulario.|
| 8. | El servidor web ofrece la carga útil de la aplicación y se envía al cliente. De manera opcional, APM detecta un inicio de sesión correcto mediante el examen de los encabezados de respuesta, donde busca el URI de redirección o cookie. |

## <a name="prerequisites"></a>Prerrequisitos

No es necesario tener experiencia previa en BIG-IP, pero necesitará lo siguiente:

- Una suscripción gratuita de Azure AD u otra de nivel superior

- Una instancia de BIG-IP existente o [implementar una instancia de BIG-IP Virtual Edition (VE) en Azure](f5-bigip-deployment-guide.md)

- Cualquiera de las siguientes SKU de licencias de F5 BIG-IP

  - F5 BIG-IP&reg; Best bundle

  - Licencia independiente de F5 BIG-IP Access Policy Manager&trade; (APM)

  - Licencia del complemento F5 BIG-IP Access Policy Manager&trade; (APM) en una instalación de F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM) ya existente.

  - [Licencia de evaluación gratuita](https://www.f5.com/trial/big-ip-trial.php) completa de 90 días de BIG-IP

- Identidades de usuario [sincronizadas](../hybrid/how-to-connect-sync-whatis.md) desde un directorio local en Azure AD

- Una cuenta con [permisos](../roles/permissions-reference.md#application-administrator) de administrador de la aplicación de Azure AD

- [Certificado SSL](f5-bigip-deployment-guide.md#ssl-profile) para publicar servicios a través de HTTPS o el uso de certificados predeterminados durante las pruebas.

- Una aplicación de autenticación basada en formularios existente o [configurar una aplicación FBA de IIS](/troubleshoot/aspnet/forms-based-authentication) para pruebas

## <a name="deployment-modes"></a>Modos de implementación

Existen varios métodos para configurar una instancia de BIG-IP para este escenario, incluidas varias opciones basadas en asistentes o una configuración avanzada.

En este tutorial se trata el enfoque avanzado, que es un enfoque más flexible para implementar el acceso híbrido seguro mediante la creación manual de todos los objetos de configuración de BIG-IP. Este enfoque también se puede usar para los escenarios que no se describen en la configuración guiada.

>[!NOTE]
>Todas las cadenas o los valores de ejemplo a los que se hace referencia en este artículo deben reemplazarse por los de su entorno real.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Adición de F5 BIG-IP desde la galería de Azure AD

La configuración de la confianza de federación de SAML entre BIG-IP APM y Azure AD es uno de los primeros pasos para implementar el acceso híbrido seguro. Establece la integración necesaria para que BIG-IP traspase la autenticación previa y el [acceso condicional](../conditional-access/overview.md) a Azure AD, antes de conceder acceso al servicio publicado.

1. Inicie sesión en **Azure Portal** con una cuenta que tenga derechos de administrador de la aplicación.

2. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.

3. Vaya a **Aplicaciones empresariales** y, en la cinta de opciones de la parte superior, seleccione **+ Nueva aplicación**.

4. Busque **F5** en la galería y seleccione **F5 BIG-IP APM Azure AD integration** (Integración de Azure AD con F5 BIG-IP APM).

5. Proporcione un nombre para la aplicación y, después, seleccione **Agregar o Crear** para agregarlo al inquilino. El nombre debe reflejar ese servicio específico.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

1. Con las nuevas propiedades de la aplicación **F5** a la vista, vaya a **Administrar** > **Inicio de sesión único**

2. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML** y omita el mensaje para guardar la configuración de inicio de sesión único; para ello, seleccione **No, lo guardaré más tarde**.

3. En la hoja **Configuración del inicio de sesión único con SAML**, seleccione el icono con forma de lápiz en **Configuración básica de SAML** y proporcione los detalles siguientes:

   a. Reemplace la dirección URL predefinida del **Identificador** por la dirección URL del servicio publicado de BIG-IP. Por ejemplo: `https://myvacation.contoso.com`

   b. Haga lo mismo con la dirección **URL de respuesta**, pero incluya la ruta de acceso del punto de conexión de SAML de APM. Por ejemplo: `https://myvacation.contoso.com/saml/sp/profile/post/acs`

      >[!NOTE]
      >En esta configuración, el flujo SAML funcionaría en un modo iniciado por IdP, donde Azure AD emite el usuario con una aserción de SAML antes de redirigir al punto de conexión de servicio de BIG-IP para la aplicación. BIG-IP APM admite los modos iniciados tanto por IdP como por SP.

   c. Para `Logout URI`, especifique el punto de conexión de cierre de sesión único (SLO) de BIG-IP APM precedido por el encabezado host del servicio que se está publicando. `Providing an SLO URI` garantiza que la sesión de BIG-IP APM del usuario también finaliza después de haber cerrado la sesión de Azure AD. Por ejemplo: `https://myvacation.contoso.com/saml/sp/profile/redirect/slr`

     ![Captura de pantalla que muestra la edición de la configuración básica de SAML](./media/f5-big-ip-forms-advanced/basic-saml-configuration.png)

     >[!Note]
     > Desde TMOS v16, el punto de conexión de cierre de sesión único de SAML ha cambiado a /saml/sp/profile/redirect/slo.

4. Seleccione **Guardar** antes de salir de la hoja de configuración de SAML y omita el mensaje de prueba del inicio de sesión único.

   Observe las propiedades de la sección **Atributos y notificaciones de usuario**, ya que estas son las que Azure AD emitirá para los usuarios para la autenticación con BIG-IP APM y el inicio de sesión único en la aplicación back-end.

5. En la sección **Certificado de firma de SAML**, seleccione el botón **Descargar** para guardar el archivo **XML de metadatos de federación** en el equipo.

   ![Captura de pantalla del vínculo de descarga de los metadatos de federación](./media/f5-big-ip-forms-advanced/saml-certificate.png)

   Los certificados de firma SAML que Azure AD crea tienen una duración de tres años y deben administrarse con la [guía](manage-certificates-for-federated-single-sign-on.md) publicada.

### <a name="azure-ad-authorization"></a>Autorización de Azure AD

De forma predeterminada, Azure AD solo emitirá tokens para los usuarios a los que se haya concedido acceso a una aplicación.

1. En la vista de configuración de la aplicación, seleccione **Usuarios y grupos**.

2. Seleccione **+** **Agregar usuario** y, en la hoja **Agregar asignación**, seleccione **Usuarios y grupos**

3. En el cuadro de diálogo **Usuarios y grupos**, agregue los grupos de usuarios que estén autorizados para acceder a la aplicación interna y, después, haga clic en **Seleccionar** \> **Asignar**.

Esto completa la parte de Azure AD de la confianza de federación de SAML. Ahora se puede configurar BIG-IP APM para publicar la aplicación web interna y configurarlo con el conjunto de propiedades correspondiente para completar la confianza para la autenticación previa de SAML y el inicio de sesión único.

## <a name="advanced-configuration"></a>Configuración avanzada

### <a name="service-provider"></a>Proveedor de servicios

Esta configuración define las propiedades SP de SAML que APM usará para superponer la aplicación heredada con la autenticación previa de SAML.

1. Seleccione **Acceso** > **Federación** > **SAML Service Provider** (Proveedor de servicios de SAML) > **Local SP Services** (Servicios del SP local)  > **Crear**.

   ![Captura de pantalla que muestra la configuración de formularios F5](./media/f5-big-ip-forms-advanced/f5-forms-configuration.png)

2. **Proporcione un nombre**  y exactamente el mismo **Id. de entidad** definido en Azure AD anteriormente.

   ![Captura de pantalla que muestra el nuevo servicio SP de SAML](./media/f5-big-ip-forms-advanced/saml-sp-service.png)

    El valor de **configuración del nombre del SP** solo es necesario si el identificador de entidad no coincide exactamente con la parte del nombre de host de la dirección URL publicada o si no está en el formato de dirección URL normal basado en el nombre de host. Proporcione el esquema externo y el nombre de host de la aplicación que se va a publicar si el identificador de entidad es `urn:myvacation:contosoonline`.

### <a name="external-idp-connector"></a>Conector de IdP externo

Un conector IdP de SAML define la configuración necesaria para que BIG-IP APM confíe en Azure AD como su IDP de SAML. Esta configuración asignará el SP de SAML a un IdP de SAML, lo que establece la confianza de federación entre APM y Azure AD.

1. Desplácese hacia abajo para seleccionar el nuevo objeto del proveedor de servicios de SAML y seleccione **Bind/UnBind IDP Connectors** (Enlazar/Desenlazar conectores de IDP).

   ![Captura de pantalla que muestra los servicios SP locales](./media/f5-big-ip-forms-advanced/local-services.png)

2. Seleccione **Create New IDP Connector** (Crear conector de IdP) y, en el menú desplegable, elija **From Metadata** (Desde metadatos).

   ![Captura de pantalla que muestra el menú desplegable de metadatos](./media/f5-big-ip-forms-advanced/from-metadata.png)
  
3. Busque el archivo XML de metadatos de federación que descargó anteriormente y proporcione el **Nombre del proveedor de identidad** para el objeto de APM que representará el IdP de SAML externo. Por ejemplo: `MyVacation\_AzureAD`

   ![Captura de pantalla que muestra el nuevo conector SAML de IdP](./media/f5-big-ip-forms-advanced/new-idp-saml-connector.png)

4. Seleccione **Agregar fila nueva** para elegir el nuevo **SAML IdP Connector** (Conector de IdP de SAML) y, después, seleccione **Actualizar**.
  
   ![Captura de pantalla que muestra la opción para agregar una fila nueva](./media/f5-big-ip-forms-advanced/add-new-row.png)

5. Seleccione **Aceptar** para guardar la configuración.

   ![Captura de pantalla que muestra la edición del IdP de SAML que usa este SP](./media/f5-big-ip-forms-advanced/edit-saml-idp-using-sp.png)

### <a name="forms-based-sso"></a>Inicio de sesión único basado en formularios

El inicio de sesión único (SSO) con autenticación basada en formularios (FBA) puede realizarse en el modo iniciado por el cliente o bien por la propia instancia de BIG-IP. Ambos métodos emulan el inicio de sesión de usuario mediante la inserción de credenciales en las etiquetas de nombre de usuario y contraseña antes de enviar el formulario de forma automática. El flujo es casi transparente, excepto para los usuarios que tienen que proporcionar su contraseña una vez, al acceder a una aplicación FBA. Después, la contraseña se almacena en caché para reutilizarla en otras aplicaciones FBA.

Esto abarca el enfoque de APM, que controla el inicio de sesión único directamente en la aplicación back-end.

Seleccione **Acceso** > **Inicio de sesión único** > **Basado en formularios** > **Crear** y proporcione la información siguiente:

|Propiedad | Descripción |
|:------|:---------|
| Nombre | Otras aplicaciones publicadas pueden reutilizar un objeto SSO APM, por lo que debe usar un nombre descriptivo para la configuración. Por ejemplo, `Contoso\FBA\sso`|
| Uso de una plantilla de inicio de sesión único | Ninguno |
| Origen de nombre de usuario | Origen del nombre de usuario preferido para el relleno previo del formulario de recopilación de contraseñas. Se puede usar cualquier variable de sesión de APM, pero el valor predeterminado `session.sso.token.last.username` tiende a funcionar mejor, ya que contiene el UPN de Azure AD de los usuarios que han iniciado sesión. |
| Origen de contraseña | Deje el valor predeterminado `session.sso.token.last.password`, ya que es la variable APM que usará BIG-IP para almacenar en caché la contraseña proporcionada por los usuarios. |

![Captura de pantalla que muestra la nueva configuración de inicio de sesión único](./media/f5-big-ip-forms-advanced/new-sso-configuration.png)

|Propiedad | Descripción |
|:------|:---------|
| URI de inicio | URI de inicio de sesión de la aplicación FBA. La autenticación basada en formularios de APM ejecutará el inicio de sesión único cuando el URI de la solicitud coincida con este valor de URI.|
| Acciones de formulario | Deje este valor en blanco para que se utilice la dirección URL de la solicitud original en el inicio de sesión único. |
| Parámetro de formulario para el nombre de usuario | Nombre del elemento del campo de nombre de usuario de los formularios de inicio de sesión. Para determinarlo, use las herramientas de desarrollo del explorador.| 
| Parámetro de formulario para la contraseña | Nombre del elemento del campo de contraseña de los formularios de inicio de sesión. Del mismo modo, use las herramientas de desarrollo.|

![Captura de pantalla que muestra la configuración del método de inicio de sesión único](./media/f5-big-ip-forms-advanced/sso-method-configuration.png)

![Captura de pantalla que muestra la página de inicio de sesión de vacaciones de contoso](./media/f5-big-ip-forms-advanced/contoso-example.png)

Puede encontrar más detalles sobre cómo configurar un APM para el inicio de sesión único de FBA [aquí](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-single-sign-on-concepts-configuration-14-1-0/single-sign-on-methods.html#GUID-F8588DF4-F395-4E44-881B-8D16EED91449).

### <a name="access-profile-configuration"></a>Configuración del perfil de acceso

Un perfil de acceso enlaza numerosos elementos de APM que administran el acceso a los servidores virtuales de BIG-IP, incluidas las directivas de acceso, la configuración de inicio de sesión único y la configuración de la interfaz de usuario.

1. Seleccione **Acceso** > **Perfiles/Directivas** > **Access Profiles (Per-Session Policies)** (Perfiles de acceso [directivas por sesión])  > **Crear** para proporcionar los datos siguientes:

   | Propiedad | Descripción |
   |:-----|:-------|
   | Nombre | Por ejemplo: `MyVacation` |
   |Tipo de perfil | Todo |
   | SSO Configuration | Objeto de configuración de inicio de sesión único de FBA que acaba de crear|
   |Idioma aceptado | Agregar al menos un idioma|

   ![Captura de pantalla que muestra cómo crear un perfil de acceso](./media/f5-big-ip-forms-advanced/create-new-access-profile.png)

2. Modifique la directiva de sesión para presentar una página de inicio de sesión con el nombre de usuario rellenado previamente. Seleccione el vínculo **Editar** del perfil por sesión que acaba de crear para iniciar el editor visual de directivas (VPE) de APM.

   ![Captura de pantalla que muestra la edición de la directiva por sesión](./media/f5-big-ip-forms-advanced/edit-per-session-policy.png)

3. Una vez que se haya iniciado el VPE, seleccione el signo **+** situado junto a la reserva.

   ![Captura de pantalla que muestra + junto a la reserva una vez que se haya iniciado el VPE](./media/f5-big-ip-forms-advanced/vpe-launched.png)

4. En el menú emergente, seleccione **Autenticación** > **SAML Auth** (Autenticación SAML)  > **Agregar elemento**.

   ![Captura de pantalla que muestra la opción de autenticación SAML y el botón para agregar elementos](./media/f5-big-ip-forms-advanced/saml-auth-add-item.png)

5. En la configuración del **SP de autenticación SAML**, cambie el nombre a **Autenticación de Azure AD** y establezca el **Servidor AAA** para que use el objeto SP de SAML que ha creado anteriormente.

   ![Captura de pantalla que muestra la configuración del servidor de autenticación de Azure AD](./media/f5-big-ip-forms-advanced/azure-ad-auth-server.png)

6. Seleccione el signo **+** en la rama **Correcto**.

7. En el elemento emergente, seleccione **Autenticación** > **Página de inicio de sesión** > **Agregar elemento**.

   ![Captura de pantalla que muestra la configuración de la página de inicio de sesión](./media/f5-big-ip-forms-advanced/logon-page.png)

8. Cambie la opción **Solo lectura** del campo de nombre de usuario a **Sí**.

   ![Captura de pantalla que muestra la opción de solo lectura establecida en Sí](./media/f5-big-ip-forms-advanced/set-read-only-as-yes.png)

9. Agregue un objeto de asignación de credenciales de SSO; para ello, seleccione el signo **+** para la reserva de la página de inicio de sesión.

10. En la ventana emergente, seleccione **Asignación** > **SSO Credential Mapping** (Asignación de credenciales de SSO) **> Agregar elemento**.

    ![Captura de pantalla que muestra la información de asignación de credenciales de inicio de sesión único](./media/f5-big-ip-forms-advanced/sso-credential-mapping.png)

11. Deje la configuración predeterminada que se muestra en el menú emergente y continúe.

    ![Captura de pantalla que muestra la información de asignación de credenciales de inicio de sesión único guardada](./media/f5-big-ip-forms-advanced/save-sso-credential-mapping.png)

12. Seleccione el vínculo del cuadro **Denegar** superior para cambiar la rama **Correcto** a **Permitir** > **Guardar**.

**Asignación de atributos**

Aunque es opcional, la adición de una configuración LogonID_Mapping permite que la lista de sesiones activas de BIG-IP muestre el UPN del usuario que ha iniciado sesión, en lugar de un número de sesión. Esto resulta útil para el análisis de registros o la solución de problemas.

1. Seleccione el símbolo **+** para la rama **Correcto** de la autenticación SAML.

2. En el menú emergente, seleccione **Asignación** > **Variable Assign** (Asignación de variables)  > **Agregar elemento**.

   ![Captura de pantalla que muestra la información de asignación de variables](./media/f5-big-ip-forms-advanced/variable-assign.png)

3. Proporcione un nombre descriptivo y, en la sección de **asignación de variables**, seleccione **Agregar nueva entrada** > **Cambiar**. Por ejemplo, `LogonID_Mapping`.

   ![Captura de pantalla que muestra cómo agregar un campo de nueva entrada](./media/f5-big-ip-forms-advanced/add-new-entry.png)

4. Establezca ambas variables para usar lo siguiente:

   | Propiedad | Descripción |
   |:-----|:-------|
   | Variable personalizada | `session.logon.last.username` |
   | Variable de sesión | `session.saml.last.identity`|

   Después, seleccione **Finished** (Terminado)  > **Guardar**.

5. Para confirmar esos valores, seleccione **Apply Access Policy** (Aplicar directiva de acceso) en la esquina superior izquierda y cierre la pestaña del editor visual de directivas.

   ![Captura de pantalla que muestra la aplicación de la directiva de acceso](./media/f5-big-ip-forms-advanced/apply-access-policy.png)

### <a name="backend-pool-configuration"></a>Configuración del grupo de back-end

Para que BIG-IP sepa a dónde reenviar el tráfico de cliente, debe crear un objeto de nodo BIG-IP que represente el servidor back-end que hospeda la aplicación. A continuación, coloque ese nodo en un grupo de servidores BIG-IP.

1. Seleccione **Local Traffic** (Tráfico local)  > **Pools** (Grupos)  > **Pool List** (Lista de grupos)  > **Crear** y proporcione un nombre para un objeto de grupo de servidores. Por ejemplo, `MyApps_VMs`.

   ![Captura de pantalla que muestra la lista de grupos](./media/f5-big-ip-forms-advanced/pool-list.png)

2. Agregue un objeto de miembro de grupo con los datos siguientes:

   | Propiedad | Descripción |
   |:-----|:-------|
   | Nombre del nodo | Nombre para mostrar opcional del servidor que hospeda la aplicación web de back-end |
   | Dirección | Dirección IP del servidor que hospeda la aplicación |
   | Puerto de servicio | Puerto HTTP/S en el que escucha la aplicación |

   ![Captura de pantalla que muestra el miembro del grupo](./media/f5-big-ip-forms-advanced/pool-member.png)

>[!NOTE]
>Las instancias de Health Monitor requieren una [configuración](https://support.f5.com/csp/article/K13397) adicional que no se cubre en este tutorial.

## <a name="virtual-server-configuration"></a>Configuración del servidor virtual

Un servidor virtual es un objeto del plano de datos de BIG-IP representado por una dirección IP virtual que escucha las solicitudes de los clientes a la aplicación. El tráfico recibido se procesa y se evalúa con respecto al perfil de acceso de APM asociado al servidor virtual, antes de dirigirse en función de los resultados y la configuración de la directiva.

1. Seleccione **Local Traffic** (Tráfico local)  > **Virtual Servers** (Servidores virtuales)  > **Virtual Server List** (Lista de servidores virtuales)  > **Crear**.

2. Proporcione al servidor virtual un **Nombre**, una dirección IP de tipo IPv4 o IPv6 sin usar que pueda asignarse a BIG-IP para recibir tráfico del cliente y establezca el **Puerto de servicio** en 443.

   ![Captura de pantalla que muestra el servidor virtual](./media/f5-big-ip-forms-advanced/virtual-server.png)

3. **HTTP Profile** (Perfil HTTP): se establece en http.

4. **SSL Profile (Client)** (Perfil SSL [cliente]): habilita el protocolo de Seguridad de la capa de transporte (TLS), lo que permite que los servicios se publiquen a través de HTTPS. Seleccione el perfil SSL del cliente que creó como parte de los requisitos previos o deje el valor predeterminado si va a realizar pruebas.

   ![Captura de pantalla que muestra el perfil SSL](./media/f5-big-ip-forms-advanced/ssl-profile.png)

5. Cambie **Source Address Translation** (Traducción de direcciones de origen) a **Asignación automática**.

   ![Captura de pantalla que muestra la asignación automática](./media/f5-big-ip-forms-advanced/auto-map.png)

6. En **Directiva de acceso**, establezca el **perfil de acceso** creado anteriormente. Esto enlaza el perfil de autenticación previa de SAML de Azure AD y la directiva de inicio de sesión único con autenticación basada en formularios al servidor virtual.

   ![Captura de pantalla que muestra la directiva de acceso](./media/f5-big-ip-forms-advanced/access-policy.png)

7. Por último, establezca el **Grupo predeterminado** en el objeto de grupo de back-end creado en la sección anterior.

   ![Captura de pantalla que muestra el grupo predeterminado](./media/f5-big-ip-forms-advanced/default-pool.png)

## <a name="session-management"></a>Administración de sesiones

La configuración de administración de sesiones de BIG-IP se usa para definir las condiciones bajo las cuales las sesiones de usuario se finalizan o se les permite continuar, así como para definir los límites para usuarios y direcciones IP y las páginas de error. Puede crear su propia directiva si se dirige a **Directiva de acceso** > **Access Profiles** (Perfiles de acceso) y selecciona su aplicación en la lista.

Con respecto a la funcionalidad SLO, al haber definido un único URI de cierre de sesión en Azure AD, se garantizará que un cierre de sesión iniciado por IdP desde el portal MyApps también finaliza la sesión entre el cliente y BIG-IP APM.

Después de haber importado el archivo metadata.xml de federación de la aplicación, se proporciona a APM el punto de conexión del cierre de sesión SAML de Azure AD para los cierres de sesión iniciados por el proveedor de servicios. No obstante, para que esto sea realmente eficaz, el APM debe saber exactamente cuándo cierra sesión un usuario.

Considere un escenario en el que no se use un portal web de BIG-IP, donde el usuario no tiene ninguna forma de indicar a APM que cierre la sesión. Incluso si el propio usuario cierra la sesión en la aplicación, BIG-IP es ajeno a esto desde el punto de vista técnico, por lo que la sesión de la aplicación podría restablecerse fácilmente a través del inicio de sesión único. Por esta razón, el inicio de sesión por parte de SP es algo que debe tenerse muy en cuenta para garantizar que las sesiones se terminen de forma segura cuando ya no sean necesarias.

Una manera de lograrlo sería agregar una función SLO al botón de cierre de sesión de las aplicaciones, para que pueda redirigir al cliente al punto de conexión de cierre de sesión de SAML de Azure AD. El punto de conexión de cierre de sesión de SAML para el inquilino puede encontrarse en **Registros de aplicaciones** > **Puntos de conexión**.

Si no es posible realizar un cambio en la aplicación, considere la posibilidad de que BIG-IP escuche la llamada de cierre de sesión de las aplicaciones y, al detectar la solicitud, desencadene SLO. Puede encontrar más detalles sobre el uso de BIG-IP iRules para lograr esto en el [artículo K42052145](https://support.f5.com/csp/article/K42052145) y el [artículo K12056](https://support.f5.com/csp/article/K12056) de F5.

## <a name="summary"></a>Resumen

Ahora la aplicación debería haberse publicado y ser accesible a través del acceso híbrido seguro, ya sea directamente a través de su dirección URL o mediante los portales de aplicaciones de Microsoft.

La aplicación también debería estar visible como recurso de destino en el acceso condicional de Azure AD. Consulte las [instrucciones](../conditional-access/concept-conditional-access-policies.md) para crear directivas de CA.

Para aumentar la seguridad, las organizaciones que usan este patrón también podrían considerar la posibilidad de bloquear todo el acceso directo a la aplicación, forzando así una ruta de acceso estricta a través de BIG-IP.

## <a name="next-steps"></a>Pasos siguientes

Desde un explorador, conéctese a la dirección URL externa de la aplicación o seleccione el icono de la aplicación en el portal MyApps. Después de autenticarse en Azure AD, se le redirigirá al servidor virtual de BIG-IP para la aplicación y se le solicitará una contraseña.

>[!Note]
>APM rellena previamente el nombre de usuario con el UPN de Azure AD.

![Captura de pantalla que muestra el inicio de sesión único protegido](./media/f5-big-ip-forms-advanced/secured-sso.png)

Tras el envío, el usuario debería iniciar sesión en la aplicación de forma automática y la contraseña debería almacenarse en caché para reutilizarla en cualquier otra aplicación que se publique con el perfil de acceso de inicio de sesión único con autenticación basada en formularios (FBA SSO).

![Captura de pantalla que muestra el mensaje de bienvenida](./media/f5-big-ip-forms-advanced/welcome-message.png)

## <a name="troubleshoot"></a>Solución de problemas

La imposibilidad de acceder a la aplicación protegida por acceso híbrido seguro puede deberse a varios factores, como una configuración errónea. A continuación, se indican algunos aspectos que deben tenerse en cuenta para solucionar problemas:

- El inicio de sesión único con autenticación basada en formularios (FBA SSO) lo realiza BIG-IP mediante el análisis del formulario de inicio de sesión en el URI especificado y la búsqueda de las etiquetas de elementos de nombre de usuario y contraseña definidas en la configuración.

- Si las etiquetas de los elementos no son coherentes, el inicio de sesión único generará un error. Los formularios más complejos que se hayan generado dinámicamente pueden requerir un análisis más detallado con herramientas de desarrollo, para comprender la composición del formulario de inicio de sesión.

- El enfoque de inicio por parte del cliente puede ser más adecuado para las páginas de inicio de sesión que contienen varios formularios, ya que permite especificar un nombre de formulario e incluso personalizar la lógica del controlador de formulario de JavaScript.

- Ambos métodos de inicio de sesión único con autenticación basada en formularios optimizan la experiencia del usuario y la seguridad al ocultar todas las interacciones del formulario; pero, en algunos casos puede ser útil validar si las credenciales se insertan realmente. Esto es posible en el modo iniciado por el cliente; para ello, deshabilite el envío automático del formulario en el perfil de SSO y, a continuación, use las herramientas de desarrollo para deshabilitar las dos propiedades de estilo que impiden que se muestre la página de inicio de sesión.

  ![Captura de pantalla que muestra las propiedades](./media/f5-big-ip-forms-advanced/properties.png)

Los registros de BIG-IP son una excelente fuente de información para aislar todo tipo de problemas de autenticación y de inicio de sesión único. Durante la solución de problemas, debe aumentar el nivel de detalle del registro.

1. Vaya a **Directiva de acceso** > **Información general** > **Event Logs (Registros de eventos)** > **Configuración**.

2. Seleccione la fila de la aplicación publicada y, a continuación, **Edit (Editar)** > **Access System Logs (Registros del sistema de acceso)**.

3. Seleccione **Depurar** en la lista de SSO y, después, elija **Aceptar**. A continuación, reproduzca el problema antes de mirar los registros, pero recuerde volver a cambiarlo cuando haya terminado.

Si ve un error de marca de BIG-IP inmediatamente después de la autenticación correcta de Azure AD, es posible que el problema esté relacionado con el inicio de sesión único de Azure AD en BIG-IP.

Vaya a **Acceso** > **Información general** > **Access reports** (Informes de acceso) y ejecute el informe de la última hora para ver si los registros proporcionan alguna pista. El vínculo **View session variables** (Ver variables de sesión) de la sesión también le ayudará a comprender si APM recibe las notificaciones esperadas de Azure AD.

Si no ve ninguna página de error de BIG-IP, es probable que el problema esté más relacionado con la solicitud de back-end o con el inicio de sesión único desde BIG-IP a la aplicación. En este caso, vaya a **Directiva de acceso** > **Información general** > **Sesiones activas** y seleccione el vínculo de su sesión activa.

El vínculo **View Variables** (Ver variables) de esta ubicación también puede ayudar a determinar la causa principal, especialmente si APM no puede obtener el identificador de usuario y la contraseña correctos.

Consulte las [referencias de las variables de sesión](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html) para BIG-IP de F5 para obtener más información.
