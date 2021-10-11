---
title: Próximos cambios importantes en Azure Security Center
description: Próximos cambios en Azure Security Center que debe tener en cuenta y para los que puede que necesite un plan
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/05/2021
ms.author: memildin
ms.openlocfilehash: e6d1c8cf55687a8e4d7612ca432a314ae38256bc
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534411"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Próximos cambios importantes en Azure Security Center

> [!IMPORTANT]
> La información de esta página está relacionada con productos o características en versión preliminar que pueden modificarse sustancialmente antes de lanzarse comercialmente, si es que lo hacen finalmente. Microsoft no ofrece ningún compromiso o garantía, ni expresa ni implícita, con respecto a la información que se proporciona aquí.

En esta página, obtendrá información sobre los cambios planeados para Security Center. Describe las modificaciones planeadas en el producto que pueden afectar a aspectos como los flujos de trabajo o la puntuación segura.

Si busca las notas de la versión más recientes, puede encontrarlas en [Novedades de Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Cambios planeados

| Cambio planeado       | Fecha estimada del cambio |
|----------------------|---------------------------|
| [Entrada en desuso de una alerta en versión preliminar: ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | Octubre de 2021|
| [La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Octubre de 2021|
| [Cambios en las recomendaciones para administrar soluciones de protección de puntos de conexión](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Octubre de 2021    |
| [Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | T1 2022    |
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>Entrada en desuso de una alerta en versión preliminar: ARM.MCAS_ActivityFromAnonymousIPAddresses

**Fecha estimada del cambio:** octubre de 2021

Va a entrar en desuso la siguiente alerta en versión preliminar:

|Nombre de la alerta| Descripción|
|----------------------|---------------------------|
|**VERSIÓN PRELIMINAR: actividad desde direcciones IP de riesgo**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|Se ha detectado actividad de usuarios desde una dirección IP que se ha identificado como una dirección IP de proxy anónima.<br>Estos servidores proxy los usan los usuarios que quieren ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados. Esta detección usa un algoritmo de aprendizaje automático que reduce los falsos positivos, como las direcciones IP mal etiquetadas que otros usuarios de la organización usan ampliamente.<br>Requiere una licencia activa de Microsoft Cloud App Security.|
|||

Hemos creado nuevas alertas que proporcionan esta información y la agregan. Además, la alerta más reciente (ARM_OperationFromSuspiciousIP, ARM_OperationFromSuspiciousProxyIP) no requiere una licencia para Microsoft Cloud App Security.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013

**Fecha estimada del cambio:** octubre de 2021

La implementación heredada de ISO 27001 se eliminará del panel de cumplimiento normativo de Security Center. Si está haciendo un seguimiento del cumplimiento de la norma ISO 27001 mediante Security Center, incorpore el nuevo estándar ISO 27001:2013 para los grupos de administración o suscripciones pertinentes, y la norma ISO 27001 heredada actual pronto se eliminará del panel.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Panel de cumplimiento normativo de Security Center que muestra el mensaje sobre la eliminación de la implementación heredada de ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Cambios en las recomendaciones para administrar soluciones de protección de puntos de conexión

**Fecha estimada del cambio:** octubre de 2021

En agosto de 2021, hemos agregado dos nuevas recomendaciones **en versión preliminar** para implementar y mantener las soluciones de protección de puntos de conexión en las máquinas. Para obtener información detallada, consulta las [notas de la versión](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Cuando las recomendaciones se liberan a disponibilidad general, reemplazarán las siguientes recomendaciones existentes:

- **La protección de puntos de conexión instalada en las máquinas** reemplazará:
    - [Instalar una solución de protección del punto de conexión en las máquinas virtuales (clave: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Instalar una solución de protección del punto de conexión en las máquinas (clave: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

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