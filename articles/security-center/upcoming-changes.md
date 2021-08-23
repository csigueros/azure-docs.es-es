---
title: Próximos cambios importantes en Azure Security Center
description: Próximos cambios en Azure Security Center que debe tener en cuenta y para los que puede que necesite un plan
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/25/2021
ms.author: memildin
ms.openlocfilehash: 2770c3532dd83051f9c2c7dcc770850e85aeae4b
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689572"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Próximos cambios importantes en Azure Security Center

> [!IMPORTANT]
> La información de esta página está relacionada con productos o características en versión preliminar que pueden modificarse sustancialmente antes de lanzarse comercialmente, si es que lo hacen finalmente. Microsoft no ofrece ningún compromiso o garantía, ni expresa ni implícita, con respecto a la información que se proporciona aquí.

En esta página, obtendrá información sobre los cambios planeados para Security Center. Describe las modificaciones planeadas en el producto que pueden afectar a aspectos como los flujos de trabajo o la puntuación segura.

Si busca las notas de la versión más recientes, puede encontrarlas en [Novedades de Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Cambios planeados

| Cambio planeado                                                                                                                                                                                          | Fecha estimada del cambio |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)                                            | Julio de 2021                 |
| [Entra en desuso la recomendación "Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas".](#deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines) | Julio de 2021                 |
| [Reorganización lógica de Azure Defender para alertas de Resource Manager](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                                           | Agosto de 2021               |
| [Las exportaciones de archivos .csv se limitarán a 20 MB.](#csv-exports-to-be-limited-to-20-mb)                                                                                                                               | Agosto de 2021               |
| [Habilitación del control de seguridad de Azure Defender para incluirse en la puntuación de seguridad](#enable-azure-defender-security-control-to-be-included-in-secure-score)                                                         | Tercer trimestre de 2021                   |
| [Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)                                               | T1 2022                   ||                                                                                                                                                                                                         |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013

**Fecha estimada del cambio:** julio de 2021

La implementación heredada de ISO 27001 se eliminará del panel de cumplimiento normativo de Security Center. Si está haciendo un seguimiento del cumplimiento de la norma ISO 27001 mediante Security Center, incorpore el nuevo estándar ISO 27001:2013 para los grupos de administración o suscripciones pertinentes, y la norma ISO 27001 heredada actual pronto se eliminará del panel.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Panel de cumplimiento normativo de Security Center que muestra el mensaje sobre la eliminación de la implementación heredada de ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>Entra en desuso la recomendación "Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas".

**Fecha estimada del cambio:** julio de 2021

Hemos detectado que la recomendación **Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas** afecta a las puntuaciones de seguridad de maneras incoherentes con el enfoque de administración de la posición de seguridad en la nube (CSPM) de Security Center. Normalmente, CSPM se relaciona con la identificación de errores de configuración de seguridad. Los problemas de estado del agente no encajan en esta categoría de problemas.

Además, la recomendación es una anomalía en comparación con los demás agentes relacionados con Security Center: este es el único agente con una recomendación relacionada con problemas de estado.

La recomendación entrará en desuso.

Como resultado de esta entrada en desuso, también realizaremos pequeños cambios en las recomendaciones sobre la instalación del agente de Log Analytics (**El agente de Log Analytics se debe instalar en...** ).

Es probable que este cambio afecte a las puntuaciones de seguridad. Para la mayoría de las suscripciones, esperamos que el cambio lleve a una puntuación mayor, pero es posible que las actualizaciones de la recomendación de instalación puedan dar lugar a una disminución de las puntuaciones en algunos casos.

> [!TIP]
> La página de [inventario de recursos](asset-inventory.md) también se verá afectada por este cambio, ya que también muestra información sobre si una máquina está supervisada, no supervisada o parcialmente supervisada (un estado que hace referencia a un agente con problemas de estado). 


### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Reorganización lógica de Azure Defender para alertas de Resource Manager

**Fecha estimada del cambio:** agosto de 2021

Las alertas que aparecen a continuación se proporcionan actualmente como parte del plan de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md).

Como parte de una reorganización lógica de algunos de los planes de Azure Defender, vamos a trasladar algunas alertas de **Azure Defender para Resource Manager** a **Azure Defender para servidores**.

Las alertas se organizarán según dos principios fundamentales:

- Las alertas que proporcionan protección del plano de control (en muchos tipos de recursos de Azure) formarán parte de Azure Defender para Resource Manager
- Las alertas que protegen cargas de trabajo específicas se trasladarán al plan de Azure Defender correspondiente relacionado con esa carga de trabajo.

Estas son las alertas que forman parte actualmente de Azure Defender para Resource Manager y que, como resultado de este cambio, se trasladarán a Azure Defender para servidores:

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

Obtenga más información sobre [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) y [Azure Defender para servidores](defender-for-servers-introduction.md).


### <a name="csv-exports-to-be-limited-to-20-mb"></a>Las exportaciones de archivos .csv se limitarán a 20 MB.

**Fecha estimada del cambio:** agosto de 2021

Al exportar datos de recomendaciones de Security Center, actualmente no hay ningún límite en la cantidad de datos que puede descargar.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="Botón &quot;Descargar informe CSV&quot; de Security Center para exportar datos de recomendaciones.":::

Con este cambio, se establece un límite de 20 MB.

Si tiene que exportar mayor cantidad de datos, use los filtros disponibles antes de seleccionarlos, o bien seleccione subconjuntos de las suscripciones y descargue los datos en lotes.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Filtrado de suscripciones en Azure Portal.":::

Más información sobre [cómo realizar una exportación de archivos .csv de las recomendaciones de seguridad](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations).

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>Habilitación del control de seguridad de Azure Defender para incluirse en la puntuación de seguridad

**Fecha estimada del cambio:** tercer trimestre de 2021

Las recomendaciones de protección de Security Center se agrupan en controles de seguridad. Cada control es un grupo lógico de recomendaciones de seguridad relacionadas y refleja una superficie de ataque vulnerable. La contribución de cada control de seguridad a la puntuación de seguridad general se muestra claramente en la página de recomendaciones, así como en la lista de controles de [Controles de seguridad y sus recomendaciones](secure-score-security-controls.md#security-controls-and-their-recommendations).

Desde su introducción, el control **Habilitar Azure Defender** ha tenido una puntuación máxima posible de 0 puntos. **Con este cambio, el control contribuye a su puntuación segura**.

Al habilitar Azure Defender, ampliará las funcionalidades del modo gratuito de Security Center a las cargas de trabajo que se ejecutan en nubes privadas y otras nubes públicas, lo que ofrece una administración de seguridad unificada y protección frente amenazas en todas sus cargas de trabajo de nube híbrida. Algunas de las características principales de Azure Defender son: licencias integradas de Microsoft Defender para punto de conexión para los servidores, examen de vulnerabilidades para máquinas virtuales y registros de contenedor, alertas de seguridad basadas en análisis avanzado del comportamiento y aprendizaje automático, y características de seguridad de contenedores. Para más información, consulte [Azure Security Center gratuito frente a Azure Defender habilitado](security-center-pricing.md).

Con este cambio, la puntuación segura de las suscripciones que no están protegidas por Azure Defender se verán afectadas. Se recomienda habilitar Azure Defender antes de que se produzca este cambio para que las puntuaciones no se vean afectadas. 

Obtenga más información en [Inicio rápido: habilitación de Azure Defender](enable-azure-defender.md).

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL

**Fecha estimada del cambio:** primer trimestre de 2022

La recomendación **Los datos confidenciales de las bases de datos SQL deben clasificarse** del control de seguridad **Aplicación de la clasificación de datos** se reemplazará por una nueva versión que esté más alineada con la estrategia de clasificación de datos de Microsoft. Como consecuencia, el identificador de la recomendación también cambiará (actualmente es b0df6f56-862d-4730-8597-38c0fd4ebd59).


## <a name="next-steps"></a>Pasos siguientes

Para ver todos los cambios recientes realizados en el producto, consulte [Novedades de Azure Security Center](release-notes.md).