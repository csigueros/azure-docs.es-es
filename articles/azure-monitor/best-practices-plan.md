---
title: 'Procedimientos recomendados de Azure Monitor: planeamiento'
description: Instrucciones y recomendaciones para planear y diseñar antes de implementar Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b04b73cfd7cd2482e14485556df5a477e47a9334
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347981"
---
# <a name="azure-monitor-best-practices---planning-your-monitoring-strategy-and-configuration"></a>Procedimientos recomendados de Azure Monitor: planeamiento de la estrategia y la configuración de supervisión
Este artículo forma parte del escenario [Recomendaciones para configurar Azure Monitor](best-practices.md). En él se describe el planeamiento que se debe tener en cuenta antes de iniciar la implementación. Esto garantiza que las opciones de configuración que se seleccionen cumplan requisitos empresariales concretos.

Si aún no está familiarizado con los conceptos de supervisión, comience con la [Guía sobre la supervisión en la nube](/azure/cloud-adoption-framework/manage/monitor), que forma parte de [Microsoft Cloud Adoption Framework para Azure](/cloud-adoption-framework/). En esa guía se definen conceptos generales de supervisión y se proporcionan instrucciones para definir los requisitos del entorno de supervisión y los procesos de apoyo. En este artículo se hace referencia a las secciones de esa guía que son relevantes para pasos de planeamiento concretos.
## <a name="understand-azure-monitor-costs"></a>Descripción de los costos de Azure Monitor
Un objetivo básico de la estrategia de supervisión es minimizar los costos. La recopilación de datos y algunas características de Azure Monitor no conllevan ningún costo, mientras que otras tienen costos en función de su configuración determinada, la cantidad de datos recopilados o la frecuencia con que se ejecutan. En los artículos de este escenario se identifican las recomendaciones que conllevan un costo, aunque es recomendable estar familiarizado con los precios de Azure Monitor cuando se diseña la implementación a fin de optimizar los costos. Vea lo siguiente para obtener detalles e instrucciones sobre los precios de Azure Monitor:

- [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)
- [Supervisión del uso y costos estimados en Azure Monitor](usage-estimated-costs.md)
- [Administrar el uso y los costos con los registros de Azure Monitor](logs/manage-cost-storage.md)
- [Administración del uso y los costos de Application Insights](app/pricing.md)

## <a name="define-strategy"></a>Definición de la estrategia
Antes de diseñar e implementar cualquier solución de supervisión, debe establecer una estrategia de supervisión para comprender los objetivos y los requisitos del plan. La estrategia define los requisitos concretos, la configuración que mejor se adapta a esos requisitos y los procesos para aprovechar el entorno de supervisión con el fin de maximizar el rendimiento y la confiabilidad de las aplicaciones. Las opciones de configuración que se seleccionen para Azure Monitor deben ser coherentes con la estrategia.

Vea [Guía sobre la supervisión en la nube: Formulación de una estrategia de supervisión](/azure/cloud-adoption-framework/strategy/monitoring-strategy) para conocer una serie de factores que se deben tener en cuenta a la hora de desarrollar una estrategia de supervisión. También debería ver [Estrategia de supervisión para los modelos de implementación en la nube](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview), que le va a ayudar a comparar la supervisión completamente basada en la nube con un modelo híbrido. 

## <a name="gather-required-information"></a>Recopilación de la información necesaria
Antes de determinar los detalles de la implementación, debe recopilar la información necesaria para definir esos detalles. En las secciones siguientes se describe la información que normalmente es necesaria para una implementación completa de Azure Monitor.

 ### <a name="what-needs-to-be-monitored"></a>Qué se debe supervisar
 No va a configurar necesariamente la supervisión completa de todos los recursos en la nube, sino que se va a centrar en las aplicaciones críticas y los componentes de los que dependen. Esto no solo reduce los costos de supervisión, sino que también disminuye la complejidad del entorno de supervisión. Vea [Guía sobre la supervisión en la nube: recopilación de los datos correctos](/azure/cloud-adoption-framework/manage/monitor/data-collection) para obtener instrucciones para definir los datos que se necesitan.

### <a name="who-needs-to-have-access-and-be-notified"></a>Quién necesita acceso y debe recibir notificaciones
Mientras configura el entorno de supervisión debe determinar qué usuarios deben tener acceso a los datos de supervisión y qué usuarios deben recibir notificaciones cuando se detecta un problema. Pueden ser propietarios de aplicaciones y recursos, o puede haber un equipo de supervisión centralizado. Esta información va a determinar cómo se configuran los permisos para el acceso a datos y las notificaciones de alertas. También puede necesitar libros personalizados para presentar determinados conjuntos de información a distintos usuarios.

### <a name="service-level-agreements"></a>Contratos de nivel de servicio 
La organización puede tener contratos de nivel de servicio que definan los compromisos de rendimiento y tiempo de actividad de las aplicaciones. Estos contratos de nivel de servicio pueden determinar cómo hay que configurar características sensibles al tiempo de Azure Monitor, como las alertas. También debe comprender la [latencia de los datos en Azure Monitor](logs/data-ingestion-time.md), ya que afecta a la capacidad de respuesta de los escenarios de supervisión y a la capacidad de cumplir los contratos de nivel de servicio.

