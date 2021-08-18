---
title: Base de referencia de seguridad de Azure para Virtual Network NAT
description: La base de referencia de seguridad de Virtual Network NAT proporciona una guía de procedimientos y recursos para implementar las recomendaciones de seguridad especificadas en Azure Security Benchmark.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 862c21e162ce43e6d7de6aa6485b01925edbe465
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288184"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Base de referencia de seguridad de Azure para Virtual Network NAT

Esta base de referencia de seguridad aplica la guía de la [versión 1.0 de Azure Security Benchmark](../../security/benchmarks/overview-v1.md) en Microsoft Virtual Network NAT. Azure Security Benchmark proporciona recomendaciones sobre cómo puede proteger sus soluciones de nube en Azure.
El contenido se agrupa en función de los **controles de seguridad** que define Azure Security Benchmark y las directrices aplicables a Virtual Network NAT. Se han excluido los **controles** que no son aplicables a Virtual Network NAT.

 
Para ver cómo Virtual Network NAT se asigna por completo a Azure Security Benchmark, consulte el [archivo completo de asignación de base de referencia de seguridad de Virtual Network NAT](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Seguridad de redes

*Para más información, consulte [Azure Security Benchmark: seguridad de red](../../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Supervisión y registro de la configuración y el tráfico de redes virtuales, subredes y NIC

**Guía**: Virtual Network NAT no admite el control del tráfico saliente con grupos de seguridad de red (NSG).  El tráfico de entrada solo se permite en respuesta a un flujo originado por la salida.

Sin embargo, los registros de flujo de los grupos de seguridad de red (NSG) se pueden usar con los recursos de puerta de enlace NAT para supervisar el tráfico originado por la salida.

Use Azure Security Center y siga las recomendaciones de protección de redes para ayudar a proteger sus recursos de red de Azure. Habilite los registros de flujo de los grupos de seguridad de red y envíe los registros a una cuenta de Azure Storage para su auditoría. También puede enviar los registros de flujo a un área de trabajo de Log Analytics y luego usar Análisis de tráfico para proporcionar información detallada sobre los patrones de tráfico en la nube de Azure. Algunas de las ventajas del Análisis de tráfico son la capacidad de visualizar la actividad de la red, identificar las zonas activas y las amenazas de seguridad, comprender los patrones de flujo de tráfico y detectar configuraciones de red incorrectas. 

- [Información general de Virtual Network NAT](./nat-overview.md)

- [Diseño de redes virtuales con recursos de puertas de enlace de NAT](./nat-gateway-resource.md)

- [Habilitación de los registros de flujo de grupos de seguridad de red](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Habilitación y uso del Análisis de tráfico](../../network-watcher/traffic-analytics.md)

- [Descripción de la seguridad de red proporcionada por Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="15-record-network-packets"></a>1.5: Registro de los paquetes de red

**Guía**: Habilite la captura de paquetes de Network Watcher para investigar las actividades anómalas. 

- [Creación de una instancia de Network Watcher](../../network-watcher/network-watcher-create.md)

**Supervisión de Azure Security Center**: Sí

**Responsabilidad**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Mantenga las configuraciones de seguridad estándar para dispositivos de red

**Guía**: defina e implemente configuraciones de seguridad estándar para subredes configuradas con recursos de puerta de enlace NAT usando definiciones y asignaciones de Azure Policy integradas o personalizadas.

- [Configuración y administración de Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Ejemplos de Azure Policy para redes](../../governance/policy/samples/built-in-policies.md#network)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use herramientas automatizadas para supervisar las configuraciones de recursos de red y detectar cambios

**Guía**: use el registro de actividad de Azure para supervisar las configuraciones de recursos y detectar cambios en los recursos de puerta de enlace NAT y recursos de redes virtuales. Cree alertas en Azure Monitor para recibir una notificación cuando se produzcan cambios en recursos críticos.

- [Visualización y recuperación de eventos del registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Creación de alertas en Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

## <a name="logging-and-monitoring"></a>Registro y supervisión

*Para más información, consulte [Azure Security Benchmark: registro y supervisión](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configuración de la administración central de registros de seguridad

**Guía**: realice la ingesta de registros relacionados con Virtual Network NAT a través de Azure Monitor para agregar datos de seguridad generados por dispositivos de punto de conexión, recursos de red y otros sistemas de seguridad. En Azure Monitor, use áreas de trabajo de Log Analytics para realizar consultas y análisis, y use cuentas de Azure Storage para el almacenamiento de archivos a largo plazo.

También puede habilitar e incorporar los datos en Azure Sentinel o en una herramienta SIEM de terceros.

- [Incorporación de Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Recopilación de registros y métricas de plataforma con Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Introducción a Azure Monitor e integración con herramienta SIEM de terceros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Supervisión de Azure Security Center**: Sí

**Responsabilidad**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitación del registro de auditoría para recursos de Azure

**Guía**: Los registros de actividad, que están disponibles automáticamente, contienen todas las operaciones de escritura (PUT, POST, DELETE) de los recursos de puerta de enlace NAT, excepto las operaciones de lectura (GET). Los registros de actividad se pueden usar para encontrar errores al solucionar problemas o para supervisar cómo un usuario de su organización modificó un recurso.

- [Recopilación de registros y métricas de plataforma con Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Descripción del registro y de los distintos tipos de registro de Azure](../../azure-monitor/essentials/platform-logs-overview.md) 

Actualmente Virtual Network NAT no genera ningún registro de diagnóstico adicional que los clientes puedan configurar.

**Supervisión de Azure Security Center**: Sí

**Responsabilidad**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitación de alertas para actividades anómalas

**Guía**: Use Azure Security Center con el área de trabajo de Log Analytics para la supervisión y alertas sobre actividades anómalas que se encuentran en los registros y eventos de seguridad. En su lugar, puede habilitar e incorporar datos en Azure Sentinel.

- [Incorporación de Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Administración de alertas de seguridad en Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md) 

- [Alertas sobre datos de registro de Log Analytics](../../azure-monitor/alerts/alerts-log.md)

**Supervisión de Azure Security Center**: Sí

**Responsabilidad**: Customer

## <a name="inventory-and-asset-management"></a>Administración de recursos y del inventario

*Para más información, consulte [Azure Security Benchmark: inventario y administración de recursos](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Uso de la solución de detección de recursos automatizada

**Guía**: use Azure Resource Graph para consultar o detectar todos los recursos (por ejemplo, proceso, almacenamiento, red, puertos y protocolos, etc.) dentro de las suscripciones. 

Asegúrese de que tiene los permisos (de lectura) adecuados en el inquilino y enumere todas las suscripciones de Azure, así como los recursos en las suscripciones.

Aunque los recursos de Azure clásico se pueden detectar a través de Resource Graph, se recomienda encarecidamente crear y usar los recursos basados en Azure Resource Manager en adelante.

- [Consultas de Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: Mantenimiento de metadatos de recursos

**Guía**: Aplique etiquetas a los recursos, grupos de recursos y suscripciones de Azure con el fin de organizarlos de manera lógica en una taxonomía. Cada etiqueta consta de un nombre y un par de valores. Por ejemplo, puede aplicar el nombre "Environment" y el valor "Production" a todos los recursos en producción.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminación de recursos de Azure no autorizados

**Guía**: aplique etiquetas para la administración de recursos de Azure.

- [Creación de suscripciones adicionales de Azure](../../cost-management-billing/manage/create-subscription.md) 

- [Creación de grupos de administración](../../governance/management-groups/create-management-group-portal.md) 

- [Creación y uso de etiquetas](../../azure-resource-manager/management/tag-resources.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definición y mantenimiento de un inventario de los recursos de Azure aprobados

**Guía**: no disponible actualmente

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Supervisión de recursos de Azure no aprobados

**Guía**: Use Azure Policy para establecer restricciones en el tipo de recursos que se pueden crear en sus suscripciones.
Use Azure Resource Graph para consultar o detectar recursos dentro de sus suscripciones. Asegúrese de que todos los recursos de Azure presentes en el entorno estén aprobados.

- [Configuración y administración de Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 
- [Creación de consultas con Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: Uso exclusivo de servicios de Azure aprobados

**Guía**: Use Azure Policy para restringir qué servicios puede aprovisionar en su entorno.

- [Configuración y administración de Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Denegación de un tipo de recurso específico con Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

## <a name="secure-configuration"></a>Configuración segura

*Para más información, consulte [Azure Security Benchmark: configuración segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Mantenga configuraciones de recursos de Azure seguras

**Guía**: no aplicable; Virtual Network NAT no tiene ninguna configuración de seguridad.

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Almacene de forma segura la configuración de los recursos de Azure

**Guía**: Use Azure DevOps para almacenar y administrar de forma segura el código, como definiciones de Azure Policy personalizadas, plantillas de Azure Resource Manager y scripts de Desired State Configuration. Para acceder a los recursos que administra en Azure DevOps, puede conceder o denegar permisos a usuarios específicos, grupos de seguridad integrados o grupos definidos en Azure Active Directory (Azure AD) si se integran con Azure DevOps, o Active Directory si se integran con TFS. 

- [Almacenamiento de código en Azure DevOps](/azure/devops/repos/git/gitworkflow) 

- [Acerca de los permisos y los grupos en Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementación de herramientas de administración de configuración para recursos de Azure

**Guía**: no aplicable; Virtual Network NAT no tiene ninguna configuración de seguridad.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

## <a name="incident-response"></a>Respuesta a los incidentes

*Para más información, consulte [Azure Security Benchmark: respuesta ante incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creación de una guía de respuesta ante incidentes

**Guía**: se debe crear un procedimiento de respuesta a incidentes para la oferta con el fin de garantizar que se puedan producir los procesos adecuados de respuesta a incidentes para que reciban el nivel adecuado de priorización hasta la resolución de los incidentes.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creación de un procedimiento de priorización y puntuación de incidentes

**Guía**: se debe crear un procedimiento de puntuación y priorización de incidentes para la oferta con el fin de garantizar que se pueda puntuar adecuadamente los riesgos o amenazas para que reciban el nivel adecuado de priorización hasta la resolución de los incidentes.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: Prueba de los procedimientos de respuesta de seguridad

**Guía**: se debe crear y probar un procedimiento de respuesta de seguridad a incidentes para la oferta con el fin de garantizar que se puedan producir la correcta mitigación con el nivel adecuado de priorización hasta la resolución prevista de los incidentes.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Provisión de detalles de contacto de incidentes de seguridad y configuración de notificaciones de alerta para incidentes de seguridad

**Guía**: se debe crear y probar un procedimiento de respuesta de seguridad a incidentes para la oferta con el fin de garantizar que se puedan producir la correcta mitigación con el nivel adecuado de priorización hasta la resolución prevista de los incidentes.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporación de alertas de seguridad en el sistema de respuesta a incidentes

**Guía**: se debe crear y probar un procedimiento de respuesta de seguridad a incidentes para la oferta con el fin de garantizar que se puedan producir la correcta mitigación con el nivel adecuado de priorización hasta la resolución prevista de los incidentes.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatización de la respuesta a las alertas de seguridad

**Guía**: se debe crear y probar un procedimiento de respuesta de seguridad a incidentes para la oferta con el fin de garantizar que se puedan producir la correcta mitigación con el nivel adecuado de priorización hasta la resolución prevista de los incidentes.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Pruebas de penetración y ejercicios del equipo rojo

*Para más información, consulte [Azure Security Benchmark: Pruebas de penetración y ejercicios del equipo rojo](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realice pruebas de penetración periódicas de los recursos de Azure y asegúrese de corregir todos los resultados de seguridad críticos

**Guía**: Siga las reglas de compromiso de la prueba de penetración de Microsoft Cloud para asegurarse de que las pruebas de penetración no infrinjan las directivas de Microsoft. Use la estrategia de Microsoft y la ejecución de las pruebas de penetración del equipo rojo y sitios activos en la infraestructura de nube, los servicios y las aplicaciones administradas por Microsoft. 

- [Reglas de interacción de las pruebas de penetración](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipo rojo de Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

## <a name="next-steps"></a>Pasos siguientes

- Consulte la [Información general sobre Azure Security Benchmark V2](../../security/benchmarks/overview.md).
- Obtenga más información sobre las [líneas de base de seguridad de Azure](../../security/benchmarks/security-baselines-overview.md).