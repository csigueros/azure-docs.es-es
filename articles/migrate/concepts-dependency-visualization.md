---
title: Análisis de dependencias en Azure Migrate Detección y evaluación
description: Se describe cómo usar el análisis de dependencias para realizar evaluaciones mediante Azure Migrate Detección y evaluación.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 240f87a6b3154995340830e1861c8a76e6e3557f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259663"
---
# <a name="dependency-analysis"></a>Análisis de dependencias

En este artículo se describe el análisis de dependencias en Azure Migrate Detección y evaluación.

El análisis de dependencias identifica las dependencias entre los servidores locales detectados. Proporciona estas ventajas:

- Puede recopilar servidores en grupos para su evaluación de manera más precisa y con mayor confianza.
- Puede identificar los servidores que se deben migrar juntos. Esto resulta especialmente útil si no está seguro de qué servidores forman parte de la implementación de una aplicación que quiere migrar a Azure.
- Puede identificar si los servidores están en uso y cuáles se pueden retirar en lugar de migrarlos.
- El análisis de dependencias le ayuda a garantizar que no se olvida de nada y a evitar interrupciones inesperadas después de la migración.
- [Revise](common-questions-discovery-assessment.md#what-is-dependency-visualization) las preguntas habituales sobre el análisis de dependencias.

## <a name="analysis-types"></a>Tipos de análisis

Hay dos opciones para implementar el análisis de dependencias:

**Opción** | **Detalles** | **Nube pública** | **Azure Government**
----  |---- | ----
**Sin agente** | Sondea los datos de los servidores de VMware mediante las API de vSphere.<br/><br/> No es necesario instalar agentes en los servidores.<br/><br/> Esta opción se encuentra actualmente en versión preliminar y solo para servidores en VMware. | Compatible. | Compatible.
**Análisis basado en agente** | Usa la [solución Service Map](../azure-monitor/vm/service-map.md) de Azure Monitor para habilitar la visualización y el análisis de dependencias.<br/><br/> Tiene que instalar agentes en cada servidor local que desee analizar. | Compatible | No compatible.

## <a name="agentless-analysis"></a>Análisis sin agente

El análisis de dependencias sin agente funciona mediante la captura de los datos de conexión TCP de los servidores en los que se ha habilitado. No hay ningún agente instalado en los servidores. Las conexiones con el mismo servidor y proceso de origen, y con el servidor, proceso y puerto de destino se agrupan lógicamente en una dependencia. Puede visualizar los datos de dependencia capturados en una vista de mapa o exportarlos como CSV. No hay ningún agente instalado en los servidores que quiera analizar.

### <a name="dependency-data"></a>Datos de dependencia

Una vez que se inicia la detección de datos de dependencia, comienza el sondeo:

- El dispositivo de Azure Migrate sondea los datos de conexión TCP de los servidores cada cinco minutos para recopilar datos.
- Los datos se recopilan de los servidores invitados a través de vCenter Server, con las API de vSphere.
- El sondeo recopila estos datos:

    - Nombre de los procesos que tienen conexiones activas.
    - Nombre de la aplicación que ejecuta los procesos que tienen conexiones activas.
    - Puerto de destino en las conexiones activas.

- Los datos recopilados se procesan en el dispositivo de Azure Migrate para deducir la información de identidad, y se envían a Azure Migrate cada seis horas.


## <a name="agent-based-analysis"></a>Análisis basado en agente

En el análisis basado en agente, Azure Migrate: Detección y evaluación emplea la [solución Service Map](../azure-monitor/vm/service-map.md) de Azure Monitor. Puede instalar [Microsoft Monitoring Agent o el agente de Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent) y el [agente de dependencias](../azure-monitor/agents/agents-overview.md#dependency-agent) en cada servidor que quiera analizar.

### <a name="dependency-data"></a>Datos de dependencia

El análisis basado en agente proporciona estos datos:

- Nombre de aplicación, proceso y nombre del servidor de origen.
- Puerto, nombre de aplicación, proceso y nombre del servidor de destino.
- Se recopila la información sobre el número de conexiones, la latencia y la transferencia de datos, y está disponible para las consultas de Log Analytics.

## <a name="compare-agentless-and-agent-based"></a>Comparación basada en agente y sin agente

La diferencia entre la visualización sin agente y la visualización basada en agente se resume en la siguiente tabla.

**Requisito** | **Sin agente** | **Basado en agente**
--- | --- | ---
**Soporte técnico** | Disponible solo para servidores en VMware. [Revise](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) los sistemas operativos compatibles. | En disponibilidad general (GA).
**Agent** | No se requiere ningún agente en los servidores que desea analizar. | Se requieren agentes en cada servidor local que quiera analizar.
**Log Analytics** | No se requiere. | Azure Migrate usa la solución [Service Map](../azure-monitor/vm/service-map.md) de los [registros de Azure Monitor](../azure-monitor/logs/log-query-overview.md) para el análisis de dependencias.<br/><br/> Puede asociar un área de trabajo de Log Analytics con un proyecto. El área de trabajo debe residir en las regiones Este de EE. UU., Sudeste Asiático u Oeste de Europa. El área de trabajo debe estar en una región en la que [se admita Service Map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Proceso** | Captura datos de conexión TCP. Después de la detección, recopila datos en intervalos de cinco minutos. | Los agentes de Service Map instalados en un servidor recopilan datos acerca de los procesos de TCP, así como de las conexiones de entrada o salida de cada proceso.
**Data** | Nombre de aplicación, proceso y nombre del servidor de origen.<br/><br/> Puerto, nombre de aplicación, proceso y nombre del servidor de destino. | Nombre de aplicación, proceso y nombre del servidor de origen.<br/><br/> Puerto, nombre de aplicación, proceso y nombre del servidor de destino.<br/><br/> Se recopila la información sobre el número de conexiones, la latencia y la transferencia de datos, y está disponible para las consultas de Log Analytics. 
**Visualización** | El mapa de dependencias de un solo servidor se puede ver durante un plazo de entre una hora y 30 días. | Mapa de dependencia de un solo servidor.<br/><br/> Mapa de dependencias de un grupo de servidores.<br/><br/>  El mapa puede verse solo durante una hora.<br/><br/> Agregue y quite servidores de un grupo desde la vista de mapa.
Exportación de datos | Los datos de los últimos 30 días se pueden descargar en formato CSV. | Los datos se pueden consultar con Log Analytics.



## <a name="next-steps"></a>Pasos siguientes

- [Configure](how-to-create-group-machine-dependencies.md) la visualización de dependencias basada en agente.
- [Pruebe](how-to-create-group-machine-dependencies-agentless.md) la visualización de dependencias sin agente para servidores en VMware.
- Revise las [preguntas habituales](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre la visualización de dependencias.
