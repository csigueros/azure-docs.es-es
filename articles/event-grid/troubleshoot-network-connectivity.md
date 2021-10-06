---
title: 'Solución de problemas de conectividad de red: Azure Event Grid | Microsoft Docs'
description: En este artículo se proporciona información sobre cómo solucionar problemas de conectividad de red con Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 09/28/2021
ms.author: batrived
ms.openlocfilehash: c565a465dbab683295cf42248640bbbf8242412c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229950"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Solución de problemas de conectividad - Azure Event Grid

Hay varias razones por las que las aplicaciones cliente no pueden conectarse a un tema o dominio de Event Grid. Los problemas de conectividad que experimenta pueden ser permanentes o transitorios. Si el problema sucede todo el tiempo (es permanente), es posible que quiera comprobar la configuración del firewall de la organización, la configuración del firewall de IP, las etiquetas de servicio, los puntos de conexión privados, etc. En el caso de problemas transitorios, la ejecución de comandos para comprobar los paquetes descartados y la obtención de seguimientos de red puede ayudar a solucionar los problemas.

En este artículo se proporcionan consejos sobre cómo solucionar problemas de conectividad con Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Solución de problemas de conectividad permanentes

Si la aplicación no puede conectarse a la cuadrícula de eventos, siga los pasos de esta sección para solucionar el problema.

### <a name="check-if-theres-a-service-outage"></a>Comprobación de una interrupción del servicio

