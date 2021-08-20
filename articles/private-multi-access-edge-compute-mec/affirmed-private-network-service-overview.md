---
title: ¿Qué es el servicio de red privada afirmada en Azure?
description: Obtenga información sobre las soluciones de Affirmed Private Network Service en Azure para redes LTE y 5G.
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: ca2327ee8e9245698dc8d9b57b2c5bf2bf8e6881
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458531"
---
# <a name="what-is-affirmed-private-network-service-on-azure"></a>¿Qué es el servicio de red privada afirmada en Azure?

Affirmed Private Network Service (APNS) es una oferta de servicio de red administrada creada para proveedores de servicios administrados y operadores de red móvil para proporcionar soluciones privadas LTE y 5G a empresas.

Affirmed Private Network Service ha combinado su tecnología principal móvil con las funcionalidades de Azure para crear una solución completa inmediata para redes LTE y 5G para ayudar a los operadores y empresas a aprovechar las ventajas de las redes administradas y el perímetro móvil. La combinación de administración y automatización en la nube permite a los proveedores de servicios administrados ofrecer una infraestructura totalmente administrada y también ofrece una solución completa de un extremo a otro para que los operadores elijan lo mejor de Radio Access Network, SIM y los servicios de Azure de un rico ecosistema de asociados que se ofrece en Azure Marketplace. La solución se compone de cinco componentes:

- **Cloud-native Mobile Core**: este componente es compatible con los estándares 3GPP y admite funciones de red para 4G y 5G, y tiene sondeos de red virtual ubicados de forma nativa dentro del núcleo móvil. El núcleo móvil se puede implementar en máquinas virtuales, servidores físicos o en una nube de un operador, lo que elimina la necesidad de hardware dedicado.

- **Private Network Service Manager - Affirmed Networks**: Private Network Service Manager es la aplicación que los operadores usan para implementar, supervisar y administrar redes de núcleos móviles privados en la plataforma Azure. Ofrece un conjunto completo de funcionalidades de administración que incluyen la activación automática simple y la administración de recursos de red privada a través de un portal controlado por GUI mediante programación.

- **Azure Network Functions Manager**: Azure Network Functions Manager es un servicio de orquestación nativo de nube totalmente administrado que permite a los clientes implementar y aprovisionar funciones de red en Azure Stack Edge Pro con GPU para una experiencia híbrida coherente mediante Azure Portal.

- **Azure Cloud**: una plataforma informática en la nube pública con soluciones como Infraestructura como servicio (IaaS), Plataforma como servicio (PaaS) y Software como servicio (SaaS) que se puede usar para servicios como análisis, informática virtual, almacenamiento, redes, etc.

- **Azure Stack Edge**: una solución de hardware como servicio administrada en la nube que distribuye Microsoft. Aporta la potencia de la nube de Azure a un servidor local, potente, que se puede implementar prácticamente en cualquier lugar en el que sea necesario realizar tareas de inteligencia artificial local y de informática avanzada.


:::image type="content" source="./media/affirmed-overview/affirmed-private-network-service-solution.png" alt-text="Solución Affirmed Private Network Service":::

## <a name="why-use-the-affirmed-private-network-solution"></a>¿Por qué usar Affirmed Private Network Service (APNS)?
APNS proporciona las siguientes ventajas clave para los operadores y sus clientes:

- **Flexibilidad de implementación**: APNS emplea tecnología de separación de los planos de usuario y de control, y admite tres tipos de modos de implementación que permiten abordar diversos escenarios operativos para ofrecer a las empresas. Mediante Private Network Service Manager, los operadores pueden configurar los siguientes modelos de implementación:

    - El modelo independiente permite a los operadores proporcionar una red privada independiente completa en el entorno local mediante la entrega del núcleo RAN, 5G en Azure Stack Edge y la capa de administración en la nube centralizada.

    - El modelo distribuido permite un procesamiento más rápido de los datos mediante la distribución del plano de usuario más cerca del perímetro de la empresa en Azure Stack Edge mientras que el plano de control está en la nube. Un ejemplo de este tipo de modelo serían las instalaciones de fabricación.

    - El modelo todo en la nube permite implementar todo el núcleo de 5G en la nube mientras RAN está en el perímetro, lo que permite la asignación dinámica de recursos en la nube para satisfacer las cambiantes demandas de las cargas de trabajo.

- **Integración con MNO**: APNS es un operador de red móvil integrado, lo que significa que proporciona una movilidad completa entre redes de operadores públicos y privados con su núcleo de suscriptor distribuido. Los operadores tienen la ventaja de poder escalar la red móvil privada a 1000 sitios perimetrales empresariales.

    - Admite todas las opciones del espectro: MNO con licencia, con licencia privada, CBRS, compartido, sin licencia.

    - Admite redes privadas aisladas o independientes, itinerancia multisitio e itinerancia de macros, ya que está integrado en MNO.

    - Puede proporcionar una disponibilidad del servicio del 99,999 % e interactuar con cualquier radio de NR para LTE y 5G compatible con 3GPP. Ofrece resistencia a nivel de operador para las empresas.

- **Automatización y facilidad de administración**: la solución APNS se puede administrar completamente de forma remota a través de Service Manager en la nube de Azure. A través de Service Manager, los usuarios finales tienen acceso a su panel personalizado y pueden administrar, ver y activar o desactivar dispositivos en la red móvil privada. Los operadores pueden supervisar el estado de las redes en busca de problemas de red y parámetros clave para garantizar un rendimiento óptimo.

    - Proporciona un servicio de red móvil privado de baja latencia, confiable y seguro que se ejecuta en un proceso perimetral multiacceso privado de Azure.

    - Admite la administración remota completa, sin necesidad de grandes desplazamientos.

    - Proporciona automatización en la nube para permitir a los operadores ofrecer servicios administrados a empresas o para asociarse con otros proveedores que, a su vez, pueden ofrecer servicios administrados.

- **Red inteligente e información empresarial**: el núcleo móvil de Affirmed tiene una función de sondeo virtual o de agente de paquetes insertada que se puede usar para proporcionar información de red. El operador puede usar esta información para adoptar mejores decisiones en relación con la red, mientras que sus clientes pueden usar esta información para tomar decisiones de monetización más inteligentes.

- **Privacidad y seguridad de los datos**: APNS usa Azure para ofrecer seguridad y cumplimiento en las redes privadas y aplicaciones empresariales. Los operadores pueden implementar con confianza la solución para los casos de uso de sectores que requieren estrictas leyes de privacidad de datos, como la asistencia sanitaria, administración pública, seguridad pública y defensa.

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a [implementar la solución Affirmed Private Network Service](deploy-affirmed-private-network-service-solution.md).



