---
title: Registro, auditoría y visibilidad de puertas de enlace en Azure Australia
description: Cómo configurar el registro, la auditoría y la visibilidad en las regiones de Australia para cumplir con los requisitos específicos de las directivas, los reglamentos y la legislación de la administración pública de Australia.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: dae5f20231a6ccf348299a0f04fb6f2ccfda36ff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183473"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Registro, auditoría y visibilidad de puertas de enlace en Azure Australia

La detección y respuesta a las amenazas de ciberseguridad se basa en la generación, recopilación y análisis de datos relacionados con el funcionamiento de un sistema.

Microsoft tiene herramientas integradas en Azure que le ayudarán a implementar el registro, la auditoría y la visibilidad para administrar la seguridad de los sistemas implementados en Azure. También existe una arquitectura de referencia en línea con la guía para el consumidor del Australian Cyber Security Centre (ACSC) y la intención del manual de seguridad de la información (ISM) de ACSC.

Las puertas de enlace actúan como mecanismos de control de flujo de información en el nivel de red y también pueden controlar la información en los niveles superiores del modelo de interconexión de sistema abierto (OSI). Las puertas de enlace son necesarias para controlar los flujos de datos entre dominios de seguridad y evitar el acceso no autorizado desde redes externas. Dada la importancia de las puertas de enlace en el control del flujo de información entre dominios de seguridad, cualquier error, especialmente en las clasificaciones más altas, puede tener consecuencias graves. De este modo, es especialmente importante para las puertas de enlace contar con mecanismos sólidos para alertar al personal en situaciones que puedan provocar incidentes de ciberseguridad.

La implementación de funcionalidades de registro y alertas para puertas de enlace puede ayudar a detectar incidentes de ciberseguridad, intentos de intrusiones y patrones de uso inusuales. Además, el almacenamiento de registros de eventos en un servidor de registro seguro independiente dificulta aún más a un adversario la posibilidad de eliminar información de registro con el fin de destruir evidencias de una intrusión cibernética dirigida.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Requisitos del Australian Cyber Security Centre (ACSC)

Los requisitos de seguridad generales para los sistemas de la Commonwealth se definen en el Manual de seguridad de la información (ISM) del ACSC. Para ayudar a las entidades de la Commonwealth a cumplir estos requisitos en Azure, las publicaciones *ACSC CONSUMER GUIDE – Microsoft Azure at PROTECTED* (GUÍA DEL CONSUMIDOR DE ACSC: Microsoft Azure en PROTEGIDO) y *ACSC CERTIFICATION REPORT – Microsoft Azure* (INFORME DE CERTIFICACIÓN DE ACSC: Microsoft Azure) detallan los siguientes requisitos específicos relacionados con el registro, la auditoría y la visibilidad:

1. Para mitigar los riesgos derivados del uso de recursos de nube subyacentes compartidos, las entidades de la Commonwealth deben elegir funcionalidades proporcionadas de Microsoft Azure, como Azure Security Centre, Azure Monitor, Azure Policy y Azure Advisor para ayudar a las entidades supervisar en tiempo real sus cargas de trabajo de Azure.

2. El ACSC también recomienda que las entidades de la Commonwealth reenvíen todos los registros de seguridad obligatorios al ACSC para la supervisión integral de la administración pública de Australia.

3. Para ayudar en la mitigación de riesgos, las entidades de la Commonwealth deben configurar sus suscripciones a Azure:

    * Habilitación de Azure Security Center
    * Actualización al nivel Estándar
    * Habilitación del aprovisionamiento automático de Microsoft Monitoring Agent para admitir Azure Virtual Machines
    * Revisión, priorización y mitigación periódicas de las recomendaciones y alertas de seguridad en el panel del centro de seguridad

4. Las entidades gubernamentales deben habilitar el registro y el reenvío de eventos desde su suscripción a Azure al ACSC para proporcionar al ACSC visibilidad del incumplimiento de esta guía. Azure Event Hubs proporciona la funcionalidad de transmitir registros externos al ASCS o a sistemas locales que pertenecen a entidades de la Commonwealth.

5. Las entidades de la Commonwealth deben adaptar el registro que habilitan en Azure a los requisitos especificados en el ISM.

6. Microsoft conserva los registros en Azure durante 90 días. Las entidades de clientes deben implementar un régimen de archivo de registro para asegurarse de que los registros se puedan conservar durante los siete años exigidos por la AFDA de NAA.

