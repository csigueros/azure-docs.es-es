---
title: Supervisión del estado de instancias de App Service
description: Obtenga información sobre cómo supervisar el estado de las instancias de App Service mediante la comprobación de estado.
keywords: azure app service, web app, health check, route traffic, healthy instances, path, monitoring, remove faulty instances, unhealthy instances, remove workers
author: msangapu-msft
ms.topic: article
ms.date: 07/19/2021
ms.author: msangapu
ms.custom: contperf-fy22q1
ms.openlocfilehash: 37b876b177b7879c57255619d3f5e7e113a2a284
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278074"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Supervisión de instancias de App Service mediante la comprobación de estado

En este artículo se usa la comprobación de estado en Azure Portal para supervisar las instancias de App Service. La comprobación de estado aumenta la disponibilidad de la aplicación al volver a enrutar las solicitudes fuera de las instancias en estado incorrecto y reemplazar las instancias si siguen en estado incorrecto. El [plan de App Service](./overview-hosting-plans.md) se debe escalar a dos o más instancias para utilizar completamente la comprobación de estado. La ruta de acceso de comprobación de estado debe comprobar los componentes críticos de la aplicación. Por ejemplo, si la aplicación depende de una base de datos y de un sistema de mensajería, el punto de conexión de comprobación de estado debe conectarse a esos componentes. Si la aplicación no se puede conectar a un componente crítico, la ruta de acceso debe devolver un código de respuesta de nivel 500 para indicar que la aplicación tiene un estado incorrecto.

![Error de la comprobación de estado][1]

## <a name="what-app-service-does-with-health-checks"></a>Lo que hace App Service con las comprobaciones de estado

- Cuando se proporciona una ruta de acceso en la aplicación, la comprobación de estado hace ping en esta ruta de acceso a todas las instancias de la aplicación App Service en intervalos de 1 minuto.
- Si una instancia no responde con un código de estado entre 200 y 299 (inclusive) después de dos o más solicitudes, o no responde al ping, el sistema determina que su estado es incorrecto y lo quita.
- Después de la eliminación, la comprobación de estado sigue haciendo ping a la instancia incorrecta. Si sigue respondiendo sin éxito, App Service reinicia la máquina virtual subyacente en un esfuerzo para devolver la instancia a un estado correcto.
- Si una instancia permanece en mal estado durante una hora, se reemplaza por una nueva.
- Además, al escalar vertical u horizontalmente, App Service hace ping en la ruta de acceso de comprobación de estado para asegurarse de que las nuevas instancias están listas.

> [!NOTE]
>- La comprobación de estado no sigue 302 redirecciones. A lo sumo, se reemplaza una instancia por hora, con un máximo de tres instancias al día por plan de App Service.
>- Tenga en cuenta que si la comprobación de estado muestra el estado `Waiting for health check response`, es probable que se deba a un error en la comprobación debido a un código de estado HTTP 307, lo que puede ocurrir si tiene habilitada la redirección HTTPS pero se ha `HTTPS Only` deshabilitado.

## <a name="enable-health-check"></a>Habilitación de la comprobación de estado

![Navegación por la comprobación de estado en Azure Portal][3]

- Para habilitar la comprobación de estado, vaya a Azure Portal y seleccione la aplicación App Service.
- En **Supervisión**, seleccione **Comprobación de estado**.
- Seleccione **Habilitar** y proporcione una ruta de acceso válida a una dirección URL en la aplicación, por ejemplo, `/health` o `/api/health`.
- Haga clic en **Save**(Guardar).

> [!CAUTION]
> Los cambios de configuración de la comprobación de estado reinician la aplicación. Para minimizar el impacto en las aplicaciones de producción, se recomienda [configurar los espacios de ensayo](deploy-staging-slots.md) y cambiar a producción.
>

### <a name="configuration"></a>Configuración

Además de configurar las opciones de la comprobación de estado, también puede configurar las siguientes [opciones de la aplicación](configure-common.md):

