---
title: Acerca de las mallas de servicio
description: Obtenga información general sobre las mallas de servicio, los escenarios admitidos, los criterios de selección y los pasos siguientes que se deben explorar.
author: pgibson
ms.topic: article
ms.date: 07/29/2021
ms.author: pgibson
ms.openlocfilehash: f1821bd9af9c09da1c5fb3ae80145dba3cc38b09
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434311"
---
# <a name="about-service-meshes"></a>Acerca de las mallas de servicio

Una malla de servicio proporciona a las cargas de trabajo funcionalidades como administración del tráfico, resistencia, directiva, seguridad, identidad sólida y observabilidad. La aplicación se desacopla de estas funciones operativas y la malla del servicio las retira de la capa de la aplicación, bajándolas al nivel de infraestructura.

## <a name="scenarios"></a>Escenarios

Estos son algunos de los escenarios que se pueden habilitar para las cargas de trabajo cuando se usa una malla de servicio:

- **Cifrar todo el tráfico del clúster**: habilite TLS mutuo entre los servicios especificados del clúster. Se puede extender a la entrada y salida en el perímetro de la red. Proporciona una opción segura de forma predeterminada sin necesidad de realizar cambios en el código y en la infraestructura de la aplicación.

- **Lanzamientos controlados y por fases**: especifique las condiciones para que un subconjunto de tráfico se enrute a un conjunto de nuevos servicios del clúster. Si la prueba del lanzamiento controlado es correcta, quite el enrutamiento y la fase condicionales, aumentando gradualmente el porcentaje de tráfico al nuevo servicio. Al final, todo el tráfico se dirigirá al nuevo servicio.

- **Administración y manipulación del tráfico**: cree una directiva en un servicio que limite la frecuencia de todo el tráfico a una versión de un servicio en un origen específico. O bien una directiva que aplique una estrategia de reintento a clases de errores entre servicios especificados. Refleje el tráfico real en nuevas versiones de servicios durante una migración o para depurar problemas. Inserte errores entre los servicios en un entorno de prueba para probar la resistencia.

- **Observabilidad**: obtenga información sobre cómo se conectan los servicios al tráfico que fluye entre ellos. Obtenga métricas, registros y seguimientos para todo el tráfico del clúster, así como para la entrada y la salida. Agregue funcionalidades de seguimiento distribuido a las aplicaciones.

## <a name="selection-criteria"></a>Criterios de selección

Antes de seleccionar una malla de servicio, asegúrese de que comprende los requisitos y las razones para instalarla. Plantéese las siguientes preguntas.

- **¿Es un controlador de entrada suficiente para mis necesidades?** A veces, contar con una funcionalidad como las pruebas A/B o la división del tráfico en la entrada es suficiente para el escenario requerido. No agregue complejidad a su entorno si no supone una ventaja.

- **¿Las cargas de trabajo y el entorno actuales pueden tolerar las sobrecargas adicionales?** Todos los componentes adicionales necesarios para admitir la malla de servicio requieren recursos adicionales, como CPU y memoria. Además, todos los proxies y sus comprobaciones de directiva asociadas agregan latencia al tráfico. Si tiene cargas de trabajo muy sensibles a la latencia o no puede proporcionar los recursos adicionales necesarios para cubrir los componentes de la malla de servicio, replantéese esta opción.

- **¿Esta opción agrega complejidad adicional innecesaria?** Si el motivo para instalar una malla de servicio es conseguir una funcionalidad que no es necesariamente crítica para los equipos empresariales u operativos, considere si merece la pena la complejidad adicional de la instalación, el mantenimiento y la configuración.

- **¿Se puede adoptar con una estrategia incremental?** Algunas de las mallas de servicio que proporcionan una gran cantidad de funciones se pueden adoptar de modo incremental. Instale solo los componentes que necesita para cubrir sus necesidades. Una vez que esté más seguro y se necesiten funcionalidades adicionales, explore dichas opciones. Resista la tentación de instalar *todo* desde el principio.

## <a name="next-steps"></a>Pasos siguientes

Como paso siguiente, explore Open Service Mesh (OSM) en Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Más información sobre OSM...][osm-about]

También puede explorar las siguientes mallas de servicio en Azure Kubernetes Service (AKS) en la documentación completa del proyecto disponible para cada una de ellas:

- [Istio][istio]
- [Linkerd][linkerd]
- [Consul Connect][consul]

Si desea obtener más información sobre el panorama de la malla de servicio, el conjunto más amplio de mallas de servicio disponibles, las herramientas y el cumplimiento, explore:

- [Infraestructura la malla de servicio de Layer 5][service-mesh-landscape]

También puede explorar los distintos esfuerzos de normalización de mallas de servicio:

- [Service Mesh Interface (SMI)][smi]
- [Federación de Service Mesh][smf]
- [Rendimiento de Service Mesh (SMP)][smp]


<!-- LINKS - external -->
[istio]: https://istio.io/latest/docs/setup/install/
[linkerd]: https://linkerd.io/getting-started/
[consul]: https://learn.hashicorp.com/tutorials/consul/service-mesh-deploy
[service-mesh-landscape]: https://layer5.io/service-mesh-landscape
[smi]: https://smi-spec.io/
[smf]: https://github.com/vmware/hamlet
[smp]: https://github.com/service-mesh-performance/service-mesh-performance

<!-- LINKS - internal -->
[osm-about]: ./open-service-mesh-about.md