7. Las entidades de la Commonwealth que tienen funcionalidades de administración de eventos e información de seguridad (SIEM) locales o basadas en Azure también pueden reenviar registros a esos sistemas.

8. Las entidades de la Commonwealth deben implementar registros de flujo de Network Watcher para grupos de seguridad de red y máquinas virtuales. Estos registros se deben almacenar en una cuenta de almacenamiento dedicada que solo contenga registros de seguridad; el acceso a la cuenta de almacenamiento debe estar protegido con el control de acceso basado en rol (RBAC de Azure).

9. Las entidades de la Commonwealth deben implementar lo establecido en la guía del consumidor de ACSC para garantizar que las cargas de trabajo de Azure cumplan la intención del ISM para el registro y la supervisión. Las entidades de la Commonwealth también deben elegir funcionalidades de Azure que permitan al ACSC recibir información de supervisión, alertas y registros en tiempo real asociados con el uso que hace de Azure la administración pública de Australia.

## <a name="architecture"></a>Architecture

Para conocer fehacientemente el tráfico de red que entra y sale de su entorno de Azure, debe habilitarse el registro necesario en el conjunto de componentes correcto. Esto garantiza una visibilidad completa del entorno y proporciona los datos necesarios para realizar análisis.

![Arquitectura de supervisión de Azure](media/visibility.png)

## <a name="components"></a>Componentes

La arquitectura mostrada anteriormente se conforma de componentes discretos que proporcionan la función de orígenes de registros, recopilación de registros, retención de registros, análisis de registros o respuesta a incidentes. Esta arquitectura incluye componentes individuales que suelen intervenir en implementaciones de Azure accesibles desde Internet.

|Functions|Componentes|
|---|---|
|Orígenes de registros|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Azure Firewall</li><li>Aplicaciones virtuales de red</li><li>Azure Load Balancer</li><li>Virtual Machines</li><li>Servidores del sistema de nombres de dominio (DNS)</li><li>Servidores de recopilación de registros o syslog</li><li>Grupos de seguridad de red</li><li>Azure Activity Log</li><li>Registro de diagnóstico de Azure</li><li>Azure Policy</li></ul>|
|Recopilación de registros|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Retención de registros|<ul><li>Azure Storage</li></ul>|
| Análisis de registro|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Soluciones de Log Analytics<ul><li>Análisis de tráfico</li><li>DNS Analytics (versión preliminar)</li><li>Análisis de registros de actividad</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|Respuesta a los incidentes|<ul><li>Alertas de Azure</li><li>Azure Automation</li></ul>|
|

La arquitectura funciona generando primero los registros de los orígenes necesarios y, a continuación, recopilándolos en repositorios centralizados. Una vez recopilados los registros:

* los servicios de análisis de Azure pueden usarlos para obtener información,
* pueden reenviarse a sistemas externos,
* pueden archivarse en el almacenamiento para su retención a largo plazo.

Para responder a eventos o incidentes clave identificados por herramientas de análisis, pueden configurarse alertas y desarrollarse mecanismos de automatización para tomar las medidas necesarias de administración y respuesta proactivas.

## <a name="general-guidance"></a>Instrucciones generales

Al implementar los componentes que se enumeran en este artículo, se aplican las siguientes directrices generales:

* Valide la disponibilidad de la región de los servicios, asegurándose de que todos los datos permanezcan en las ubicaciones autorizadas e implemente en el Centro de Australia o el Centro de Australia 2 como primera preferencia para las cargas de trabajo PROTEGIDAS.

* Consulte la publicación *Azure - ACSC Certification Report – Protected 2018* (Azure: informe de certificación de ACSC de protección de 2018) para conocer el estado de certificación de servicios individuales y realizar sus propias valoraciones sobre cualquiera de los componentes pertinentes que no se incluyan en el informe de conformidad con *ACSC CONSUMER GUIDE – Microsoft Azure at PROTECTED* (GUÍA DEL CONSUMIDOR DE ACSC: Microsoft Azure en PROTEGIDO)

* En el caso de los componentes a los que no se hace referencia en este artículo, las entidades de la Commonwealth deben seguir los principios incluidos sobre la generación, la recopilación, el análisis y la retención de registros.

