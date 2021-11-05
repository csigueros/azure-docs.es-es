---
title: Gateway Load Balancer (versión preliminar)
titleSuffix: Azure Load Balancer
description: Introducción al SKU Gateway Load Balancer para Azure Load Balancer.
ms.service: load-balancer
author: asudbring
ms.author: allensu
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: ignite-fall-2021
ms.openlocfilehash: 22548d5bb75bb70c20296ddd68fde5d232213403
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093507"
---
# <a name="gateway-load-balancer-preview"></a>Gateway Load Balancer (versión preliminar)

Gateway Load Balancer es un SKU de la cartera de Azure Load Balancer diseñado específicamente para escenarios de alto rendimiento y alta disponibilidad con aplicaciones virtuales de red (NVA, por sus siglas en inglés) de terceros. Las funcionalidades de Gateway Load Balancer le permiten implementar, escalar y administrar fácilmente aplicaciones virtuales de red. El encadenamiento de Gateway Load Balancer al punto de conexión público se completa con un solo clic. 

Puede insertar dispositivos de forma transparente para diferentes tipos de escenarios, por ejemplo:

* Firewalls
* Análisis avanzado de paquetes
* Sistemas de detección y prevención de intrusiones
* Creación de reflejo del tráfico
* DDoS en línea
* Dispositivos personalizados

Con Gateway Load Balancer, puede agregar o quitar fácilmente la funcionalidad de red avanzada sin una sobrecarga de administración adicional. Proporciona la tecnología "bump-in-the-wire" que necesita para asegurarse de que todo el tráfico a un punto de conexión público se envía primero al dispositivo antes que su aplicación. En escenarios con NVA, es especialmente importante que los flujos sean simétricos. Gateway Load Balancer garantiza la permanencia del flujo en una instancia específica del grupo de back-end, así como la simetría de flujo. Como resultado, se garantiza una ruta coherente a su aplicación virtual de red, sin configuración manual adicional. De este modo, los paquetes recorren la misma ruta de acceso de red en ambas direcciones y los dispositivos que necesitan esta funcionalidad clave pueden funcionar sin problemas.

El sondeo de estado escucha todos los puertos y enruta el tráfico a las instancias de back-end mediante la regla de puertos de alta disponibilidad. El tráfico enviado a y desde Gateway Load Balancer usa el protocolo VXLAN. 

> [!IMPORTANT]
> Gateway Load Balancer se encuentra actualmente en versión preliminar.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefits"></a>Ventajas

Gateway Load Balancer tiene las siguientes ventajas:

* Integra las aplicaciones virtuales de forma transparente en la ruta de acceso de la red.

* Permite agregar o quitar fácilmente las aplicaciones virtuales de red en la ruta de acceso de la red. 

* Facilita el escalado y administra los costos.

* Mejora la disponibilidad de las aplicaciones virtuales de red.

* Encadena aplicaciones entre regiones y suscripciones.

Un equilibrador de carga público estándar o la configuración IP de una máquina virtual se pueden encadenar a Gateway Load Balancer. Una vez encadenado al front-end de un equilibrador de carga público estándar o a una configuración IP en una máquina virtual, no se necesita ninguna configuración adicional para garantizar que el tráfico hacia y desde el punto de conexión de la aplicación se envía a Gateway Load Balancer.

El tráfico se mueve de la red virtual del consumidor a la red virtual del proveedor. Después, el tráfico vuelve a la red virtual del consumidor. La red virtual de consumidor y la red virtual del proveedor pueden estar en suscripciones, inquilinos o regiones diferentes, lo que elimina la sobrecarga de administración.

:::image type="content" source="./media/gateway-overview/gateway-load-balancer-diagram.png" alt-text="Diagrama de Gateway Load Balancer":::

*Ilustración: Diagrama de Gateway Load Balancer.*

## <a name="components"></a>Componentes

Gateway Load Balancer consta de los siguientes componentes:

* **Configuración IP de front-end**: la dirección IP de su instancia de Gateway Load Balancer. Esta dirección IP es privada. 

* **Reglas de equilibrio de carga**: una regla de equilibrador de carga que se usa para definir cómo se distribuye el tráfico entrante a todas las instancias del grupo de back-end. Las reglas de equilibrio de carga asignan una configuración de IP de front-end y un puerto determinados a varios puertos y direcciones IP de back-end. 

    * Las reglas de Gateway Load Balancer solo pueden ser reglas de puertos de alta disponibilidad. 

    * Una regla de Gateway Load Balancer se puede asociar a un máximo de dos grupos de back-end. 

* **Grupos de back-end**: el grupo de máquinas virtuales o instancias de un conjunto de escalado de máquinas virtuales que atienden la solicitud entrante. Para escalar de forma rentable, con el fin de satisfacer grandes volúmenes de instrucciones para el procesamiento de tráfico entrante, generalmente se recomienda agregar más instancias al grupo de back-end. Load Balancer se reconfigura al instante de forma automática mediante el escalado o la reducción vertical de instancias. Si se agregan o quitan máquinas virtuales del grupo de back-end, el equilibrador de carga se vuelve a configurar sin operaciones adicionales. El ámbito del grupo de back-end es cualquier máquina virtual de una sola red virtual. 

* **Interfaces de túnel**: los grupos de back-end de Gateway Load Balancer tienen otro componente denominado interfaces de túnel. Con las interfaces de túnel, los dispositivos del back-end pueden garantizar que los flujos de red se procesan según lo previsto. Cada grupo de back-end puede tener hasta dos interfaces de túnel. Las interfaces de túnel pueden ser internas o externas. Para el tráfico que llega a su grupo de back-end, debe usar el tipo externo. Para el tráfico que va desde el dispositivo a la aplicación, debe usar el tipo interno.

* **Cadena**: se puede hacer referencia a Gateway Load Balancer mediante el front-end de un equilibrador de carga público estándar o una configuración IP pública en una máquina virtual. La adición de funcionalidades de red avanzadas en una secuencia específica se conoce como encadenamiento de servicios. Como resultado, esta referencia se denomina cadena.

## <a name="pricing"></a>Precios

No se cobra ningún cargo por el uso de Gateway Load Balancer durante la versión preliminar. 

Para ver los precios que se aplicarán durante la versión de disponibilidad general, consulte la página de [precios de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Limitaciones

* Gateway Load Balancer no funciona con el nivel global de Load Balancer.
* La compatibilidad de Gateway Load Balancer con el portal no está disponible actualmente en la nube de China ni en las regiones de la nube de Azure Government. Se pueden usar la CLI, PowerShell, plantillas u otras alternativas.
* El encadenamiento entre inquilinos no se admite mediante Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el artículo [Creación de una instancia de Gateway Load Balancer mediante Azure Portal](tutorial-gateway-portal.md) para crear un equilibrador de carga de puerta de enlace.
- Más información sobre [Azure Load Balancer](load-balancer-overview.md).
