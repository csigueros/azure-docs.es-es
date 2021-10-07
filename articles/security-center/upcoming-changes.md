---
title: Próximos cambios importantes en Azure Security Center
description: Próximos cambios en Azure Security Center que debe tener en cuenta y para los que puede que necesite un plan
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 09/12/2021
ms.author: memildin
ms.openlocfilehash: 2d78588e900becc8fc802f1e4665b4fe533f0f6a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766851"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Próximos cambios importantes en Azure Security Center

> [!IMPORTANT]
> La información de esta página está relacionada con productos o características en versión preliminar que pueden modificarse sustancialmente antes de lanzarse comercialmente, si es que lo hacen finalmente. Microsoft no ofrece ningún compromiso o garantía, ni expresa ni implícita, con respecto a la información que se proporciona aquí.

En esta página, obtendrá información sobre los cambios planeados para Security Center. Describe las modificaciones planeadas en el producto que pueden afectar a aspectos como los flujos de trabajo o la puntuación segura.

Si busca las notas de la versión más recientes, puede encontrarlas en [Novedades de Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Cambios planeados

| Cambio planeado       | Fecha estimada del cambio |
|----------------------|---------------------------|
| [La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Septiembre de 2021|
| [Cambio del prefijo de algunos tipos de alerta de "ARM_" a "VM_"](#changing-prefix-of-some-alert-types-from-arm_-to-vm_)                                          | Octubre de 2021|
| [Cambios en las recomendaciones para administrar soluciones de protección de puntos de conexión](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | T4 2021    |
| [Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | T1 2022    |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013

**Fecha estimada del cambio:** septiembre de 2021

La implementación heredada de ISO 27001 se eliminará del panel de cumplimiento normativo de Security Center. Si está haciendo un seguimiento del cumplimiento de la norma ISO 27001 mediante Security Center, incorpore el nuevo estándar ISO 27001:2013 para los grupos de administración o suscripciones pertinentes, y la norma ISO 27001 heredada actual pronto se eliminará del panel.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Panel de cumplimiento normativo de Security Center que muestra el mensaje sobre la eliminación de la implementación heredada de ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changing-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Cambio del prefijo de algunos tipos de alerta de "ARM_" a "VM_" 

**Fecha estimada del cambio:** octubre de 2021

En julio de 2021 hemos anunciado una [reorganización lógica de Azure Defender para alertas de Resource Manager](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts). 

Como parte de una reorganización lógica de algunos de los planes de Azure Defender, hemos trasladado veintiuna alertas de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) a [Azure Defender para servidores](defender-for-servers-introduction.md).

Ahora tenemos previsto actualizar los prefijos de estas alertas para que coincidan con esta reasignación. Vamos a reemplazar "ARM_" por "VM_" como se muestra en la tabla siguiente.

| Nombre actual                                   | Después del cambio                             |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |

Obtenga más información sobre los planes de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) y [Azure Defender para servidores](defender-for-servers-introduction.md).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Cambios en las recomendaciones para administrar soluciones de protección de puntos de conexión

**Fecha estimada del cambio:** cuarto trimestre de 2021

En agosto de 2021, hemos agregado dos nuevas recomendaciones **en versión preliminar** para implementar y mantener las soluciones de protección de puntos de conexión en las máquinas. Para obtener información detallada, consulta las [notas de la versión](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Cuando las recomendaciones se liberan a disponibilidad general, reemplazarán las siguientes recomendaciones existentes:

- **La protección de puntos de conexión instalada en las máquinas** reemplazará:
    - [Instalar una solución de protección de punto de conexión en máquinas virtuales](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Instalar una solución de protección de punto de conexión en las máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee) 

- **Los problemas de estado de la protección de puntos de conexión que deben resolverse en las máquinas** reemplazarán la recomendación existente que tiene el mismo nombre. Las dos recomendaciones tienen claves de evaluación diferentes:
    - Clave de evaluación para la recomendación de **versión preliminar**: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - Clave de evaluación de la recomendación de **GA**: 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

Más información:
- [Soluciones de protección de puntos de conexión admitidas por Security Center](security-center-services.md#endpoint-supported)
- [Cómo evalúan estas recomendaciones el estado de las soluciones implementadas](security-center-endpoint-protection.md)

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL

**Fecha estimada del cambio:** primer trimestre de 2022

La recomendación **Los datos confidenciales de las bases de datos SQL deben clasificarse** del control de seguridad **Aplicación de la clasificación de datos** se reemplazará por una nueva versión que esté más alineada con la estrategia de clasificación de datos de Microsoft. Como consecuencia, el identificador de la recomendación también cambiará (actualmente es b0df6f56-862d-4730-8597-38c0fd4ebd59).


## <a name="next-steps"></a>Pasos siguientes

Para ver todos los cambios recientes realizados en Security Center, consulte [Novedades de Azure Security Center](release-notes.md).