* Identifique y priorice el registro, la auditoría y la visibilidad en sistemas de gran valor, así como todos los puntos de entrada y salida de red a sistemas hospedados en Azure.

* Consolide los registros y minimice el número de instancias de herramientas de registro, como cuentas de almacenamiento, áreas de trabajo de Log Analytics e instancias de Event Hubs.

* Restrinja los privilegios administrativos mediante el control de acceso basado en rol de Azure (RBAC de Azure).

* Use Multi-Factor Authentication (MFA) para las cuentas que administran o configuran recursos en Azure.

* Al centralizar la recopilación de registros en varias suscripciones, asegúrese de que los administradores tienen los privilegios necesarios en cada suscripción.

* Asegúrese de que cuenta con la conectividad de red y la configuración de proxy necesaria para que las máquinas virtuales (incluidas las aplicaciones virtuales de red (NVA), los servidores de recopilación de registros y los servidores DNS) se conecten a los servicios de Azure necesarios, como las áreas de trabajo de Log Analytics, Event Hubs y Storage.

* Configure Microsoft Monitoring Agent (MMA) para que utilice TLS 1.2.

* Use Azure Policy para supervisar y aplicar el cumplimiento de los requisitos.

* Aplique el cifrado en todos los repositorios de datos, como Almacenamiento y bases de datos.

* Use el almacenamiento con redundancia local (LRS) e instantáneas para la disponibilidad de las cuentas de almacenamiento y los datos asociados.

* Considere la posibilidad de usar almacenamiento con redundancia geográfica (GRS) o almacenamiento externo para adaptarse a estrategias de recuperación ante desastres.

|Resource|URL|
|---|---|
|Documentos de cumplimiento normativo y de las directivas de Australia|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Productos de Azure: por regiones de Australia e independientes de la región|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,centro de australia,centro de australia 2,este de australia,sudeste de australia](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Administración de registros de auditoría y seguridad de Microsoft Azure (notas del producto)|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Configuración de Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](../azure-monitor/agents/log-analytics-agent.md)|
|

## <a name="component-guidance"></a>Guía de componentes

En esta sección se proporciona información sobre el propósito de cada componente y su rol en la arquitectura general de registro, auditoría y visibilidad. Se proporcionan vínculos adicionales de acceso a recursos útiles, como documentación de referencia, guías y tutoriales.

## <a name="log-sources"></a>Orígenes de registros

Antes de que se pueda completar cualquier análisis, alerta o informe, se deben generar los registros necesarios. Los registros de Azure se clasifican en registros de control y administración, registros de plano de datos y eventos procesados.

|Tipo|Descripción|
|---|---|
|Registros de control y administración|Proporcionan información sobre operaciones de Azure Resource Manager.|
|Registros del plano de datos|Proporcionan información sobre eventos que se producen como parte del uso de recursos de Azure, como registros de una máquina virtual y registros de diagnóstico disponibles mediante Azure Monitor.|
|Eventos procesados|Proporcionan información sobre eventos o alertas analizados que ha procesado Azure, como, por ejemplo, si Azure Security Center ha procesado y analizado suscripciones para proporcionar alertas de seguridad.|
|

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway es uno de los posibles puntos de entrada en un entorno de Azure, por lo que debe capturar información relacionada con las conexiones entrantes que se comunican con aplicaciones web. Application Gateway puede proporcionar información fundamental relacionada con el uso de aplicaciones web, así como ayudar a detectar incidentes de ciberseguridad. Application Gateway envía metadatos al registro de actividad y los registros de diagnóstico en Azure Monitor, donde se pueden usar en Log Analytics o distribuir a una instancia de Event Hubs o una cuenta de almacenamiento.

|Recursos|Vínculo|
|---|---|
|Documentación de Application Gateway|[https://docs.microsoft.com/azure/application-gateway/](../application-gateway/index.yml)|
|Guía de inicio rápido de Application Gateway|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](../application-gateway/quick-create-portal.md)|
|

### <a name="vpn-gateway"></a>VPN Gateway

VPN Gateway es un punto de entrada potencial para una amplia gama de comunicaciones en el entorno de Azure, como la conexión a un entorno local y el tráfico administrativo. El registro en instancias de VPN Gateway proporciona información y rastreabilidad para las conexiones realizadas en el entorno de Azure. El registro puede proporcionar auditoría y análisis, así como ayuda para la detección o investigación de conexiones malintencionadas o anómalas. Los registros de VPN Gateway se envían al registro de actividad de Azure Monitor, donde se pueden usar en Log Analytics o distribuir a una instancia de Event Hubs o cuenta de almacenamiento.

