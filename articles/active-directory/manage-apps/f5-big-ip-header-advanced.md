---
title: Integración de SSO de Azure Active Directory con F5 BIG-IP para la autenticación basada en encabezados
description: Obtenga información sobre cómo integrar F5 BIG-IP Access Policy Manager (APM) y SSO de Azure Active Directory para la autenticación basada en encabezados.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/10/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d55f82483f496da314fe7b81c3369cc03d1a2f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717494"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-with-f5-big-ip-for-header-based-authentication"></a>Tutorial: Integración de inicio de sesión único de Azure Active Directory con F5 BIG-IP para la autenticación basada en encabezados

En este tutorial, descubrirá cómo integrar F5 BIG-IP Access Policy Manager (APM) y Azure Active Directory (Azure AD) para el acceso híbrido seguro a las aplicaciones basadas en encabezados.

La integración de las aplicaciones publicadas de BIG-IP en Azure AD proporciona numerosas ventajas, incluidas las siguientes:

- Gobernanza mejorada de la confianza cero mediante la autenticación previa y la autorización de Azure AD.

- Inicio de sesión único (SSO) completo entre Azure AD y los servicios publicados de BIG-IP

- Administración de identidades y acceso desde un solo plano de control: [Azure Portal](https://azure.microsoft.com/features/azure-portal)

Para obtener información sobre todas las ventajas, vea el artículo sobre [Integración de F5 BIG-IP y Azure AD](./f5-aad-integration.md) y [qué es el acceso a aplicaciones y el inicio de sesión único con Azure AD](/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="scenario-description"></a>Descripción del escenario

En este escenario, tenemos una aplicación interna cuyo acceso se basa en la recepción de encabezados de autorización HTTP de un sistema de agente heredado. Esto permite que los usuarios se dirijan a sus respectivas áreas de contenido.

El escenario ideal es que la aplicación se administra y se rige directamente mediante Azure AD. Pero como carece de cualquier forma de interoperabilidad de protocolo moderna, su modernización requeriría un tiempo y esfuerzo considerables, lo que conllevaría costos inevitables y riesgos de posibles tiempos de inactividad.

En su lugar, se usará una instancia de BIG-IP Virtual Edition (VE) implementada entre la red pública de Internet y la red virtual interna de Azure a la que está conectada la aplicación. Habilitará la puerta de acceso de entrada, con Azure AD para su amplia selección de capacidades de autenticación y autorización.

Tener un BIG-IP delante de la aplicación permite superponer el servicio con la autenticación previa y el inicio de sesión único basado en encabezados de Azure AD. Mejora significativamente la posición de seguridad general de la aplicación, lo que permite que la empresa siga funcionando a su ritmo, sin interrupciones.

La solución de acceso híbrido seguro para este escenario está integrada por los siguientes componentes:

- **Aplicación**: servicio back-end que se va a proteger con Azure AD y el acceso híbrido seguro de BIG-IP.

- **Azure AD**: proveedor de identidades SAML (IdP), responsable de la comprobación de las credenciales de usuario, el acceso condicional (CA) y el inicio de sesión único en BIG-IP APM.

- **BIG-IP**: proxy inverso y proveedor de servicios SAML (SP) en la aplicación, que delega la autenticación al IdP de SAML, antes de realizar el inicio de sesión único basado en encabezados en la aplicación back-end.

![Captura de pantalla en la que se muestra el diagrama de flujo de arquitectura](./media/f5-big-ip-header-advanced/flow-diagram.png)

| Paso | Descripción |
|:-------|:-----------|
| 1. | El usuario se conecta al punto de conexión del proveedor de servicios SAML de la aplicación (BIG-IP APM). |
| 2. | La directiva de acceso de APM redirige al usuario al IdP de SAML (Azure AD) para la autenticación previa.|
| 3. | El IdP de SAML autentica al usuario y aplica las directivas de CA exigidas. |
| 4. | Azure AD redirige al usuario al proveedor de servicios de SAML con el token y las notificaciones que se han emitido. |
| 5. | BIG-IP APM concede acceso de usuario e inserta encabezados en la solicitud a la aplicación. |

Para aumentar la seguridad, las organizaciones que usan este patrón también podrían considerar la posibilidad de bloquear todo el acceso directo a la aplicación, forzando así una ruta de acceso estricta mediante BIG-IP.

## <a name="prerequisites"></a>Requisitos previos

No es necesario tener experiencia previa en BIG-IP, pero necesitará lo siguiente:

- Una suscripción gratuita de Azure AD u otra de nivel superior

- Una instancia de BIG-IP existente o [implementar una instancia de BIG-IP Virtual Edition (VE) en Azure](./f5-bigip-deployment-guide.md)

- Cualquiera de las siguientes SKU de licencias de F5 BIG-IP

  - F5 BIG-IP® Best bundle

  - Licencia independiente de F5 BIG-IP Access Policy Manager™ (APM).

  - Licencia del complemento F5 BIG-IP Access Policy Manager™ (APM) en una instalación de F5 BIG-IP® Local Traffic Manager™ (LTM) ya existente.

  - [Licencia de evaluación gratuita](https://www.f5.com/trial/big-ip-trial.php) completa de 90 días de BIG-IP.

- Identidades de usuario [sincronizadas](../hybrid/how-to-connect-sync-whatis.md) desde un directorio local en Azure AD

- Una cuenta con [permisos](/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator) de administrador de la aplicación de Azure AD

- [Certificado SSL](./f5-bigip-deployment-guide.md#ssl-profile) para publicar servicios a través de HTTPS o el uso de certificados predeterminados durante las pruebas

- Una aplicación basada en encabezados existente o [configurar una aplicación de encabezados de IIS sencilla](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90)) para pruebas

## <a name="deployment-modes"></a>Modos de implementación

Existen varios métodos a fin de configurar una instancia de BIG-IP para este escenario, incluidas dos opciones basadas en asistentes o una configuración avanzada.

En este tutorial se trata el enfoque avanzado, que proporciona una forma más flexible para implementar el acceso híbrido seguro creando de manera manual todos los objetos de configuración de BIG-IP. Este enfoque también se puede usar para los escenarios que no se describen en la configuración guiada.

>[!NOTE]
>Todas las cadenas o los valores de ejemplo a los que se hace referencia en este artículo deben reemplazarse por los de su entorno real.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Adición de F5 BIG-IP desde la galería de Azure AD

La configuración de la confianza de federación de SAML entre BIG-IP APM y Azure AD es uno de los primeros pasos para implementar el acceso híbrido seguro. Establece la integración necesaria para que BIG-IP traspase la autenticación previa y el [acceso condicional](../conditional-access/overview.md) a Azure AD, antes de conceder acceso al servicio publicado.

1. Inicie sesión en el portal de Azure AD con una cuenta que tenga derechos de administrador de la aplicación.

2. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.

3. Vaya a **Aplicaciones empresariales** y, en la cinta de opciones de la parte superior, seleccione **+ Nueva aplicación**.

4. Busque **F5** en la galería y seleccione **F5 BIG-IP APM Azure AD integration** (Integración de Azure AD con F5 BIG-IP APM).

5. Proporcione un nombre para la aplicación y, después, seleccione **Agregar o Crear** para agregarlo al inquilino. El nombre debe reflejar ese servicio específico.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD 

1. Con las nuevas propiedades de la aplicación **F5** a la vista, vaya a **Administrar** > **Inicio de sesión único**.

2. En la página **Selección de un método de inicio de sesión único**, seleccione **SAML** y omita el mensaje para guardar la configuración de inicio de sesión único; para ello, seleccione **No, lo guardaré más tarde**.

3. En la hoja **Configuración del inicio de sesión único con SAML**, seleccione el icono con forma de lápiz en **Configuración básica de SAML** y proporcione los detalles siguientes:

   a. Reemplace la dirección URL predefinida del **Identificador** por la dirección URL del servicio publicado de BIG-IP. Por ejemplo: `https://mytravel.contoso.com`

   b. Haga lo mismo con la dirección **URL de respuesta**, pero incluya la ruta de acceso del punto de conexión de SAML de APM. Por ejemplo: `https://mytravel.contoso.com/saml/sp/profile/post/acs`

   >[!NOTE]
   >En esta configuración, el flujo SAML funcionaría en un modo iniciado por IdP, donde Azure AD emite el usuario con una aserción de SAML antes de redirigir al punto de conexión de servicio de BIG-IP para la aplicación. BIG-IP APM admite los modos iniciados tanto por IdP como por SP.

   c. Para `Logout URI`, especifique el punto de conexión de cierre de sesión único (SLO) de BIG-IP APM precedido por el encabezado host del servicio que se está publicando. Proporcionar un URI de SLO garantiza que la sesión de BIG-IP APM del usuario ha finalizado después de haber cerrado la sesión de Azure AD. Por ejemplo: `https://mytravel.contoso.com/saml/sp/profile/redirect/slr`

    ![Captura de pantalla en la que se muestra la configuración básica de SAML](./media/f5-big-ip-header-advanced/basic-saml-configuration.png)

    >[!Note]
    >Desde la versión 16 de TMOS, el punto de conexión de SLO de SAML ha cambiado a `/saml/sp/profile/redirect/slo`.

4. Seleccione **Guardar** antes de salir de la hoja de configuración de SAML y omita el mensaje de prueba del inicio de sesión único.

5. Seleccione el icono de lápiz para editar **User Attributes & Claims > + Add new claim** (Atributos y notificaciones del usuario > Agregar nueva notificación).

6. Establezca las propiedades de notificación con lo siguiente y luego seleccione **Guardar**.

   | Propiedad |Descripción|
   |:------|:---------|
   |Nombre | Employeeid |
   | Atributo de origen | user.employeeid |

   ![Captura de pantalla en la que se muestra cómo administrar la configuración de notificaciones](./media/f5-big-ip-header-advanced/manage-claims.png)

7. Seleccione **Agregar una notificación de grupo** y luego **Grupos asignados a la aplicación** > **Atributo de origen** > **sAMAccountName**.

   ![Captura de pantalla en la que se muestra la configuración de notificaciones de grupo](./media/f5-big-ip-header-advanced/group-claims.png)

8. **Guarde** la configuración y cierre la hoja.

   Observe las propiedades de la sección **User Attributes & Claims** (Atributos y notificaciones del usuario). Azure AD emitirá a los usuarios estas propiedades para la autenticación BIG-IP APM y el inicio de sesión único a la aplicación de back-end:

   ![Captura de pantalla en la que se muestran los atributos del usuario y la configuración de notificaciones](./media/f5-big-ip-header-advanced/user-attributes-claims.png)

   No dude en agregar cualquier otra notificación específica que la aplicación publicada de BIG-IP pueda esperar como encabezado. Las notificaciones definidas además del conjunto predeterminado solo se emitirán si existen en Azure AD. Del mismo modo, las pertenencias de [roles o grupos](../hybrid/how-to-connect-fed-group-claims.md) de directorio también se deben definir en un objeto de usuario de Azure AD antes de que puedan emitirse como una notificación.

9. En la sección **Certificado de firma de SAML**, seleccione el botón **Descargar** para guardar el archivo **XML de metadatos de federación** en el equipo.

   ![Captura de pantalla en la que se muestra el certificado de firma de SAML](./media/f5-big-ip-header-advanced/saml-signing-certificate.png)

Los certificados de firma de SAML que Azure AD crea tienen una duración de tres años y deben administrarse con la [guía](./manage-certificates-for-federated-single-sign-on.md) publicada.

### <a name="azure-ad-authorization"></a>Autorización de Azure AD

De forma predeterminada, Azure AD solo emitirá tokens para los usuarios a los que se haya concedido acceso a una aplicación.

1. En la vista de configuración de la aplicación, seleccione **Usuarios y grupos**.

2. Seleccione **+** **Agregar usuario** y, en la hoja **Agregar asignación**, seleccione **Usuarios y grupos**

3. En el cuadro de diálogo **Usuarios y grupos**, agregue los grupos de usuarios que estén autorizados para acceder a la aplicación interna basada en encabezados y, después, haga clic en **Seleccionar** > **Asignar**.

Esto completa la parte de Azure AD de la confianza de federación de SAML. Ahora se puede configurar BIG-IP APM para publicar la aplicación web interna y configurarlo con el conjunto de propiedades correspondiente a fin de completar la confianza para la autenticación previa de SAML.

## <a name="advanced-configuration"></a>Configuración avanzada

### <a name="saml-configuration"></a>Configuración de SAML

En los pasos siguientes se crea el proveedor de servicios de SAML de BIG-IP y los objetos de IdP de SAML correspondientes, necesarios para completar la federación de la aplicación publicada, con Azure AD.

1. Seleccione **Acceso** > **Federación** > **SAML Service Provider** (Proveedor de servicios de SAML) > **Local SP Services** (Servicios del SP local)  > **Crear**.

   ![Captura de pantalla en la que se muestra la creación del proveedor de servicios de SAML](./media/f5-big-ip-header-advanced/create-saml-sp.png)

2. **Proporcione un nombre**  y exactamente el mismo **Id. de entidad** definido en Azure AD anteriormente.

   ![Captura de pantalla en la que se muestra el nuevo servicio de proveedor de servicios de SAML ](./media/f5-big-ip-header-advanced/new-saml-sp-information.png)

   Los valores de **SP Name Settings** (Configuración del nombre del SP) solo son necesarios si el id. de entidad no coincide exactamente con la parte del nombre de host de la dirección URL publicada o si no está en el formato de dirección URL normal basado en el nombre de host. Proporcione el esquema externo y el nombre de host de la aplicación que se va a publicar si el identificador de entidad es `urn:mytravel:contosoonline`.

3. Desplácese hacia abajo para seleccionar el nuevo objeto del proveedor de servicios de SAML y seleccione **Bind/UnBind IdP Connectors** (Enlazar/Desenlazar conectores de IdP).

   ![Captura de pantalla en la que se muestran los nuevos conectores de objetos del proveedor de servicios de SAML](./media/f5-big-ip-header-advanced/idp-connectors.png)

4. Seleccione **Create New IdP Connector** (Crear conector de IdP) y, en el menú desplegable, elija **De metadatos**.

   ![Captura de pantalla en la que se muestra la edición del nuevo IdP del servicio de SAML](./media/f5-big-ip-header-advanced/edit-saml-idp.png)

5. Busque el archivo XML de metadatos de federación que descargó anteriormente y proporcione un **nombre de proveedor de identidades** para el objeto de APM que representará el IdP externo de SAML. Por ejemplo: `MyTravel_AzureAD`

   ![Captura de pantalla en la que se muestra el nuevo conector IdP](./media/f5-big-ip-header-advanced/idp-name.png)

6. Seleccione **Agregar fila nueva** para elegir el nuevo **SAML IdP Connector** (Conector de IdP de SAML) y, después, seleccione **Actualizar**.

   ![Captura de pantalla en la que se muestra cómo actualizar el conector IdP](./media/f5-big-ip-header-advanced/update-idp-connector.png)

7. Seleccione **Aceptar** para guardar la configuración.

   ![Captura de pantalla en la que se muestra cómo guardar la configuración](./media/f5-big-ip-header-advanced/save-settings.png)

### <a name="header-sso-configuration"></a>Configuración de SSO de encabezado

Cree un objeto de SSO de APM para realizar encabezados de SSO en la aplicación de back-end.

1. Vaya a **Access** > **Profiles/Policies** > **Per-Request Policies** > **Create** (Acceso > Perfiles o directivas > Por directivas de sesión > Crear).

2. Proporcione un nombre a un perfil único y agregue al menos un **Accepted Language** (Idioma aceptado) y, después, seleccione **Finalizado**. Por ejemplo, SSO_Headers

   ![Captura de pantalla en la que se muestra la configuración del encabezado](./media/f5-big-ip-header-advanced/header-configuration.png)

3. Seleccione el vínculo **Editar** de la directiva por solicitud que acaba de crear.

    ![Captura de pantalla en la que se muestra la edición de la directiva por solicitud](./media/f5-big-ip-header-advanced/header-configuration-edit.png)

4. Después de que se haya iniciado el editor de directivas visuales, seleccione el símbolo **+** que hay junto a la reserva.

    ![Captura de pantalla en la que se muestra el editor de directivas visuales](./media/f5-big-ip-header-advanced/visual-policy-editor.png)

5. En el menú emergente, cambie a la pestaña **De uso general** para seleccionar **Encabezados HTTP** > **Agregar elemento**.

    ![Captura de pantalla en la que se muestra el elemento agregar encabezado HTTP](./media/f5-big-ip-header-advanced/add-item.png)

6. Seleccione **Agregar nueva entrada** para crear tres entradas de **Header modify** (Modificación de encabezado) **HTTP** independientes mediante lo siguiente:

   | Propiedad | Descripción |
   |:------|:-------|
   | Nombre de encabezado | upn |
   | Valor de encabezado | %{session.saml.last.identity}|
   | Nombre de encabezado | employeeid |
   | Valor de encabezado | %{session.saml.last.attr.name.employeeid} |
   | Nombre de encabezado | group\_authz |
   | Valor de encabezado | %{session.saml.last.attr.name.`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`} |

   >[!Note]
   >Las variables de sesión de APM definidas entre llaves distinguen entre mayúsculas y minúsculas. Por lo tanto, escribir EmployeeID cuando el nombre del atributo de Azure AD se envía como employeeid provocará un error de asignación de atributos. A menos que sea necesario, se recomienda definir todos los atributos en minúsculas.

   ![Captura de pantalla en la que se muestra la modificación del encabezado HTTP](./media/f5-big-ip-header-advanced/http-header-modify.png)

7. Cuando haya terminado, seleccione **Guardar** y cierre el editor de directivas visuales.

   ![Captura de pantalla en la que se muestra la directiva por solicitud realizada y guardada](./media/f5-big-ip-header-advanced/per-request-policy-done.png)

### <a name="access-profile-configuration"></a>Configuración del perfil de acceso

Un perfil de acceso enlaza numerosos elementos de APM que administran el acceso a los servidores virtuales de BIG-IP, incluidas las directivas de acceso, la configuración de inicio de sesión único y la configuración de la interfaz de usuario.

1. Seleccione **Acceso** > **Perfiles/Directivas** > **Access Profiles (Per-Session Policies)** (Perfiles de acceso (directivas por sesión))  > **Crear** para proporcionar los datos siguientes y luego seleccione **Finalizado**:

   | Propiedad | Descripción |
   |:--------|:----------|
   | Nombre | MyTravel |
   | Tipo de perfil | Todo |
   | Idioma aceptado | Agregar al menos un idioma|

   ![Captura de pantalla en la que se muestra la configuración del perfil de acceso](./media/f5-big-ip-header-advanced/access-profile-configuration.png)

2. Seleccione el vínculo **Editar** del perfil por sesión que acaba de crear.

    ![Captura de pantalla en la que se muestra la edición por perfil de sesión](./media/f5-big-ip-header-advanced/edit-per-session-profile.png)

3. Una vez que se haya iniciado el editor de directivas visuales, seleccione el símbolo **+** que hay junto a la reserva.

   ![Captura de pantalla en la que se muestra cómo iniciar el editor de directivas visuales](./media/f5-big-ip-header-advanced/visual-policy-editor-launch.png)

4. En el menú emergente, seleccione **Autenticación** > **SAML Auth** (Autenticación SAML)  > **Agregar elemento**.

   ![Captura de pantalla en la que se muestra cómo agregar la autenticación SAML](./media/f5-big-ip-header-advanced/add-saml-auth.png)

5. En la configuración del **Proveedor de servicios de la autenticación SAML**, seleccione la opción **AAA Server** (Servidor AAA) para usar el objeto SAML SP que creó anteriormente y, después, seleccione **Guardar**.

   ![Captura de pantalla en la que se muestra el uso del Servidor AAA para el proveedor de servicios de autenticación SAML](./media/f5-big-ip-header-advanced/aaa-server.png)

### <a name="attribute-mapping"></a>Asignación de atributos

Aunque es opcional, la adición de una configuración LogonID_Mapping permite que la lista de sesiones activas de BIG-IP muestre el UPN del usuario que ha iniciado sesión, en lugar de un número de sesión. Esto resulta útil para el análisis de registros o la solución de problemas.

1. Seleccione el símbolo **+** para la rama **Correcto** de la autenticación SAML.

    ![Captura de pantalla en la que se muestra cómo crear una rama de autenticación SAML](./media/f5-big-ip-header-advanced/create-saml-auth-branch.png)

2. En el menú emergente, seleccione **Asignación** > **Variable Assign** (Asignación de variables)  > **Agregar elemento**.

   ![Captura de pantalla en la que se muestra cómo asignar una variable](./media/f5-big-ip-header-advanced/assign-variable.png)

3. Proporcione un nombre descriptivo y, en la sección **Variable Assign** (Asignación de variables), seleccione **Agregar nueva entrada** > **Cambiar**. Por ejemplo, LogonID_Mapping.

   ![Captura de pantalla en la que se muestra cómo agregar una nueva entrada](./media/f5-big-ip-header-advanced/assign-variable-change.png)

4. Establezca ambas variables para usar lo siguiente y luego seleccione **Finalizado** >
    **Guardar**.

   | Propiedad | Descripción |
   |:--------|:----------|
   | Variable personalizada | session.saml.last.identity |
   | Variable de sesión | session.logon.last.username |

5. Seleccione el terminal **Denegar** de la rama **Correcto** de la directiva de acceso y cámbielo a **Permitir**, seguido de **Guardar**.

6. Para confirmar la directiva, seleccione **Apply Access Policy** (Aplicar directiva de acceso) y cierre la pestaña del editor de directivas visuales.

### <a name="backend-pool-configuration"></a>Configuración del grupo de back-end

Para que BIG-IP sepa dónde reenviar el tráfico de cliente, debe crear un objeto de nodo APM que represente el servidor back-end que hospeda la aplicación y colocar ese nodo en un grupo de APM.

1. Seleccione **Local Traffic > Pools > Pool List > Create** (Tráfico local > Grupos > Lista de grupos > Crear) y proporcione un nombre para un objeto de grupo de servidores. Por ejemplo, MyApps_VMs

      ![Captura de pantalla en la que se muestra cómo aplicar la directiva de acceso](./media/f5-big-ip-header-advanced/apply-access-policy.png)

2. Agregue un objeto de miembro de grupo con los datos siguientes:

    | Propiedad | Descripción |
    |:--------|:----------|
    | Nombre del nodo | Nombre para mostrar opcional del servidor que hospeda la aplicación web de back-end |
    | Dirección | Dirección IP del servidor que hospeda la aplicación|
    | Puerto de servicio | Puerto HTTP/S en el que escucha la aplicación |

    ![Captura de pantalla en la que se muestra cómo agregar un objeto miembro de grupo](./media/f5-big-ip-header-advanced/add-object.png)

>[!NOTE]
>Las instancias de Health Monitor requieren una [configuración](https://support.f5.com/csp/article/K13397) adicional que no se cubre en este tutorial.

## <a name="virtual-server-configuration"></a>Configuración del servidor virtual

Un servidor virtual es un objeto del plano de datos de BIG-IP representado por una dirección IP virtual que escucha las solicitudes de los clientes a la aplicación. El tráfico recibido se procesa y se evalúa con respecto al perfil de acceso de APM asociado al servidor virtual, antes de dirigirse en función de los resultados y la configuración de la directiva.

1. Seleccione **Local Traffic** (Tráfico local)  > **Virtual Servers** (Servidores virtuales)  > **Virtual Server List** (Lista de servidores virtuales)  > **Crear**.

2. Proporcione al servidor virtual un **Nombre**, una dirección IP de tipo IPv4 o IPv6 sin usar que pueda asignarse a BIG-IP para recibir tráfico del cliente y establezca el **Puerto de servicio** en 443.

   ![Captura de pantalla en la que se muestra cómo agregar un nuevo servidor virtual](./media/f5-big-ip-header-advanced/new-virtual-server.png)

3. **HTTP Profile** (Perfil HTTP): se establece en http.

4. **SSL Profile (Client)** (Perfil SSL (cliente)): habilita el protocolo de Seguridad de la capa de transporte (TLS), lo que permite que los servicios se publiquen a través de HTTPS. Seleccione el perfil SSL del cliente que creó como parte de los requisitos previos o deje el valor predeterminado si va a realizar pruebas.

   ![Captura de pantalla en la que se muestra el cliente de perfil SSL](./media/f5-big-ip-header-advanced/ssl-profile.png)

5. Cambie la opción **Source Address Translation** (Traducción de direcciones de origen) a **Asignación automática**.

   ![Captura de pantalla en la que se muestra la opción de asignación automática](./media/f5-big-ip-header-advanced/change-source-address.png)

6. En **Directiva de acceso**, establezca el **Perfil de acceso** creado anteriormente. Esto enlaza el perfil de autenticación previa de SAML de Azure AD y la directiva de inicio de sesión único con autenticación basada en encabezados al servidor virtual.

   ![Captura de pantalla en la que se muestra cómo establecer el perfil de acceso](./media/f5-big-ip-header-advanced/set-access-profile.png)

7. Por último, establezca el **Grupo predeterminado** para usar los objetos de grupo de back-end creados en la sección anterior y luego seleccione **Finalizado**.

   ![Captura de pantalla en la que se muestra cómo establecer el grupo predeterminado](./media/f5-big-ip-header-advanced/default-pool.png)

## <a name="session-management"></a>Administración de sesiones

La configuración de administración de sesiones de BIG-IP se utiliza para definir las condiciones bajo las cuales las sesiones de usuario se finalizan o se les permite continuar, así como para definir los límites para usuarios y direcciones IP y las páginas de error. Puede crear su propia directiva si se dirige a **Directiva de acceso** > **Access Profiles** (Perfiles de acceso) y selecciona su aplicación en la lista.

Con respecto a la función SLO, al haber definido un único URI de SLO en Azure AD, se garantizará que un cierre de sesión iniciado por IdP desde el portal MyApps también finaliza la sesión entre el cliente y BIG-IP APM. Después de haber importado el archivo metadata.xml de federación de la aplicación, se proporciona a APM el punto de conexión del cierre de sesión SAML de Azure AD para los cierres de sesión iniciados por el proveedor de servicios. No obstante, para que esto sea realmente eficaz, el APM debe saber exactamente cuándo cierra sesión un usuario.

Considere un escenario en el que no se use un portal web de BIG-IP, donde el usuario no tiene ninguna forma de indicar a APM que cierre la sesión. Incluso si el propio usuario cierra la sesión en la aplicación, BIG-IP es ajeno a esto desde el punto de vista técnico, por lo que la sesión de la aplicación podría restablecerse fácilmente a través del inicio de sesión único. Por esta razón, el inicio de sesión por parte de SP es algo que debe tenerse muy en cuenta para garantizar que las sesiones se terminen de forma segura cuando ya no sean necesarias.

Una manera de lograrlo sería agregar una función SLO al botón de cierre de sesión de las aplicaciones, para que pueda redirigir al cliente al punto de conexión de cierre de sesión de SAML de Azure AD. El punto de conexión de cierre de sesión de SAML para el inquilino puede encontrarse en **Registros de aplicaciones** > **Puntos de conexión**.

Si no es posible realizar un cambio en la aplicación, considere la posibilidad de que BIG-IP escuche la llamada de cierre de sesión de las aplicaciones y, al detectar la solicitud, desencadene SLO. Puede encontrar más detalles sobre el uso de BIG-IP iRules para lograr esto en el [artículo K42052145](https://support.f5.com/csp/article/K42052145) y el [artículo K12056](https://support.f5.com/csp/article/K12056).

## <a name="summary"></a>Resumen

Este último paso proporciona un desglose de todas las configuraciones aplicadas antes de que se confirman. Seleccione **Implementar** para confirmar toda la configuración y comprobar que la aplicación ha aparecido en el inquilino.

Ahora la aplicación está publicada y es accesible mediante el acceso híbrido seguro, ya sea directamente mediante su dirección URL o los portales de aplicaciones de Microsoft.

Para aumentar la seguridad, las organizaciones que usan este patrón también podrían considerar la posibilidad de bloquear todo el acceso directo a la aplicación, forzando así una ruta de acceso estricta a través de BIG-IP.

## <a name="next-steps"></a>Pasos siguientes

Como usuario, inicie un explorador y conéctese a la dirección URL externa de la aplicación, o seleccione el icono de la aplicación en el portal de Microsoft MyApps. Después de autenticarse en Azure AD, se le redirigirá al servidor virtual de BIG-IP para la aplicación y se iniciará sesión de forma automática mediante SSO.
Se muestra la salida de los encabezados insertados que muestra nuestra aplicación basada en encabezados.

![Captura de pantalla en la que se muestra la salida](./media/f5-big-ip-header-advanced/mytravel-example.png)

## <a name="troubleshooting"></a>Solución de problemas

La imposibilidad de acceder a la aplicación protegida por acceso híbrido seguro puede deberse a varios posibles factores, como una configuración errónea.

- Los registros de BIG-IP son una excelente fuente de información para aislar todo tipo de problemas de autenticación y de inicio de sesión único. Durante la solución de problemas, debe aumentar el nivel de detalle del registro yendo a **Directiva de acceso** > **Información general** > **Registros de eventos** > **Configuración**. Seleccione la fila de la aplicación publicada y, a continuación, **Edit (Editar)** > **Access System Logs (Registros del sistema de acceso)**. Seleccione **Depurar** en la lista de SSO y, después, elija **Aceptar**. Ahora puede reproducir el problema antes de mirar los registros, pero no olvide volver a cambiar esto cuando haya terminado.

- Si ve un error de marca BIG-IP después de que se le redirija tras la autenticación previa de Azure AD, es probable que el problema esté relacionado con el inicio de sesión único de Azure AD a BIG-IP. Vaya a **Acceso** > **Información general** > **Access reports** (Informes de acceso) y ejecute el informe de la última hora para ver si los registros proporcionan alguna pista. El vínculo **View session variables** (Ver variables de sesión) de la sesión también le ayudará a comprender si APM recibe las notificaciones esperadas de Azure AD.

- Si no ve una página de error de BIG-IP, el problema probablemente esté más relacionado con el inicio de sesión único desde BIG-IP a la aplicación de back-end. En este caso, debe ir a **Directiva de acceso** > **Información general** > **Sesiones activas** y seleccionar el vínculo de su sesión activa. El vínculo **View Variables** (Ver variables) de esta ubicación también puede ayudar a determinar la causa principal de los problemas de SSO, especialmente si BIG-IP APM no puede obtener los identificadores de dominio y de usuario correctos.

Vea los [ejemplos de asignación de variables de BIG-IP APM](https://devcentral.f5.com/s/articles/apm-variable-assign-examples-1107) y [referencia de variables de sesión de F5 BIG-IP](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html) para obtener más información.

## <a name="additional-resources"></a>Recursos adicionales

Consulte estos artículos para obtener más información:

- [El fin de las contraseñas, cambie al modo sin contraseña](https://www.microsoft.com/security/business/identity/passwordless)

- [¿Qué es el acceso condicional?](../conditional-access/overview.md)

- [Marco de confianza cero de Microsoft para habilitar el trabajo remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)