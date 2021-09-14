---
title: Configuración HTTP de Azure Application Gateway
description: En este artículo se describe cómo configurar los valores de HTTP de Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f2fb9d2f6221928f093895914b8fc0082573a8b2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866270"
---
# <a name="application-gateway-http-settings-configuration"></a>Configuración HTTP de Application Gateway

La puerta de enlace de aplicaciones enrutará el tráfico a los servidores back-end mediante la configuración que especifique aquí. Después de crear una configuración de HTTP, debe asociarla con una o varias reglas de enrutamiento de solicitudes.

## <a name="cookie-based-affinity"></a>Afinidad basada en cookies

Azure Application Gateway usa cookies administradas por la puerta de enlace para mantener las sesiones de usuario. Cuando un usuario envía la primera solicitud a Application Gateway, establece una cookie de afinidad en la respuesta con un valor de hash que contiene los detalles de la sesión, de modo que las solicitudes posteriores que lleven la cookie de afinidad se enrutarán al mismo servidor back-end para mantener la adherencia.

Esta característica es útil cuando se desea mantener una sesión de usuario en el mismo servidor y cuando el estado de la sesión se guarda localmente en el servidor para una sesión de usuario. Si la aplicación no puede administrar la afinidad basada en cookies, no podrá usar esta característica. Para poder utilizarla, asegúrese de que los clientes admiten cookies.
> [!NOTE]
> Algunos exámenes de vulnerabilidades pueden marcar la cookie de afinidad de Applicaton Gateway porque las marcas Secure o HttpOnly no están establecidas. Estos exámenes no tienen en cuenta que los datos de la cookie se generan mediante un hash unidireccional. La cookie no contiene ninguna información de usuario y se usa exclusivamente para el enrutamiento. 