|Recursos|Vínculo|
|---|---|
|Documentación de VPN Gateway|[https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|Instrucciones de VPN Gateway específicas de la administración pública de Australia|[Configuración de Azure VPN Gateway](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall proporciona un punto de salida controlado desde un entorno de Azure y los registros generados, que incluyen información sobre conexiones salientes intentadas o correctas, son un elemento importante de la estrategia de registro. Estos registros pueden validar que los sistemas funcionan según lo previsto, así como ayudar a detectar código malintencionado o actores que intentan conectarse a sistemas externos no autorizados. Azure Firewall escribe registros en el registro de actividad y registros de diagnóstico en Azure Monitor, donde se pueden usar en Log Analytics o distribuir a una instancia de Event Hubs o una cuenta de almacenamiento.

|Recursos|Vínculo|
|---|---|
|Documentación sobre Azure Firewall|[https://docs.microsoft.com/azure/firewall/](../firewall/index.yml)|
|Tutorial: Métricas y registros de Azure Firewall|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](../firewall/firewall-diagnostics.md)|
|

### <a name="network-virtual-appliances-nva"></a>Aplicaciones virtuales de red (NVA)

Las NVA pueden usarse para complementar las funcionalidades de seguridad disponibles de forma nativa en Azure. Los registros generados en NVA pueden ser recursos valiosos para detectar incidentes de ciberseguridad y son una parte fundamental de una estrategia general de registro, auditoría y visibilidad. Para capturar registros de NVA, debe utilizar Microsoft Monitoring Agent (MMA). En el caso de las NVA que no admitan la instalación de MMA, considere la posibilidad de usar Syslog u otro servidor de recopilación de registros para retransmitir los registros.

|Recursos|Vínculo|
|---|---|
|Información general de aplicaciones virtuales de red|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Documentación de NVA|Consulte la documentación del proveedor sobre la implementación de las NVA pertinentes en Azure.|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Los registros de Azure Load Balancer se usan para obtener información útil sobre las conexiones y el uso relacionados con los sistemas implementados en Azure. Se puede usar para la supervisión de estado y disponibilidad, pero también conforma otro componente clave para obtener la información necesaria sobre el tráfico de comunicaciones y detectar patrones de tráfico malintencionados o anómalos. Azure Load Balancer escribe registros en el registro de actividad y registros de diagnóstico en Azure Monitor, donde se pueden usar en Log Analytics o distribuir a una instancia de Event Hubs o una cuenta de almacenamiento.

|Recursos|Vínculo|
|---|---|
|Documentación de Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](../load-balancer/index.yml)|
|Métricas y diagnóstico de mantenimiento de Load Balancer Estándar|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](../load-balancer/load-balancer-standard-diagnostics.md)|
|

### <a name="virtual-machines"></a>Virtual Machines

Las instancias de Virtual Machines son puntos de conexión que envían y reciben comunicaciones de red, procesan datos y proporcionan servicios. Como las instancias de Virtual Machines pueden hospedar datos o servicios fundamentales del sistema, puede resultar esencial garantizar que funcionan correctamente y detectan incidentes de ciberseguridad. Las instancias de Virtual Machines recopilan varios registros de eventos y auditoría que pueden realizar un seguimiento del funcionamiento del sistema y las acciones realizadas en ese sistema. Los registros recopilados en Virtual Machines se pueden reenviar a un área de trabajo de Log Analytics mediante Microsoft Monitoring Agent, donde se pueden analizar mediante Azure Security Center y las soluciones de Log Analytics aplicables. Virtual Machines también puede integrarse directamente con Azure Event Hubs o con SIEM, ya sea directamente o mediante un servidor de recopilación de registros.

|Recursos|Vínculo|
|---|---|
|Virtual Machines|[https://docs.microsoft.com/azure/virtual-machines](../virtual-machines/index.yml)|
|Recopilación de datos de Virtual Machines|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](../azure-monitor/vm/monitor-virtual-machine.md)|
|Transmisión de registros de Virtual Machines a Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)|
|

### <a name="domain-name-services-dns-servers"></a>Servidores de servicios de nombres de dominio (DNS)

