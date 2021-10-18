---
title: Redirección de URL y reescritura de URL con Azure Front Door Estándar/Premium (versión preliminar)
description: Este artículo le ayudará a comprender cómo Azure Front Door admite la redirección de URL y la reescritura de URL mediante un conjunto de reglas de Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 2e758560eed1ffb01117764f9399aa6f4f4b1395
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857557"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Redirección de URL y reescritura de URL con Azure Front Door Estándar/Premium (versión preliminar)

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

Este artículo le ayuda a entender cómo Azure Front Door Estándar/Premium admite la redirección de URL y la reescritura de URL en un conjunto de reglas.

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Redirección de direcciones URL

Azure Front Door puede redirigir el tráfico en cada uno de estos niveles: protocolo, nombre de host, ruta de acceso, cadena de consulta y fragmento. Estas funcionalidades se pueden configurar para microservicios individuales, ya que el redireccionamiento está basado en la ruta de acceso. Con el redireccionamiento de URL puede simplificar la configuración de la aplicación, ya que optimiza el uso de recursos y admite nuevos escenarios de redireccionamiento que incluyen el redireccionamiento basado en la ruta de acceso y el global.

Puede configurar el redireccionamiento de URL a través del conjunto de reglas.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Captura de pantalla de creación del redireccionamiento de URL con un conjunto de reglas." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Tipo de redireccionamiento
Un tipo de redirección establece el código de estado de respuesta para que los clientes comprendan el propósito de la redirección. Se admiten los siguientes tipos de redireccionamiento:

* **301 (movido permanentemente)** : Indica que se ha asignado un nuevo URI permanente al recurso de destino. Todas las referencias futuras a este recurso usarán uno de los identificadores URI delimitados. Use el código de estado 301 para el redireccionamiento de HTTP a HTTPS.
* **302 (encontrado)** : Indica que el recurso de destino se encuentra temporalmente en otro URI. Puesto que el redireccionamiento se puede modificar, el cliente debe seguir usando el URI de solicitud efectivo para las solicitudes futuras.
* **307 (redirección temporal)** : Indica que el recurso de destino se encuentra temporalmente en otro URI. El agente de usuario NO DEBE cambiar el método de solicitud si realiza un redireccionamiento automático a ese URI. Puesto que el redireccionamiento puede cambiar con el tiempo, el cliente debería seguir usando el URI de solicitud efectivo original para las solicitudes futuras.
* **308 (redirección permanente)** : Indica que se ha asignado un nuevo URI permanente al recurso de destino. Todas las referencias futuras a este recurso deben usar uno de los URI delimitados.

### <a name="redirection-protocol"></a>Protocolo de redireccionamiento
Puede establecer el protocolo que se usará para el redireccionamiento. Los casos de uso más comunes de la característica de redireccionamiento consiste en establecer el redireccionamiento de HTTP a HTTPS.

* **Solo HTTPS**: Establezca el protocolo en solo HTTPS, si busca redirigir el tráfico de HTTP a HTTPS. Azure Front Door recomienda que siempre establezca el redireccionamiento en solo HTTPS.
* **Solo HTTP**: se redirige la solicitud entrante a HTTP. Use este valor solo si busca mantener el tráfico HTTP tal cual, sin cifrar.
* **Confrontar solicitud**: esta opción conserva el protocolo usado por la solicitud entrante. Por lo tanto, una solicitud HTTP permanece HTTP, y una solicitud HTTPS permanece HTTPS después del redireccionamiento.

### <a name="destination-host"></a>Host de destino
Como parte de la configuración de una ruta de redireccionamiento, también puede cambiar el nombre de host o el dominio para la solicitud de redireccionamiento. Puede establecer este campo para cambiar el nombre de host en la dirección URL para el redireccionamiento o, de lo contrario, conservar el nombre de host de la solicitud entrante. Por lo tanto, usar este campo le permite redirigir todas las solicitudes enviadas en `https://www.contoso.com/*` hacia `https://www.fabrikam.com/*`.

### <a name="destination-path"></a>Ruta de acceso de destino
Para los casos en los que quiera reemplazar el segmento de ruta de acceso de una dirección URL como parte del redireccionamiento, puede establecer este campo en el nuevo valor de la ruta de acceso. En caso contrario, puede elegir conservar el valor de ruta de acceso como parte del redireccionamiento. Por lo tanto, usar este campo le permite redirigir todas las solicitudes enviadas a `https://www.contoso.com/\*` hacia `https://www.contoso.com/redirected-site`.

