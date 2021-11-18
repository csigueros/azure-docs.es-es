---
title: Próximos cambios importantes en Microsoft Defender for Cloud
description: Próximos cambios en Microsoft Defender for Cloud que debe tener en cuenta y para los que puede que necesite un plan
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/10/2021
ms.author: memildin
ms.openlocfilehash: fca622c8c49a16d9926a5fc1e8e0f0064ee758d7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157450"
---
# <a name="important-upcoming-changes-to-microsoft-defender-for-cloud"></a>Próximos cambios importantes en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> La información de esta página está relacionada con productos o características en versión preliminar que pueden modificarse sustancialmente antes de lanzarse comercialmente, si es que lo hacen finalmente. Microsoft no ofrece ningún compromiso o garantía, ni expresa ni implícita, con respecto a la información que se proporciona aquí.

En esta página, obtendrá información sobre los cambios planeados para Defender for Cloud. Describe las modificaciones planeadas en el producto que pueden afectar a aspectos como los flujos de trabajo o la puntuación segura.

Si busca las notas de la versión más recientes, puede encontrarlas en [Novedades de Microsoft Defender for Cloud](release-notes.md).


## <a name="planned-changes"></a>Cambios planeados

| Cambio planeado       | Fecha estimada del cambio |
|----------------------|---------------------------|
| [Entrada en desuso de una alerta en versión preliminar: ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | Noviembre de 2021|
| [La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Noviembre de 2021|
| [La visualización de inventario de las máquinas locales usará una plantilla diferente para el nombre del recurso](#inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name)    | Noviembre de 2021    |
| [Varios cambios en las recomendaciones de identidad](#multiple-changes-to-identity-recommendations)                                                                                          | Diciembre de 2021    |
| [Cambios en una alerta de seguridad de Microsoft Defender para Storage](#changes-to-a-security-alert-from-microsoft-defender-for-storage)  | Noviembre de 2021    |
| [Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | T1 2022    |
| [Cambios en las recomendaciones para administrar soluciones de protección de puntos de conexión](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Febrero de 2022| 
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>Entrada en desuso de una alerta en versión preliminar: ARM.MCAS_ActivityFromAnonymousIPAddresses

**Fecha estimada del cambio:** noviembre de 2021

Va a entrar en desuso la siguiente alerta en versión preliminar:

|Nombre de la alerta| Descripción|
|----------------------|---------------------------|
|**VERSIÓN PRELIMINAR: actividad desde direcciones IP de riesgo**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|Se ha detectado actividad de usuarios desde una dirección IP que se ha identificado como una dirección IP de proxy anónima.<br>Estos servidores proxy los usan los usuarios que quieren ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados. Esta detección usa un algoritmo de aprendizaje automático que reduce los falsos positivos, como las direcciones IP mal etiquetadas que otros usuarios de la organización usan ampliamente.<br>Requiere una licencia activa de Microsoft Cloud App Security.|
|||

Hemos creado nuevas alertas que proporcionan esta información y la agregan. Además, las alertas más recientes (ARM_OperationFromSuspiciousIP y ARM_OperationFromSuspiciousProxyIP) no requieren una licencia para Microsoft Defender for Cloud Apps (anteriormente conocido como Microsoft Cloud App Security).

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>La implementación heredada de ISO 27001 se está reemplazando por la nueva ISO 27001:2013

**Fecha estimada del cambio:** noviembre de 2021

La implementación heredada de ISO 27001 se eliminará del panel de cumplimiento normativo de Defender for Cloud. Si está haciendo un seguimiento del cumplimiento de la norma ISO 27001 mediante Defender for Cloud, incorpore el nuevo estándar ISO 27001:2013 para los grupos de administración o suscripciones pertinentes, y la norma ISO 27001 heredada actual pronto se eliminará del panel.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Panel de cumplimiento normativo de Defender for Cloud que muestra el mensaje sobre la eliminación de la implementación heredada de ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::


### <a name="inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name"></a>La visualización de inventario de las máquinas locales usará una plantilla diferente para el nombre del recurso

**Fecha estimada del cambio:** noviembre de 2021

Para mejorar la presentación de los recursos en el [inventario de recursos](asset-inventory.md), quitaremos el elemento "source-computer-IP" de la plantilla para asignar nombres a las máquinas locales.

- **Formato actual:** ``machine-name_source-computer-id_VMUUID``
- **A partir de la actualización:** ``machine-name_VMUUID``


### <a name="multiple-changes-to-identity-recommendations"></a>Varios cambios en las recomendaciones de identidad

**Fecha estimada del cambio:** diciembre de 2021

Defender for Cloud incluye varias recomendaciones para mejorar la administración de usuarios y cuentas. En diciembre, realizaremos los cambios que se describen a continuación.

- **Intervalo de actualización mejorado**: actualmente, las recomendaciones de identidad tienen un intervalo de actualización de 24 horas. Esta actualización reducirá ese intervalo a 12 horas.

- **Funcionalidad de exención de cuentas**: Defender for Cloud tiene muchas características para personalizar la experiencia y garantizar que la puntuación de seguridad refleje las prioridades de seguridad de la organización. La opción de exención en las recomendaciones de seguridad es una de estas características. Para obtener información general e instrucciones completas, consulte [Exención de recursos y recomendaciones de la puntuación de seguridad](exempt-resource.md). Con esta actualización, podrá excluir cuentas específicas de la evaluación mediante las ocho recomendaciones enumeradas en la tabla siguiente.

    Normalmente, se excluirían las cuentas de emergencia "break glass" de las recomendaciones de MFA, ya que estas cuentas a menudo se excluyen deliberadamente de los requisitos de MFA de una organización. Como alternativa, es posible que tenga cuentas externas a las que le gustaría permitir el acceso, pero que no tienen MFA habilitado.

    > [!TIP]
    > Al excluir una cuenta, no se mostrará como en estado incorrecto y tampoco hará que una suscripción aparezca en estado incorrecto.

    |Recomendación| Clave de evaluación|
    |-|-|
    |[MFA debe estar habilitada en las cuentas con permisos de propietario en la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/94290b00-4d0c-d7b4-7cea-064a9554e681)|94290b00-4d0c-d7b4-7cea-064a9554e681|
    |[MFA debe estar habilitada en las cuentas con permisos de lectura en la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/151e82c5-5341-a74b-1eb0-bc38d2c84bb5)|151e82c5-5341-a74b-1eb0-bc38d2c84bb5|
    |[MFA debe estar habilitada en las cuentas con permisos de escritura en la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/57e98606-6b1e-6193-0e3d-fe621387c16b)|57e98606-6b1e-6193-0e3d-fe621387c16b|
    |[Las cuentas externas con permisos de propietario deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/c3b6ae71-f1f0-31b4-e6c1-d5951285d03d)|c3b6ae71-f1f0-31b4-e6c1-d5951285d03d|
    |[Las cuentas externas con permisos de lectura deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b)|a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b|
    |[Las cuentas externas con permisos de escritura deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/04e7147b-0deb-9796-2e5c-0336343ceb3d)|04e7147b-0deb-9796-2e5c-0336343ceb3d|
    |[Las cuentas en desuso con permisos de propietario deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)|e52064aa-6853-e252-a11e-dffc675689c2|
    |[Las cuentas en desuso deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|00c6d40b-e990-6acf-d4f3-471e747a27c4|
    |||
 
- **Cambio de nombre de recomendaciones**: a partir de esta actualización, vamos a cambiar el nombre de dos recomendaciones. También estamos revisando sus descripciones. Las claves de evaluación permanecerán sin cambios. 


    |Propiedad  |Valor actual  | A partir de la actualización|
    |---------|---------|---------|
    |Clave de evaluación     | e52064aa-6853-e252-a11e-dffc675689c2        | Sin cambios|
    |Nombre     |[Las cuentas en desuso con permisos de propietario deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)         |Las suscripciones se deben purgar de las cuentas que están bloqueadas en Active Directory y tienen permisos de propietario.        |
    |Descripción     |Las cuentas de usuario cuyo inicio de sesión se ha bloqueado deben quitarse de las suscripciones.<br>Estas cuentas pueden ser objetivo de los atacantes que buscan formas de acceder a los datos sin ser detectados.|Las cuentas de usuario cuyo inicio de sesión en Active Directory se haya bloqueado se deben quitar de las suscripciones. Estas cuentas pueden ser objetivo de los atacantes que buscan formas de acceder a los datos sin ser detectados.<br>Más información sobre cómo proteger el perímetro de identidad en [Procedimientos recomendados para la administración de identidades y la seguridad del control de acceso en Azure](../security/fundamentals/identity-management-best-practices.md).|
    |Directiva relacionada     |[Las cuentas en desuso con permisos de propietario deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)         |Las suscripciones se deben purgar de las cuentas que están bloqueadas en Active Directory y tienen permisos de propietario. |
    |||

    |Propiedad  |Valor actual  | A partir de la actualización|
    |---------|---------|---------|
    |Clave de evaluación     | 00c6d40b-e990-6acf-d4f3-471e747a27c4        | Sin cambios|
    |Nombre     |[Las cuentas en desuso deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|Las suscripciones se deben purgar de las cuentas que están bloqueadas en Active Directory y tienen permisos de lectura y escritura.|
    |Descripción     |Las cuentas de usuario cuyo inicio de sesión se ha bloqueado deben quitarse de las suscripciones.<br>Estas cuentas pueden ser objetivo de los atacantes que buscan formas de acceder a los datos sin ser detectados.|Las cuentas de usuario cuyo inicio de sesión en Active Directory se haya bloqueado se deben quitar de las suscripciones. Estas cuentas pueden ser objetivo de los atacantes que buscan formas de acceder a los datos sin ser detectados.<br>Más información sobre cómo proteger el perímetro de identidad en [Procedimientos recomendados para la administración de identidades y la seguridad del control de acceso en Azure](../security/fundamentals/identity-management-best-practices.md).|
    |Directiva relacionada     |[Las cuentas en desuso deben quitarse de la suscripción](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)|Las suscripciones se deben purgar de las cuentas que están bloqueadas en Active Directory y tienen permisos de lectura y escritura.|
    |||
 

### <a name="changes-to-a-security-alert-from-microsoft-defender-for-storage"></a>Cambios en una alerta de seguridad de Microsoft Defender para Storage

**Fecha estimada del cambio:** noviembre de 2021

Una de las alertas en versión preliminar proporcionadas por Microsoft Defender para Storage se divide en dos nuevas recomendaciones para ofrecer una mayor claridad sobre los eventos sospechosos detectados. Esta alerta solo concierne a Azure Blob Storage.

**El tipo de alerta también está cambiando.**

- Antes del cambio, la alerta era:<br>
    "Preview - Anonymous scan of public storage containers" (Versión preliminar: examen anónimo de contenedores de almacenamiento público)<br>(Storage.Blob_ContainerAnonymousScan)

- A partir de este cambio, habrá dos recomendaciones:

    - "Open storage containers discovered by external scanning tool or script" (Abrir contenedores de almacenamiento detectados por la herramienta o script de examen externo)<br>(Storage.Blob_OpenContainersScanning.FailedAttempt)
    - "Successful discovery of open storage containers by external scanning script or tool" (Detección correcta de contenedores de almacenamiento abiertos mediante una herramienta o script de examen externo)<br>(Storage.Blob_OpenContainersScanning.SuccessfulDiscovery)

Cuando se realice el lanzamiento del cambio, se publicarán más detalles de estas alertas.


### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Mejoras en la recomendación para clasificar datos confidenciales en bases de datos SQL

**Fecha estimada del cambio:** primer trimestre de 2022

La recomendación **Los datos confidenciales de las bases de datos SQL deben clasificarse** del control de seguridad **Aplicación de la clasificación de datos** se reemplazará por una nueva versión que esté más alineada con la estrategia de clasificación de datos de Microsoft. Como consecuencia, el identificador de la recomendación también cambiará (actualmente es b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Cambios en las recomendaciones para administrar soluciones de protección de puntos de conexión

**Fecha estimada del cambio:** febrero de 2022

En agosto de 2021, hemos agregado dos nuevas recomendaciones **en versión preliminar** para implementar y mantener las soluciones de protección de puntos de conexión en las máquinas. Para obtener información detallada, consulta las [notas de la versión](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Cuando las recomendaciones se liberan a disponibilidad general, reemplazarán las siguientes recomendaciones existentes:

- **La protección de puntos de conexión instalada en las máquinas** reemplazará:
    - [Instalar una solución de protección del punto de conexión en las máquinas virtuales (clave: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Instalar una solución de protección del punto de conexión en las máquinas (clave: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **Los problemas de estado de la protección de puntos de conexión que deben resolverse en las máquinas** reemplazarán la recomendación existente que tiene el mismo nombre. Las dos recomendaciones tienen claves de evaluación diferentes:
    - Clave de evaluación para la recomendación de **versión preliminar**: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - Clave de evaluación de la recomendación de **GA**: 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

Más información:
- [Soluciones de protección de puntos de conexión admitidas por Defender for Cloud](supported-machines-endpoint-solutions-clouds.md#endpoint-supported)
- [Cómo evalúan estas recomendaciones el estado de las soluciones implementadas](endpoint-protection-recommendations-technical.md)



## <a name="next-steps"></a>Pasos siguientes

Para ver todos los cambios recientes en Defender for Cloud, consulte [Novedades de Microsoft Defender for Cloud](release-notes.md).