La [actualización v80](https://chromiumdash.appspot.com/schedule) del [explorador Chromium](https://www.chromium.org/Home) establece un mandato por el que las cookies HTTP sin el atributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) se tratan como SameSite=Lax. En el caso de las solicitudes CORS (uso compartido de recursos entre orígenes), si la cookie tiene que enviarse en un contexto de terceros, debe usar los atributos *SameSite=None; Secure* y solo debe enviarse utilizando HTTPS. De lo contrario, en un escenario en que solo se utilice HTTP, el explorador no enviará las cookies en el contexto de terceros. El objetivo de esta actualización de Chrome es mejorar la seguridad y evitar los ataques de falsificación de solicitudes entre sitios (CSRF). 

Para admitir este cambio, a partir del 17 de febrero de 2020, Application Gateway (y todos los tipos de SKU) insertarán otra cookie llamada *ApplicationGatewayAffinityCORS* además de la cookie *ApplicationGatewayAffinity* existente. La cookie *ApplicationGatewayAffinityCORS* incorpora dos atributos más ( *"SameSite=None; Secure"* ) para que las sesiones persistentes se mantengan incluso en las solicitudes entre orígenes.

Tenga en cuenta que el nombre predeterminado de la cookie de afinidad es *ApplicationGatewayAffinity*, aunque puede cambiarlo. Si usa un nombre de cookie de afinidad personalizado, se agregará una cookie adicional con CORS como sufijo. Por ejemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Si se establece el atributo *SameSite=None*, es obligatorio que la cookie contenga también la marca *Secure* y se envíe mediante HTTPS.  Si es necesario que la afinidad de la sesión se establezca mediante CORS, deberá migrar la carga de trabajo a HTTPS. Consulte la documentación sobre la descarga de TLS y TLS de un extremo a otro para Application Gateway aquí: [Introducción](ssl-overview.md), [Tutorial: Configuración de una puerta de enlace de aplicaciones con terminación SSL mediante de Azure Portal](create-ssl-portal.md), [Configuración de SSL de un extremo a otro con Application Gateway mediante el portal](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Purga de la conexión

La purga de conexión ayuda a la correcta eliminación de miembros del grupo de servidores back-end durante las actualizaciones de servicio planeadas. Puede aplicar esta configuración a todos los miembros de un grupo back-end habilitando la purga de conexión en la configuración de HTTP. Garantiza que todas las instancias de anulación del registro de un grupo de back-end sigan manteniendo las conexiones existentes y atiendan las solicitudes en curso durante un tiempo de espera configurable y no reciban solicitudes o conexiones nuevas. La única excepción a esto son las solicitudes enlazadas para cancelar el registro de instancias debido a la afinidad de la sesión administrada por la puerta de enlace y que se seguirán reenviando a las instancias de cancelación del registro. La purga de conexión se aplica a instancias de back-end que se eliminan explícitamente del grupo de servidores back-end.

## <a name="protocol"></a>Protocolo

Application Gateway admite HTTP y HTTPS para las solicitudes de enrutamiento a los servidores back-end. Si elige HTTP, el tráfico a los servidores back-end no estará cifrado. Si la comunicación sin cifrar no es aceptable, seleccione HTTPS.

Esta configuración combinada con HTTPS en el cliente de escucha admite [TLS de un extremo a otro](ssl-overview.md). Esto le permite transmitir de forma segura información confidencial cifrada al back-end. Cada servidor back-end del grupo con TLS de un extremo a otro habilitada debe configurarse con un certificado para permitir la comunicación segura.

## <a name="port"></a>Port

Este valor especifica el puerto en el que escuchan los servidores back-end el tráfico de la puerta de enlace de aplicaciones. Puede configurar los puertos comprendidos entre 1 y 65535.

## <a name="request-timeout"></a>Tiempo de espera de solicitud

Este valor es el número de segundos que la puerta de enlace de aplicaciones espera para recibir una respuesta del servidor back-end.

## <a name="override-back-end-path"></a>Ruta de acceso de back-end de sustitución

Este valor le permite configurar una ruta de reenvío personalizada opcional que se usará cuando la solicitud se reenvíe al servidor back-end. Cualquier parte de la ruta de acceso entrante que coincida con la ruta de acceso personalizada del campo **ruta de acceso de back-end de sustitución** se copiará en la ruta de acceso reenviada. En la tabla siguiente se muestra cómo funciona esta característica:

- Cuando la configuración de HTTP se asocia a una regla básica de enrutamiento de solicitudes:

  | Solicitud original  | Ruta de acceso de back-end de sustitución | Solicitud que se reenvía al back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Cuando la configuración de HTTP se asocia a una regla de enrutamiento de solicitudes basada en una ruta de acceso:

  | Solicitud original           | Regla de la ruta       | Ruta de acceso de back-end de sustitución | Solicitud que se reenvía al back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

## <a name="use-for-app-service"></a>Uso para App Service

Se trata de un acceso directo solo para la interfaz de usuario que selecciona los dos valores necesarios para el back-end de Azure App Service. Habilita la opción de **selección del nombre de host de la dirección de back-end** y crea un nuevo sondeo personalizado si no existe ya uno. (Para más información, consulte la sección dedicada a la opción [Seleccionar nombre de host de la dirección de back-end](#pick-host-name-from-back-end-address) de este artículo). Se crea un nuevo sondeo y se selecciona el encabezado de este a partir de la dirección de un miembro del back-end.

## <a name="use-custom-probe"></a>Usar sondeo personalizado

Esta opción asocia un [sondeo personalizado](application-gateway-probe-overview.md#custom-health-probe) con una configuración de HTTP. Solo puede asociar un sondeo personalizado con una configuración de HTTP. Si no asocia explícitamente un sondeo personalizado, se empleará el [sondeo predeterminado](application-gateway-probe-overview.md#default-health-probe-settings) para supervisar el estado del back-end. Es recomendable crear un sondeo personalizado para un mayor control sobre la supervisión del estado de los servidores back-end.

> [!NOTE]
> El sondeo personalizado no supervisa el estado del grupo de servidores back-end a menos que la configuración de HTTP correspondiente esté explícitamente asociada a un cliente de escucha.

## <a name="pick-host-name-from-back-end-address"></a>Seleccionar nombre de host de la dirección de back-end

Esta funcionalidad establece dinámicamente el encabezado *host* de la solicitud en el nombre de host del grupo de servidores back-end. Usa una dirección IP o FQDN.

Esta característica resulta útil cuando el nombre de dominio del back-end es diferente del nombre DNS de la puerta de enlace de aplicaciones y el back-end se basa en un encabezado de host específico para resolver en el punto de conexión correcto.

Un ejemplo podría ser el uso de servicios multiinquilino como back-end. Una instancia de App Service es un servicio multiinquilino que usa un espacio compartido con una sola dirección IP. Por lo tanto, solo se puede acceder a una instancia de App Service mediante los nombres de host que se establecen en la configuración del dominio personalizado.

De forma predeterminada, el nombre de dominio personalizado es *example.azurewebsites.net*. Para acceder a la instancia de App Service con una puerta de enlace de aplicaciones mediante un nombre de host que no está explícitamente registrado en dicha instancia o mediante el FQDN de la puerta de enlace de aplicaciones, debe reemplazar el nombre de host de la solicitud original por el nombre de host de la instancia de App Service. Para ello, habilite la opción **Seleccionar nombre de host de la dirección de back-end**.

Para un dominio personalizado cuyo nombre DNS personalizado ya existente está asignado a la instancia de App Service, no es necesario que habilite esta opción.

> [!NOTE]
> Esta opción no es necesaria para App Service Environment, que es una implementación dedicada.

## <a name="host-name-override"></a>Sustitución del nombre de host

Esta funcionalidad reemplaza el encabezado *host* de la solicitud entrante en la puerta de enlace de aplicaciones por el nombre de host que especifique.

Por ejemplo, si se especifica *www.contoso.com* en el valor **Nombre de host**, la solicitud original *`https://appgw.eastus.cloudapp.azure.com/path1` cambia a *`https://www.contoso.com/path1` cuando la solicitud se reenvía al servidor back-end.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre el grupo de back-end](configuration-overview.md#back-end-pool)
