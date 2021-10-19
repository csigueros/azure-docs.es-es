---
title: Otras protecciones contra amenazas de Azure Security Center
description: Más información sobre la protección contra amenazas disponible en Azure Security Center además de Azure Defender
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.openlocfilehash: c2bed032510e278663a1d1d9f10043eaa6e9b0db
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858203"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Protecciones adicionales contra amenazas en Azure Security Center
Además de las [protecciones de Azure Defender](azure-defender.md) integradas, Azure Security Center también ofrece las siguientes funcionalidades de protección contra amenazas.

> [!TIP]
> Para habilitar las funcionalidades de protección contra amenazas de Security Center, debe habilitar Azure Defender en la suscripción que contenga las cargas de trabajo aplicables.

## <a name="threat-protection-for-azure-network-layer"></a>Protección contra amenazas para la capa de red de Azure <a name="network-layer"></a>
El análisis de la capa de red de Security Center se basa en [datos IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) de ejemplo, que son encabezados de paquete recopilados por los enrutadores principales de Azure. En función de esta fuente de distribución de datos, Security Center utiliza modelos de Machine Learning para identificar y marcar actividades de tráfico malintencionado. Security Center también utiliza la base de datos de Microsoft Threat Intelligence para enriquecer las direcciones IP.

Algunas configuraciones de red pueden impedir a Security Center generar alertas sobre una actividad de red sospechosa. Para que Security Center genere alertas de red, asegúrese de que:
- La máquina virtual tenga una dirección IP pública (o se encuentre en un equilibrador de carga con una dirección IP pública).
- El tráfico de salida de red de la máquina virtual no esté bloqueado por una solución de IDS externa.

Para obtener una lista de las alertas de nivel de red de Azure, consulte la [tabla de referencia de alertas](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Protección contra amenazas para Azure Cosmos DB (versión preliminar)<a name="cosmos-db"></a>

Las alertas de Azure Cosmos DB las generan los intentos inusuales y potencialmente dañinos de acceso o aprovechamiento de cuentas de Azure Cosmos DB.

Para más información, consulte:

* [Advanced Threat Protection para Azure Cosmos DB (versión preliminar)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [La lista de alertas de protección contra amenazas para Azure Cosmos DB (versión preliminar)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-azure-security-center-recommendations-in-microsoft-cloud-app-security"></a>Visualización de las recomendaciones de Azure Security Center en Microsoft Cloud App Security <a name="azure-mcas"></a>

Microsoft Cloud App Security es un agente de seguridad de acceso a la nube (CASB) que admite distintos modos de implementación, incluyendo la recopilación de registros, los conectores de API y el proxy inverso. Proporciona visibilidad enriquecida, control sobre el viaje de los datos y análisis sofisticados para identificar y combatir las ciberamenazas en todos los servicios en la nube de terceros y Microsoft.

Si ha habilitado Microsoft Cloud App Security y ha seleccionado la integración desde dentro de la configuración de Security Center, las recomendaciones de protección de Security Center aparecerán en Cloud App Security sin necesidad de ninguna configuración adicional.

> [!NOTE]
> Security Center almacena datos de clientes relacionados con la seguridad en la misma zona geográfica que su recurso. Si Microsoft aún no ha implementado Security Center en la zona geográfica del recurso, almacenará los datos en Estados Unidos. Cuando Microsoft Cloud App Security esté habilitado, esta información se almacenará con arreglo a las reglas de ubicación geográfica de Microsoft Cloud App Security. Para obtener más información, consulte [Almacenamiento de datos para servicios no regionales](https://azuredatacentermap.azurewebsites.net/).


## <a name="stream-security-alerts-from-other-microsoft-services"></a>Transmisión de alertas de seguridad de otros servicios de Microsoft <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-security-center"></a>Visualización de alertas de Azure WAF en Security Center <a name="azure-waf"></a>

Firewall de aplicaciones web (WAF) es una característica de Azure Application Gateway que proporciona a las aplicaciones web una protección centralizada contra vulnerabilidades de seguridad comunes.

Las aplicaciones web son cada vez más el objetivo de ataques malintencionados que aprovechan vulnerabilidades habitualmente conocidas. El firewall de aplicaciones web de Application Gateway se basa en el conjunto de reglas básicas 3.0 o 2.2.9 de Open Web Application Security Project. El firewall de aplicaciones web se actualiza automáticamente para incluir protección frente a nuevas vulnerabilidades. 

Si tiene una licencia para el firewall de aplicaciones web de Azure, las alertas de este se transmitirán a Security Center sin necesidad de configuración adicional. Para más información sobre las alertas generadas por WAF, consulte [Reglas y grupos de reglas de CRS del firewall de aplicaciones web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="display-azure-ddos-protection-alerts-in-security-center"></a>Visualización de alertas de Azure DDoS Protection en Security Center <a name="azure-ddos"></a>

Los ataques de denegación de servicio distribuido (DDoS) son conocidos por lo fáciles que son de ejecutar. Se han convertido en un problema de seguridad muy importante, especialmente si va a trasladar sus aplicaciones a la nube. Un ataque DDoS intenta agotar los recursos de una aplicación haciendo que esta no esté disponible para los usuarios legítimos. Los ataques DDoS pueden dirigirse a cualquier punto de conexión accesible a través de Internet.

Para defenderse contra ataques DDoS, adquiera una licencia de Azure DDoS Protection y asegúrese de que está siguiendo los procedimientos recomendados de diseño de aplicaciones. DDoS Protection proporciona distintos niveles de servicio. Para más información, consulte [Introducción a Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md).

Si tiene Azure DDoS Protection habilitado, las alertas de DDoS se transmiten a Security Center sin necesidad de ninguna configuración adicional. Para más información sobre las alertas generadas por DDoS Protection, consulte [Tabla de referencia de alertas](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de las alertas de seguridad de estas características de protección contra amenazas, consulte los siguientes artículos:

* [Tabla de referencia para todas las alertas de Azure Security Center](alerts-reference.md)
* [Alertas de seguridad en el Centro de seguridad de Azure](security-center-alerts-overview.md)
* [Administración y respuesta a alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exportación continua de datos de Security Center](continuous-export.md)