## <a name="identify-monitoring-services-and-products"></a>Identificación de servicios y productos de supervisión
Azure Monitor está diseñado para abordar la supervisión de estado. Una solución de supervisión completa normalmente comprende varios servicios de Azure y posiblemente otros productos. En la Guía sobre la supervisión en la nube, en los [objetivos principales de supervisión](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements), se describen otros objetivos de supervisión que pueden requerir soluciones adicionales. 

En las secciones siguientes se describen otros servicios y productos que se pueden usar junto con Azure Monitor. Actualmente, este escenario no incluye instrucciones sobre la implementación de estas soluciones, por lo que debe consultar su documentación correspondiente.

### <a name="security-monitoring"></a>Supervisión de la seguridad
Aunque los datos operativos almacenados en Azure Monitor pueden ser útiles para investigar incidentes de seguridad, se han diseñado otros servicios de Azure para supervisar la seguridad. Microsoft Defender for Cloud y Microsoft Sentinel realizan la supervisión de seguridad en Azure.

- [Microsoft Defender for Cloud](../security-center/security-center-introduction.md) recopila información sobre los recursos de Azure y los servidores híbridos. Aunque puede recopilar eventos de seguridad, Microsoft Defender for Cloud se centra en la recopilación de datos de inventario, resultados de exámenes de evaluación y auditorías de directivas para resaltar las vulnerabilidades y recomendar acciones correctivas. Entre las características destacadas se incluyen un mapa de red interactivo, el acceso a máquinas virtuales Just-In-Time, la seguridad de red adaptable y controles de aplicación adaptables para bloquear archivos ejecutables sospechosos.

- [Microsoft Defender para servidores](../security-center/azure-defender.md) es la solución de evaluación de servidores proporcionada por Microsoft Defender for Cloud. Microsoft Defender para servidores puede enviar eventos de seguridad de Windows a Log Analytics. Microsoft Defender for Cloud no se basa en eventos de seguridad de Windows para las alertas ni el análisis. El uso de esta característica permite el archivado centralizado de eventos con fines de investigación u otros fines.

- [Microsoft Sentinel](../sentinel/overview.md) es una solución de administración de eventos e información de seguridad (SIEM) de y respuesta automatizada de orquestación de seguridad (SOAR). Sentinel recopila datos de seguridad de una amplia gama de orígenes de Microsoft y de terceros para proporcionar alertas, visualización y automatización. Esta solución se centra en consolidar tantos registros de seguridad como sea posible, incluidos los eventos de seguridad de Windows. Microsoft Sentinel también puede recopilar registros de eventos de seguridad de Windows y normalmente comparte un área de trabajo de Log Analytics con Microsoft Defender for Cloud. Los eventos de seguridad solo se pueden recopilar desde Microsoft Sentinel o Microsoft Defender for Cloud cuando comparten la misma área de trabajo. A diferencia de Microsoft Defender for Cloud, los eventos de seguridad son un componente clave de las alertas y el análisis en Microsoft Sentinel.

- [Defender para punto de conexión](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) es una plataforma de seguridad empresarial para puntos de conexión diseñada para evitar, detectar, investigar y responder a amenazas avanzadas. Se ha diseñado con un enfoque principal en la protección de dispositivos de usuario para Windows. Defender para punto de conexión supervisa estaciones de trabajo, servidores, tabletas y teléfonos móviles con varios sistemas operativos en busca de problemas de seguridad y vulnerabilidades. Defender para punto de conexión está estrechamente alineado con Microsoft Endpoint Manager para recopilar datos y proporcionar evaluaciones de seguridad. La recopilación de datos se basa principalmente en los registros de seguimiento ETW y se almacena en un área de trabajo aislada.


### <a name="system-center-operations-manager"></a>System Center Operations Manager
Es posible que tenga una inversión existente en System Center Operations Manager para supervisar las cargas de trabajo y los recursos locales que se ejecutan en las máquinas virtuales. Puede optar por [migrar esta supervisión a Azure Monitor](azure-monitor-operations-manager.md) o seguir usando ambos productos juntos en una configuración híbrida. Vea [Guía sobre la supervisión en la nube: Introducción a las plataformas de supervisión](/azure/cloud-adoption-framework/manage/monitor/platform-overview) para obtener una comparación de los dos productos. Vea [Estrategia de supervisión para los modelos de implementación en la nube](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) para obtener instrucciones sobre cómo usar los dos en una configuración híbrida y sobre cómo determinar el modelo más adecuado para el entorno.



## <a name="next-steps"></a>Pasos siguientes

- Vea [Configuración de la recopilación de datos](best-practices-data-collection.md) para obtener pasos y recomendaciones para configurar la recopilación de datos en Azure Monitor.