Los registros de servidores DNS proporcionan información clave relacionada con los servicios a los que los sistemas están intentando acceder, ya sea interna o externamente. La captura de registros DNS puede ayudar a identificar un incidente de ciberseguridad y proporcionar información sobre el tipo de incidente y los sistemas que pueden verse afectados. Microsoft Management Agent (MMA) se puede usar en servidores DNS para reenviar los registros mediante Log Analytics para su uso en DNS Analytics (versión preliminar).

|Recursos|Vínculo|
|---|---|
|Resolución de nombres de Azure para redes virtuales|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)|
|

### <a name="syslog-and-log-collection-servers"></a>Servidores de recopilación de registros y syslog

Para recibir registros de aplicaciones virtuales de red o registros de seguridad personalizados de otros sistemas para su uso en SIEM, se pueden implementar servidores dedicados en redes virtuales de Azure. Los registros de syslog se pueden recopilar en un servidor de syslog y retransmitirse a Log Analytics para su análisis. Un servidor de recopilación de registros es un término genérico para cualquier funcionalidad de agregación y distribución de registros utilizada por sistemas de supervisión centralizados o SIEM. Se puede usar para simplificar la arquitectura y la seguridad de red, así como para filtrar y agregar registros antes de distribuirlos a la funcionalidad centralizada.

|Recursos|Vínculo|
|---|---|
|Orígenes de datos de Syslog en Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](../azure-monitor/agents/data-sources-syslog.md)|
|Servidor de recopilación de registros|Consulte la documentación del proveedor para obtener más información sobre la supervisión y la arquitectura de SIEM.|
|

### <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSG)

Los NSG controlan el tráfico entrante y saliente de las redes virtuales de Azure. Los NSG aplican reglas para los flujos de tráfico que se permiten o deniegan, lo que incluye el tráfico dentro de Azure y entre Azure y redes externas, como locales o Internet. Los NSG se aplican a subredes de una red virtual o a interfaces de red individuales. Para capturar información sobre el tráfico que entra y sale de los sistemas en Azure, los registros de NSG se pueden habilitar mediante la característica de registro de flujos de NSG de Network Watcher. Estos registros se utilizan para establecer una base para el funcionamiento estándar de un sistema y son el origen de datos de Análisis de tráfico, que proporciona información detallada sobre los patrones de tráfico de los sistemas hospedados en Azure.

|Recursos|Vínculo|
|---|---|
|Documentación sobre grupos de seguridad de red|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|Introducción al registro de flujo de grupos de seguridad de red|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](../network-watcher/network-watcher-nsg-flow-logging-overview.md)|
|Tutorial: Registro del tráfico de red hacia y desde una máquina virtual mediante Azure Portal|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](../network-watcher/network-watcher-nsg-flow-logging-portal.md)|
|

### <a name="azure-activity-log"></a>Azure Activity Log

El registro de actividad de Azure, que forma parte de Azure Monitor, es un registro de suscripción que proporciona información sobre los eventos de nivel de suscripción que se han producido en Azure. El registro de actividad puede ayudar a dar respuesta a los interrogantes qué, quién y cuándo de las operaciones de escritura (PUT, POST, DELETE) ***en*** los recursos de una suscripción. El registro de actividad es fundamental para el seguimiento de los cambios de configuración realizados en el entorno de Azure. Los registros de actividad de Azure están disponibles automáticamente para su uso en soluciones de Log Analytics y se pueden enviar a Event Hubs o Azure Storage para su procesamiento o retención.

|Recursos|Vínculo|
|---|---|
|Documentación de registro de actividad de Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|Transmisión del registro de actividad de Azure a Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](../azure-monitor/essentials/activity-log.md#legacy-collection-methods)|
|

### <a name="azure-diagnostic-log"></a>Registro de diagnóstico de Azure

Los registros de diagnóstico de Azure Monitor son registros emitidos por un servicio de Azure que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese servicio. Los registros de diagnóstico proporcionan información sobre el funcionamiento de un recurso a un nivel detallado y se pueden usar para una variedad de requisitos, como la auditoría o la solución de problemas. Los registros de diagnóstico de Azure están disponibles automáticamente para su uso en soluciones de Log Analytics y se pueden enviar a Event Hubs o Azure Storage para su procesamiento o retención.

|Recursos|Vínculo|
|---|---|
|Documentación de registros de diagnóstico de Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|Servicios de soporte técnico para registros de diagnóstico|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](../azure-monitor/essentials/resource-logs-schema.md)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy aplica reglas sobre cómo se pueden implementar los recursos, como el tipo, la ubicación y la configuración. Azure Policy se puede configurar para garantizar que los recursos solo se puedan implementar si cumplen unos requisitos. Azure Policy es un componente básico para mantener la integridad de un entorno de Azure. Los eventos relacionados con Azure Policy se registran en el registro de actividad de Azure y están disponibles automáticamente para su uso en soluciones de Log Analytics o para enviarse a Event Hubs o Azure Storage para su procesamiento o retención.

