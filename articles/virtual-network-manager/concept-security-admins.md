---
title: Reglas de administración de seguridad en Azure Virtual Network Manager (versión preliminar)
description: Obtenga información sobre qué reglas de administración de seguridad hay en Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: d533ae6860850830640f052f4737b61c199d952e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851785"
---
# <a name="security-admin-rules-in-azure-virtual-network-manager-preview"></a>Reglas de administración de seguridad en Azure Virtual Network Manager (versión preliminar)

Azure Virtual Network Manager proporciona dos tipos diferentes de configuraciones que puede implementar en las redes virtuales, una de ellas es una *configuración SecurityAdmin*. Una configuración de administrador de seguridad contiene un conjunto de colecciones de reglas. Cada colección de reglas contiene una o varias reglas de administración de seguridad. A continuación, asocie la colección de reglas con los grupos de red a los que desea aplicar las reglas de administración de seguridad.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="security-admin-rules"></a>Reglas de administrador de seguridad

Una regla de administración de seguridad permite aplicar criterios de directiva de seguridad que coincidan con las condiciones establecidas. Solo puede definir reglas administrativas de seguridad para los recursos dentro del ámbito de la instancia de Azure Virtual Network Manager. Estas reglas de seguridad tienen una prioridad más alta que las reglas del grupo de seguridad de red (NSG) y se evaluarán antes que las reglas de NSG. Tenga en cuenta también que las reglas de administración de seguridad no cambian las reglas de NSG. Consulte la ilustración siguiente.

:::image type="content" source="./media/concept-security-admins/traffic-evaluation.png" alt-text="Diagrama en el que se muestra cómo se evalúa el tráfico con reglas de administración de seguridad y NSG":::.

Las reglas de administración de seguridad se pueden usar para aplicar reglas de seguridad. Por ejemplo, un administrador puede denegar todos los puertos o protocolos de alto riesgo de Internet con reglas de administrador de seguridad, ya que estas reglas se evaluarán antes que todas las reglas de NSG.

> [!IMPORTANT]
> Algunos servicios tienen directivas de intención de red para asegurarse de que el tráfico de red funciona según sea necesario para sus servicios. Al usar reglas de administración de seguridad, podría interrumpir las directivas de intención de red creadas para esos servicios. Por ejemplo, la creación de una regla de administración de denegación puede bloquear parte del tráfico permitido por el servicio *instancia administrada de SQL*, que se define mediante su directiva de intención de red. Asegúrese de revisar el entorno antes de aplicar una configuración de administrador de seguridad. Para más información, vea [¿Cómo puedo permitir explícitamente el tráfico SQLMI antes de tener reglas de denegación?](faq.md#how-can-i-explicitly-allow-sqlmi-traffic-before-having-deny-rules)

Estos son los campos que puede definir en una regla de administración de seguridad:

## <a name="required-fields"></a>Campos obligatorios

### <a name="priority"></a>Priority

La prioridad de la regla de seguridad viene determinada por un entero comprendido entre 0 y 99. Cuanto menor es el valor, mayor es la prioridad de la regla. Por ejemplo, una regla de denegación con una prioridad de 10 invalida una regla de permitir con una prioridad de 20. 

### <a name="action"></a><a name = "action"></a>Acción

Puede definir una de estas tres acciones para una regla de seguridad:

* **Permitir**: permite el tráfico en el puerto, el protocolo y los prefijos IP de origen o destino específicos en la dirección especificada.
* **Denegar**: bloquea el tráfico en el puerto, el protocolo y los prefijos IP de origen o destino específicos en la dirección especificada.
* **Permitir siempre**: independientemente de otras reglas con prioridad inferior o grupos de seguridad de red definidos por el usuario, permita el tráfico en el puerto, el protocolo y los prefijos IP de origen o destino especificados en la dirección especificada.

### <a name="direction"></a>Dirección

Puede especificar la dirección del tráfico a la que se aplica la regla. Puede definir entrantes o salientes.

### <a name="protocol"></a>Protocolo

Los protocolos admitidos actualmente con las reglas de administración de seguridad son:

* TCP
* UDP
* ICMP
* ESP
* AH
* Cualquier protocolo

## <a name="optional-fields"></a>Campos opcionales

### <a name="source-and-destination-types"></a>Tipos de origen y destino

* **Direcciones IP**: puede proporcionar direcciones IPv4 o IPv6 o bloques de direcciones en notación CIDR. Para enumerar varias direcciones IP, separe cada dirección IP con una coma.
* **Etiqueta de servicio**: puede definir etiquetas de servicio específicas basadas en regiones o en un servicio completo. Consulte [Etiquetas de servicio disponibles](../virtual-network/service-tags-overview.md#available-service-tags) para obtener la lista de etiquetas admitidas.

### <a name="source-and-destination-ports"></a>Puertos de origen y destino

Puede definir puertos comunes específicos para bloquear desde el origen o hacia el destino. Consulte a continuación una lista de puertos TCP comunes:

| Puertos | Nombre del servicio |
| ----- | ------------ |
| 20, 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |

## <a name="next-steps"></a>Pasos siguientes 

Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-portal.md).
