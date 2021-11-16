---
title: Protección contra amenazas adicional de Microsoft Defender para nube
description: Obtenga información sobre las protecciones contra amenazas disponibles en Microsoft Defender para nube
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: c9c3b8da52e1d8725c6ddee92377d300b9c9cb3a
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373444"
---
# <a name="additional-threat-protections-in-microsoft-defender-for-cloud"></a>Protección contra amenazas adicional de Microsoft Defender para nube

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Además de los [planes de protección avanzada](defender-for-cloud-introduction.md) integrados, Microsoft Defender para la nube también ofrece las siguientes funcionalidades de protección contra amenazas.

> [!TIP]
> Para habilitar las funcionalidades de protección contra amenazas de Defender para nube, debe habilitar características de seguridad mejoradas en la suscripción que contenga las cargas de trabajo aplicables.

## <a name="threat-protection-for-azure-network-layer"></a>Protección contra amenazas para la capa de red de Azure <a name="network-layer"></a>
El análisis de la capa de red de Defender para nube se basa en [datos IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) de ejemplo, que son encabezados de paquete recopilados por los enrutadores principales de Azure. En función de esta fuente de distribución de datos, Defender para nube utiliza modelos de Machine Learning para identificar y marcar actividades de tráfico malintencionado. Defender para nube también utiliza la base de datos de Microsoft Threat Intelligence para enriquecer las direcciones IP.

Algunas configuraciones de red pueden impedir a Defender para nube generar alertas sobre una actividad de red sospechosa. Para que Defender para nube genere alertas de red, asegúrese de que:

- La máquina virtual tenga una dirección IP pública (o se encuentre en un equilibrador de carga con una dirección IP pública).
- El tráfico de salida de red de la máquina virtual no esté bloqueado por una solución de IDS externa.

Para obtener una lista de las alertas de nivel de red de Azure, consulte la [tabla de referencia de alertas](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Protección contra amenazas para Azure Cosmos DB (versión preliminar)<a name="cosmos-db"></a>

Las alertas de Azure Cosmos DB las generan los intentos inusuales y potencialmente dañinos de acceso o aprovechamiento de cuentas de Azure Cosmos DB.

Para más información, consulte:

- [Advanced Threat Protection para Azure Cosmos DB (versión preliminar)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
- [La lista de alertas de protección contra amenazas para Azure Cosmos DB (versión preliminar)](alerts-reference.md#alerts-azurecosmos)


## <a name="display-recommendations-in-microsoft-defender-for-cloud-apps"></a>Visualización de las recomendaciones de seguridad en aplicaciones de Microsoft Defender para la nube <a name="azure-mcas"></a>

Microsoft Defender para aplicaciones en la nube (antes conocido como Microsoft Cloud App Security) es un agente de seguridad de acceso a la nube (CASB) que admite distintos modos de implementación, incluyendo la recopilación de registros, los conectores de API y el proxy inverso. Proporciona visibilidad enriquecida, control sobre el viaje de los datos y análisis sofisticados para identificar y combatir las ciberamenazas en todos los servicios en la nube de terceros y Microsoft.

Si ha habilitado Microsoft Defender para aplicaciones en la nube, y ha seleccionado la integración desde la configuración de Defender para la nube, las recomendaciones de protección de Defender para la nube aparecerán en Defender para aplicaciones en la nube sin necesidad de ninguna configuración adicional.

> [!NOTE]
> Defender para nube almacena datos de clientes relacionados con la seguridad en la misma ubicación geográfica que su recurso. Si Microsoft aún no ha implementado Defender para nube en la ubicación geográfica del recurso, almacenará los datos en Estados Unidos. Cuando Microsoft Defender para aplicaciones en la nube esté habilitado, esta información se almacenará de acuerdo con a las reglas de ubicación geográfica de Microsoft Defender para aplicaciones en la nube. Para obtener más información, consulte [Almacenamiento de datos para servicios no regionales](https://azuredatacentermap.azurewebsites.net/).


## <a name="stream-security-alerts-from-other-microsoft-services"></a>Transmisión de alertas de seguridad de otros servicios de Microsoft <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-defender-for-cloud"></a>Visualización de alertas de WAF de Azure en Defender para la nube <a name="azure-waf"></a>

Firewall de aplicaciones web (WAF) es una característica de Azure Application Gateway que proporciona a las aplicaciones web una protección centralizada contra vulnerabilidades de seguridad comunes.

Las aplicaciones web son cada vez más el objetivo de ataques malintencionados que aprovechan vulnerabilidades habitualmente conocidas. El firewall de aplicaciones web de Application Gateway se basa en el conjunto de reglas básicas 3.0 o 2.2.9 de Open Web Application Security Project. El firewall de aplicaciones web se actualiza automáticamente para incluir protección frente a nuevas vulnerabilidades. 

Si tiene una licencia para el firewall de aplicaciones web de Azure, las alertas de este se transmitirán a Defender para nube sin necesidad de configuración adicional. Para más información sobre las alertas generadas por WAF, consulte [Reglas y grupos de reglas de CRS del firewall de aplicaciones web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="display-azure-ddos-protection-alerts-in-defender-for-cloud"></a>Visualización de alertas de Azure DDoS Protection en Defender para nube <a name="azure-ddos"></a>

Los ataques de denegación de servicio distribuido (DDoS) son conocidos por lo fáciles que son de ejecutar. Se han convertido en un problema de seguridad muy importante, especialmente si va a trasladar sus aplicaciones a la nube. Un ataque DDoS intenta agotar los recursos de una aplicación haciendo que esta no esté disponible para los usuarios legítimos. Los ataques DDoS pueden dirigirse a cualquier punto de conexión accesible a través de Internet.

Para defenderse contra ataques DDoS, adquiera una licencia de Azure DDoS Protection y asegúrese de que está siguiendo los procedimientos recomendados de diseño de aplicaciones. DDoS Protection proporciona distintos niveles de servicio. Para más información, consulte [Introducción a Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md).

Si tiene Azure DDoS Protection habilitado, las alertas de DDoS se transmiten a Defender para nube sin necesidad de ninguna configuración adicional. Para más información sobre las alertas generadas por DDoS Protection, consulte [Tabla de referencia de alertas](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de las alertas de seguridad de estas características de protección contra amenazas, consulte los siguientes artículos:

- [Tabla de referencia de alertas de Defender para nube](alerts-reference.md)
- [Alertas de seguridad en Defender para nube](alerts-overview.md)
- [Administración de alertas de seguridad y respuesta a ellas en Defender para nube](managing-and-responding-alerts.md)
- [Exportación continua de datos de Defender para nube](continuous-export.md)
