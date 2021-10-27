---
title: 'Procedimientos recomendados de Azure Monitor: alertas y acciones automatizadas'
description: Recomendaciones para la implementación de alertas y acciones automatizadas de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 933c05bc48fedf9502d63acd5f5dc3fa1f4c133f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181575"
---
# <a name="deploying-azure-monitor---alerts-and-automated-actions"></a>Implementación de Azure Monitor: alertas y acciones automatizadas
Este artículo forma parte del escenario de [recomendaciones para configurar Azure Monitor](best-practices.md). Proporciona instrucciones sobre las alertas de Azure Monitor, que notifican al usuario proactivamente sobre datos o patrones importantes identificados en los datos de supervisión. Puede ver las alertas en Azure Portal, hacer que envíen una notificación proactiva o que inicien alguna acción automatizada para intentar corregir el problema. 
## <a name="alerting-strategy"></a>Estrategia de alertas
Una estrategia de alertas define los estándares de las organizaciones sobre los tipos de reglas de alertas que se van a crear para distintos escenarios, cómo se van a clasificar y a administrar las alertas después de crearlas, así como las notificaciones y las acciones automatizadas que se obtendrán como respuesta a las alertas. La definición de una estrategia de alertas le ayuda a definir la configuración de las reglas de alertas, incluidos los grupos de acciones y la gravedad de las alertas.