### <a name="query-string-parameters"></a>Parámetros de cadena de consulta
También puede reemplazar los parámetros de la cadena de consulta en la dirección URL redirigida. Para reemplazar cualquier cadena de consulta existente desde la dirección URL de solicitud entrante, establezca este campo en "Replace" (Reemplazar) y, después, el valor adecuado. En caso contrario, conserve el conjunto original de las cadenas de consulta estableciendo el campo en "Preserve" (Conservar). Como ejemplo, usar este campo le permite redirigir todo el tráfico enviado a `https://www.contoso.com/foo/bar` hacia `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`. 

### <a name="destination-fragment"></a>Fragmento de destino
El fragmento de destino es la parte de la dirección URL tras "#", los exploradores lo usan para aterrizar en una sección concreta de una página web. Puede establecer este campo para agregar un fragmento a la dirección URL de redireccionamiento.

## <a name="url-rewrite"></a>Reescritura de direcciones URL

Azure Front Door admite la reescritura de URL para volver a escribir la ruta de acceso de una solicitud que está en tránsito hacia su origen. La reescritura de URL le permite agregar las condiciones necesarias para asegurarse de que los encabezados especificados y las direcciones URL se reescriben solo cuando se cumplen ciertas condiciones. Estas condiciones se basan en la información de solicitud y respuesta.

Con esta característica, puede redirigir a los usuarios a diferentes orígenes según el escenario, el tipo de dispositivo y el tipo de archivo solicitado.

Puede configurar el redireccionamiento de URL a través del conjunto de reglas.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Captura de pantalla de creación de la reescritura de URL con un conjunto de reglas." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Patrón de origen

El patrón de origen es la ruta de acceso URL en la solicitud de origen que se va a reemplazar. Actualmente, el patrón de origen usa una coincidencia basada en el prefijo. Para una coincidencia con todas las ruta de acceso de las direcciones URL, use una barra (/) como valor de patrón de origen.

Para el patrón de origen de reescritura de direcciones URL, solo se tiene en cuenta la ruta de acceso después de la configuración de ruta "patrones de coincidencia". Por ejemplo, tiene el siguiente formato de dirección URL entrante `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-Source-pattern>`, solo `/<Rule-URL-Rewrite-Source-pattern>` será considerado por el motor de reglas como el patrón de origen que se va a reescribir. Por lo tanto, cuando tenga una regla de reescritura de URL con coincidencia de patrón de origen, el formato de la URL saliente será `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-destination>`.

En escenarios en los que se debe quitar el segmento `/<route-patterns-to-match-path` de la ruta de acceso URL, establezca la ruta de acceso Origen del grupo Origen en la configuración de ruta en `/`.

### <a name="destination"></a>Destination

Puede definir la ruta de acceso de destino que se va a usar en la reescritura. La ruta de acceso de destino sobrescribe el patrón de origen.

### <a name="preserve-unmatched-path"></a>Conservar la ruta de acceso sin coincidencia

Conservar la ruta de acceso no coincidente permite anexar el resto de la ruta después del patrón de origen a la nueva ruta de acceso.

Por ejemplo, si he establecido la opción **Conservar la ruta de acceso no coincidente en Sí**.
* Si la solicitud entrante es `www.contoso.com/sub/1.jpg`, el patrón de origen se establece en `/`, el destino se establece en `/foo/` y el contenido se ofrece desde `/foo/sub/1`.jpg desde el origen.

* Si la solicitud entrante es `www.contoso.com/sub/image/1.jpg`, el patrón de origen se establece en `/sub/`, el destino se establece en `/foo/` y el contenido se ofrece desde `/foo/image/1.jpg` desde el origen.

Por ejemplo, si he establecido la opción **Conservar la ruta de acceso no coincidente en No**.
* Si la solicitud entrante es `www.contoso.com/sub/image/1.jpg`, el patrón de origen se establece en `/sub/`, el destino se establece en `/foo/2.jpg`, el contenido siempre se ofrecerá desde `/foo/2.jpg` desde el origen, independientemente de las rutas de acceso que se hayan seguido en `wwww.contoso.com/sub/`.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [conjunto de reglas de Azure Front Door Estándar/Prémium](concept-rule-set.md).