Compruebe la interrupción del servicio de Azure Event Grid en el [sitio de estado del servicio de Azure](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Compruebe si los puertos necesarios para comunicarse con Event Grid no están bloqueados por el firewall de la organización

Compruebe que los puertos utilizados para comunicarse con Azure Event Grid no están bloqueados en el firewall de la organización. Consulte en la siguiente tabla los puertos de salida que se deben abrir para comunicarse con Azure Event Grid.

| Protocolo | Puertos |
| -------- | ----- |
| HTTPS    | 443   |

Este es un comando de ejemplo que comprueba si el puerto 443 está bloqueado.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

En Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Compruebe que las direcciones IP se permiten en el firewall corporativo

Cuando trabaja con Azure, en ocasiones tiene que permitir intervalos de direcciones IP específicos o direcciones URL en el firewall o proxy corporativo para acceder a todos los servicios de Azure usa o intenta usar. Compruebe que se permite el tráfico en las direcciones IP utilizadas por Event Grid. En el caso de las direcciones IP utilizadas por Azure Event Grid: consulte [Rangos de direcciones IP y etiquetas de servicio de Azure: nube pública](https://www.microsoft.com/download/details.aspx?id=56519) y [Etiqueta de servicio: AzureEventGrid](network-security.md#service-tags).

En el documento [Intervalos de direcciones IP y etiquetas de servicio de Azure: nube pública](https://www.microsoft.com/download/details.aspx?id=56519) también se enumeran las direcciones IP **por región**. Puede permitir intervalos de direcciones para la **región del tema** y la **región emparejada** en el firewall o el proxy corporativo. Para obtener una región emparejada para una región, vea [Continuidad empresarial y recuperación ante desastres (BCDR): Regiones emparejadas de Azure](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Se pueden agregar nuevas direcciones IP a la etiqueta de servicio AzureEventGrid, aunque no es habitual. Por lo tanto, es conveniente realizar una comprobación semanal de las etiquetas de servicio.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Compruebe que se permite la etiqueta de servicio AzureEventGrid en los grupos de seguridad de red

Si la aplicación se ejecuta dentro de una subred y hay un grupo de seguridad de red asociado, confirme si se permite el tráfico saliente de Internet o la etiqueta de servicio AzureEventGrid. Vea [Etiquetas de servicio](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Compruebe la configuración del firewall de IP para su tema o dominio

Compruebe que el firewall de IP del tema o dominio EventGrid no bloquea la dirección IP pública de la máquina en la que se ejecuta la aplicación.

De forma predeterminada, los temas o dominios de Event Grid son accesibles desde Internet, siempre que la solicitud venga con una autenticación y una autorización válidas. Con el firewall de IP, puede restringirlo aún más a solo un conjunto de direcciones o intervalos de direcciones IPv4 en notación [CIDR (Enrutamiento de interdominios sin clases)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Las reglas de firewall de IP se aplican en el nivel de tema o dominio de Event Grid. Por lo tanto, las reglas se aplican a todas las conexiones de clientes que usan cualquier protocolo admitido. Cualquier intento de conexión desde una dirección IP que no coincida con una regla IP admitida en el tema o dominio de Event Grid se rechazará como prohibido. La respuesta no menciona la regla IP.

Para obtener más información, consulte [Configuración de reglas de firewall de IP para un tema o dominio de Azure Event Grid](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Busque las direcciones IP bloqueadas por el firewall de IP

Habilite los registros de diagnóstico para el tema o dominio de Event Grid [Habilitar los registros de diagnóstico](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Verá la dirección IP para la conexión que se ha denegado.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Compruebe si se puede obtener acceso al tema o dominio EventGrid con solo un punto de conexión privado

Si el tema o dominio de Event Grid está configurado para ser accesible únicamente a través de un punto de conexión privado, confirme que la aplicación cliente obtiene acceso al tema o dominio a través del punto de conexión privado. Para confirmarlo, compruebe si la aplicación cliente se ejecuta dentro de una subred y hay un punto de conexión privado para el tema o dominio de Event Grid en esa subred.

El [servicio Azure Private Link](../private-link/private-link-overview.md) permite el acceso a Azure Event Grid a través de un **punto de conexión privado** en la red virtual. Un punto de conexión privado es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. El punto de conexión privado usa una dirección IP privada de la red virtual para incorporar el servicio de manera eficaz a su red virtual. Todo el tráfico dirigido al servicio se puede enrutar mediante el punto de conexión privado, por lo que no se necesita ninguna puerta de enlace, dispositivos NAT, conexiones de ExpressRoute o VPN ni direcciones IP públicas. El tráfico entre la red virtual y el servicio atraviesa la red troncal de Microsoft, eliminando la exposición a la red pública de Internet. Puede conectarse a una instancia de un recurso de Azure, lo que le otorga el nivel más alto de granularidad en el control de acceso.

Para obtener más información, consulte [Configuración de puntos de conexión privados](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Solución de problemas de conectividad transitorios

Si tiene problemas de conectividad intermitentes, consulte las sugerencias de solución de problemas de las secciones siguientes.

### <a name="run-the-command-to-check-dropped-packets"></a>Ejecutar el comando para comprobar los paquetes descartados

Si hay problemas de conectividad intermitentes, ejecute el siguiente comando para comprobar si hay paquetes descartados. Este comando intentará establecer 25 conexiones TCP diferentes cada segundo con el servicio. A continuación, puede comprobar cuántas de ellas se han realizado correctamente y cuántas han fallado y, además, ver la latencia de conexión TCP. Puede descargar la herramienta `psping` desde [aquí](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Puede usar comandos equivalentes si utiliza otras herramientas como `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Realice un seguimiento de red si los pasos anteriores no ayudan y analícelo con herramientas como [Wireshark](https://www.wireshark.org/). Si lo necesita, póngase en contacto con el [soporte técnico de Microsoft](https://support.microsoft.com/).

### <a name="service-upgradesrestarts"></a>Actualizaciones o reinicios del servicio

Pueden producirse problemas de conectividad transitorios debido a actualizaciones y reinicios del servicio back-end. Cuando se producen, es posible que vea los síntomas siguientes:

- Puede haber una caída en la llegada de mensajes o solicitudes entrantes.
- El archivo de registro puede contener mensajes de error.
- Puede que las aplicaciones se desconecten del servicio durante unos segundos.
- Puede que las solicitudes se limiten momentáneamente.

La detección de estos errores transitorios, la interrupción y el posterior reintento de la llamada garantizará que el código sea resistente a estos problemas transitorios.

## <a name="next-steps"></a>Pasos siguientes

Si necesita más ayuda, publique su problema en el [foro de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).