|Recursos|Vínculo|
|---|---|
|Documentación de Azure Policy|[https://docs.microsoft.com/azure/governance/policy](../governance/policy/index.yml)|
|Uso de plantillas de Azure Policy y Resource Manager con Azure Blueprints|[https://docs.microsoft.com/azure/governance/blueprints/overview](../governance/blueprints/overview.md)|
|

## <a name="log-collection"></a>Recopilación de registros

Los registros, una vez generados a partir de varios orígenes de registro, deben almacenarse en una ubicación centralizada para facilitar el acceso y el análisis continuos. Azure proporciona varios métodos y opciones para la recopilación de registros que se pueden usar según el tipo de registro y los requisitos.

### <a name="event-hubs"></a>Event Hubs

El propósito de una instancia de Event Hubs es agregar los datos de registro de los distintos orígenes para su distribución. Desde la instancia de Event Hubs, los datos de registro se pueden enviar a SIEM, al ACSC para cumplimiento y a Storage para la retención a largo plazo.

|Recursos|Vínculo|
|---|---|
|Documentación de Event Hubs|[https://docs.microsoft.com/azure/event-hubs](../event-hubs/index.yml)|
|Instrucciones sobre Event Hubs y herramientas externas|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics forma parte de Azure Monitor y se utiliza para el análisis de registros. Log Analytics usa un área de trabajo como mecanismo de almacenamiento donde los datos de registro pueden estar disponibles para una serie de soluciones y herramientas de análisis disponibles en Azure. Log Analytics se integra con una amplia gama de componentes de Azure directamente, así como con Virtual Machines mediante Microsoft Monitoring Agent.

|Recursos|Vínculo|
|---|---|
|Documentación de Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](../azure-monitor/index.yml)|
|Tutorial: Análisis de datos en Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](../azure-monitor/logs/log-analytics-tutorial.md)|
|

### <a name="network-watcher"></a>Network Watcher

El ACSC recomienda el uso de Network Watcher para ayudar a comprender y capturar el tráfico de red en una suscripción a Azure. Los registros de flujo de NSG proporcionan la entrada a la solución de Análisis de tráfico en Log Analytics, que proporciona mayor visibilidad, análisis e informes de forma nativa mediante Azure. Network Watcher también proporciona una funcionalidad de captura de paquetes directamente desde Azure Portal sin necesidad de iniciar sesión en la máquina virtual. La captura de paquetes permite crear sesiones de captura de paquetes para realizar el seguimiento del tráfico hacia y desde una máquina virtual.

|Recursos|Vínculo|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](../network-watcher/index.yml)|
|Introducción a la captura de paquetes|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](../network-watcher/network-watcher-packet-capture-overview.md)|
|

## <a name="log-retention"></a>Retención de registros

En el caso de las organizaciones de la administración pública de Australia, los registros capturados en Azure deben conservarse de acuerdo con la [Administrative Functions Disposal Authority (AFDA)](https://www.naa.gov.au/information-management/records-authorities/types-records-authorities/afda-express-version-2-functions) (Autoridad de eliminación de funciones administrativas) del National Archives of Australia, que establece la retención de registros hasta siete años.

|Ubicación del registro|Período de retención|
|---|---|
|Azure Activity Log|Hasta 90 días|
|Área de trabajo de Log Analytics|Hasta dos años|
|Centro de eventos|Hasta siete días|
|

Es su responsabilidad asegurarse de que los registros se archiven correctamente para cumplir con la AFDA y otros requisitos legislativos.

### <a name="azure-storage"></a>Azure Storage

Azure Storage es el repositorio para registros de retención a largo plazo en Azure. Azure Storage se puede usar para archivar registros de Azure, incluidos Event Hubs, el registro de actividad de Azure y los registros de diagnóstico de Azure. El período de retención de datos en Storage se puede establecer en cero o puede especificarse como un número de días. Una retención de cero días significa que los registros se mantienen indefinidamente; de lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.

|Recursos|Vínculo|
|---|---|
|Documentación de Azure Storage|[https://docs.microsoft.com/azure/storage](../storage/index.yml)|
|Captura de eventos a través de Azure Event Hubs en Azure Blob Storage o Azure Data Lake Storage|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](../event-hubs/event-hubs-capture-overview.md)|
|Tutorial: Archivado de datos de registro y métricas de Azure con Azure Storage|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](../azure-monitor/essentials/platform-logs-overview.md)|
|Replicación de Azure Storage|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](../storage/common/storage-redundancy.md)|
|Creación de una instantánea de un blob|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Análisis de registro