Consulte [Estrategia de alertas correcta](/azure/cloud-adoption-framework/manage/monitor/alerting#successful-alerting-strategy) para ver los factores que debe tener en cuenta al desarrollar una estrategia de alertas.


## <a name="alert-rule-types"></a>Tipos de reglas de alerta
Las alertas de Azure Monitor se crean mediante reglas de alertas que debe crear. Consulte la documentación sobre supervisión de cada servicio de Azure para obtener instrucciones sobre las reglas de alertas recomendadas. Azure Monitor no tiene ninguna regla de alertas de forma predeterminada. 

Hay varios tipos de reglas de alerta definidos por el tipo de datos que usan. Cada una tiene capacidades y costos diferentes. La estrategia básica que debe seguir es usar el tipo de regla de alerta con el costo más bajo que proporciona la lógica que necesita.

- [Reglas del registro de actividad](alerts/activity-log-alerts.md). Crea una alerta en respuesta a un nuevo evento de registro de actividad que coincide con las condiciones especificadas. Estas alertas no tienen ningún costo, por lo que deberían elegirse como primera opción, aunque las condiciones que pueden detectar son limitadas. Consulte [Crear, ver y administrar las alertas del registro de actividad mediante Azure Monitor](alerts/alerts-activity-log.md) para más detalles sobre la creación de alertas de registro de actividad.
- [Reglas de alertas de métricas](alerts/alerts-metric-overview.md). Crea una alerta en respuesta a uno o varios valores de métrica que superan un umbral. Las alertas de métricas tienen estado, lo que significa que la alerta se cerrará automáticamente cuando el valor caiga por debajo del umbral y solo enviará notificaciones cuando cambie el estado. Hay un costo para las alertas de métricas, pero suele ser significativamente menor que para las alertas de registro. Consulte [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](alerts/alerts-metric.md) para más detalles sobre la creación de alertas de métricas.
- [Reglas de alertas de registro](alerts/alerts-unified-log.md). Crea una alerta cuando los resultados de una consulta de programación coinciden con los criterios especificados. Son las reglas de alertas más caras, pero permiten los criterios más complejos. Consulte [Creación, visualización y administración de alertas de registro mediante Azure Monitor](alerts/alerts-log.md) para más detalles sobre la creación de alertas de consulta de registros.
- Las [alertas de aplicación](app/monitor-web-app-availability.md) permiten realizar pruebas proactivas de rendimiento y disponibilidad de la aplicación web. Puede realizar una prueba de ping simple sin costo alguno, pero hay un costo para las pruebas más complejas. Consulte [Supervisión de la disponibilidad de un sitio web](app/monitor-web-app-availability.md) para obtener una descripción de las diferentes pruebas y detalles sobre cómo crearlas.

## <a name="alert-severity"></a>Gravedad de la alerta
Cada regla de alertas define la gravedad de las alertas que crea en función de la tabla siguiente. Las alertas de Azure Portal se agrupan por nivel para que pueda administrar las alertas similares de forma conjunta e identificar rápidamente aquellas que requieran la máxima urgencia.

| Nivel | Nombre | Descripción |
|:---|:---|:---|
| Gravedad 0 | Crítico  | Pérdida de disponibilidad del servicio o de la aplicación o una degradación grave del rendimiento. Requiere atención inmediata. |
| Gravedad 1 | Error  | Degradación del rendimiento o pérdida de disponibilidad de algún aspecto de una aplicación o servicio. Requiere atención, pero no inmediata. |
| Gravedad 2 | Advertencia | Un problema que no incluye ninguna pérdida actual de la disponibilidad o el rendimiento, pero que puede generar problemas más graves si no se corrige. |
| Gravedad 3 | Informativo | No indica ningún problema; muestra información interesante a un operador, como la finalización correcta de un proceso normal. |
| Gravedad 4 | Verbose | Información detallada no útil 

Debe evaluar la gravedad de la condición que identifica cada regla para asignar un nivel adecuado. Los tipos de problemas que asigne a cada nivel de gravedad y la respuesta estándar a cada uno de ellos deben definirse en la estrategia de alertas. 

## <a name="action-groups"></a>Grupos de acciones
Las respuestas automatizadas a las alertas en Azure Monitor se definen en los [grupos de acciones](alerts/action-groups.md). Un grupo de acciones es la recopilación de una o varias notificaciones y acciones que se activan cuando se desencadena una alerta. Un único grupo de acciones se puede usar con varias reglas de alertas y contener una o varias de las opciones siguientes:

- Notificaciones. Mensajes que notifican a los operadores y a los administradores que se ha creado una alerta.
- Acciones. Procesos automatizados que intentan corregir el problema detectado. 
## <a name="notifications"></a>Notificaciones
Las notificaciones son mensajes que se envían a uno o varios usuarios para informarles de que se ha creado una alerta. Dado que un único grupo de acciones se puede usar con varias reglas de alertas, debe diseñar un conjunto de grupos de acciones para distintos conjuntos de administradores y usuarios que recibirán los mismos conjuntos de alertas. Use cualquiera de los tipos de notificaciones siguientes en función de las preferencias de los operadores y de los estándares de la organización.

- Correo electrónico
- SMS
- Inserción en una aplicación de Azure
- Voz
- Rol de Azure Resource Manager de correo electrónico

## <a name="actions"></a>Acciones
Las acciones son respuestas automatizadas ante una alerta. Puede usar las acciones disponibles para cualquier escenario compatible, pero en las secciones siguientes se describe cómo se usan normalmente.

### <a name="automated-remediation"></a>Corrección automatizada
Use las acciones siguientes para intentar realizar una corrección automatizada del problema identificado por la alerta. 

- Runbook de Automation: inicie un runbook integrado o personalice uno en Azure Automation. Por ejemplo, los runbooks integrados están disponibles para realizar funciones como reiniciar o escalar verticalmente una máquina virtual.
- Función de Azure: inicie una función de Azure.


### <a name="itsm-and-on-call-management"></a>ITSM y administración en las llamadas

- ITSM: use el [conector ITSM]() para crear elementos de trabajo en la herramienta ITSM en función de las alertas de Azure Monitor. En primer lugar, configure el conector y, a continuación, use la acción **ITSM** en las reglas de alertas.
- Webhooks: envíe la alerta a un sistema de administración de incidentes que admita webhooks, como PagerDuty y Splunk On-Call.
- Webhook seguro: integración de ITSM con Autenticación de Azure AD


## <a name="minimizing-alert-activity"></a>Minimizar la actividad de alerta
Si bien debería crear alertas para cualquier información importante de su entorno, debe asegurarse de no crear demasiadas alertas y notificaciones para los problemas que no las justifiquen. Use las directrices siguientes para minimizar la actividad de alerta, a fin de garantizar que se resalten los problemas críticos sin generar una cantidad excesiva de información y notificaciones para los administradores. 

- Consulte [Estrategia de alertas correcta](/azure/cloud-adoption-framework/manage/monitor/alerting#successful-alerting-strategy) para ver los principios que determinan si un síntoma es un candidato adecuado para una alerta.
- Use la opción **Resolver alertas automáticamente** en las reglas de alertas de métricas para resolver alertas cuando la condición se haya corregido.
- Use la opción **Suprimir alertas** en las reglas de alertas de consulta de registro, que impide la creación de varias alertas para el mismo problema.
- Asegúrese de usar los niveles de gravedad adecuados para las reglas de alertas de forma que los problemas de prioridad alta puedan analizarse conjuntamente.
- Limite las notificaciones para las alertas cuya gravedad sea de tipo Advertencia o más baja, ya que no requieren una atención inmediata.

## <a name="create-alert-rules-at-scale"></a>Creación de reglas de alertas a escala
Lo habitual es querer que se alerte sobre los problemas de todas las aplicaciones y recursos críticos de Azure, por lo que debe aprovechar los métodos de creación de reglas de alertas a escala.

- Azure Monitor permite supervisar varios recursos del mismo tipo con una sola regla de alertas de métricas para los recursos que se encuentren en la misma región de Azure. Consulte [Supervisión a escala mediante alertas de métricas en Azure Monitor](alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) para obtener una lista de los servicios de Azure que se admiten actualmente para esta característica.
- Respecto a las reglas de alertas de métricas para servicios de Azure que no admitan varios recursos, aproveche las herramientas de automatización, como la CLI y PowerShell, con plantillas de Resource Manager, a fin de crear la misma regla de alertas para varios recursos. Consulte [Ejemplos de plantillas de Azure Resource Manager para reglas de alertas de métrica en Azure Monitor](alerts/resource-manager-alerts-metric.md) para ver ejemplos.
- Escriba consultas en reglas de alertas de consulta de registro para devolver datos de varios recursos. Use la configuración **Split by dimensions** (Dividir por dimensiones) en la regla para crear alertas independientes de cada recurso.


> [!NOTE]
> Las reglas de alertas de consulta de registro basadas en recursos que se encuentren actualmente en versión preliminar pública permiten usar todos los recursos de una suscripción o de un grupo de recursos como destino de una alerta de consulta de registro. 

## <a name="next-steps"></a>Pasos siguientes

- [Definición de alertas y acciones automatizadas a partir de los datos de Azure Monitor](best-practices-alerts.md)