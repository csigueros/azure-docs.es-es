---
title: Tutorial para configurar Azure Active Directory B2C con BIG-IP de F5
titleSuffix: Azure AD B2C
description: Aprenda a integrar la autenticación de Azure AD B2C con BIG-IP de F5 para proporcionar un acceso híbrido seguro
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.service: active-directory
ms.subservice: B2C
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: ca0d912c837a4c3fb218d1bb3fb8b07b43100119
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044634"
---
# <a name="tutorial-extend-azure-active-directory-b2c-to-protect-on-premises-applications-using-f5-big-ip"></a>Tutorial: Ampliación de Azure Active Directory B2C para proteger aplicaciones locales mediante BIG-IP de F5

En este tutorial de ejemplo, aprenderá a integrar Azure Active Directory (Azure AD) B2C con [F5 BIG-IP Access Policy Manager (APM)](https://www.f5.com/services/resources/white-papers/easily-configure-secure-access-to-all-your-applications-via-azure-active-directory). En este tutorial se muestra cómo las aplicaciones heredadas se pueden exponer de forma segura a Internet mediante la seguridad BIG-IP combinada con la autenticación previa de Azure AD B2C, el acceso condicional (CA) y el inicio de sesión único (SSO).

F5 Inc. se centra en la entrega, la seguridad, el rendimiento y la disponibilidad de los servicios conectados, incluida la disponibilidad de recursos de computación, almacenamiento y red. Proporciona hardware, software modular y soluciones de aplicaciones virtuales listas para la nube.

El controlador de entrega de aplicaciones BIG-IP (ADC) de F5 se suele implementar como puerta de enlace segura entre redes privadas e Internet.
Proporciona una gran cantidad de características, incluida la inspección de nivel de aplicación y controles de acceso totalmente personalizables. Cuando se implementa como proxy inverso, BIG-IP también se puede usar para habilitar el acceso híbrido seguro a aplicaciones empresariales críticas, mediante servicios front-end con una capa de acceso de identidad federada administrada por APM de F5.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Un [inquilino de Azure AD B2C](tutorial-create-tenant.md) vinculado a la suscripción de Azure

- Una BIG-IP existente o implementar un [entorno virtual BIG-IP (EV) de prueba en Azure](../active-directory/manage-apps/f5-bigip-deployment-guide.md)

- Cualquiera de las siguientes SKU de licencias de F5 BIG-IP

  - F5 BIG-IP® Best bundle

  - Licencia independiente de F5 BIG-IP Access Policy Manager™

  - Licencia del complemento F5 BIG-IP Access Policy Manager™ en una instalación de F5 BIG-IP® Local Traffic Manager™ (LTM) ya existente

  - [Licencia de evaluación gratuita](https://www.f5.com/trial/big-ip-trial.php) completa de 90 días de BIG-IP

- Una aplicación web basada en encabezados existente o [configurar una aplicación IIS](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90)) para pruebas

- [Certificado SSL](../active-directory/manage-apps/f5-bigip-deployment-guide.md#ssl-profile) para publicar servicios a través de HTTPS o usar el valor predeterminado durante las pruebas.

## <a name="scenario-description"></a>Descripción del escenario

En este escenario, tenemos una aplicación interna cuyo acceso se basa en la recepción de encabezados de autorización HTTP desde un sistema de agente heredado, lo que permite que los agentes de ventas se dirijan a sus respectivas áreas de contenido.

El servicio debe expandirse a una base de consumidores más amplia, por lo que la aplicación debe actualizarse para ofrecer una selección de opciones de autenticación de consumidor o reemplazarse por una solución más adecuada.

Lo ideal sería que la aplicación se actualizara para admitir la administración directa y la regulación a través de un plano de control moderno. Pero como carece de cualquier forma de interoperabilidad moderna, su modernización requeriría un tiempo y esfuerzo considerables, lo que conllevaría costes inevitables y riesgos de posibles tiempos de inactividad.

En su lugar, BIG-IP Virtual Edition (VE), implementada entre la red pública de Internet y la red virtual interna de Azure a la que está conectada la aplicación, se usará como puerta de acceso con Azure AD B2C por su amplia selección de funcionalidades de inicio de sesión y registro.

Tener un BIG-IP delante de la aplicación nos permite superponer el servicio con la autenticación previa de Azure AD B2C y el inicio de sesión único basado en encabezados, mejorando significativamente la posición de seguridad general de la aplicación, lo que permite que la empresa siga creciendo de forma eficiente, sin interrupción.

La solución de acceso híbrido seguro para este escenario está integrada por los siguientes componentes:

- **Aplicación**: servicio back-end protegido por Azure AD B2C y acceso híbrido seguro de BIG-IP

- **Azure AD B2C**: el servidor de autorización de IdP y Open ID Connect (OIDC) responsable de la comprobación de las credenciales de usuario, la autenticación multifactor (MFA) y el inicio de sesión único en BIG-IP APM.

- **BIG-IP**: como proxy inverso para la aplicación, BIG-IP APM también se convierte en el cliente OIDC, delegando la autenticación al servidor de autorización de OIDC antes de llevar a cabo el inicio de sesión único basado en encabezados en el servicio back-end.

En el diagrama siguiente se muestra el flujo iniciado por el proveedor de servicios (PS) para este escenario.

![Captura de pantalla que muestra el flujo iniciado por el PS para este escenario](./media/partner-f5/flow-diagram.png)

|Paso| Descripción|
|:----|:-------|
| 1. | El usuario se conecta al punto de conexión de la aplicación, donde BIG-IP es el proveedor de servicios |
| 2. | BIG-IP APM, que es el cliente OIDC, redirige al usuario al punto de conexión de inquilino de Azure AD B2C, el servidor de autorización de OIDC |
| 3. | El inquilino de Azure AD B2C autentica previamente al usuario y aplica cualquier directiva de acceso condicional impuesta |
|4. | Azure AD B2C redirige al usuario de nuevo al PS con el código de autorización |
| 5. | El cliente OIDC pide al servidor de autorización que intercambie el código de autorización por un token de identificador |
| 6. | BIG-IP APM concede acceso de usuario e inserta los encabezados HTTP en la solicitud de cliente reenviada a la aplicación |

Para aumentar la seguridad, las organizaciones que usan este patrón también podrían considerar la posibilidad de bloquear todo el acceso directo a la aplicación, forzando así una ruta de acceso estricta a través de BIG-IP.

## <a name="azure-ad-b2c-configuration"></a>Configuración de Azure AD B2C

Habilitar un BIG-IP con autenticación de Azure AD B2C requiere un inquilino de Azure AD B2C con un flujo de usuario adecuado o una directiva personalizada. [Configure un flujo de usuario de Azure AD B2C](tutorial-create-user-flows.md).

### <a name="create-custom-attributes"></a>Creación de atributos personalizados

Los atributos personalizados se pueden obtener de varios orígenes, incluso directamente de objetos de usuario de Azure AD B2C existentes; pueden solicitarse a idP federados, conectores de API o recopilarse durante el proceso de registro de un usuario. Cuando sea necesario, se pueden incluir en el token enviado a la aplicación.

Como la aplicación heredada espera atributos específicos, incluya estos atributos en el flujo de usuario. Pero no dude en reemplazarlos por los atributos que requiera la aplicación. O bien, si se configura una aplicación de prueba siguiendo las instrucciones de los requisitos previos, cualquier encabezado servirá porque los mostrará todos.

1. Inicio de sesión en el portal del inquilino de Azure AD B2C

2. En el panel izquierdo, seleccione **Atributos de usuario** y, a continuación, seleccione **Agregar** para crear dos atributos personalizados

   - Id. de agente: cadena **tipo de datos**

   - Geolocalización de agente: cadena **tipo de datos**

### <a name="add-attributes-to-user-flow"></a>Adición de atributos a un flujo de usuario

1. En el panel izquierdo, vaya a **Directivas** > **Flujos de usuario**.

2. Seleccione la directiva, por ejemplo, **B2C_1_SignupSignin**

3. Seleccione **Atributos de usuario** y agregue ambos atributos personalizados, además del atributo **Nombre para mostrar**. Estos son los atributos que se recopilarán durante el registro del usuario.

4. Seleccione **Notificaciones de la aplicación** y agregue ambos atributos personalizados, además del **Nombre para mostrar**. Estos son los atributos que se enviarán a BIG-IP.

Puede usar la característica [Ejecutar flujo de usuario](tutorial-create-user-flows.md) en el menú de flujo de usuario de la barra de navegación izquierda para comprobar que solicita todos los atributos definidos.

### <a name="azure-ad-b2c-federation"></a>Federación Azure AD B2C

Para que BIG-IP y Azure AD B2C confíen entre sí necesitan federación, por lo que BIG-IP debe estar registrado en el inquilino de Azure AD B2C como una aplicación OIDC.

1. En el portal Azure AD B2C, seleccione **Registro de aplicaciones** >  **Nuevo registro**.

2. Proporcione un nombre para la aplicación. Por ejemplo, **HeaderApp1**

3. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** .

4. En **URI de redirección**, seleccione **Web** y escriba el FQDN público del servicio que se va a proteger, junto con la ruta de acceso.

5. Deje el resto y seleccione **Registrar**.

6. Vaya a **Certificados y secretos** >  y, después, **+ Nuevo secreto de cliente**.

7. Proporcione un nombre descriptivo y un TTL para el secreto que usará BIG-IP.

8. Anote el secreto de cliente, lo necesitará más adelante para configurar BIG-IP.

El URI de redirección es el punto de conexión de BIG-IP al que el servidor de autorización envía a un usuario (Azure AD B2C) después de la autenticación. [Registre una aplicación](tutorial-register-applications.md) para Azure AD B2C.

## <a name="big-ip-configuration"></a>Configuración de BIG-IP

BIG-IP ofrece varios métodos para configurar un acceso híbrido seguro de Azure AD, incluida una configuración guiada basada en asistente, lo que minimiza el tiempo y el esfuerzo para implementar varios escenarios comunes. Su marco controlado por flujo de trabajo proporciona una experiencia intuitiva adaptada a topologías de acceso específicas y se usa para la publicación rápida de servicios web que requieren una configuración mínima para publicar.

### <a name="version-check"></a>Comprobación de versión

Este tutorial está basado en la versión 7/8 de la configuración guiada, pero también se puede aplicar a versiones anteriores. Para comprobar la versión, inicie sesión en la configuración web de BIG-IP con una cuenta de administrador y vaya a **Acceso** > **Guided Configuration** (Configuración guiada). La versión debe mostrarse en la esquina superior derecha. Para actualizar la configuración guiada de BIG-IP, siga [estas instrucciones](https://support.f5.com/csp/article/K85454683).

### <a name="ssl-profiles"></a>Perfiles SSL

La configuración de BIG-IP con un perfil SSL de cliente le permitirá proteger el tráfico del cliente a través de TLS. Para ello, deberá importar un certificado que coincida con el nombre de dominio usado por la dirección URL de acceso público para la aplicación. Siempre que sea posible, se recomienda usar una entidad de certificación pública, pero los certificados integrados autofirmados de BIG-IP también se pueden usar durante las pruebas.
[Agregue y administre certificados](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-ssl-administration-13-0-0.html) en BIG-IP VE.

## <a name="guided-configuration"></a>Configuración guiada

1. En la configuración web, vaya a **Access (Acceso)** > **Guided Configuration (Configuración guiada)** para iniciar el asistente para implementación.

2. Seleccione la **Federación** > **F5 como cliente de OAuth y servidor de recursos.**

3. Observe el resumen del flujo para este escenario y, a continuación, seleccione **Siguiente** para iniciar el asistente.

### <a name="oauth-properties"></a>Propiedades de OAuth

En esta sección se definen las propiedades que habilitan la federación entre BIG-IP APM y el servidor de autorización de OAuth, el inquilino de Azure AD B2C. Se hará referencia a OAuth a lo largo de la configuración de BIG-IP, pero la solución utilizará en realidad OIDC, una capa de identidad simple sobre el protocolo OAuth 2.0 que permite a los clientes OIDC verificar la identidad de los usuarios y obtener otra información de perfil.

Preste mucha atención a los detalles, ya que los errores afectarán a la autenticación y al acceso.

#### <a name="configuration-name"></a>Nombre de la configuración

Proporcionar un nombre para mostrar para la configuración le ayudará a distinguir entre las muchas configuraciones de implementación que podrían existir en la configuración guiada. Una vez definido, el nombre no se puede cambiar y solo está visible en la vista de configuración guiada.

#### <a name="mode"></a>Modo

BIG-IP APM actuará como un cliente OIDC, por lo que solo debe seleccionar la opción Cliente.

#### <a name="dns-resolver"></a>Solucionador DNS

El destino especificado debe ser capaz de resolver las direcciones IP públicas de los puntos de conexión de Azure AD B2C. Elija un solucionador DNS público existente o cree uno nuevo.

#### <a name="provider-settings"></a>Configuración del proveedor

En este caso, configuraremos Azure AD B2C como el idP de OAuth2. Observará que la versión 8 de la configuración guiada ofrece plantillas de Azure AD B2C, pero como faltan varios ámbitos, usaremos un tipo personalizado por ahora. F5 pretende incluir los ámbitos que faltan en una futura actualización de la configuración guiada. Agregue un nuevo proveedor y configúrelo de la siguiente manera:

- **Propiedades generales de OAuth**

  | Propiedades | Descripción |
  |:-------|:---------|
  |Tipo de proveedor de OAuth | Personalizado |
  | Elegir el proveedor de OAuth | Crear nuevo (o usar un proveedor de OAuth existente si existe) |
  | Nombre | Un nombre para mostrar único para el IdP de B2C. Este nombre se mostrará a los usuarios como una opción de proveedor para iniciar sesión.|
  | Tipo de token | Token web JSON |

- **Configuración de directiva de OAuth**

  | Propiedades | Descripción |
  |:-----------|:----------------|
  | Ámbito | Dejar en blanco; el ámbito de OpenID para iniciar la sesión de los usuarios se añadirá automáticamente |
  | Tipo de concesión | Código de autorización |
  | Habilitar OpenID Connect | Comprobación para poner el cliente OAuth de APM en modo OIDC |
  | Tipo de flujo | Código de autorización |

- **Configuración de proveedor de OAuth**

  El siguiente URI de OpenID hace referencia al punto de conexión de metadatos utilizado por los clientes de OIDC para detectar automáticamente información de IdP crítica, como la sustitución de certificados de firma. Busque el punto de conexión de metadatos para el inquilino de Azure AD B2C; para ello, vaya a **Registros de aplicaciones** > **Puntos de conexión** y copie el URI del documento de metadatos de Azure AD B2C OpenID Connect. Por ejemplo, `https://wacketywackb2c .b2clogin.com/<tenantname>.onmicrosoft.com/<policyname>/v2.0/.well-known/openid-configuration`.

  A continuación, actualice el URI con sus propias propiedades, `https://<tenantname>.b2clogin.com/WacketywackB2C.onmicrosoft.com/B2C_1_SignUpIn/v2.0/.well-known/openid-configuration`.

  Pegue este URI en el explorador para ver los metadatos de OIDC del inquilino de Azure AD B2C.

  | Propiedades | Descripción |
  |:----------|:----------|
  | Público | El identificador de cliente de la aplicación que representa BIG-IP en el inquilino de Azure AD B2C |
  | URI de autenticación | El punto de conexión de autorización en los metadatos de OIDC de B2C |
  | URI de token | El punto de conexión del token en los metadatos de Azure AD B2C |
  | URI de solicitud userinfo | Dejar en blanco. Azure AD B2C no admite actualmente esta característica |
  |URI de OpenID | El punto de conexión de metadatos de URI de OpenID que creó anteriormente |
  | Omitir la validación de certificados expirados | Deje la opción desactivada |
  | Permitir certificado de configuración de JWK autofirmado | Comprobar |
  | Conjunto de entidades de certificación de confianza | Seleccione ca-bundle.crt para usar las entidades de confianza de F5 predeterminadas |
  | Intervalo de detección | Proporcione un intervalo adecuado para que BIG-IP consulte las actualizaciones del inquilino de Azure AD B2C. El tiempo mínimo de intervalo que ofrece la versión 16.1 0.0.19 final de AGC es de 5 minutos.|

- **Configuración del servidor de OAuth**

  Esta sección hace referencia al servidor de autorización de OIDC, que es el inquilino de Azure AD B2C.

  |Propiedades | Descripciones|
  |:---------|:---------|
  | Id. de cliente | El identificador de cliente de la aplicación que representa BIG-IP en el inquilino de Azure AD B2C. |
  | Secreto del cliente | El secreto de cliente correspondiente de la aplicación. |
  |Perfil SSL de cliente-servidor | Al establecer un perfil SSL, se asegurará de que APM se comunica con el IdP de Azure AD B2C a través de TLS. Seleccione la opción `serverssl` predeterminada. |

- **Configuración de solicitudes de OAuth**

  Curiosamente, BIG-IP tiene todas las solicitudes requeridas de Azure AD B2C en su conjunto de solicitudes preconfiguradas. Sin embargo, se observó que, en la versión que estábamos implementando, estas solicitudes tenían un formato incorrecto y faltaban parámetros importantes. Por lo tanto, optamos por crearlas manualmente.

- **Solicitud de token: habilitada**

  | Propiedades | Descripción |
  |:-----------|:------------|
  | Elección de la solicitud de OAuth | Crear nuevo |
  | HTTP method | POST |
  | Activación de encabezados| No activado |
  | Activación de parámetros | Activada |

  | Tipo de parámetro | Nombre de parámetro | Valor del parámetro|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | valor de seguridad | valor de seguridad| |
  | redirect-uri | redirect-uri | |
  | scope | scope | |
  | response-type | response-type | |
  | client-secret | client-secret | |
  | custom | grant_type | authorization_code |

- **Solicitud de redireccionamiento de autenticación: habilitada**

  | Propiedades | Descripción |
  |:-----------|:------------|
  | Elección de la solicitud de OAuth | Crear nuevo |
  | HTTP method | GET |
  | Tipo de aviso | None |
  | Activación de encabezados | No activado |
  | Activación de parámetros | Activada |

  | Tipo de parámetro | Nombre de parámetro | Valor del parámetro|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | redirect-uri | redirect-uri | |
  | response-type |response-type | |
  | scope | scope | |
  | valor de seguridad | valor de seguridad | |

- **Solicitud de actualización de tokens** - **Deshabilitada**: se puede habilitar y configurar si es necesario.

- **Solicitud UserInfo de OpenID** - **Deshabilitada**: no se admite actualmente en inquilinos globales de Azure AD B2C.

- **Propiedades del servidor virtual**

  Se debe crear un servidor virtual BIG-IP para interceptar las solicitudes de cliente externas para el servicio back-end que se protege mediante acceso híbrido seguro. El servidor virtual debe tener asignada una dirección IP que esté asignada al registro DNS público para el punto de conexión del servicio BIG-IP que representa la aplicación. Siga adelante y utilice un servidor virtual existente; de lo contrario, proporcione lo siguiente:

  | Propiedades | Descripción |
  |:-----------|:------------|
  | Dirección de destino | Dirección IP pública o privada que se convertirá en el punto de conexión de servicio BIG-IP para la aplicación back-end |
  | Puerto del servicio | HTTPS |
  | Activación de puerto de redireccionamiento | Compruebe que los usuarios se redirijan automáticamente de http a https |
  | Puerto de redireccionamiento | HTTP |
  | Perfil SSL de cliente | Intercambie el perfil `clientssl` predefinido por el que contiene el certificado SSL. Las pruebas con el perfil predeterminado también son correctas, pero probablemente provocarán una alerta del explorador. |

- **Propiedades del grupo**

  Los servicios back-end se representan en BIG-IP como un grupo, que contiene uno o más servidores de aplicaciones a los que los servidores virtuales dirigen el tráfico entrante. Seleccione un puerto existente o cree uno nuevo.

  | Propiedades | Descripción |
  |:-----------|:------------|
  | Método de equilibrio de carga | Déjelo como Round Robin |
  |Servidor de grupo | Dirección IP interna de la aplicación back-end |
  | Port | Puerto de servicio de la aplicación back-end |
  
>[!NOTE]
>BIG-IP debe tener línea de visión con la dirección del servidor de grupo especificada.

- **Single sign-on settings (Configuración de inicio de sesión único)**

  BIG-IP admite muchas opciones de SSO, pero en el modo de cliente de OAuth, la configuración guiada se limita a los encabezados Kerberos o HTTP. Habilite el inicio de sesión único y use la siguiente información para que los atributos de entrada de APM que definió anteriormente se asignen a los encabezados de salida.

  | Propiedades | Descripción |
  |:-----------|:------------|
  | Operación de encabezado |`Insert`|
  | Nombre de encabezado | 'nombre' |
  | Valor de encabezado | `%{session.oauth.client.last.id_token.name}`|
  | Operación de encabezado | `Insert`|
  |Nombre de encabezado| `agentid`|
  |Valor de encabezado | `%{session.oauth.client.last.id_token.extension_AgentGeo}`|
 
  >[!Note]
  > Las variables de sesión de APM definidas entre llaves distinguen entre mayúsculas y minúsculas. Por lo tanto, escribir agentid cuando el nombre del atributo de Azure AD B2C se envía como AgentID provocará un error de asignación de atributos. A menos que sea necesario, se recomienda definir todos los atributos en minúsculas. En un caso de Azure AD B2C, el flujo de usuario solicita al usuario los atributos adicionales mediante el nombre del atributo tal como se muestra en el portal, por lo que puede ser preferible usar mayúsculas y minúsculas en lugar de minúsculas.

  ![Captura de pantalla que muestra la configuración del inicio de sesión único](./media/partner-f5/single-sign-on.png)

- **Propiedades de personalización**

  Esta configuración le permite personalizar el idioma y la apariencia de las pantallas que encuentran los usuarios cuando interactúan con el flujo de directiva de acceso de APM. Puede personalizar los mensajes y solicitudes en pantalla, cambiar los diseños de pantalla, los colores, las imágenes y localizar subtítulos, descripciones y mensajes que normalmente se pueden personalizar en los elementos de la directiva de acceso.

  Reemplace la cadena "F5 Networks" (Redes F5) del campo de texto Encabezado de formulario por el nombre de su propia organización. Por ejemplo, "Acceso híbrido seguro Wacketywack Inc.".

- **Propiedades de administración de sesión**

  La configuración de administración de sesiones de BIG-IP se utiliza para definir las condiciones bajo las cuales las sesiones de usuario se finalizan o se les permite continuar, así como para definir los límites para usuarios y direcciones IP y las páginas de error. Son opcionales, pero se recomienda encarecidamente implementar la funcionalidad de cierre de sesión único (SLO), que garantiza que las sesiones se finalizan de forma segura cuando ya no son necesarias, lo que reduce el riesgo de que alguien obtenga accidentalmente acceso no autorizado a las aplicaciones publicadas.

## <a name="related-information"></a>Información relacionada

El último paso proporciona información general sobre las configuraciones. Al pulsar Implementar, se confirmará la configuración y se crearán todos los objetos BIG-IP y APM necesarios para habilitar el acceso híbrido seguro a la aplicación.
La aplicación también debe estar visible como un recurso de destino en CA. Consulte las [instrucciones para crear directivas de CA para Azure AD B2C](conditional-access-identity-protection-overview.md).
Para aumentar la seguridad, las organizaciones que usan este patrón también podrían considerar la posibilidad de bloquear todo el acceso directo a la aplicación, forzando así una ruta de acceso estricta a través de BIG-IP.

## <a name="next-steps"></a>Pasos siguientes

Como usuario, inicie un explorador y conéctese a la dirección URL externa de la aplicación. La página de inicio de sesión del cliente OAuth de BIG-IP le pedirá que inicie sesión mediante la concesión de código de autorización. Las instrucciones para quitar este paso se proporcionan en la sección de configuración complementaria.

A continuación, se le redirigirá para registrarse y autenticarse en su inquilino de Azure AD B2C.

![Captura de pantalla que muestra el inicio de sesión del usuario](./media/partner-f5/sign-in-message.png)

![Captura de pantalla que muestra el mensaje de bienvenida de inicio de sesión posterior](./media/partner-f5/welcome-page.png)

### <a name="supplemental-configurations"></a>Configuraciones complementarias

**Cierre de sesión único (SLO)**

Azure AD B2C es totalmente compatible con el IdP y el cierre de sesión de la aplicación a través de distintos [mecanismos](session-behavior.md?pivots=b2c-custom-policy#single-sign-out).
Hacer que la función de cierre de sesión de la aplicación llame al punto de conexión de cierre de sesión de Azure AD B2C sería una manera de lograr el SLO. De este modo, podemos estar seguros de que Azure AD B2C emite una redirección final a BIG-IP para asegurarse de que la sesión de APM entre el usuario y la aplicación también se ha finalizado.
Otra alternativa es hacer que BIG-IP escuche la solicitud seleccionando el botón de cierre de sesión de las aplicaciones y, al detectar la solicitud, haga una llamada simultánea al punto de conexión de cierre de sesión de Azure AD B2C. Este enfoque evitaría tener que hacer cambios en la propia aplicación, pero logra el SLO. Hay[disponibles](https://support.f5.com/csp/article/K42052145) más detalles sobre el uso de BIG-IP iRules para implementar esto.
En cualquier caso, el inquilino de Azure AD B2C tendría que conocer el punto de conexión de cierre de sesión de APM. 

1. Vaya a **Administrar** > **Manifiesto** en el portal Azure AD B2C y busque la propiedad logoutUrl. Debería ser nula.

2. Agregue el URI posterior al cierre de sesión de APM: `https://<mysite.com>/my.logout.php3`, donde `<mysite.com>` es el FQDN de BIG-IP para su propia aplicación basada en certificados.

**Flujo de inicio de sesión optimizado**

Un paso opcional para mejorar la experiencia de inicio de sesión del usuario sería suprimir la solicitud de inicio de sesión de OAuth que se muestra a los usuarios antes de la autenticación previa de Azure AD. 

1. Vaya a **Access (Acceso)** > **Guided Configuration (Configuración guiada)** y seleccione el icono de candado pequeño situado en el extremo derecho de la fila de la aplicación basada en encabezados para desbloquear la configuración strict.

   ![Captura de pantalla que muestra el flujo de inicio de sesión optimizado](./media/partner-f5/optimized-login-flow.png)

   El desbloqueo de la configuración strict impide que se hagan más cambios a través de la interfaz de usuario del asistente, lo que deja todos los objetos BIG-IP asociados a la instancia publicada de la aplicación abiertos para su administración directa.

2. Vaya a **Access (Acceso)** > **Profiles/Policies (Perfiles/Directivas)** > **Access Profiles (Per-session Policies) (Perfiles de acceso [Perfiles por sesión])** y seleccione el vínculo de edición de la **directiva por sesión** del objeto de la directiva de la aplicación.

   ![Captura de pantalla que muestra los perfiles de acceso](./media/partner-f5/access-profile.png)

3. Seleccione la cruz pequeña para eliminar el objeto de directiva de la página de inicio de sesión de OAuth y, cuando se le solicite, elija conectarse al nodo anterior.

   ![Captura de pantalla que muestra la página de inicio de sesión de OAuth](./media/partner-f5/oauth-logon.png)

4. Seleccione **Apply Access Policy** (Aplicar directiva de acceso) en la esquina superior izquierda y cierre la pestaña del editor visual. El siguiente intento de conectarse a la aplicación debería llevarle directamente a la página de inicio de sesión de Azure AD B2C.

>[!Note]
>Al volver a habilitar el modo strict e implementar una configuración, se sobrescribirá cualquier configuración realizada fuera de la interfaz de usuario de configuración guiada, por lo que se recomienda implementar este escenario mediante la creación manual de todos los objetos de configuración para los servicios de producción.

### <a name="troubleshooting"></a>Solución de problemas

La imposibilidad de acceder a la aplicación protegida puede deberse a varios factores posibles, como una configuración errónea.

- Los registros BIG-IP son una excelente fuente de información para aislar todos los problemas de autenticación y SSO. Si va a solucionar problemas, debe aumentar el nivel de detalle del registro.

  1. Vaya a **Directiva de acceso** > **Información general** > **Event Logs (Registros de eventos)** > **Configuración**.

  2. Seleccione la fila de la aplicación publicada y, a continuación, **Edit (Editar)** > **Access System Logs (Registros del sistema de acceso)**.

  3. Seleccione **Depurar** en la lista SSO y, a continuación, seleccione **Aceptar**. Ahora puede reproducir el problema antes de mirar los registros, pero no olvide volver a cambiar esto cuando haya terminado.

- Si ve un error de marca de BIG-IP inmediatamente después de la autenticación correcta de Azure AD B2C, es posible que el problema esté relacionado con el inicio de sesión único de Azure AD en BIG-IP.

  1. Vaya a **Access (Acceso)** > **Overview (Información general)** > **Access reports (Informes de acceso)**.

  2. Ejecute el informe de la última hora para ver si los registros proporcionan alguna pista. El vínculo View session variables (Ver variables de sesión) de la sesión también le ayudará a saber si APM recibe las notificaciones esperadas de Azure AD.

- Si no ve una página de error de BIG-IP, el problema probablemente esté más relacionado con la solicitud de back-end o con el inicio de sesión único desde BIG-IP a la aplicación.

  1. Go to **Directiva de acceso** > **Información general** > **Active Sessions (Sesiones activas)**.

  2. Seleccione el vínculo de la sesión activa.

- El vínculo View Variables (Ver variables) de esta ubicación también puede ayudar a determinar la causa principal, especialmente si BIG-IP APM no puede obtener los atributos de sesión correctos.
Los registros de la aplicación le ayudarán a saber si recibió esos atributos como encabezados o no.

- Si usa la versión 8 de la configuración guiada, tenga en cuenta un problema conocido que genera el siguiente error de BIG-IP después de la autenticación correcta de Azure AD B2C.  

  ![Captura de pantalla del mensaje de error](./media/partner-f5/error-message.png)

  Se trata de una infracción de directiva debido a la incapacidad de BIG-IP para validar la firma del token emitido por Azure AD B2C. El mismo registro de acceso debe ser capaz de proporcionar más detalles sobre el problema.

  ![Captura de pantalla que muestra los registros de acceso](./media/partner-f5/access-log.png)

  La ingeniería de F5 sigue investigando la causa principal exacta, pero el problema parece estar relacionado con que AGC no habilita la configuración de JWT automático durante la implementación, lo que impide que APM obtenga las claves de firma de tokens actuales.

  Hasta que se resuelva, una manera de solucionar el problema es habilitar manualmente esta configuración. 

  1. Vaya a **Access (Acceso)** > **Guided Configuration (Configuración guiada)** y seleccione el icono de candado pequeño situado en el extremo derecho de la fila de la aplicación basada en encabezados.

  2. Con la configuración administrada desbloqueada, vaya a **Access (Acceso)** > **Federación** > **OAuth Client/Resource Server (Cliente de OAuth/Servidor de recursos)** > **Providers (Proveedores)**.

  3. Seleccione el proveedor para la configuración de Azure AD B2C.

  4. Active la casilla **Use Auto JWT** (Usar JWT automático) y seleccione **Descubrir** seguido de **Guardar**.

    Ahora debería ver el campo Clave (JWT) rellenado con el identificador de clave (KID) del certificado de firma de tokens proporcionado a través de los metadatos del URI de OpenID.
  
  5. Por último, seleccione la opción amarilla **Apply Access Policy (Aplicar directiva de acceso)** en la esquina superior izquierda, junto al logotipo de F5. Aplique esa configuración y seleccione **Aplicar** de nuevo para actualizar la lista de perfiles de acceso.

Consulte la guía de F5 para obtener más [sugerencias de solución de problemas del servidor de recursos y el cliente de OAuth](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-oauth-configuration-14-1-0/apm-oauth-client-and-resource-server.html#GUID-774384BC-CF63-469D-A589-1595D0DDFBA2)