Una vez generados y almacenados en una ubicación centralizada, los registros deben analizarse para ayudar a detectar incidentes de seguridad exitosos o fallidos. Cuando se detectan incidentes de seguridad, una agencia debe tener la capacidad de responder a esos incidentes y rastrear, contener y corregir cualquier amenaza.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center proporciona administración unificada de la seguridad y protección avanzada contra amenazas. Azure Security Center puede aplicar directivas de seguridad en las cargas de trabajo, limitar la exposición a amenazas y detectar y responder a los ataques. Azure Security Center proporciona paneles y análisis en una amplia gama de componentes de Azure. El uso de Azure Security Center se especifica como un requisito en la guía del consumidor de ACSC.

|Recursos|Vínculo|
|---|---|
|Documentación de Azure Security Center|[https://docs.microsoft.com/azure/security-center](../security-center/index.yml)|
|Inicio rápido: Incorporación de una suscripción de Azure a Security Center Standard|[https://docs.microsoft.com/azure/security-center/security-center-get-started](../security-center/security-center-get-started.md)|
|

### <a name="traffic-analytics"></a>Análisis de tráfico

Análisis de tráfico es una solución basada en la nube, que proporciona visibilidad de la actividad de usuarios y aplicaciones en Azure. Análisis de tráfico permite analizar los registros de flujo de NSG de Network Watcher para proporcionar información sobre el flujo de tráfico en Azure. Análisis de tráfico se usa para proporcionar paneles, informes, análisis y funcionalidades de respuesta de eventos relacionados con el tráfico de red que se observa en las redes virtuales. Análisis de tráfico proporciona una información significativa y ayuda a identificar y resolver incidentes de ciberseguridad.

|Recursos|Vínculo|
|---|---|
|Documentación de Análisis de tráfico|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](../network-watcher/traffic-analytics.md)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analiza la configuración de los recursos y otros datos y, a continuación, recomienda soluciones que ayudan a mejorar el rendimiento, la seguridad y la alta disponibilidad de los recursos, al mismo tiempo que busca oportunidades para reducir el gasto general en Azure. El ACSC recomienda Azure Advisor, que proporciona consejos detallados y fácilmente accesibles sobre configuración del entorno de Azure.

|Recursos|Vínculo|
|---|---|
|Documentación de Azure Advisor|[https://docs.microsoft.com/azure/advisor](../advisor/index.yml)|
|Introducción a Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](../advisor/advisor-get-started.md)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (versión preliminar)

DNS Analytics es una solución de Log Analytics que recopila, analiza y correlaciona los registros analíticos y de auditoría de Windows DNS y otros datos relacionados. DNS Analytics identifica clientes que intentan resolver nombres de dominio malintencionados, registros de recursos obsoletos, nombres de dominio consultados con frecuencia y clientes DNS participativos. DNS Analytics también proporciona información sobre la carga de solicitudes en los servidores DNS y los errores de registro DNS dinámicos. DNS Analytics se usa para proporcionar paneles, informes, análisis y funcionalidades de respuesta de eventos relacionados con las consultas de DNS realizadas en un entorno de Azure. DNS Analytics proporciona una información significativa y ayuda a identificar y resolver incidentes de ciberseguridad.

|Recursos|Vínculo|
|---|---|
|Documentación de DNS Analytics|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](../azure-monitor/insights/dns-analytics.md)|
|

### <a name="activity-log-analytics"></a>Análisis de registros de actividad

