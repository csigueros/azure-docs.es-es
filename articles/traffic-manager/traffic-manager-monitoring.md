---
title: Supervisión de puntos de conexión de Azure Traffic Manager y conmutación por error | Microsoft Docs
description: Este artículo le puede ayudar a comprender la forma en que el Administrador de tráfico utiliza la supervisión de puntos de conexión y la conmutación por error automática de los puntos de conexión para permitir que los clientes de Azure implementen aplicaciones de alta disponibilidad
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: ce4ff8cfa4bee895e17cd7ad22c54ff777d210ff
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435189"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Supervisión de puntos de conexión de Traffic Manager

El Administrador de tráfico de Azure incluye la supervisión de puntos de conexión integrados y la conmutación por error automática de los puntos de conexión. Esta característica le ayuda a ofrecer aplicaciones de alta disponibilidad que son resistentes a los errores de punto de conexión, como los errores de las regiones de Azure.

## <a name="configure-endpoint-monitoring"></a>Configuración de la supervisión de los puntos de conexión

Para configurar la supervisión de los puntos de conexión, debe especificar la siguiente configuración en el perfil del Administrador de tráfico:

* **Protocolo**. Elija HTTP, HTTPS o TCP como el protocolo que utilizará Traffic Manager al sondear su punto de conexión para comprobar su estado. La supervisión HTTPS no comprueba si el certificado TLS/SSL es válido; solo comprueba que está presente.
* **Port**. Elija el puerto que se usará para la solicitud.
* **Path**. Esta opción de configuración solo es válida para los protocolos HTTP y HTTPS, para los que la configuración de la ruta de acceso especifica es necesaria. Si utiliza esta configuración para el protocolo de supervisión TCP se producirá un error. Para el protocolo HTTP y HTTPS, proporcione la ruta de acceso relativa y el nombre de la página web o el archivo a los que accederá la supervisión. Una barra diagonal (/) es una entrada válida para la ruta de acceso relativa. Este valor implica que el archivo se encuentra en el directorio raíz (valor predeterminado).
* **Configuración del encabezado personalizado**. Esta opción le ayuda a agregar encabezados HTTP específicos para las comprobaciones de estado que Traffic Manager envía a los puntos de conexión de un perfil. Los encabezados personalizados se pueden especificar a nivel de perfil para que se apliquen en todos los puntos de conexión del perfil o a nivel de punto de conexión para que solo se apliquen a este último. Puede usar encabezados personalizados para las comprobaciones de estado de los puntos de conexión en un entorno de varios inquilinos. De este modo, se puede enrutar correctamente a su destino especificando un encabezado host. Para usar esta configuración, también puede agregar encabezados únicos que se usarán para identificar las solicitudes HTTPS originadas por Traffic Manager y procesarlas de manera distinta. Puede especificar hasta ocho pares de valores de encabezado:valor separados por una coma. Por ejemplo, "encabezado1:valor1, encabezado2:valor2". 

   > NOTA: No se admite el uso de caracteres de asterisco (\*) en encabezados `Host` personalizados.

