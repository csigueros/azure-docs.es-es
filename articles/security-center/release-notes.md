---
title: Notas de la versión de Microsoft Defender for Cloud
description: Descripción de las novedades y los cambios en Microsoft Defender for Cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 11/04/2021
ms.author: memildin
ms.openlocfilehash: 0e93c254a28aa62ce84f60daa02f91b247893a5b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577163"
---
# <a name="whats-new-in-microsoft-defender-for-cloud"></a>Novedades de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud está en desarrollo activo y recibe mejoras continuas. Para mantenerse al día con los avances más recientes, esta página proporciona información sobre las nuevas características, las correcciones de errores y las funcionalidades en desuso.

Esta página se actualiza con frecuencia, por lo que se recomienda visitarla a menudo. 

Para obtener información sobre los cambios *planeados* que están próximos a materializarse en Defender for Cloud, consulte el artículo [Próximos cambios importantes en Microsoft Defender for Cloud](upcoming-changes.md). 

> [!TIP]
> Si busca elementos de más de 6 meses, puede encontrarlos en el [Archivo de novedades de Microsoft Defender for Cloud](release-notes-archive.md).


## <a name="november-2021"></a>Noviembre de 2021

Nuestra versión de Ignite incluye:

- [Azure Security Center y Azure Defender ahora se denominan Microsoft Defender para la nube](#azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud).
- [CSPM nativo para AWS y protección contra amenazas para Amazon EKS y AWS EC2](#native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2)
- [Priorización de las acciones de seguridad por confidencialidad de datos (con tecnología de Azure Purview; en versión preliminar)](#prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview).
- [Evaluaciones de control de seguridad ampliadas con Azure Security Benchmark v3](#expanded-security-control-assessments-with-azure-security-benchmark-v3).
- [Sincronización de alertas bidireccionales opcionales del conector de Microsoft Sentinel; está publicada para disponibilidad general (GA)](#microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga).
- [Nueva recomendación para insertar registros de Azure Kubernetes Service (AKS) en Sentinel](#new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel).

Otros cambios de noviembre incluyen:

- [Se agregó la Administración de amenazas y vulnerabilidades de Microsoft como solución de evaluación de vulnerabilidades; versión disponible de forma general (GA)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga).
- [Microsoft Defender para punto de conexión para Linux es ahora compatible con Microsoft Defender para servidores; versión disponible de forma general (GA)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga).
- [Exportación de instantáneas para recomendaciones y conclusiones de seguridad (en versión preliminar)](#snapshot-export-for-recommendations-and-security-findings-in-preview).
- [Aprovisionamiento automático de soluciones de evaluación de vulnerabilidades publicadas para disponibilidad general (GA)](#auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga).
- [Filtros de inventario de software en el inventario de recursos publicados para disponibilidad general (GA)](#software-inventory-filters-in-asset-inventory-released-for-general-availability-ga).

### <a name="azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud"></a>Azure Security Center y Azure Defender ahora se denominan Microsoft Defender para la nube

Según el [Informe de estado de la nube de 2021](https://info.flexera.com/CM-REPORT-State-of-the-Cloud#download), el 92 % de las organizaciones tienen ahora una estrategia de varias nubes. En Microsoft, nuestro objetivo es centralizar la seguridad en estos entornos y ayudar a los equipos de seguridad a trabajar de forma más eficaz.

**Microsoft Defender para la nube** (anteriormente conocido como Azure Security Center y Azure Defender) es una solución de administración de la posición de seguridad en la nube (CSPM) y protección de cargas de trabajo en la nube (CWP) que detecta puntos débiles en la configuración de la nube, le permite reforzar la posición de seguridad general del entorno y protege las cargas de trabajo en entornos híbridos y de varias nubes.

En Ignite 2019, compartimos nuestra visión de crear el enfoque más completo para proteger su patrimonio digital e integrar tecnologías XDR bajo la marca Microsoft Defender. La unificación de Azure Security Center y Azure Defender con el nuevo nombre **Microsoft Defender para la nube** refleja las funcionalidades integradas de nuestra oferta de seguridad y nuestra capacidad para admitir cualquier plataforma en la nube.


### <a name="native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2"></a>CSPM nativo para AWS y protección contra amenazas para Amazon EKS y AWS EC2

Una nueva página de **configuración del entorno** le proporciona mayor visibilidad y control sobre los grupos de administración, las suscripciones y las cuentas de AWS. La página está diseñada para incorporar cuentas de AWS a escala: conecte su **cuenta de administración de AWS** e incorporará automáticamente cuentas existentes y futuras. 

:::image type="content" source="media/release-notes/add-aws-account.png" alt-text="Use la nueva página de configuración del entorno para conectar las cuentas de AWS.":::

Cuando haya agregado las cuentas de AWS, Defender para la nube protege los recursos de AWS con cualquiera o todos los planes siguientes:

- Las **características de CSPM de Defender para la nube** se extienden a los recursos de AWS. Este plan sin agente evalúa los recursos de AWS según las recomendaciones de seguridad específicas de AWS, que se incluyen en la puntuación de seguridad. También se evaluará el cumplimiento de los estándares integrados específicos de AWS (AWS CIS, AWS PCI DSS y Procedimientos recomendados de seguridad fundamentales de AWS). La [página de inventario de recursos](asset-inventory.md) de Defender para la nube es una característica habilitada para varias nubes que le permite administrar los recursos de AWS junto con los recursos de Azure.
- **Microsoft Defender para Kubernetes** amplía la detección de amenazas de contenedor y defensas avanzadas a los **clústeres de Linux de Amazon EKS**.
- **Microsoft Defender para servidores** proporciona detección de amenazas y defensas avanzadas para instancias de EC2 de Windows y Linux. Este plan incluye la licencia integrada de Microsoft Defender para punto de conexión, líneas de base de seguridad y evaluaciones de nivel de sistema operativo, análisis de evaluación de vulnerabilidades, controles de aplicaciones adaptables (AAC), supervisión de la integridad de archivos (FIM) y mucho más.

Obtenga más información sobre cómo [conectar las cuentas de AWS a Microsoft Defender para la nube](quickstart-onboard-aws.md).


### <a name="prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview"></a>Priorización de las acciones de seguridad por confidencialidad de datos (con tecnología de Azure Purview; en versión preliminar)
Los recursos de datos siguen siendo un objetivo popular para los actores de amenazas. Por lo tanto, es fundamental que los equipos de seguridad identifiquen, prioricen y protejan los recursos de datos confidenciales en sus entornos de nube.

Para abordar este desafío, Microsoft Defender para la nube ahora integra la información de confidencialidad de [Azure Purview](../purview/overview.md). Azure Purview es un servicio unificado de gobernanza de datos que proporciona información valiosa sobre la confidencialidad de los datos en cargas de trabajo locales y en varias nubes.

La integración con Azure Purview amplía la visibilidad de seguridad en Defender para la nube desde el nivel de infraestructura hasta los datos, lo que permite una manera completamente nueva de priorizar los recursos y las actividades de seguridad para los equipos de seguridad.

Obtenga más información sobre la [Clasificación de acciones de seguridad según la confidencialidad de los datos](information-protection.md).


### <a name="expanded-security-control-assessments-with-azure-security-benchmark-v3"></a>Evaluaciones de control de seguridad ampliadas con Azure Security Benchmark v3
Las recomendaciones de seguridad de Microsoft Defender para la nube están habilitadas y son compatibles con Azure Security Benchmark. 

[Azure Security Benchmark](../security/benchmarks/introduction.md) es el conjunto de directrices específico de Azure creado por Microsoft para ofrecer los procedimientos recomendados de seguridad y cumplimiento basados en marcos de cumplimiento comunes. Este punto de referencia ampliamente respetado está basado en los controles del [Centro de seguridad de Internet (CIS)](https://www.cisecurity.org/benchmark/azure/) y del [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) y hace hincapié en la seguridad centrada en la nube.

A partir de Ignite 2021, Azure Security Benchmark **v3** está disponible en el [panel de cumplimiento normativo de Defender para la nube](update-regulatory-compliance-packages.md) y se habilita como la nueva iniciativa predeterminada para todas las suscripciones de Azure protegidas con Microsoft Defender para la nube. 

Entre las mejoras de v3 se incluyen: 

- Asignaciones adicionales a marcos del sector [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) y [controles CIS v8](https://www.cisecurity.org/controls/v8/).
- Instrucciones y prácticas más pormenorizados para los controles con la introducción:
    - **Principios de seguridad**: permiten proporcionar información sobre los objetivos generales de seguridad que crean la base de nuestras recomendaciones.
    - **Guía de Azure**: es el "cómo" técnico para cumplir estos objetivos.

Los nuevos controles incluyen la seguridad de DevOps para problemas como el modelado de amenazas y la seguridad de la cadena de suministro de software, así como la administración de claves y certificados para los procedimientos recomendados en Azure.

Puede obtener más información en [Introducción a Azure Security Benchmark](/security/benchmark/azure/introduction).


### <a name="microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga"></a>Sincronización de alertas bidireccionales opcionales del conector de Microsoft Sentinel; está publicada para disponibilidad general (GA)

En julio, [anunciamos](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview) una característica en versión preliminar, una **sincronización de alertas bidireccional**, para el conector integrado en [Microsoft Sentinel](../sentinel/index.yml) (solución SIEM y SOAR nativa de la nube de Microsoft). Esta característica se ha publicado con disponibilidad general (GA).

Al conectar Microsoft Defender para la nube a Microsoft Sentinel, el estado de las alertas de seguridad se sincroniza entre los dos servicios. Por ejemplo, cuando se cierra una alerta en Defender for Cloud, esa alerta también se mostrará como cerrada en Microsoft Sentinel. El cambio del estado de una alerta en Defender para la nube no afectará al estado de los **incidentes** de Microsoft Sentinel que contienen la alerta de Microsoft Sentinel sincronizada, sino solo al de la propia alerta sincronizada.

Al habilitar la **sincronización de alertas bidireccional**, se sincronizará automáticamente el estado de las alertas de Defender para la nube originales con los incidentes de Microsoft Sentinel que contienen las copias de esas alertas de Defender para la nube. Por ejemplo, cuando se cierra un incidente de Microsoft Sentinel que contiene una alerta de Defender para la nube, Defender para la nube cerrará automáticamente la alerta original correspondiente.

Obtenga más información sobre cómo [conectar alertas de Azure Defender desde Azure Security Center](../sentinel/connect-azure-security-center.md) y [transmitirlas a Azure Sentinel](export-to-siem.md#stream-alerts-to-microsoft-sentinel).


### <a name="new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel"></a>Nueva recomendación para insertar registros de Azure Kubernetes Service (AKS) en Sentinel

En una mejora adicional del valor combinado de Defender para la nube y Microsoft Sentinel, ahora resaltaremos las instancias de Azure Kubernetes Service que no envían datos de registro a Microsoft Sentinel.

Los equipos de SecOps pueden elegir el área de trabajo de Microsoft Sentinel que les interese directamente de la página de detalles de la recomendación y habilitar inmediatamente el streaming de registros sin procesar. Esta conexión sin problemas entre los dos productos facilita a los equipos de seguridad garantizar una cobertura de registro completa en todas sus cargas de trabajo para mantener actualizado todo el entorno.

La nueva recomendación "Los registros de diagnóstico en los servicios de Kubernetes deben estar habilitados" incluye la opción "Corregir" para realizar una corrección más rápida.

También se ha mejorado la recomendación "La auditoría en el servidor de SQL se debe habilitar" con las mismas funcionalidades de streaming de Sentinel. 


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga"></a>Se agregó la Administración de amenazas y vulnerabilidades de Microsoft como solución de evaluación de vulnerabilidades; versión disponible de forma general (GA)

En octubre [anunciamos](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview) la ampliación de la integración entre [Microsoft Defender para servidores](defender-for-servers-introduction.md) y Microsoft Defender para punto de conexión, para poder admitir un nuevo proveedor de vulnerabilidades para las máquinas: [Administración de amenazas y vulnerabilidades de Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). Esta característica se ha publicado con disponibilidad general (GA).

Use la **administración de amenazas y vulnerabilidades** para detectar vulnerabilidades y configuraciones incorrectas casi en tiempo real con la [integración con Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md) habilitada y sin necesidad de agentes adicionales ni exámenes periódicos. La administración de amenazas y vulnerabilidades prioriza las vulnerabilidades en función del panorama de amenazas y las detecciones de su organización.

Use la recomendación de seguridad "[Se debe habilitar una solución de evaluación de vulnerabilidades en las máquinas virtuales](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)" para revelar las vulnerabilidades detectadas por la administración de amenazas y vulnerabilidades para sus [máquinas admitidas](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true). 

Para detectar automáticamente las vulnerabilidades en máquinas nuevas y existentes, sin necesidad de corregir manualmente la recomendación, consulte [Las soluciones de evaluación de vulnerabilidades ahora se pueden habilitar automáticamente (en versión preliminar)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

Obtenga más información en [Investigación de puntos débiles con la solución de administración de amenazas y vulnerabilidades de Microsoft Defender para punto de conexión](deploy-vulnerability-assessment-tvm.md).

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga"></a>Microsoft Defender para punto de conexión para Linux es ahora compatible con Microsoft Defender para servidores; versión disponible de forma general (GA)

En agosto, [anunciamos](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview) la compatibilidad con la versión preliminar para implementar el sensor [Defender para punto de conexión para Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) en máquinas Linux compatibles. Esta característica se ha publicado con disponibilidad general (GA).

[Microsoft Defender para servidores](defender-for-servers-introduction.md) incluye una licencia integrada para [Microsoft Defender para punto de conexión](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Esta integración ofrece funcionalidades completas de detección y respuesta (EDR) de puntos de conexión.

Cuando Defender for Endpoint detecta una amenaza, desencadena una alerta. La alerta se muestra en Defender for Cloud. En Defender for Cloud, también puede dinamizar hasta la consola de Defender para punto de conexión para realizar una investigación detallada y descubrir el alcance del ataque.

Para más información, consulte [Proteja los puntos de conexión con la solución EDR integrada de Security Center: Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md).


### <a name="snapshot-export-for-recommendations-and-security-findings-in-preview"></a>Exportación de instantáneas para recomendaciones y conclusiones de seguridad (en versión preliminar)

Defender para la nube genera recomendaciones y alertas de seguridad detalladas. Puede verlas en el portal o mediante herramientas de programación. También puede que deba exportar parte o la totalidad de esta información para realizar el seguimiento con otras herramientas de supervisión de su entorno.

La **exportación continua** de Defender para la nube le permite personalizar completamente *qué* se exportará y *a dónde* irá. Obtenga más información en [Exportación continua de datos de Defender para la nube](continuous-export.md).

Aunque la característica se denomina *continua*, también hay una opción para exportar instantáneas semanales. Hasta ahora, estas instantáneas semanales se limitaban a la puntuación segura y a los datos de cumplimiento normativo. Hemos agregado la capacidad de exportar recomendaciones y conclusiones de seguridad.

### <a name="auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga"></a>Aprovisionamiento automático de soluciones de evaluación de vulnerabilidades publicadas para disponibilidad general (GA)

En octubre, [anunciamos](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview) la adición de soluciones de evaluación de vulnerabilidades a la página de aprovisionamiento automático de Defender para la nube. Esto es relevante para las máquinas virtuales de Azure y de Azure Arc que están en suscripciones protegidas por [Azure Defender para servidores](defender-for-servers-introduction.md). Esta característica se ha publicado con disponibilidad general (GA).

Además, si la [integración con Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md) está habilitada, Defender para la nube tendrá una selección de soluciones de evaluación de vulnerabilidades:

- (**NUEVO**) El módulo de administración de amenazas y vulnerabilidades de Microsoft de Microsoft Defender para punto de conexión (consulte [la nota de la versión](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga))
- El agente de Qualys integrado

La solución elegida se habilitará automáticamente en las máquinas admitidas.

Obtenga más información en [Configuración automática de la evaluación de vulnerabilidades para las máquinas](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-in-asset-inventory-released-for-general-availability-ga"></a>Filtros de inventario de software en el inventario de recursos publicados para disponibilidad general (GA)

En octubre, [anunciamos](#software-inventory-filters-added-to-asset-inventory-in-preview) nuevos filtros para la página de [inventario de recursos](asset-inventory.md) para seleccionar las máquinas que ejecutan software específico e incluso especificar las versiones de interés. Esta característica se ha publicado con disponibilidad general (GA).

Asimismo, puede consultar los datos del inventario de software en el **Explorador de Azure Resource Graph**.

Para usar estas características, deberá habilitar la [integración con Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md). 

Para obtener detalles completos, incluidas las consultas de Kusto de ejemplo para Azure Resource Graph, consulte [Acceso a un inventario de software](asset-inventory.md#access-a-software-inventory).

### <a name="new-aks-security-policy-added-to-default-initiative--for-use-by-private-preview-customers-only"></a>Se han agregado nuevas directivas de seguridad de AKS a la iniciativa para que oslo las usen clientes de la versión preliminar privada

Para asegurarse de que las cargas de trabajo de Kubernetes son seguras de forma predeterminada, Defender para la nube incluye directivas en el nivel de Kubernetes y recomendaciones de protección, incluidas las opciones de cumplimiento con el control de admisión de Kubernetes.

Como parte de este proyecto, hemos agregado una directiva y una recomendación (deshabilitadas de forma predeterminada) para la implementación de acceso en los clústeres de Kubernetes. La directiva está en la iniciativa predeterminada, pero solo es relevante para las organizaciones que se registran para la versión preliminar privada relacionada.

Puede omitir de forma segura las directivas y la recomendación ("los clústeres de Kubernetes deben realizar la implementación de imágenes vulnerables") y no habrá ningún impacto en el entorno. 

Si quiere participar en la versión preliminar privada, deberá ser miembro del anillo de la versión preliminar privada. Si aún no es miembro, envíe una solicitud [aquí](https://aka.ms/atscale). Los miembros recibirán una notificación cuando comience la versión preliminar.

## <a name="october-2021"></a>Octubre de 2021

Las actualizaciones de octubre incluyen:

- [Se ha agregado Administración de amenazas y vulnerabilidades de Microsoft como solución de evaluación de vulnerabilidades (en versión preliminar)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview).
- [Las soluciones de evaluación de vulnerabilidades ahora se pueden habilitar automáticamente (en versión preliminar)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).
- [Se han agregado filtros de inventario de software agregados al inventario de recursos (en versión preliminar)](#software-inventory-filters-added-to-asset-inventory-in-preview).
- [Cambio del prefijo de algunos tipos de alerta de "ARM_" a "VM_"](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)
- [Cambios en la lógica de una recomendación de seguridad para clústeres de Kubernetes](#changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters)
- [Las páginas de detalles de recomendaciones ahora muestran recomendaciones relacionadas](#recommendations-details-pages-now-show-related-recommendations)
- [Nuevas alertas para Azure Defender para Kubernetes (en versión preliminar)](#new-alerts-for-azure-defender-for-kubernetes-in-preview)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Se ha agregado Administración de amenazas y vulnerabilidades de Microsoft como solución de evaluación de vulnerabilidades (en versión preliminar)

Hemos ampliado la integración entre [Azure Defender para servidores](defender-for-servers-introduction.md) y Microsoft Defender para punto de conexión para admitir una nueva evaluación de vulnerabilidades para sus máquinas: [Administración de amenazas y vulnerabilidades de Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). 

Use la **administración de amenazas y vulnerabilidades** para detectar vulnerabilidades y configuraciones incorrectas casi en tiempo real con la [integración con Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md) habilitada y sin necesidad de agentes adicionales ni exámenes periódicos. La administración de amenazas y vulnerabilidades prioriza las vulnerabilidades en función del panorama de amenazas y las detecciones de su organización.

Use la recomendación de seguridad "[Se debe habilitar una solución de evaluación de vulnerabilidades en las máquinas virtuales](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)" para revelar las vulnerabilidades detectadas por la administración de amenazas y vulnerabilidades para sus [máquinas admitidas](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true). 

Para detectar automáticamente las vulnerabilidades en máquinas nuevas y existentes, sin necesidad de corregir manualmente la recomendación, consulte [Las soluciones de evaluación de vulnerabilidades ahora se pueden habilitar automáticamente (en versión preliminar)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

Obtenga más información en [Investigación de puntos débiles con la solución de administración de amenazas y vulnerabilidades de Microsoft Defender para punto de conexión](deploy-vulnerability-assessment-tvm.md).

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>Las soluciones de evaluación de vulnerabilidades ahora se pueden habilitar automáticamente (en versión preliminar)

La página de aprovisionamiento automático de Security Center ahora incluye la opción para habilitar automáticamente una solución de evaluación de vulnerabilidades para máquinas virtuales de Azure y máquinas de Azure Arc en suscripciones protegidas por [Azure Defender para servidores](defender-for-servers-introduction.md).

Además, si la [integración con Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md) está habilitada, Defender para la nube tendrá una selección de soluciones de evaluación de vulnerabilidades:

- (**NUEVO**) El módulo de administración de amenazas y vulnerabilidades de Microsoft de Microsoft Defender para punto de conexión (consulte [la nota de la versión](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview))
- El agente de Qualys integrado

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="Configure el aprovisionamiento automático del módulo de administración de amenazas y vulnerabilidades de Microsoft desde Azure Security Center.":::

La solución elegida se habilitará automáticamente en las máquinas admitidas.

Obtenga más información en [Configuración automática de la evaluación de vulnerabilidades para las máquinas](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>Se han agregado filtros de inventario de software agregados al inventario de recursos (en versión preliminar)

La página de [inventario de recursos](asset-inventory.md) ahora incluye un filtro para seleccionar las máquinas que ejecutan software específico e incluso especificar las versiones de interés. 

Además, puede consultar los datos del inventario de software en **Azure Resource Graph Explorer**.

Para usar estas nuevas características, deberá habilitar la [integración con Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md). 

Para obtener detalles completos, incluidas las consultas de Kusto de ejemplo para Azure Resource Graph, consulte [Acceso a un inventario de software](asset-inventory.md#access-a-software-inventory).

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="Si ha habilitado la solución de amenazas y vulnerabilidades, el inventario de recursos de Security Center ofrece un filtro para seleccionar los recursos por su software instalado.":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Cambio del prefijo de algunos tipos de alerta de "ARM_" a "VM_" 

En julio de 2021 anunciamos una [reorganización lógica de Azure Defender para alertas de Resource Manager](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts). 

Como parte de una reorganización lógica de algunos de los planes de Azure Defender, hemos trasladado veintiuna alertas de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) a [Azure Defender para servidores](defender-for-servers-introduction.md).

Con esta actualización, hemos cambiado los prefijos de estas alertas para que coincidan con esta reasignación, y hemos reemplazado "ARM_" por "VM_", tal como se muestra en la tabla siguiente:

| Nombre original                                  | A partir de este cambio                              |
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
|||

Obtenga más información sobre los planes de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) y [Azure Defender para servidores](defender-for-servers-introduction.md).

### <a name="changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters"></a>Cambios en la lógica de una recomendación de seguridad para clústeres de Kubernetes

La recomendación "Los clústeres de Kubernetes no deben usar el espacio de nombres predeterminado" impide el uso del espacio de nombres predeterminado para un intervalo de tipos de recursos. Se han eliminado dos de los tipos de recursos incluidos en esta recomendación: ConfigMap y Secret. 

Obtenga más información sobre esta recomendación y la seguridad de los clústeres de Kubernetes en [Descripción de Azure Policy para clústeres de Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md).

### <a name="recommendations-details-pages-now-show-related-recommendations"></a>Las páginas de detalles de recomendaciones ahora muestran recomendaciones relacionadas

Para aclarar las relaciones entre las distintas recomendaciones, hemos agregado un área de **recomendaciones relacionadas** a las páginas de detalles de muchas recomendaciones. 

Los tres tipos de relación que se muestran en estas páginas son los siguientes:

- **Requisito previo**: una recomendación que debe completarse antes de la recomendación seleccionada.
- **Alternativa**: una recomendación distinta que proporciona otra manera de lograr los objetivos de la recomendación seleccionada.
- **Dependiente**: una recomendación para la cual la recomendación seleccionada es un requisito previo.

Para cada recomendación relacionada, el número de recursos incorrectos se muestra en la columna "Recursos afectados".

> [!TIP]
> Si una recomendación relacionada está atenuada, su dependencia aún no se ha completado y, por tanto, no está disponible.

Un ejemplo de recomendaciones relacionadas:

1. Security Center comprueba si las máquinas admiten soluciones de evaluación de vulnerabilidades:<br>
    **Debe habilitarse una solución de evaluación de vulnerabilidades en sus máquinas virtuales**

1. Si se encuentra una, se le notificará sobre las vulnerabilidades detectadas:<br>
    **Es necesario corregir las vulnerabilidades de las máquinas virtuales**

Obviamente, Security Center no puede notificarle sobre las vulnerabilidades detectadas a menos que encuentre soluciones de evaluación de vulnerabilidades admitidas.

Por lo tanto:

 - La primera recomendación es un requisito previo de la segunda
 - La segunda recomendación depende de la primera

:::image type="content" source="media/release-notes/related-recommendations-solution-not-found.png" alt-text="Captura de pantalla de la recomendación para implementar la solución de evaluación de vulnerabilidades.":::

:::image type="content" source="media/release-notes/related-recommendations-vulnerabilities-found.png" alt-text="Captura de pantalla de la recomendación para resolver las vulnerabilidades detectadas.":::



### <a name="new-alerts-for-azure-defender-for-kubernetes-in-preview"></a>Nuevas alertas para Azure Defender para Kubernetes (en versión preliminar)

Para expandir las protecciones contra amenazas proporcionadas por Azure Defender para Kubernetes, hemos agregado dos alertas de versión preliminar.

Estas alertas se generan en función de un nuevo modelo de aprendizaje automático y análisis avanzado de Kubernetes, ya que miden varios atributos de implementación y asignación de roles con respecto a las actividades anteriores del clúster y en todos los clústeres que supervisa Azure Defender.

| Alerta (tipo de alerta)                                                                 | Descripción                                                                                                                                                                                                                                                                                                                                                      | Táctica MITRE | severity |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| **Implementación de pods anómalos (versión preliminar)**<br>(K8S_AnomalousPodDeployment)             | El análisis del registro de auditoría de Kubernetes detectó una implementación de pods, lo que es anómalo según la actividad de implementación de pods anterior. Esta actividad se considera una anomalía al tener en cuenta cómo las distintas características que se ven en la operación de implementación se relacionan entre sí. Entre las características supervisadas por este análisis se incluye el registro de imágenes de contenedor usado, la cuenta que realiza la implementación, el día de la semana, la frecuencia con la que esta cuenta realiza implementaciones de pods, el agente de usuario utilizado en la operación, si se trata de un espacio de nombres en que la implementación de pods se da a menudo, etc. Las razones que contribuyen a la notificación de esta alerta como actividad anómala se detallan en las propiedades extendidas de la alerta. | Ejecución | Media |
| **Permisos de rol excesivos asignados en el clúster de Kubernetes (versión preliminar)**<br>(K8S_ServiceAcountPermissionAnomaly) | El análisis de los registros de auditoría de Kubernetes detectó una asignación excesiva de roles de permisos en el clúster. Al examinar las asignaciones de roles, los permisos enumerados no son comunes para la cuenta de servicio específica. Esta detección tiene en cuenta las asignaciones de roles anteriores a la misma cuenta de servicio en los clústeres supervisados por Azure, el volumen por permiso y el impacto del permiso específico. El modelo de detección de anomalías usado para esta alerta tiene en cuenta cómo se usa este permiso en todos los clústeres supervisados por Azure Defender. | Elevación de privilegios | Bajo |
|||

Para ver una lista completa de las alertas de Kubernetes, consulte [Alertas de clústeres de Kubernetes](alerts-reference.md#alerts-k8scluster).

## <a name="september-2021"></a>Septiembre de 2021

En septiembre, se publicó la siguiente actualización:

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>Dos nuevas recomendaciones para auditar las configuraciones del sistema operativo para el cumplimiento de la línea de base de seguridad de Azure (en versión preliminar)

Se han publicado las dos recomendaciones siguientes para evaluar el cumplimiento de las máquinas con la [línea de base de seguridad de Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) y la [línea de base de seguridad de Linux](../governance/policy/samples/guest-configuration-baseline-linux.md):

- En las máquinas Windows, las [vulnerabilidades de la configuración de seguridad de las máquinas Windows deben corregirse (con tecnología de Configuración de invitado)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6).
- En las máquinas Linux, las [vulnerabilidades de la configuración de seguridad de las máquinas Linux deben corregirse (con tecnología de Configuración de invitado)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda).

Estas recomendaciones usan la característica Configuración de invitado de Azure Policy para comparar la configuración del sistema operativo de una máquina con la línea de base definida en [Azure Security Benchmark](/security/benchmark/azure/overview).

Obtenga más información sobre el uso de estas recomendaciones en la [configuración del sistema operativo de una máquina mediante la configuración de invitado](apply-security-baseline.md).

## <a name="august-2021"></a>Agosto de 2021

Las actualizaciones de agosto incluyen:

- [Microsoft Defender para punto de conexión para Linux ahora es compatible con Azure Defender para servidores (en versión preliminar)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [Dos nuevas recomendaciones para administrar soluciones de protección de puntos de conexión (en versión preliminar)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [Solución de problemas integrada y guía para resolver problemas comunes](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [Informes de auditoría de Azure del panel de cumplimiento normativo publicados para disponibilidad general (GA)](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- [Entra en desuso la recomendación "Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas"](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [Azure Defender para registros de contenedor ahora examina si hay vulnerabilidades en los registros protegidos con Azure Private Link](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [Ahora, Security Center puede aprovisionar automáticamente la extensión de configuración de invitado de Azure Policy (en versión preliminar)](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [Recomendaciones para habilitar los planes Azure Defender ahora admiten "Aplicar"](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [Las exportaciones CSV de datos de recomendación ahora se limitan a 20 MB](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [La página de recomendaciones ahora incluye varias vistas](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>Microsoft Defender para punto de conexión para Linux ahora es compatible con Azure Defender para servidores (en versión preliminar)

[Azure Defender para servidores](defender-for-servers-introduction.md) incluye una licencia integrada para [Microsoft Defender para punto de conexión](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Esta integración ofrece funcionalidades completas de detección y respuesta (EDR) de puntos de conexión.

Cuando Defender for Endpoint detecta una amenaza, desencadena una alerta. La alerta se muestra en Security Center. En Security Center, también puede dinamizar hasta la consola de Defender for Endpoint para realizar una investigación detallada y descubrir el alcance del ataque.

En el período de versión preliminar, el sensor [Defender para punto de conexión para Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) se implementará en las máquinas Linux compatibles de una de estas dos maneras, en función de si ya lo ha implementado en las máquinas Windows:

- [Usuarios existentes con las características de seguridad mejorada de Defender for Cloud habilitadas y Microsoft Defender para punto de conexión para Windows](integration-defender-for-endpoint.md#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [Nuevos usuarios que no han habilitado nunca la integración con Microsoft Defender para punto de conexión para Windows](integration-defender-for-endpoint.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

Para más información, consulte [Proteja los puntos de conexión con la solución EDR integrada de Security Center: Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md).

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>Dos nuevas recomendaciones para administrar soluciones de protección de puntos de conexión (en versión preliminar)

Hemos agregado dos recomendaciones **en versión preliminar** para implementar y mantener las soluciones de protección del punto de conexión en las máquinas. Ambas recomendaciones incluyen compatibilidad con máquinas virtuales de Azure y máquinas conectadas a servidores habilitados para Azure Arc.

|Recomendación |Descripción |severity |
|---|---|---|
|[La protección de los puntos de conexión debe instalarse en las máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |Para proteger las máquinas frente a amenazas y vulnerabilidades, instale una solución Endpoint Protection compatible.  <br> <a href="/azure/security-center/endpoint-protection-recommendations-technical">Obtenga más información sobre la forma en que se evalúa Endpoint Protection para máquinas</a>.<br />(Directiva relacionada: [supervisar la falta de Endpoint Protection en Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Alto |
|[Los problemas de estado de protección de puntos de conexión se deben resolver en las máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |Resuelva los problemas del estado de la protección de los puntos de conexión en las máquinas virtuales para protegerlas frente a amenazas y vulnerabilidades más recientes. Las soluciones de protección del punto de conexión admitidas por Azure Security Center se documentan [aquí](./supported-machines-endpoint-solutions-clouds.md?tabs=features-windows). La evaluación de la protección del punto de conexión se documenta <a href='/azure/security-center/endpoint-protection-recommendations-technical'>aquí</a>.<br />(Directiva relacionada: [supervisar la falta de protección de puntos de conexión en Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Media |
|||

> [!NOTE]
> Las recomendaciones muestran su intervalo de actualización como 8 horas, pero hay algunos escenarios en los que se puede tardar mucho más. Por ejemplo, cuando se elimina una máquina local, Security Center tarda 24 horas en identificar la eliminación. Después, la valoración tardará un máximo de 8 horas en devolver la información. Por consiguiente, en esa situación concreta, se pueden tardar 32 horas en quitar la máquina de la lista de recursos afectados.
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="Indicador del intervalo de actualización de estas dos nuevas recomendaciones de Security Center":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>Solución de problemas integrada y guía para resolver problemas comunes

Una nueva área dedicada de las páginas de Security Center de Azure Portal proporciona un conjunto recopilado y en constante crecimiento de materiales de autoayuda para resolver los desafíos comunes relacionados con Security Center y Azure Defender.

Si se enfrenta a un problema o busca asesoramiento de nuestro equipo de soporte técnico, **Diagnose and solve problems** (Diagnosticar y resolver problemas) es otra herramienta que puede ayudarle a encontrar la solución:

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Página &quot;Diagnose and solve problems&quot; (Diagnosticar y resolver problemas) de Security Center":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>Informes de auditoría de Azure del panel de cumplimiento normativo publicados para disponibilidad general (GA)

La barra de herramientas del panel de cumplimiento normativo ofrece informes de certificación de Azure y Dynamics para los estándares aplicados a las suscripciones. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barra de herramientas del panel de cumplimiento normativo que muestra el botón para generar informes de auditoría.":::

Puede seleccionar la pestaña correspondiente a cada tipo de informe pertinente (PCI, SOC, ISO y otros) y usar filtros para buscar los informes específicos que necesita.

Para más información, consulte [Generación de informes y certificados de estado de cumplimiento.](regulatory-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="Listas con pestañas de los informes de auditoría de Azure disponibles. Se muestran las pestañas de los informes ISO, informes SOC, PCI, etc.":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>Entra en desuso la recomendación "Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas"

Hemos detectado que la recomendación **Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas** afecta a las puntuaciones de seguridad de maneras incoherentes con el enfoque de administración de la posición de seguridad en la nube (CSPM) de Security Center. Normalmente, CSPM se relaciona con la identificación de errores de configuración de seguridad. Los problemas de estado del agente no encajan en esta categoría de problemas.

Además, la recomendación es una anomalía en comparación con los demás agentes relacionados con Security Center: este es el único agente con una recomendación relacionada con problemas de estado.

La recomendación entra en desuso.

Como resultado de esta entrada en desuso, también hemos realizado pequeños cambios en las recomendaciones sobre la instalación del agente de Log Analytics (**el agente de Log Analytics se debe instalar en...** ).

Es probable que este cambio afecte a las puntuaciones de seguridad. Para la mayoría de las suscripciones, esperamos que el cambio lleve a una puntuación mayor, pero es posible que las actualizaciones de la recomendación de instalación puedan dar lugar a una disminución de las puntuaciones en algunos casos.

> [!TIP]
> La página de [inventario de recursos](asset-inventory.md) también se ha visto afectada por este cambio, ya que muestra el estado de supervisión de las máquinas (supervisada, no supervisada o parcialmente supervisada, un estado que hace referencia a un agente con problemas de estado).


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>Azure Defender para registros de contenedor ahora examina si hay vulnerabilidades en los registros protegidos con Azure Private Link
Azure Defender para registros de contenedor incluye un detector de vulnerabilidades para detectar imágenes en los registros de Azure Container Registry. Aprenda a examinar los registros y corregir los resultados en [Uso de Azure Defender para registros de contenedor para examinar las imágenes en busca de vulnerabilidades](defender-for-container-registries-usage.md).

Para limitar el acceso a un registro hospedado en Azure Container Registry, asigne direcciones IP privadas de red virtual a los puntos de conexión del Registro y use Azure Private Link como se explica en [Conexión privada a un registro de contenedor de Azure mediante Azure Private Link](../container-registry/container-registry-private-link.md).

Como parte de nuestros esfuerzos continuos para admitir más entornos y casos de uso, Azure Defender ahora también examina los registros de contenedor protegidos con [Azure Private Link](../private-link/private-link-overview.md).


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>Ahora, Security Center puede aprovisionar automáticamente la extensión de configuración de invitado de Azure Policy (en versión preliminar)
Azure Policy puede auditar la configuración dentro de un equipo, tanto para las máquinas que se ejecutan en Azure como para las conectadas a Arc. La validación se realiza mediante el cliente y la extensión Guest Configuration. Puede encontrar más información en [Información sobre Guest Configuration de Azure Policy](../governance/policy/concepts/guest-configuration.md).

Con esta actualización, ahora puede configurar Security Center para que aprovisione automáticamente esta extensión en todas las máquinas admitidas. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="Habilite la implementación automática de la extensión de configuración de invitado.":::

Obtenga más información sobre cómo funciona el aprovisionamiento automático en [Configuración del aprovisionamiento automático para agentes y extensiones](enable-data-collection.md).

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>Recomendaciones para habilitar los planes Azure Defender ahora admiten "Aplicar"
Security Center incluye dos características que ayudan a garantizar que los recursos recién creados se aprovisionan de forma segura: **aplicar** y **denegar**. Cuando una recomendación ofrece estas opciones, puede tener la certeza de que se cumplen los requisitos de seguridad cada vez que alguien intenta crear un recurso:

- **Denegar** impide la creación de recursos incorrectos
- **Aplicar** corrige automáticamente los recursos no compatibles cuando se crean

Con esta actualización, la opción de aplicar ahora está disponible en las recomendaciones para habilitar los planes de Azure Defender (por ejemplo, **Azure Defender para App Service debe estar habilitado**, **Azure Defender para Key Vault debe estar habilitado**, **Azure Defender para Storage debe estar habilitado**).

Encontrará más información sobre estas opciones en [Evitar errores de configuración con las recomendaciones Aplicar o Denegar](prevent-misconfigurations.md).

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>Las exportaciones CSV de datos de recomendación ahora se limitan a 20 MB

Estamos estableciendo un límite de 20 MB al exportar datos de recomendaciones de Security Center.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="Botón &quot;Descargar informe CSV&quot; de Security Center para exportar datos de recomendaciones.":::

Si tiene que exportar mayor cantidad de datos, use los filtros disponibles antes de seleccionarlos, o bien seleccione subconjuntos de las suscripciones y descargue los datos en lotes.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Filtrado de suscripciones en Azure Portal.":::

Más información sobre [cómo realizar una exportación de archivos .csv de las recomendaciones de seguridad](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations).



### <a name="recommendations-page-now-includes-multiple-views"></a>La página de recomendaciones ahora incluye varias vistas

La página de recomendaciones ahora tiene dos pestañas para proporcionar formas alternativas de ver las recomendaciones pertinentes para los recursos:

- **Recomendaciones de puntuación segura**: esta pestaña se usa para ver la lista de recomendaciones agrupadas por control de seguridad. Puede encontrar más información sobre estos controles en [Controles de seguridad y sus recomendaciones.](secure-score-security-controls.md#security-controls-and-their-recommendations)
- **Todas las recomendaciones**: esta pestaña se usa para ver las recomendaciones en forma de lista plana. Esta pestaña también es excelente para saber qué iniciativa (incluidos los estándares de cumplimiento normativo) generó la recomendación. Para más información sobre las iniciativas y su relación con las recomendaciones, consulte [¿Qué son las directivas de seguridad, las iniciativas y las recomendaciones?](security-policy-concept.md).

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Pestañas para cambiar la vista de la lista de recomendaciones en Azure Security Center.":::

## <a name="july-2021"></a>Julio de 2021

Las actualizaciones de julio incluyen:

- [El conector de Azure Sentinel ahora incluye la sincronización de alertas bidireccional opcional (en versión preliminar)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Reorganización lógica de Azure Defender para alertas de Resource Manager](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [Mejoras en la recomendación para habilitar Azure Disk Encryption](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [Exportación continua de puntuación segura y datos de cumplimiento normativo publicados para disponibilidad general (GA)](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [Las automatizaciones de los flujos de trabajo se pueden desencadenar mediante los cambios en las evaluaciones de cumplimiento normativo (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [Los campos de API de evaluaciones "FirstEvaluationDate" y "StatusChangeDate" ahora están disponibles en los esquemas de área de trabajo y las aplicaciones lógicas](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [Se ha agregado la plantilla del libro "Cumplimiento a lo largo del tiempo" a la galería Azure Monitor Workbooks](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>El conector de Azure Sentinel ahora incluye la sincronización de alertas bidireccional opcional (en versión preliminar)

Security Center se integra de forma nativa con [Azure Sentinel](../sentinel/index.yml), la solución SOAR y SIEM nativa de nube de Azure. 

Azure Sentinel incluye conectores integrados para Azure Security Center en los niveles de suscripción y de inquilino. Encontrará más información en [Transmisión de alertas a Azure Sentinel](export-to-siem.md#stream-alerts-to-microsoft-sentinel).

Cuando se conecta Azure Defender a Azure Sentinel, el estado de las alertas de Azure Defender que se ingieren en Azure Sentinel se sincroniza entre los dos servicios. Por ejemplo, cuando se cierra una alerta en Azure Defender, también se mostrará como cerrada en Azure Sentinel. El cambio del estado de una alerta en Azure Defender "no"* afectará al estado de los **incidentes** de Azure Sentinel que contienen la alerta de Azure Sentinel sincronizada, solo al de la propia alerta sincronizada.

Al habilitar esta característica en vista previa, **sincronización de alertas bidireccional**, se sincronizará automáticamente el estado de las alertas de Azure Defender originales con incidentes de Azure Sentinel que contienen las copias de esas alertas de Azure Defender. Por ejemplo, cuando se cierra un incidente de Azure Sentinel que contiene una alerta de Azure Defender, Azure Defender cerrará automáticamente la alerta original correspondiente.

Encontrará más información al respecto en [Conexión de alertas de Azure Defender desde Azure Security Center](../sentinel/connect-azure-security-center.md).

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Reorganización lógica de Azure Defender para alertas de Resource Manager

Las alertas que aparecen a continuación se proporcionaron como parte del plan de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md).

Como parte de una reorganización lógica de algunos de los planes de Azure Defender, hemos trasladado algunas alertas de **Azure Defender para Resource Manager** a **Azure Defender para servidores**.

Las alertas se organizan según dos principios fundamentales:

- Las alertas que proporcionan protección del plano de control (en muchos tipos de recursos de Azure) forman parte de Azure Defender para Resource Manager.
- Las alertas que protegen cargas de trabajo específicas se encuentran en el plan de Azure Defender relacionado con la carga de trabajo correspondiente.

Estas son las alertas que formaban parte de Azure Defender para Resource Manager y que, como resultado de este cambio, ahora forman parte de Azure Defender para servidores:

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

Obtenga más información sobre los planes de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) y [Azure Defender para servidores](defender-for-servers-introduction.md).


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>Mejoras en la recomendación para habilitar Azure Disk Encryption

Tras los comentarios de los usuarios, se ha cambiado el nombre de la recomendación **El cifrado de disco se debe aplicar a las máquinas virtuales**.

La nueva recomendación usa el mismo identificador de evaluación y se denomina **Las máquinas virtuales deben cifrar los discos temporales, las memorias caché y los flujos de datos entre los recursos de proceso y almacenamiento**.

La descripción también se ha actualizado para explicar mejor el propósito de esta recomendación de seguridad:

| Recomendación                                                                                               | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | severity |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **Las máquinas virtuales deben cifrar los discos temporales, las cachés y los flujos de datos entre los recursos de Proceso y Almacenamiento** | De forma predeterminada, los discos de datos y del sistema operativo de una máquina virtual se cifran en reposo mediante claves administradas por la plataforma; los discos temporales y las cachés de datos no se cifran y los datos no se cifran cuando fluyen entre los recursos de proceso y almacenamiento. Para ver una comparación de las distintas tecnologías de cifrado de disco en Azure, consulte, https://aka.ms/diskencryptioncomparison.<br>Use Azure Disk Encryption para cifrar todos estos datos. Ignore esta recomendación si: (1) usa la característica de cifrado en host o (2) el cifrado del servidor en Managed Disks cumple los requisitos de seguridad. Encontrará más información en Cifrado del lado servidor de Azure Disk Storage. | Alto     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>Exportación continua de puntuación segura y datos de cumplimiento normativo publicados para disponibilidad general (GA)

La [exportación continua](continuous-export.md) proporciona el mecanismo para exportar las alertas de seguridad y las recomendaciones necesarias para realizar el seguimiento con otras herramientas de supervisión de su entorno.

Al configurar la exportación continua, se configura lo que se exporta y el lugar al que irán los elementos exportados. Encontrará más información al respecto en la [introducción a la exportación continua](continuous-export.md).

Esta característica se ha mejorado y ampliado:

- En noviembre de 2020, se agregó la opción de **vista previa** para transmitir en secuencias los cambios a su **puntuación segura**.<br/>En [La puntuación de seguridad ahora está disponible en la exportación continua (versión preliminar)](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview), encontrará todos los detalles.

- En diciembre de 2020, se agregó la opción de **vista previa** para transmitir en secuencias los cambios a sus **datos de evaluación de cumplimiento normativo**.<br/>Para ver todos los detalles, consulte la sección [La exportación continua obtiene nuevos tipos de datos y directivas deployifnotexist mejoradas](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies).

Con esta actualización, estas dos opciones se liberan para disponibilidad general (GA). 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>Las automatizaciones de los flujos de trabajo se pueden desencadenar mediante los cambios en las evaluaciones de cumplimiento normativo (GA)

En febrero de 2021, se agregó un tercer tipo de datos en **vista previa** a las opciones del desencadenador para las automatizaciones del flujo de trabajo: cambios en las evaluaciones de cumplimiento normativo. Obtendrá más información en [Las automatizaciones de los flujos de trabajo se pueden desencadenar mediante cambios en las evaluaciones de cumplimiento normativo](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview).

Con esta actualización, esta opción del desencadenador se libera para disponibilidad general (GA).

Aprenda a usar las herramientas de automatización del flujo de trabajo en [Automatización de respuestas a desencadenadores de Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Uso de los cambios en las evaluaciones de cumplimiento normativo para desencadenar la automatización de un flujo de trabajo." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>Los campos de API de evaluaciones "FirstEvaluationDate" y "StatusChangeDate" ahora están disponibles en los esquemas de área de trabajo y las aplicaciones lógicas

En mayo de 2021, se actualizó Assessment API con dos nuevos campos, **FirstEvaluationDate** y **StatusChangeDate**. Para ver todos los detalles al respecto, consulte [Assessment API se ha ampliado con dos nuevos campos](release-notes-archive.md#assessments-api-expanded-with-two-new-fields).

Se puede acceder a esos campos a través de la API REST, Azure Resource Graph, la exportación continua y en las exportaciones de CSV.

Con este cambio, la información estará disponible en el esquema del área de trabajo de Log Analytics y desde las aplicaciones lógicas.


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>Se ha agregado la plantilla del libro "Cumplimiento a lo largo del tiempo" a la galería Azure Monitor Workbooks

En marzo, se anunció la experiencia integrada de Azure Monitor Workbooks en Security Center (consulte [Se integran en Security Center los libros de Azure Monitor y se proporcionan tres plantillas](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)).

La versión inicial incluía tres plantillas para crear informes dinámicos y visuales sobre la posición de seguridad de la organización.

Ahora se ha agregado un libro dedicado a realizar un seguimiento del cumplimiento que realiza una suscripción de los estándares normativos o del sector que se le aplican. 

Obtenga información sobre el uso de estos informes o sobre la creación de sus propios informes en [Creación de informes enriquecidos e interactivos con los datos de Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Libro del cumplimiento con el paso del tiempo de Azure Security Center":::


## <a name="june-2021"></a>Junio de 2021

Las actualizaciones de junio incluyen:

- [Nueva alerta para Azure Defender para Key Vault](#new-alert-for-azure-defender-for-key-vault)
- [Recomendaciones para cifrar con claves administradas por el cliente (CMK) deshabilitadas de manera predeterminada](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [El prefijo de las alertas de Kubernetes cambia de "AKS_" a "K8S_"](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [Han entrado en desuso dos recomendaciones del control de seguridad "Aplicar actualizaciones del sistema".](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Nueva alerta para Azure Defender para Key Vault

Para expandir las protecciones contra amenazas proporcionadas por Azure Defender para Key Vault, hemos agregado la siguiente alerta:

| Alerta (tipo de alerta)                                                                 | Descripción                                                                                                                                                                                                                                                                                                                                                      | Táctica MITRE | severity |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Acceso desde una dirección IP sospechosa a un almacén de claves<br>(KV_SuspiciousIPAccess)  | Una dirección IP identificada por la inteligencia sobre amenazas de Microsoft como una dirección IP sospechosa accedió correctamente a un almacén de claves. Esto puede indicar que la infraestructura se ha puesto en peligro. Se recomienda seguir investigando. Obtenga más información sobre las [funcionalidades de inteligencia sobre amenazas de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). | Acceso con credenciales                            | Media   |
|||

Para más información, consulte:
- [Introducción a Azure Defender para Key Vault](defender-for-resource-manager-introduction.md)
- [Respuesta a las alertas de Azure Defender para Key Vault](defender-for-key-vault-usage.md)
- [Lista de alertas proporcionadas por Azure Defender para Key Vault](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>Recomendaciones para cifrar con claves administradas por el cliente (CMK) deshabilitadas de manera predeterminada

Security Center incluye varias recomendaciones para cifrar los datos en reposo con claves administradas por el cliente, como:

- Las instancias de Container Registry se deben cifrar con una clave administrada por el cliente (CMK)
- Las cuentas de Azure Cosmos DB deben usar claves administradas por el cliente para cifrar los datos en reposo
- Las áreas de trabajo de Azure Machine Learning deben cifrarse con una clave administrada por el cliente (CMK).

Los datos de Azure se cifran automáticamente mediante claves administradas por la plataforma, por lo que el uso de claves administradas por el cliente solo se debe aplicar cuando sea necesario para el cumplimiento de una directiva específica que la organización decida aplicar.

Con este cambio, las recomendaciones para usar CMK ahora están **deshabilitadas de manera predeterminada**. Cuando sea pertinente para su organización, puede habilitarlas cambiando el parámetro *Effect* (Efecto) de la directiva de seguridad correspondiente a **AuditIfNotExists** o **Enforce**. Más información en [Habilitación de una directiva de seguridad](tutorial-security-policy.md#enable-a-security-policy).

Este cambio se refleja en los nombres de la recomendación con un nuevo prefijo, **[Habilitar si es necesario]** , como se muestra en los ejemplos siguientes:

- [Habilitar si es necesario] Las cuentas de almacenamiento deben usar claves administradas por el cliente para cifrar los datos en reposo.
- [Habilitar si es necesario] Los registros de contenedor se deben cifrar con una clave administrada por el cliente (CMK).
- [Habilitar si es necesario] Las cuentas de Azure Cosmos DB deben usar claves administradas por el cliente para cifrar los datos en reposo.

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Las recomendaciones sobre CMK de Security Center estarán deshabilitadas de manera predeterminada." lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>El prefijo de las alertas de Kubernetes cambia de "AKS_" a "K8S_"

Azure Defender para Kubernetes se amplió recientemente para proteger los clústeres de Kubernetes hospedados en entornos locales y de varias nubes. Puede encontrar más información en [Uso de Azure Defender para Kubernetes para proteger implementaciones de Kubernetes híbridas y de varias nubes (en versión preliminar)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

Para reflejar el hecho de que las alertas de seguridad proporcionadas por Azure Defender para Kubernetes ya no están restringidas a los clústeres de Azure Kubernetes Service, hemos cambiado el prefijo de los tipos de alerta de "AKS_" a "K8S_". Cuando ha sido necesario, los nombres y las descripciones también se han actualizado. Por ejemplo, esta alerta:

|Alerta (tipo de alerta)|Descripción|
|----|----|
|Se ha detectado la herramienta de pruebas de penetración de Kubernetes.<br>(**AKS** _PenTestToolsKubeHunter)|El análisis del registro de auditoría de Kubernetes ha detectado el uso de la herramienta de pruebas de penetración de Kubernetes en el clúster de **AKS**. Aunque este comportamiento puede ser legítimo, los atacantes podrían usar estas herramientas públicas con fines malintencionados.
|||

Ha cambiado a:

|Alerta (tipo de alerta)|Descripción|
|----|----|
|Se ha detectado la herramienta de pruebas de penetración de Kubernetes.<br>(**K8S** _PenTestToolsKubeHunter)|El análisis del registro de auditoría de Kubernetes ha detectado el uso de la herramienta de pruebas de penetración de Kubernetes en el clúster de **Kubernetes**. Aunque este comportamiento puede ser legítimo, los atacantes podrían usar estas herramientas públicas con fines malintencionados.|
|||

Las reglas de eliminación que hacen referencia a las alertas que comienzan por "AKS_" se han convertido automáticamente. Si ha configurado exportaciones de SIEM o scripts de automatización personalizados que hacen referencia a las alertas de Kubernetes por tipo de alerta, deberá actualizarlos con los nuevos tipos de alerta.

Para ver una lista completa de las alertas de Kubernetes, consulte [Alertas de clústeres de Kubernetes](alerts-reference.md#alerts-k8scluster).

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>Han entrado en desuso dos recomendaciones del control de seguridad "Aplicar actualizaciones del sistema".

Las dos recomendaciones siguientes han entrado en desuso:

- **La versión del sistema operativo debe actualizarse en los roles del servicio en la nube**: de manera predeterminada, Azure actualiza periódicamente el sistema operativo invitado a la imagen compatible más reciente dentro de la familia de sistema operativo que ha especificado en la configuración del servicio (.cscfg), como Windows Server 2016.
- Los **servicios de Kubernetes deben actualizarse a una versión de Kubernetes no vulnerable**: las evaluaciones de esta recomendación no son tan amplias como nos gustaría. Tenemos previsto reemplazar la recomendación por una versión mejorada que se ajuste mejor a sus necesidades de seguridad.