Activity Log Analytics es una solución de Log Analytics que ayuda a analizar y buscar en el registro de actividad de Azure en varias suscripciones a Azure. Activity Log Analytics se usa para proporcionar paneles centralizados, informes, análisis y funcionalidades de respuesta de eventos relacionados con las acciones que se realizan en los recursos de todo el entorno de Azure. Activity Log Analytics puede ayudar con la auditoría y la investigación.

|Recursos|Vínculo|
|---|---|
|Recopilar y analizar los registros de actividad de Azure en Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](../azure-monitor/essentials/activity-log.md)|
|

### <a name="security-information-and-event-management-siem"></a>Administración de eventos e información de seguridad (SIEM)

SIEM es un sistema que proporciona almacenamiento centralizado, auditoría y análisis de registros de seguridad, con mecanismos definidos para ingerir una amplia gama de datos de registro y herramientas inteligentes para el análisis, la creación de informes y la detección y respuesta de incidentes. Puede usar las funcionalidades de SIEM que incluyen información de registro de Azure para complementar las funcionalidades de seguridad proporcionadas de forma nativa en Azure. Las entidades de la Commonwealth pueden utilizar un sistema SIEM hospedado en Virtual Machines en Azure, de forma local o como una funcionalidad de software como servicio (SaaS) en función de los requisitos específicos.

|Recursos|Vínculo|
|---|---|
|Azure Sentinel (versión preliminar)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Documentación de SIEM|Consulte la documentación del proveedor para obtener instrucciones e información de la arquitectura de SIEM.|
|Uso de Azure Monitor para su integración con herramientas SIEM|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australian Cyber Security Centre

El Australian Cyber Security Centre (ACSC) es el organismo responsable en materia de ciberseguridad nacional de la administración pública de Australia. Reúne las funcionalidades de ciberseguridad de la administración pública de Australia para mejorar la resistencia cibernética de la comunidad australiana y respaldar la prosperidad económica y social de Australia en la era digital. El ACSC recomienda que las entidades de la Commonwealth reenvíen todos los archivos registro generados por el sistema, eventos y archivos requeridos al ACSC para la supervisión integral de la administración pública de Australia.

|Recursos|Vínculo|
|---|---|
|Sitio web del Australian Cyber Security Centre|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Respuesta a incidentes

La generación de los registros adecuados, su recopilación en repositorios centralizados y la realización de análisis aumenta la comprensión de los sistemas y proporciona mecanismos para detectar incidentes de ciberseguridad. Una vez detectados los incidentes o eventos, el siguiente paso consiste en reaccionar a esos eventos y realizar acciones para mantener el estado del sistema y proteger los servicios y los datos. Azure proporciona una combinación de servicios para responder de forma eficaz a cualquier evento que se produzca.

### <a name="azure-alerts"></a>Alertas de Azure

Las alertas de Azure se pueden usar para notificar al personal de soporte técnico y seguridad en respuesta a eventos concretos. Esto permite a una entidad de la Commonwealth responder de forma proactiva a la detección de eventos pertinentes generados por los servicios de análisis mencionados en este artículo.

|Recursos|Vínculo|
|---|---|
|Introducción a las alertas en Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](../azure-monitor/alerts/alerts-overview.md)|
|Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](../security-center/security-center-managing-and-responding-alerts.md)|
|Alertas de registro de Azure Monitor|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](../azure-monitor/alerts/alerts-log.md)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation permite a las entidades de la Commonwealth desencadenar acciones en respuesta a eventos, por ejemplo, para iniciar una captura de paquetes en Virtual Machines, ejecutar un flujo de trabajo, detener o iniciar Virtual Machines o servicios o para realizar diversas tareas más. La automatización permite una rápida respuesta a las alertas sin intervención manual, lo que reduce el tiempo de respuesta y la gravedad de un incidente o evento.

|Recursos|Vínculo|
|---|---|
|Documentación de Azure Automation|[https://docs.microsoft.com/azure/automation](../automation/index.yml)|
|Guía paso a paso: Uso de una alerta para desencadenar un runbook de Azure Automation|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](../automation/automation-create-alert-triggered-runbook.md)|
|

## <a name="next-steps"></a>Pasos siguientes

Consulte el artículo sobre la [administración remota segura de puertas de enlace](gateway-secure-remote-administration.md) para más información sobre cómo administrar de forma segura un entorno de puerta de enlace en Azure.