* **Intervalos de códigos de estado esperados**. Esta configuración permite especificar varios intervalos de código de correcto en el formato 200-299, 301-301. Si estos códigos de estado se reciben como respuesta desde un punto de conexión al iniciarse una comprobación de estado, Traffic Manager marca el punto de conexión como correcto. Se puede especificar un máximo de 8 intervalos de código de estado. Esta configuración es aplicable únicamente a los protocolos HTTP y HTTPS y a todos los puntos de conexión. Esta configuración se encuentra a nivel de perfil de Traffic Manager y, de forma predeterminada,se define el valor 200 como código de estado correcto.
* **Intervalo de sondeo**. Este valor especifica la frecuencia con la que el agente de sondeo de Traffic Manager comprueba el estado de un punto de conexión. Puede especificar dos valores aquí: 30 segundos (sondeo normal) y 10 segundos (sondeo rápido). Si no se proporciona ningún valor, el perfil se establece en un valor predeterminado de 30 segundos. Visite la página [Precios de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) para más información sobre precios del sondeo rápido.
* **Número tolerado de errores**. Este valor especifica cuántos errores tolera un agente de sondeo de Traffic Manager antes de marcar un punto de conexión como en mal estado. Su valor puede oscilar entre 0 y 9. Un valor de 0 significa que un único error de supervisión puede dar lugar a que ese punto de conexión se marque como en mal estado. Si no se especifica ningún valor, el valor predeterminado será 3.
* **Tiempo de expiración del sondeo**. Esta propiedad especifica la cantidad de tiempo que debe esperar el agente de sondeo de Traffic Manager antes de considerar la comprobación de sondeo de estado como errónea en un punto de conexión. Si el intervalo de sondeo se establece en 30 segundos, puede establecer el valor del tiempo de espera entre 5 y 10 segundos. Si no se especifica ningún valor, el valor predeterminado será de 10 segundos. Si el intervalo de sondeo se establece en 10 segundos, puede establecer el valor del tiempo de espera entre 5 y 9 segundos. Si no se especifica ningún valor, el valor predeterminado será de 9 segundos.

    ![Supervisión de puntos de conexión de Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Ilustración:  Supervisión de puntos de conexión de Traffic Manager**

## <a name="how-endpoint-monitoring-works"></a>Funcionamiento de la supervisión de puntos de conexión

Cuando el protocolo de supervisión se establece en HTTP o HTTPS, el agente de sondeo de Traffic Manager envía una solicitud GET al punto de conexión utilizando el protocolo, puerto y ruta de acceso relativa indicados. Un punto de conexión se considera correcto si el agente de sondeo recibe una respuesta 200-OK, o cualquiera de las respuestas configuradas en los **\*intervalos de códigos de estado esperados**. Si la respuesta es un valor diferente o no se recibe ninguna dentro del período de espera, el agente de sondeo de Traffic Manager vuelve a intentarlo según la configuración del número tolerado de errores. Si este valor es 0, no se realiza ningún reintento. Si el número de errores consecutivos es mayor que la configuración del número tolerado de errores, ese punto de conexión queda marcado como incorrecto.

Cuando el protocolo de supervisión es TCP, el agente de sondeo de Traffic Manager inicia una solicitud de conexión TCP utilizando el puerto especificado. Si el punto de conexión responde a la solicitud con una respuesta para establecer la conexión, dicha comprobación de estado se marca como correcta, y el agente de sondeo de Traffic Manager restablece la conexión TCP. En los casos en los que la respuesta es un valor diferente o no se recibe ninguna dentro del período de espera, el agente de sondeo de Traffic Manager vuelve a intentarlo según la configuración del número tolerado de errores. Si este valor es 0, no se realiza ningún reintento. Si el número de errores consecutivos es mayor que la configuración del número tolerado de errores, ese punto de conexión queda marcado como incorrecto.

En todos los casos, Traffic Manager sondea desde varias ubicaciones. El error consecutivo determina lo que ocurre dentro de cada región. Esto también significa que los puntos de conexión están recibiendo sondeos de estado desde Traffic Manager con una frecuencia mayor que la configuración usada para el intervalo de sondeo.

>[!NOTE]
>Para los protocolos de supervisión HTTP y HTTPS, una práctica común es implementar en el punto de conexión una página personalizada dentro de la aplicación, como por ejemplo /health.aspx. Con esta ruta de acceso para la supervisión, puede realizar comprobaciones específicas de la aplicación, como la comprobación de los contadores de rendimiento o la comprobación de disponibilidad de la base de datos. Según estas comprobaciones personalizadas, la página devuelve un código de estado HTTP adecuado.

Todos los puntos de conexión de un perfil de Traffic Manager comparten la configuración de supervisión. Si necesita usar diferentes valores de supervisión para los distintos puntos de conexión, puede crear [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Estado de los puntos de conexión y de los perfiles

Puede habilitar y deshabilitar los perfiles y los puntos de conexión del Administrador de tráfico. Sin embargo, como resultado de la configuración y los procesos automatizados de Traffic Manager, también podría producirse un cambio en el estado del punto de conexión.

### <a name="endpoint-status"></a>Estado del extremo

Puede habilitar o deshabilitar un punto de conexión específico. El servicio subyacente, que puede seguir siendo correcto, no se ve afectado. Al cambiar el estado del punto de conexión, se controla la disponibilidad del punto de conexión en el perfil de Traffic Manager. Cuando el estado de un punto de conexión es Deshabilitado, Traffic Manager no comprueba su estado y el punto de conexión no se incluye en la respuesta DNS.

### <a name="profile-status"></a>Estado del perfil

Con la configuración de estado de perfil, puede habilitar o deshabilitar un perfil específico. Aunque el estado del punto de conexión afecta a un solo punto de conexión, el estado del perfil afecta a todo el perfil, lo que incluye todos los puntos de conexión. Cuando se deshabilita un perfil, no se comprueba si son correctos los puntos de conexión y no se incluyen en una respuesta DNS. Se devuelve un código de respuesta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) para la consulta de DNS.

### <a name="endpoint-monitor-status"></a>Estado de supervisión de punto de conexión

El estado de supervisión de un punto de conexión es un valor generado por Traffic Manager que muestra el estado del punto de conexión. No se puede cambiar esta configuración manualmente. El estado de la supervisión del punto de conexión es una combinación de los resultados de la supervisión del punto de conexión y el estado del punto de conexión configurado. Los valores posibles del estado de supervisión del punto de conexión se muestran en la tabla siguiente:

| Estado del perfil | Estado del extremo | Estado de supervisión de punto de conexión | Notas |
| --- | --- | --- | --- |
| Disabled |habilitado |Inactivo |El perfil se ha deshabilitado. Aunque el estado del punto de conexión es Habilitado, el estado del perfil (Deshabilitado) tiene preferencia. Los puntos de conexión en los perfiles deshabilitado no se supervisan. Se devuelve un código de respuesta NXDOMAIN para la consulta de DNS. |
| &lt;cualquiera&gt; |Disabled |Disabled |El extremo se ha deshabilitado. Los puntos de conexión deshabilitados no se supervisan. El punto de conexión no se incluye en las respuestas DNS, por lo que no recibe tráfico. |
| habilitado |habilitado |En línea |El punto de conexión se supervisa y su estado es correcto. Se incluye en las respuestas DNS y, por tanto, puede recibir tráfico. |
| habilitado |habilitado |Degradado |Error en las comprobaciones de estado de supervisión de punto de conexión. El punto de conexión no se incluye en las respuestas DNS y no recibe tráfico. <br>La excepción es que todos los puntos de conexión estén degradados. En ese caso, todos ellos se devuelven en la respuesta de la consulta.</br>|
| habilitado |habilitado |CheckingEndpoint |El punto de conexión se supervisa, pero los resultados del primer sondeo no se han recibido todavía. CheckingEndpoint es un estado temporal que por lo general se produce inmediatamente después de agregar o habilitar un punto de conexión en el perfil. Un punto de conexión en este estado se incluye en las respuestas DNS y puede recibir tráfico. |
| habilitado |habilitado |Detenido |La aplicación web a la que apunta el punto de conexión no se está ejecutando. Compruebe la configuración de la aplicación web. Esto también puede ocurrir si el punto de conexión es de tipo anidado y el perfil del secundario está deshabilitado o inactivo. <br>Un punto de conexión en estado Detenido no se supervisa. Tampoco se incluye en las respuestas DNS y no recibe tráfico. La excepción es que todos los puntos de conexión estén degradados. En ese caso, todos ellos se devolverán en la respuesta de la consulta.</br>|

Para obtener más información sobre cómo se calcula el valor de estado de supervisión del punto de conexión en el caso de puntos de conexión anidados, consulte [Perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md).

>[!NOTE]
> Un estado de supervisión de punto de conexión detenido puede ocurrir en App Service si la aplicación web no se ejecutan en el nivel estándar o superior. Para obtener más información, consulte [Integración de Traffic Manager con App Service](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Estado de supervisión de perfiles

El estado de supervisión de perfiles es una combinación del estado del perfil configurado y los valores del estado de supervisión del punto de conexión para todos los puntos de conexión. En la siguiente tabla se describen los valores posibles:

| Estado del perfil (según la configuración) | Estado de supervisión de punto de conexión | Estado de supervisión de perfiles | Notas |
| --- | --- | --- | --- |
| Disabled |&lt;cualquiera&gt; o un perfil sin puntos de conexión definidos. |Disabled |El perfil se ha deshabilitado. |
| habilitado |El estado de al menos un punto de conexión es Degradado. |Degradado |Revise los valores del estado de los puntos de conexión individuales para determinar cuáles de ellos requieren mayor atención. |
| habilitado |El estado de al menos un punto de conexión es En línea. Ningún punto de conexión tiene un estado Degradado. |En línea |El servicio acepta el tráfico. No se requiere ninguna acción adicional. |
| habilitado |El estado de al menos un punto de conexión es CheckingEndpoint. Ningún punto de conexión está en estado En línea o Degradado. |CheckingEndpoints |Este estado de transición se produce cuando se crea o habilita un perfil. El estado del punto de conexión se comprueba por primera vez. |
| habilitado |El estado de todos los puntos de conexión definidos en el perfil es Deshabilitado o Detenido, o el perfil no tiene ningún punto de conexión definido. |Inactivo |No hay puntos de conexión activos, pero el perfil está todavía habilitado. |

## <a name="endpoint-failover-and-recovery"></a>Conmutación por error y recuperación de un punto de conexión

Traffic Manager comprueba periódicamente el estado de cada punto de conexión, incluidos aquellos incorrectos. Traffic Manager detecta cuándo es correcto un punto de conexión y lo pone de nuevo en rotación.

Un punto de conexión es incorrecto cuando tienen lugar alguna de estas condiciones:

- Si el protocolo de supervisión es HTTP o HTTPS:
    - Se recibe una respuesta distinta de 200 o una que no incluya el intervalo de estado especificado en la opción de configuración **Intervalos de código de estado esperados** (códigos 2xx distintos o redireccionamientos 301/302 incluidos).
- Si el protocolo de supervisión es TCP: 
    - Se recibe una respuesta que no sea ACK o SYN ACK en respuesta a la solicitud SYN enviada por Traffic Manager para intentar establecer una conexión.
- Tiempo de espera. 
- Cualquier otro problema de conexión que provoque que el punto de conexión no sea accesible.

Para más información sobre la solución de problemas de comprobaciones erróneas, consulte [Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md). 

La escala de tiempo de la siguiente ilustración es una descripción detallada del proceso de supervisión del punto de conexión de Traffic Manager que tiene la siguiente configuración:

* El protocolo de supervisión es HTTP.
* El intervalo de sondeo es de 30 segundos.
* El número de errores tolerados es 3.
* El valor de tiempo de espera es de 10 segundos.
* El valor de TTL de DNS es de 30 segundos.

![Secuencia de conmutación por error y conmutación por error de puntos de conexión del Administrador de tráfico](./media/traffic-manager-monitoring/timeline.png)

**Ilustración:  Secuencia de conmutación por error y recuperación de un punto de conexión con Traffic Manager**

1. **GET**. Para cada punto de conexión, el sistema de supervisión de Traffic Manager realiza una solicitud GET en la ruta de acceso especificada en la configuración de supervisión.
2. **Respuesta correcta de 200 o intervalo de código personalizado especificados en la configuración de supervisión del perfil de Traffic Manager**. El sistema de supervisión espera que se devuelva una respuesta HTTP 200 correcta en el intervalo especificado en la configuración de supervisión; es decir, 10 segundos. Cuando recibe esta respuesta, reconoce que el servicio está disponible.
3. **30 segundos entre comprobaciones**. La comprobación de estado del punto de conexión se repite cada 30 segundos.
4. **Servicio no disponible**. El servicio en la nube deja de estar disponible. Traffic Manager no lo sabrá hasta la siguiente comprobación de estado.
5. **Intentos de acceso a la ruta de acceso de supervisión**. El sistema de supervisión realiza una solicitud GET, pero no recibe una respuesta dentro del período de espera de 10 segundos. Después lo intenta tres veces en intervalos de 30 segundos. Si uno de los intentos anteriores es correcto, se restablece el número de intentos.
6. **Estado establecido en Degradado**. Después del cuarto error consecutivo, el sistema de supervisión marca el estado del punto de conexión no disponible como Degradado.
7. **El tráfico se desvía a otros puntos de conexión**. Se actualizan los servidores de nombres DNS del Administrador de tráfico y el Administrador de tráfico ya no devuelve el punto de conexión en respuesta a consultas DNS. Las nuevas conexiones se dirigen a otros puntos de conexión disponibles. En cambio, es posible que las respuestas DNS anteriores que incluyen este punto de conexión aún se puedan almacenar en caché por parte de servidores y clientes DNS recursivos. Los clientes siguen usando el punto de conexión hasta que expira la caché DNS. Conforme la caché DNS expira, los clientes realizan nuevas solicitudes DNS, que se dirigen a otros puntos de conexión. La duración de la caché la controla la opción de TTL del perfil del Administrador de tráfico, por ejemplo 30 segundos.
8. **Continúan las comprobaciones de estado**. El Administrador de tráfico sigue comprobando el estado del punto de conexión mientras tenga un estado Degradado. Traffic Manager detecta cuándo vuelve a ser correcto el punto de conexión.
9. **El servicio vuelve a estar en línea**. El servicio vuelve a estar disponible. El punto de conexión permanece en estado Degradado en Traffic Manager hasta que el sistema de supervisión realiza su siguiente comprobación de estado.
10. **Se reanuda el tráfico al servicio**. El Administrador de tráfico envía una solicitud GET y recibe una respuesta de estado 200 - Correcto. El servicio ha vuelto a un estado correcto. Los servidores de nombres de Traffic Manager se actualizan y comienzan a distribuir el nombre DNS del servicio en las respuestas DNS. El tráfico vuelve al punto de conexión a medida que caducan las respuestas DNS en caché que devuelven otros puntos de conexión, y conforme se terminan las conexiones existentes a otros puntos de conexión.

    > [!NOTE]
    > Como el Administrador de tráfico funciona en el nivel de DNS, no influye en las conexiones existentes a algún punto de conexión. Cuando dirige el tráfico entre los puntos de conexión (bien por la configuración del perfil modificada o durante la conmutación por error o la conmutación por recuperación), el Administrador de tráfico dirige las nuevas conexiones a los puntos de conexión disponibles. Sin embargo, otros puntos de conexión pueden continuar recibiendo tráfico a través de las conexiones existentes hasta que finalizan esas sesiones. Para habilitar el tráfico que se depura de las conexiones existentes, las aplicaciones deben limitar la duración de la sesión que se utiliza con cada punto de conexión.

## <a name="traffic-routing-methods"></a>Métodos de enrutamiento del tráfico

Cuando un punto de conexión tiene un estado Degradado, ya no se devuelve en respuesta a las consultas de DNS. En su lugar, se elige y se devuelve un punto de conexión alternativo. El método de enrutamiento de tráfico configurado en el perfil determina cómo se elige el punto de conexión alternativo.

* **Prioridad**. Los puntos de conexión forman una lista clasificada en orden de prioridad. Siempre se devuelve el primer punto de conexión disponible de la lista. Si un punto de conexión pasa al estado Degradado, se devuelve el siguiente punto de conexión disponible.
* **Ponderado**. Se elige un punto de conexión disponible al azar según sus ponderaciones asignadas y las ponderaciones de los demás puntos de conexión disponibles.
* **Rendimiento**. Se devuelve el punto de conexión más cercano al usuario final. Si ese punto de conexión no está disponible, Traffic Manager moverá el tráfico a los puntos de conexión de la región de Azure siguiente más cercana. Puede configurar planes de conmutación por error alternativos para el enrutamiento del tráfico de rendimiento mediante los [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfico**. Se devuelve el punto de conexión asignado para dar servicio a la ubicación geográfica en función de la dirección IP de la solicitud de consulta. Si ese punto de conexión no está disponible, no se seleccionará otro punto de conexión para la conmutación por error, ya que una ubicación geográfica se puede asignar solo a un punto de conexión en un perfil (puede encontrar más detalles en el artículo [Preguntas más frecuentes](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Como práctica recomendada al usar enrutamiento geográfico, se recomienda que los clientes usen perfiles de Traffic Manager anidados con más de un punto de conexión como puntos de conexión del perfil.
* **Multivalor**. Se devuelven varios puntos de conexión asignados a direcciones IPv4/IPv6. Al recibirse una consulta relacionada con este perfil, se devuelven los puntos de conexión correctos en función del valor de **Número máximo de registros en la respuesta**. El número predeterminado de respuestas es dos puntos de conexión.
* **Subred**. Se devuelve el punto de conexión asignado a un conjunto de intervalos de direcciones IP. Al recibirse una solicitud de esa dirección IP, se devuelve el punto de conexión asignado a ella. 

Para más información, consulte [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md).

> [!NOTE]
> Una excepción a un comportamiento normal del enrutamiento de tráfico se produce cuando todos los puntos de conexión tienen un estado Degradado. Traffic Manager hace todo lo posible por *responder como si todos los puntos de conexión en estado Degradado estuvieran en realidad en estado en línea*. Este comportamiento es preferible a la otra alternativa, que sería no devolver ningún punto de conexión en la respuesta DNS. No se supervisan los puntos de conexión con el estado Detenido o Deshabilitado, por lo tanto, no se consideran aptos para el tráfico.
>
> Normalmente, esta condición es consecuencia de una configuración incorrecta del servicio, como por ejemplo:
>
> * Una lista de control de acceso [ACL] que bloquea las comprobaciones de estado de Traffic Manager.
> * Una configuración incorrecta del puerto o del protocolo de supervisión en el perfil de Traffic Manager.
>
> La consecuencia de este comportamiento es que si las comprobaciones de estado de Traffic Manager no están configuradas correctamente, podría parecer por el enrutamiento del tráfico que Traffic Manager *funciona* correctamente. En cambio, en este caso, la conmutación por error del punto de conexión no se puede producir, lo que afecta a la disponibilidad general de la aplicación. Es importante comprobar que el perfil muestra un estado En línea y no un estado Degradado. Un estado En línea indica que las comprobaciones de estado de Traffic Manager funcionan según lo esperado.

Para obtener más información sobre la solución de problemas de comprobaciones se estado erróneas, consulte [Solución de problemas de estado degradado en Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Preguntas más frecuentes

* [¿Traffic Manager es resistente a errores de región de Azure?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [¿Cómo afecta a Traffic Manager la elección de la ubicación del grupo de recursos?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [¿Cómo se determina el estado actual de cada punto de conexión?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [¿Puedo supervisar puntos de conexión HTTPS?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [¿Uso una dirección IP o un nombre DNS al agregar un punto de conexión?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [¿Qué tipos de direcciones IP puedo usar al agregar un punto de conexión?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [¿Puedo usar tipos de direccionamiento de punto de conexión diferentes en un único perfil?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [¿Qué ocurre cuando el tipo de registro de la consulta entrante es diferente del tipo de registro asociado con el tipo de direccionamiento de los puntos de conexión?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [¿Puedo usar un perfil con puntos de conexión con direcciones IPv4 o IPv6 en un perfil anidado?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Detuve un punto de conexión de una aplicación web en mi perfil de Traffic Manager, pero no recibo tráfico ni siquiera después de haberlo reiniciado. ¿Cómo lo puedo corregir?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [¿Puedo usar Traffic Manager incluso si mi aplicación no tiene compatibilidad con HTTP o HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [¿Qué respuestas específicas se necesitan del punto de conexión cuando se usa la supervisión TCP?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [¿Con qué rapidez mueve Traffic Manager mis usuarios de un punto de conexión incorrecto?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [¿Cómo puedo especificar diferentes opciones de supervisión para los diferentes puntos de conexión de un perfil?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [¿Cómo puedo asignar encabezados HTTP para las comprobaciones de estado de Traffic Manager a mis puntos de conexión?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [¿Qué encabezado de host se utiliza en las comprobaciones de estado del punto de conexión?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [¿Cuáles son las direcciones IP desde las que proceden las comprobaciones de estado?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [¿Cuántas comprobaciones de estado en mi punto de conexión puedo esperar de Traffic Manager?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [¿Cómo puedo recibir notificación si uno de mis puntos de conexión deja de funcionar?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Pasos siguientes

Aprenda [cómo funciona el Administrador de tráfico](traffic-manager-how-it-works.md)

Aprenda más sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.

Aprenda a [crear un perfil del Administrador de tráfico](traffic-manager-manage-profiles.md)

[Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md)
