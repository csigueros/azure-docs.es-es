---
title: Solución de problemas de errores y tiempos de espera de respuesta del cliente con API Management
description: Solución de errores de conexión intermitentes y problemas de latencia relacionados en API Management
author: dlepow
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: danlep
ms.service: api-management
ms.openlocfilehash: d7b6f8ed20f1af7b4a6f512b9aa4611936f784fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623919"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Solución de problemas de errores y tiempos de espera de respuesta del cliente con API Management

Este artículo lo ayudará a solucionar errores de conexión intermitentes y problemas de rendimiento relacionados en [Azure API Management](./api-management-key-concepts.md). En concreto, en este artículo se proporciona más información sobre problemas de agotamiento de los puertos de traducción de red de direcciones (SNAT) de origen y cómo solucionarlos. Si necesita más ayuda, póngase en contacto con los expertos de Azure en [Soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/) o abra una solicitud de soporte técnico con [Soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Síntomas

Las aplicaciones cliente que llaman a las API a través del servicio API Management (APIM) pueden presentar uno o varios de los síntomas siguientes:

* Errores HTTP 500 intermitentes
* Mensaje de error relativo al tiempo de expiración.

Estos síntomas se manifiestan como instancias de `BackendConnectionFailure` en los [registros de recursos de Azure Monitor](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Causa

Este patrón de síntomas suele producirse debido a los límites de los puertos de traducción de direcciones de red (SNAT) con el servicio APIM.

Cada vez que un cliente llama a una de las API de APIM, el servicio Azure API Management abre un puerto SNAT para acceder a la API de back-end. Tal y como se describe en [Conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md), Azure usa la traducción de direcciones de red de origen (SNAT) y un equilibrador de carga (no expuesto a los clientes) para comunicarse con los puntos de conexión externos a Azure en el espacio de direcciones IP públicas, así como con los puntos de conexión internos de Azure que no usan los [puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md). Esta situación solo es aplicable a las API de back-end expuestas en direcciones IP públicas.

A cada instancia del servicio API Management se le asigna inicialmente un número predefinido de puertos SNAT. Ese límite afecta a la apertura de conexiones a la misma combinación de host y puerto. Los puertos SNAT se usan cuando hay llamadas repetidas a la misma combinación de dirección y puerto. Una vez que se ha liberado un puerto SNAT, está disponible para reutilizarse según sea necesario. El equilibrador de carga de red de Azure solo reclama el puerto SNAT de las conexiones cerradas después de esperar 4 minutos.

Una sucesión rápida de solicitudes de cliente a las API puede agotar la cuota preasignada de puertos SNAT si estos puertos no se cierran y se reciclan lo suficientemente rápido, lo que impide que el servicio APIM procese las solicitudes de cliente de manera oportuna.

## <a name="mitigations-and-solutions"></a>Mitigaciones y soluciones

Para abordar el problema del agotamiento de los puertos SNAT, primero hay que diagnosticar y optimizar el rendimiento de los servicios de back-end.

Las estrategias generales para mitigar el agotamiento de los puertos SNAT se describen en el tema [Solución de errores de conexiones salientes](../load-balancer/troubleshoot-outbound-connection.md) de la documentación de *Azure Load Balancer*. De estas estrategias, se aplican las siguientes a API Management.

### <a name="scale-your-apim-instance"></a>Escalado de la instancia de APIM

A cada instancia de API Management se le asigna un número de puertos SNAT, en función de las unidades de APIM. Puede asignar más puertos SNAT escalando la instancia de API Management con unidades adicionales. Para obtener más información, consulte [Escalado del servicio API Management](upgrade-and-scale.md#scale-your-api-management-service).

> [!NOTE]
> El uso de puertos SNAT no está disponible actualmente como una métrica para el escalado automático de unidades de API Management.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Uso de varias direcciones IP para las direcciones URL de back-end

Cada conexión de la instancia de APIM a la misma dirección IP de destino y puerto de destino del servicio de back-end usará un puerto SNAT para mantener un flujo de tráfico distinto. Sin puertos SNAT diferentes para el tráfico de retorno del servicio en segundo plano, APIM no tendría ninguna manera de separar una respuesta de otra.

Dado que los puertos SNAT se pueden reutilizar si el puerto de destino o la dirección IP de destino son diferentes, otra manera de evitar el agotamiento de los puertos SNAT es usar varias direcciones IP para las direcciones URL del servicio de back-end.

Para obtener más información, consulte [Azure Load Balancer como proxy de salida](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Colocación del servicio de back-end y APIM en la misma red virtual

Si la API de back-end está hospedada en un servicio de Azure que admite *puntos de conexión de servicio*, como App Service, puede evitar problemas de agotamiento de puertos SNAT colocando la instancia de APIM y el servicio de back-end en la misma red virtual y exponiéndolos a través de los [puntos de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md) o de [puntos de conexión privados](../private-link/private-endpoint-overview.md). Cuando se usa una red virtual común y se colocan puntos de conexión de servicio en la subred de integración, el tráfico saliente desde la instancia de APIM a esos servicios omite Internet y, por tanto, se evitan las restricciones de los puertos SNAT. Del mismo modo, si usa una red virtual y puntos de conexión privados, no tendrá ningún problema con los puertos SNAT de salida en ese destino.

Para obtener más información, consulte [Usar Azure API Management con redes virtuales](api-management-using-with-vnet.md) y [Integración de App Service con una red virtual de Azure](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Colocación de APIM en una red virtual y enrutamiento de las llamadas salientes a Azure Firewall

De forma similar a la colocación de APIM y los servicios de back-end en una red virtual, puede emplear Azure Firewall en una red virtual con el servicio APIM y, a continuación, enrutar las llamadas salientes de APIM a Azure Firewall. Entre APIM y Azure Firewall (que se encuentran en la misma red virtual), no se requieren puertos SNAT. En el caso de las conexiones SNAT a los servicios de back-end, Azure Firewall tiene 64 000 puertos disponibles, una cantidad mucho más alta que la asignada a las instancias de APIM.

Consulte la documentación de [Azure Firewall](../firewall/overview.md) para obtener más información.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Use el almacenamiento en caché de respuesta y otros ajustes de rendimiento de back-end

Otra posible mitigación que se debe tener en cuenta es mejorar los tiempos de procesamiento de las API de back-end. Una manera de hacerlo es configurando ciertas API con el almacenamiento en caché de respuesta para reducir la latencia entre las aplicaciones cliente que llaman a la API y la carga de back-end de APIM.

Para obtener más información, consulte [Incorporación del almacenamiento en caché para mejorar el rendimiento en Azure API Management](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Implemente directivas de restricción de acceso

Si tiene sentido para su escenario empresarial, puede implementar directivas de restricción de acceso para su producto API Management. Por ejemplo, la directiva `rate-limit-by-key` puede usarse para evitar los picos de uso de la API según clave limitando la tasa de llamadas durante un período de tiempo establecido.

Para obtener más información, consulte [Directivas de restricción de acceso de Azure API Management](api-management-access-restriction-policies.md).

## <a name="see-also"></a>Consulte también

* [Azure Load Balancer: Solución de errores de conexiones salientes](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: Solución de errores intermitentes en la conexión de salida](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