| Nombre del valor de configuración de la aplicación | Valores permitidos | Descripción |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Número necesario de solicitudes con error para que una instancia se considere en estado incorrecto y se quite del equilibrador de carga. Por ejemplo, si se establece en `2`, las instancias se quitarán después de `2` ping con error. (El valor predeterminado es `10`). |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 - 100 | De forma predeterminada, no se excluirá más de la mitad de las instancias del equilibrador de carga a la vez para evitar sobrecargar las instancias en estado correcto restantes. Por ejemplo, si un plan de App Service se escala a cuatro instancias y tres son incorrectas, se excluirán dos. Las otras dos instancias (una correcta y otra incorrecta) seguirán recibiendo solicitudes. En el peor de los casos, si todas las instancias están en mal estado, no se excluye ninguna. <br /> Para invalidar este comportamiento, establezca la configuración de la aplicación en un valor entre `0` y `100`. Un valor mayor significa que se eliminarán más instancias incorrectas (el valor predeterminado es `50`). |

#### <a name="authentication-and-security"></a>Autenticación y seguridad

La comprobación de estado se integra con las [características de autenticación y autorización](overview-authentication-authorization.md) de App Service. No se requiere ninguna configuración adicional si estas características de seguridad están habilitadas.

Si usa un sistema de autenticación propio, la ruta de comprobación de estado debe permitir el acceso anónimo. Para proteger el punto de conexión de la comprobación de estado, primero debe usar características como [restricciones de IP](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [certificados de cliente](app-service-ip-restrictions.md#set-an-ip-address-based-rule) o una red virtual para restringir el acceso a la aplicación. Puede proteger el punto de conexión de la comprobación de estado requiriendo que `User-Agent` de la solicitud entrante coincida con `HealthCheck/1.0`. El agente de usuario no se puede suplantar porque la solicitud ya estaría protegida por las características de seguridad anteriores.

## <a name="monitoring"></a>Supervisión

Después de proporcionar la ruta de acceso de la comprobación de estado de la aplicación, puede supervisar el estado del sitio mediante Azure Monitor. En la hoja **Comprobación de estado** del portal, haga clic en **Métricas** en la barra de herramientas superior. Se abre una nueva hoja donde puede ver el estado de mantenimiento histórico del sitio y crear una regla de alerta. Para obtener más información sobre la supervisión de sitios, [vea la guía sobre Azure Monitor](web-sites-monitor.md).

## <a name="limitations"></a>Limitaciones

- No se debe habilitar la comprobación de estado en los sitios de funciones prémium. Debido al rápido escalado de las funciones Premium, las solicitudes de comprobación de estado pueden provocar fluctuaciones innecesarias en el tráfico HTTP. Las funciones prémium tienen sus propios sondeos de estado internos que se usan para informar sobre las decisiones de escalado.
- La comprobación de estado se puede habilitar para los planes **Gratis** y **Compartido** de App Service, por lo que puede tener métricas del estado del sitio y configurar alertas, pero como los sitios de los planes **Gratis** y **Compartido** no se pueden escalar horizontalmente, no se sustituirán las instancias incorrectas. Debe escalar verticalmente al nivel **Básico** o superior para poder escalar horizontalmente a 2 o más instancias y usar todas las ventajas de la comprobación de estado. Esto se recomienda para las aplicaciones orientadas a producción, ya que aumentará la disponibilidad y el rendimiento de la aplicación.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-happens-if-my-app-is-running-on-a-single-instance"></a>¿Qué ocurre si mi aplicación se ejecuta en una sola instancia?

Si la aplicación solo se escala a una instancia y queda en estado incorrecto, no se quitará del equilibrador de carga, ya que eso haría que la aplicación se quede completamente fuera de servicio. Escale horizontalmente a dos o más instancias para obtener la ventaja de volver a enrutar que ofrece la comprobación de estado. Si la aplicación se ejecuta en una sola instancia, todavía puede usar la característica de [supervisión](#monitoring) de la comprobación de estado para realizar un seguimiento del estado de la aplicación.
 
### <a name="why-are-the-health-check-request-not-showing-in-my-frontend-logs"></a>¿Por qué no se muestra la solicitud de comprobación de estado en mis registros de front-end?

La solicitud de comprobación de estado se envía al sitio internamente, por lo que la solicitud no se mostrará en [los registros de front-end](troubleshoot-diagnostic-logs.md#enable-web-server-logging). Esto también significa que la solicitud tendrá el origen `127.0.0.1`, ya que la solicitud se envía internamente. Puede agregar instrucciones de registro en el código de comprobación de estado para mantener registros de cuándo se hace ping a la ruta de acceso de la comprobación de estado.

### <a name="are-the-health-check-requests-sent-over-http-or-https"></a>¿Las solicitudes de comprobación de estado se envían mediante HTTP o HTTPS?

Las solicitudes de comprobación de estado se envían mediante HTTPS cuando la opción [Solo HTTPS](configure-ssl-bindings.md#enforce-https) está habilitada en el sitio. De lo contrario, se envían mediante HTTP.

### <a name="what-if-i-have-multiple-apps-on-the-same-app-service-plan"></a>¿Qué ocurre si tengo varias aplicaciones en el mismo plan de App Service?

Las instancias en estado incorrecto se quitarán siempre de la rotación del equilibrador de carga, independientemente de otras aplicaciones del plan de App Service (hasta el porcentaje especificado en [`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`](#configuration)). Cuando una aplicación de una instancia permanece en estado incorrecto durante más de una hora, la instancia solo se reemplazará si todas las demás aplicaciones con la comprobación de estado habilitada también tienen un estado incorrecto. Las aplicaciones que no tienen habilitada la comprobación de estado no se tendrán en cuenta. 

#### <a name="example"></a>Ejemplo 

Imagine que tiene dos aplicaciones (o una aplicación con una ranura) con la comprobación de estado habilitada, llamadas Aplicación A y Aplicación B. Están en el mismo plan de App Service y el plan se escala horizontalmente a 4 instancias. Si la Aplicación A tiene un estado incorrecto en dos instancias, el equilibrador de carga dejará de enviar solicitudes a la Aplicación A en esas dos instancias. Las solicitudes se seguirán enrutando a la Aplicación B en esas instancias, suponiendo que la Aplicación B esté en estado correcto. Si la Aplicación A permanece en estado incorrecto durante más de una hora en esas dos instancias, esas instancias solo se reemplazarán si la Aplicación B **también** tiene un estado incorrecto en esas instancias. Si la Aplicación B tiene un estado correcto, la instancia no se reemplazará.

![Diagrama visual que explica el escenario de ejemplo anterior.][2]

> [!NOTE]
> Si hubiera otro sitio o ranura en el plan (sitio C) sin la comprobación de estado habilitada, no se tendría en cuenta para la sustitución de la instancia.

### <a name="what-if-all-my-instances-are-unhealthy"></a>¿Qué ocurre si todas mis instancias tienen un estado incorrecto?

En el escenario en el que todas las instancias de la aplicación tienen un estado incorrecto, App Service quitará las instancias del equilibrador de carga hasta el porcentaje especificado en `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`. En este escenario, quitar todas las instancias de aplicación en estado incorrecto de la rotación del equilibrador de carga provocaría una interrupción de la aplicación.

### <a name="does-health-check-work-on-app-service-environments"></a>¿Funciona la comprobación de estado en instancias de App Service Environment?

Sí, en App Service Environments (ASE), la plataforma hará ping a las instancias en la ruta de acceso especificada y quitará las instancias incorrectas del equilibrador de carga para que las solicitudes no se enruten a ellas. Sin embargo, actualmente estas instancias incorrectas no se reemplazarán por nuevas instancias si permanecen en estado incorrecto durante 1 hora.

## <a name="next-steps"></a>Pasos siguientes
- [Creación de una alerta de registro de actividades para supervisar todas las operaciones del motor de escalado automático en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [Creación de una alerta de registro de actividades para supervisar todas las operaciones erróneas de escalado automático y reducción horizontal en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)
- [Variables de entorno y configuración de la aplicación en Azure App Service](reference-app-settings.md)

[1]: ./media/app-service-monitor-instances-health-check/health-check-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-multi-app-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
