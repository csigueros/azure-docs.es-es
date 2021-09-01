---
title: Notas de la versión de Azure Security Center
description: Una descripción de las novedades y los cambios en Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 08/15/2021
ms.author: memildin
ms.openlocfilehash: 04cff79878c3110c6da8b40e7a207693e693e2ba
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229039"
---
# <a name="whats-new-in-azure-security-center"></a>Novedades de Azure Security Center

Defender está en desarrollo activo y recibe mejoras continuas. Para mantenerse al día con los avances más recientes, esta página proporciona información sobre las nuevas características, las correcciones de errores y las funcionalidades en desuso.

Esta página se actualiza con frecuencia, por lo que se recomienda visitarla a menudo. 

Para obtener información sobre los cambios *planeados* que están próximos a materializarse en Security Center, consulte el artículo [Próximos cambios importantes en Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Si busca elementos de más de 6 meses, puede encontrarlos en las [Novedades de Azure Security Center](release-notes-archive.md).

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

- [Usuarios existentes de Azure Defender y Microsoft Defender para punto de conexión para Windows](security-center-wdatp.md?tabs=linux#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [Nuevos usuarios que no han habilitado nunca la integración con Microsoft Defender para punto de conexión para Windows](security-center-wdatp.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

Para más información, consulte [Proteja los puntos de conexión con la solución EDR integrada de Security Center: Microsoft Defender para punto de conexión](security-center-wdatp.md).

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>Dos nuevas recomendaciones para administrar soluciones de protección de puntos de conexión (en versión preliminar)

Hemos agregado dos recomendaciones **en versión preliminar** para implementar y mantener las soluciones de protección del punto de conexión en las máquinas. Ambas recomendaciones incluyen la compatibilidad con máquinas virtuales de Azure y máquinas conectadas a servidores habilitados para Azure Arc.

|Recomendación |Descripción |severity |
|---|---|---|
|[La protección de los puntos de conexión debe instalarse en las máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |Para proteger las máquinas frente a amenazas y vulnerabilidades, instale una solución Endpoint Protection compatible.  <br> <a href="/azure/security-center/security-center-endpoint-protection">Obtenga más información sobre la forma en que se evalúa Endpoint Protection para máquinas</a>.<br />(Directiva relacionada: [supervisar la falta de Endpoint Protection en Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Alto |
|[Los problemas de estado de protección de puntos de conexión se deben resolver en las máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |Resuelva los problemas del estado de la protección de los puntos de conexión en las máquinas virtuales para protegerlas frente a amenazas y vulnerabilidades más recientes. Las soluciones de protección del punto de conexión admitidas por Azure Security Center se documentan [aquí](./security-center-services.md?tabs=features-windows). La evaluación de la protección del punto de conexión se documenta <a href='/azure/security-center/security-center-endpoint-protection'>aquí</a>.<br />(Directiva relacionada: [supervisar la falta de protección de puntos de conexión en Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Media |
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

Para más información, consulte [Generación de informes y certificados de estado de cumplimiento.](security-center-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)

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

Con esta actualización, ahora puede configurar que Security Center aprovisione automáticamente esta extensión en todas las máquinas admitidas. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="Habilite la implementación automática de la extensión de configuración de invitado.":::

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

Azure Sentinel incluye conectores integrados para Azure Security Center en los niveles de suscripción y de inquilino. Encontrará más información en [Transmisión de alertas a Azure Sentinel](export-to-siem.md#stream-alerts-to-azure-sentinel).

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
- ARM_AmMalwareCampaignRelatedExclusion
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

En mayo de 2021, se actualizó Assessment API con dos nuevos campos, **FirstEvaluationDate** y **StatusChangeDate**. Para ver todos los detalles al respecto, consulte [Assessment API se ha ampliado con dos nuevos campos](#assessments-api-expanded-with-two-new-fields).

Se puede acceder a esos campos a través de la API REST, Azure Resource Graph, la exportación continua y en las exportaciones de CSV.

Con este cambio, la información estará disponible en el esquema del área de trabajo de Log Analytics y desde las aplicaciones lógicas.


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>Se ha agregado la plantilla del libro "Cumplimiento a lo largo del tiempo" a la galería Azure Monitor Workbooks

En marzo, se anunció la experiencia integrada de Azure Monitor Workbooks en Security Center (consulte [Se integran en Security Center los libros de Azure Monitor y se proporcionan tres plantillas](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)).

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

Azure Defender para Kubernetes se amplió recientemente para proteger los clústeres de Kubernetes hospedados en entornos locales y de varias nubes. Puede encontrar más información en [Uso de Azure Defender para Kubernetes para proteger implementaciones de Kubernetes híbridas y de varias nubes (en versión preliminar)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

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


## <a name="may-2021"></a>Mayo de 2021

Las actualizaciones de mayo incluyen:

- [Lanzamiento de Azure Defender para DNS y Azure Defender para Resource Manager para disponibilidad general (GA)](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [Lanzamiento de Azure Defender para bases de datos relacionales de código abierto para disponibilidad general (GA)](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Nuevas alertas para Azure Defender para Resource Manager](#new-alerts-for-azure-defender-for-resource-manager)
- [Examen de vulnerabilidades de CI/CD de imágenes de contenedor con flujos de trabajo de GitHub y Azure Defender (versión preliminar)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [Más consultas de Resource Graph disponibles para algunas recomendaciones](#more-resource-graph-queries-available-for-some-recommendations)
- [Se ha cambiado la gravedad de la recomendación de clasificación de datos SQL](#sql-data-classification-recommendation-severity-changed)
- [Nuevas recomendaciones para habilitar las funcionalidades de inicio seguro (en versión preliminar)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Nuevas recomendaciones para reforzar los clústeres de Kubernetes (en versión preliminar)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [La API de evaluaciones se ha ampliado con dos nuevos campos](#assessments-api-expanded-with-two-new-fields)
- [El inventario de recursos obtiene un filtro de entorno de nube](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Lanzamiento de Azure Defender para DNS y Azure Defender para Resource Manager para disponibilidad general (GA)

Estos dos planes de protección contra amenazas con amplitud nativa de nube ahora están disponibles de forma general.

Estas nuevas protecciones mejoran enormemente la resistencia frente a los ataques de actores de amenazas y aumentan considerablemente el número de recursos de Azure protegidos por Azure Defender.

- **Azure Defender para Resource Manager**: supervisa automáticamente todas las operaciones de administración de recursos realizadas en la organización. Para más información, consulte:
    - [Introducción a Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)
    - [Respuesta a las alertas de Azure Defender para Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista de alertas proporcionadas por Azure Defender para Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender para DNS**: supervisa continuamente todas las consultas de DNS de los recursos de Azure. Para más información, consulte:
    - [Introducción a Azure Defender para DNS](defender-for-dns-introduction.md)
    - [Respuesta a las alertas de Azure Defender para DNS](defender-for-dns-usage.md)
    - [Lista de alertas proporcionadas por Azure Defender para DNS](alerts-reference.md#alerts-dns)

Para simplificar el proceso de habilitación de estos planes, use las recomendaciones siguientes:

- **Se debe habilitar Azure Defender para Resource Manager**
- **Se debe habilitar Azure Defender para DNS**

> [!NOTE]
> La habilitación de los planes de Azure Defender conlleva cargos. Obtenga información sobre los detalles de los precios por región en la página de precios de Security Center: https://aka.ms/pricing-security-center.


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>Lanzamiento de Azure Defender para bases de datos relacionales de código abierto para disponibilidad general (GA)

Azure Security Center amplía su oferta de protección de SQL con un nuevo conjunto para cubrir las bases de datos relacionales de código abierto:

- **Azure Defender para servidores de bases de datos Azure SQL**: defiende los servidores SQL Server nativos de Azure.
- **Azure Defender para servidores SQL Server en máquinas**: amplía las mismas protecciones a los servidores SQL Server en entornos híbridos, multinube y locales.
- **Azure Defender para bases de datos relacionales de código abierto**: protege los servidores únicos de Azure Database for MySQL, PostgreSQL y MariaDB.

Azure Defender para bases de datos relacionales de código abierto supervisa constantemente las amenazas de seguridad de los servidores y detecta actividades anómalas de base de datos que indican posibles amenazas para Azure Database for MySQL, PostgreSQL y MariaDB. Ejemplos:

- **Detección granular de ataques por fuerza bruta**: Azure Defender para bases de datos relacionales de código abierto proporciona información detallada sobre los ataques por fuerza bruta intentados y cometidos. Esto le permite investigar y responder con una comprensión más completa de la naturaleza y el estado del ataque en su entorno.
- **Detección de alertas de comportamiento**: Azure Defender para bases de datos relacionales de código abierto le alertan de comportamientos sospechosos e inesperados en los servidores, como cambios en el patrón de acceso a la base de datos.
- **Detección basada en inteligencia sobre amenazas**: Azure Defender aplica la inteligencia sobre amenazas de Microsoft y la amplia base de conocimientos para que pueda actuar contra ellas.

Obtenga más información en [Introducción a Azure Defender para bases de datos relacionales de código abierto](defender-for-databases-introduction.md).

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Nuevas alertas para Azure Defender para Resource Manager

Para expandir las protecciones contra amenazas proporcionadas por Azure Defender para Resource Manager, hemos agregado las siguientes alertas:

| Alerta (tipo de alerta)                                                                                                                                                | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                              | Tácticas MITRE | severity |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**Permisos concedidos para un rol RBAC de forma inusual para el entorno de Azure (versión preliminar)**<br>(ARM_AnomalousRBACRoleAssignment)|Azure Defender para Resource Manager detectó una asignación de roles RBAC inusual en comparación con otras asignaciones realizadas por el mismo asignador o realizadas para el mismo asignado o en el inquilino debido a las siguientes anomalías: hora de asignación, ubicación del asignador, asignador, método de autenticación, entidades asignadas, software cliente usado y extensión de asignación. Es posible que un usuario legítimo de su organización haya realizado esta operación. Como alternativa, podría indicar que se ha vulnerado una cuenta de su organización y que el actor de la amenaza intenta conceder permisos a una cuenta de usuario adicional de su propiedad.|Desplazamiento lateral, evasión de las defensas|Media|
|**Rol personalizado con privilegios creado para la suscripción de forma sospechosa (versión preliminar)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender para Resource Manager detectó una creación sospechosa de la definición de roles personalizados con privilegios en la suscripción. Es posible que un usuario legítimo de su organización haya realizado esta operación. Como alternativa, podría indicar que se ha vulnerado una cuenta de la organización y que el actor de la amenaza intenta crear un rol con privilegios para usarlo en el futuro para eludir la detección.|Desplazamiento lateral, evasión de las defensas|Bajo|
|**Operación de Azure Resource Manager desde una dirección IP sospechosa (versión preliminar)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender para Resource Manager detectó una operación desde una dirección IP que se ha marcado como sospechosa en fuentes de inteligencia sobre amenazas.|Ejecución|Media|
|**Operación de Azure Resource Manager desde una dirección IP de proxy sospechosa (versión preliminar)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender para Resource Manager ha detectado una operación de administración de recursos desde una dirección IP asociada a servicios de proxy, como TOR. Aunque este comportamiento puede ser legítimo, a menudo se considera como actividades malintencionadas, cuando los actores de las amenazas intentan ocultar su dirección IP de origen.|Evasión defensiva|Media|
||||

Para más información, consulte:
- [Introducción a Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)
- [Respuesta a las alertas de Azure Defender para Resource Manager](defender-for-resource-manager-usage.md)
- [Lista de alertas proporcionadas por Azure Defender para Resource Manager](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>Examen de vulnerabilidades de CI/CD de imágenes de contenedor con flujos de trabajo de GitHub y Azure Defender (versión preliminar)

Azure Defender para registros de contenedor ahora proporciona a los equipos de DevSecOps observabilidad en los flujos de trabajo de una Acción de GitHub.

La nueva característica de examen de vulnerabilidades para imágenes de contenedor, que utiliza Trivy, ayuda a los desarrolladores a buscar vulnerabilidades comunes en las imágenes de contenedor *antes* de insertar las imágenes en los registros de contenedor.

Los informes del examen de contenedores se resumen en Azure Security Center, lo que proporciona a los equipos de seguridad una mejor información y comprensión sobre el origen de las imágenes de contenedor vulnerables y los flujos de trabajo y repositorios desde donde se originan.

Más información en [Identificación de imágenes de contenedor vulnerables en los flujos de trabajo de CI/CD](defender-for-container-registries-cicd.md).

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>Más consultas de Resource Graph disponibles para algunas recomendaciones

Todas las recomendaciones de Security Center ofrecen la opción de ver la información sobre el estado de los recursos afectados mediante Azure Resource Graph desde la opción **Abrir consulta**. Para obtener detalles completos sobre esta eficaz característica, consulte [Revisión de los datos de recomendación en Azure Resource Graph Explorer (ARG)](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg).

Security Center incluye escáneres de vulnerabilidades integrados para examinar las VM, los servidores SQL Server y sus hosts, y los registros de contenedor en busca de vulnerabilidades de seguridad. Los resultados se devuelven como recomendaciones con todas las conclusiones individuales de cada tipo de recurso recopiladas en una sola vista. Las recomendaciones son:

- Es necesario corregir las vulnerabilidades de las imágenes de Azure Container Registry (con tecnología de Qualys)
- Es necesario corregir las vulnerabilidades de las máquinas virtuales
- Las bases de datos SQL deben tener resueltos los hallazgos de vulnerabilidades.
- Los servidores SQL de las máquinas deben tener resueltos los hallazgos de vulnerabilidades.

Con este cambio, puede usar el botón **Abrir consulta** para abrir también la consulta que muestra los hallazgos de seguridad.

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="El botón Abrir consulta ahora ofrece opciones para una consulta más profunda que muestra las conclusiones sobre seguridad de las recomendaciones relacionadas con el detector de vulnerabilidades.":::

El botón **Abrir consulta** también ofrece opciones adicionales para algunas otras recomendaciones si procede.

Obtenga más información sobre los detectores de vulnerabilidades de Security Center:

- [Detector de evaluación de vulnerabilidades integrado en Azure Defender para Azure y máquinas híbridas](deploy-vulnerability-assessment-vm.md)
- [Detector de evaluación de vulnerabilidades integrado de Azure Defender para los servidores SQL Server](defender-for-sql-on-machines-vulnerability-assessment.md)
- [Detector de evaluación de vulnerabilidades integrado de Azure Defender para los registros de contenedor](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>Se ha cambiado la gravedad de la recomendación de clasificación de datos SQL

La gravedad de la recomendación **Sensitive data in your SQL databases should be classified** (Se deben clasificar los datos confidenciales de las bases de datos SQL) ha cambiado de **Alta** a **Baja**.

Este cambio forma parte de los cambios en curso de esta recomendación anunciados en [Mejoras en la recomendación de clasificación de datos confidenciales de las bases de datos SQL](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases).

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>Nuevas recomendaciones para habilitar las funcionalidades de inicio seguro (en versión preliminar)

Azure ofrece el inicio seguro como una manera continua de mejorar la seguridad de las máquinas virtuales de [generación 2](../virtual-machines/generation-2.md). El inicio seguro protege frente a técnicas de ataque persistentes y avanzadas. El inicio seguro se compone de varias tecnologías de infraestructura coordinadas que se pueden habilitar de manera independiente. Cada tecnología proporciona otro nivel de defensa contra amenazas sofisticadas. Obtenga más información en [Inicio seguro para máquinas virtuales de Azure](../virtual-machines/trusted-launch.md).

> [!IMPORTANT]
> El inicio seguro requiere la creación de nuevas máquinas virtuales. No se puede habilitar el inicio seguro en las máquinas virtuales existentes que se crearon inicialmente sin él.
> 
> El inicio seguro está actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin acuerdo de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

La recomendación de Security Center, **vTPM debe estar habilitado en las máquinas virtuales admitidas**, garantiza que las VM de Azure usen vTPM. Esta versión virtualizada del hardware Módulo de plataforma segura permite la atestación mediante la medición de toda la cadena de arranque de la VM (UEFI, sistema operativo, sistema y controladores).

Con vTPM habilitado, la **extensión de atestación de invitado** puede validar de forma remota el arranque seguro. Las siguientes recomendaciones garantizan la implementación de esta extensión:

- **El arranque seguro debe estar habilitado en las máquinas virtuales Windows admitidas**
- **La extensión de atestación de invitados debe estar instalada en las máquinas virtuales Windows**
- **La extensión de atestación de invitados debe estar instalada en conjuntos de escalado de máquinas virtuales Windows admitidos**
- **La extensión de atestación de invitados debe estar instalada en las máquinas virtuales Linux**
- **La extensión de atestación de invitados debe estar instalada en conjuntos de escalado de máquinas virtuales Linux admitidos**

Obtenga más información en [Inicio seguro para máquinas virtuales de Azure](../virtual-machines/trusted-launch.md). 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Nuevas recomendaciones para reforzar los clústeres de Kubernetes (en versión preliminar)

Las siguientes recomendaciones le permiten reforzar aún más los clústeres de Kubernetes.

- **Los clústeres de Kubernetes no deben utilizar el espacio de nombres predeterminado**: para proteger del acceso no autorizado los tipos de recurso ConfigMap, Pod, Secret, Service y ServiceAccount, evite utilizar el espacio de nombres predeterminado en los clústeres de Kubernetes.
- **Los clústeres de Kubernetes deben deshabilitar el montaje automático de credenciales de API**: para evitar que un recurso pod potencialmente comprometido ejecute comandos de API en clústeres de Kubernetes, deshabilite el montaje automático de credenciales de API.
- **Los clústeres de Kubernetes no deben otorgar funcionalidades de seguridad CAPSYSADMIN**.

Descubra qué puede hacer Security Center para proteger los entornos contenedorizados en [Seguridad de contenedor en Security Center](container-security.md).

### <a name="assessments-api-expanded-with-two-new-fields"></a>La API de evaluaciones se ha ampliado con dos nuevos campos

Se han agregado los dos campos siguientes a la [API REST de evaluaciones](/rest/api/securitycenter/assessments):

- **FirstEvaluationDate**: hora a la que se creó y evaluó por primera vez la recomendación. Se devuelve en horario UTC en formato ISO 8601.
- **StatusChangeDate**: hora a la que cambió por última vez el estado de la recomendación. Se devuelve en horario UTC en formato ISO 8601.

El valor predeterminado inicial de estos campos (para todas las recomendaciones) es `2021-03-14T00:00:00+0000000Z`.

Para acceder a esta información, puede usar cualquiera de los métodos de la tabla siguiente.

| Herramienta                 | Detalles                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Llamada a API REST        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| Exportación continua    | Los dos campos dedicados estarán disponibles para los datos del área de trabajo de Log Analytics.                                                                                            |
| [Exportación de CSV](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | Los dos campos se incluyen en los archivos CSV.                                                        |
|                      |                                                                                                                                                                        |


Más información sobre la [API REST de evaluaciones](/rest/api/securitycenter/assessments).


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>El inventario de recursos obtiene un filtro de entorno de nube

La página del inventario de recursos de Security Center ofrece muchos filtros para refinar rápidamente la lista de recursos que se muestran. Más información en [Exploración y administración de los recursos con Asset Inventory](asset-inventory.md).

Un nuevo filtro ofrece la opción de refinar la lista según las cuentas en la nube conectadas con las características de varias nubes de Security Center:

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="Filtro de entorno del inventario":::

Obtenga más información sobre las funcionalidades de varias nubes:

- [Conexión de las cuentas de AWS a Azure Security Center](quickstart-onboard-aws.md)
- [Conexión de las cuentas de GCP a Azure Security Center](quickstart-onboard-gcp.md)


## <a name="april-2021"></a>Abril de 2021

Las actualizaciones de abril incluyen:
- [Actualización de la página de estado de recursos (en versión preliminar)](#refreshed-resource-health-page-in-preview)
- [Las imágenes del registro de contenedor que se han extraído recientemente ahora se vuelven a examinar semanalmente (lanzado en disponibilidad general)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Use Azure Defender para Kubernetes para proteger implementaciones de Kubernetes híbridas y de varias nubes (versión preliminar)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [La integración de Microsoft Defender para punto de conexión con Azure Defender ahora es compatible con Windows Server 2019 y Windows 10 Virtual Desktop (WVD) lanzado en disponibilidad general](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [Recomendaciones para habilitar Azure Defender para DNS y Resource Manager (en versión preliminar)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [Se han agregado tres estándares de cumplimiento normativo: Azure CIS 1.3.0, CMMC Nivel 3 e ISM restringido de Nueva Zelanda](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Cuatro nuevas recomendaciones relacionadas con la configuración de invitado (en versión preliminar)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [Recomendaciones de CMK trasladadas al control de seguridad de procedimientos recomendados](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 alertas de Azure Defender se han puesto en desuso](#11-azure-defender-alerts-deprecated).
- [Dos recomendaciones del control de seguridad "Aplicar actualizaciones del sistema" entraron en desuso](#two-recommendations-from-apply-system-updates-security-control-were-deprecated).
- [Azure Defender para SQL en el icono de la máquina se ha eliminado del panel de Azure Defender](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [Traslado de 21 recomendaciones entre controles de seguridad](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>Actualización de la página de estado de recursos (en versión preliminar)

El estado de los recursos de Security Center se ha ampliado y mejorado para proporcionar una vista de instantánea del estado general de un recurso individual. 

Puede revisar información detallada sobre el recurso y todas las recomendaciones que se aplican a ese recurso. Además, si usa [Azure Defender](azure-defender.md), también puede ver alertas de seguridad pendientes para ese recurso específico.

Para abrir la página de mantenimiento de un recurso, seleccione cualquier recurso en la [página de inventario de recursos](asset-inventory.md).

En esta página de vista previa del portal de Security Center se muestra lo siguiente:

1. **Información sobre los recursos**: el grupo de recursos y la suscripción a la que está asociado, la ubicación geográfica, etc.
1. **Característica de seguridad aplicada:** muestra si Azure Defender está habilitado para el recurso o no.
1. **Recuentos de recomendaciones y alertas pendientes:** el número de recomendaciones de seguridad y alertas pendientes de Azure Defender.
1. **Recomendaciones y alertas prácticas:** en dos pestañas se incluyen las recomendaciones y alertas que son aplicables al recurso.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Página de estado de los recursos de Azure Security Center que muestra la información de estado de una máquina virtual":::

Obtenga más información en [Tutorial: Investigación del estado de los recursos](investigate-resource-health.md).


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>Las imágenes del registro de contenedor que se han extraído recientemente ahora se vuelven a examinar semanalmente (lanzado en disponibilidad general)

Azure Defender para registros de contenedor incluye un analizador de vulnerabilidades integrado. Este analizador examina inmediatamente cualquier imagen que se inserte en el registro y cualquier imagen que se haya extraído en los últimos 30 días.

Cada día se detectan nuevas vulnerabilidades. Con esta actualización, las imágenes de contenedor que fueron extraídas de los registros durante los últimos 30 días se **volverán a examinar** cada semana. Esto garantiza que se identifiquen las vulnerabilidades recién detectadas en las imágenes.

El examen se cobra por imagen, por lo que no hay ningún cargo adicional por estos nuevos exámenes.

Más información sobre este analizador en [Uso de Azure Defender para registros de contenedor para examinar las imágenes en busca de vulnerabilidades](defender-for-container-registries-usage.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Use Azure Defender para Kubernetes para proteger implementaciones de Kubernetes híbridas y de varias nubes (versión preliminar)

Azure Defender para Kubernetes está expandiendo sus funcionalidades de protección contra amenazas para defender los clústeres dondequiera que estén implementados. Esto se ha habilitado mediante la integración con [Kubernetes habilitado para Azure Arc](../azure-arc/kubernetes/overview.md) y sus nuevas [funcionalidades de extensiones](../azure-arc/kubernetes/extensions.md). 

Una vez habilitado Azure Arc en los clústeres de Kubernetes que no son de Azure, una nueva recomendación de Azure Security Center ofrece la implementación de la extensión de Azure Defender en ellos con solo unos clics.

Use la recomendación (los **clústeres de Kubernetes habilitados para Azure Arc deben tener instalada la extensión de Azure Defender**) y la extensión para proteger los clústeres de Kubernetes implementados en otros proveedores de nube, aunque no en los servicios administrados de Kubernetes.

Esta integración entre Azure Security Center, Azure Defender y Kubernetes habilitado para Azure Arc ofrece:

- Aprovisionamiento sencillo de la extensión de Azure Defender en clústeres de Kubernetes habilitado para Azure Arc sin protección (manualmente y a escala)
- Supervisión de la extensión de Azure Defender y su estado de aprovisionamiento desde el portal de Azure Arc
- Las recomendaciones de seguridad de Security Center se muestran en la nueva página de seguridad del portal de Azure Arc.
- Las amenazas de seguridad identificadas de Azure Defender se muestran en la nueva página de seguridad del portal de Azure Arc.
- Los clústeres de Kubernetes habilitados para Azure Arc están integrados en la plataforma y la experiencia de Azure Security Center

Puede encontrar más información en [Uso de Azure Defender para Kubernetes con los clústeres de Kubernetes locales y de varias nubes](defender-for-kubernetes-azure-arc.md).

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="La recomendación de Azure Security Center para implementar la extensión de Azure Defender para clústeres de Kubernetes habilitados para Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>La integración de Microsoft Defender para punto de conexión con Azure Defender ahora es compatible con Windows Server 2019 y Windows 10 Virtual Desktop (WVD) lanzado en disponibilidad general

Microsoft Defender para punto de conexión es una solución integral de seguridad de punto de conexión que se entrega en la nube. Proporciona funciones de administración y evaluación de vulnerabilidades basadas en riesgos, así como de detección y respuesta de puntos de conexión (EDR). Para obtener una lista completa de las ventajas del uso de Defender para punto de conexión junto con Azure Security Center, consulte [Proteja los puntos de conexión con la solución EDR integrada de Security Center: Microsoft Defender para punto de conexión](security-center-wdatp.md).

Cuando habilita Azure Defender para servidores en un servidor de Windows, el plan incluye una licencia de Defender para punto de conexión. Si ya ha habilitado Azure Defender para servidores y tiene servidores de Windows 2019 en la suscripción, los servidores recibirán automáticamente Defender para punto de conexión con esta actualización. No se requiere ninguna acción manual. 

Ahora se ha ampliado la compatibilidad para incluir Windows Server 2019 y [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Si va a habilitar Defender para punto de conexión en un equipo con Windows Server 2019, asegúrese de que cumple los requisitos previos descritos en [Habilitación de la integración de Microsoft Defender para punto de conexión](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration).


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Recomendaciones para habilitar Azure Defender para DNS y Resource Manager (en versión preliminar)

Se han agregado dos nuevas recomendaciones para simplificar el proceso de habilitación de [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md) y [Azure Defender para DNS](defender-for-dns-introduction.md):

- **Azure Defender para Resource Manager se debe habilitar**: Defender para Resource Manager supervisa automáticamente las operaciones de administración de recursos de la organización. Azure Defender detecta amenazas y alerta sobre actividades sospechosas.
- **Azure Defender para DNS se debe habilitar**: Defender para DNS proporciona una capa adicional de protección para los recursos de la nube, ya que supervisa de forma ininterrumpida todas las consultas de DNS de los recursos de Azure. Azure Defender alerta sobre las actividades sospechosas en la capa de DNS.

La habilitación de los planes de Azure Defender conlleva cargos. Obtenga información sobre los detalles de los precios por región en la página de precios de Security Center: https://aka.ms/pricing-security-center.

> [!TIP]
> Las recomendaciones de la versión preliminar no representan un recurso incorrecto y no se incluyen en los cálculos de una puntuación segura. Corríjalas siempre que sea posible, de tal forma que, cuando finalice el período de versión preliminar, contribuyan a la puntuación. Puede encontrar más información sobre cómo responder a estas recomendaciones en [Recomendaciones de corrección en Azure Security Center](security-center-remediate-recommendations.md).


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Se han agregado tres estándares de cumplimiento normativo: Azure CIS 1.3.0, CMMC Nivel 3 e ISM restringido de Nueva Zelanda

Hemos agregado tres estándares para su uso con Azure Security Center. Con el panel de cumplimiento normativo, ahora puede realizar un seguimiento del cumplimiento con:

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC nivel 3](../governance/policy/samples/cmmc-l3.md)
- [ISM restringido en Nueva Zelanda](../governance/policy/samples/new-zealand-ism.md)

Puede asignarlos a las suscripciones como se describe en [Personalización del conjunto de estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Se han agregado tres estándares para su uso en el panel de cumplimiento normativo de Azure Security Center." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Puede encontrar más información en:
- [Personalización del conjunto de estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md)
- [Tutorial: Mejora del cumplimiento normativo](security-center-compliance-dashboard.md)
- [Tutorial: Mejora del cumplimiento normativo](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>Cuatro nuevas recomendaciones relacionadas con la configuración de invitado (en versión preliminar)

La [extensión de configuración de invitado](../governance/policy/concepts/guest-configuration.md) de Azure informa a Security Center para ayudar a garantizar la protección de la configuración de invitado de las máquinas virtuales. La extensión no es necesaria para los servidores habilitados para Arc porque está incluida en el agente Connected Machine de Arc. La extensión requiere una identidad administrada por el sistema en la máquina.

Hemos agregado cuatro nuevas recomendaciones a Security Center para sacar el máximo partido a esta extensión.

- En dos recomendaciones se le pide que instale la extensión y su identidad administrada por el sistema requerida:
    - **La extensión "Configuración de invitado" debe estar instalada en las máquinas**
    - **La extensión "Configuración de invitado" de las máquinas virtuales debe implementarse con una identidad administrada asignada por el sistema**

- Cuando la extensión esté instalada y en ejecución, empezará a auditar las máquinas y se le pedirá que refuerce la configuración del sistema operativo y del entorno. Estas dos recomendaciones le pedirán que proteja sus máquinas Windows y Linux como se describe a continuación:
    - **La Protección contra vulnerabilidades de seguridad de Windows Defender debe estar habilitada en las máquinas**.
    - **La autenticación en máquinas Linux debe requerir claves SSH.**

Puede encontrar más información en [Información sobre Guest Configuration de Azure Policy](../governance/policy/concepts/guest-configuration.md).

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>Recomendaciones de CMK trasladadas al control de seguridad de procedimientos recomendados

El programa de seguridad de cada organización incluye requisitos de cifrado de datos. De manera predeterminada, los datos de los clientes de Azure se cifran en reposo con claves administradas por el servicio. Sin embargo, las claves administradas por el cliente (CMK) suelen ser necesarias para cumplir estándares de cumplimiento normativo. Las CMK permiten cifrar los datos con una clave de [Azure Key Vault](../key-vault/general/overview.md) creada por el usuario y propiedad de este. Esto le da control y responsabilidad total sobre el ciclo de vida de la clave, incluidas la rotación y la administración.

Los controles de seguridad de Azure Security Center son grupos lógicos de recomendaciones de seguridad relacionadas y reflejan las superficies de ataque vulnerables. Cada control tiene un número máximo de puntos que puede sumar a la puntuación de seguridad si corrige todas las recomendaciones enumeradas en el control para todos los recursos. El control de seguridad **Implementar procedimientos recomendados de seguridad** vale cero puntos. Por lo tanto, las recomendaciones de este control no afectan a la puntuación de seguridad.

Las recomendaciones que se enumeran a continuación se trasladan al control de seguridad **Implementar procedimientos recomendados de seguridad** para reflejar mejor su naturaleza opcional. Este traslado garantiza que cada una de estas recomendaciones está en el control más adecuado para cumplir su objetivo.

- Las cuentas de Azure Cosmos DB deben usar claves administradas por el cliente para cifrar los datos en reposo
- Las áreas de trabajo de Azure Machine Learning deben cifrarse con una clave administrada por el cliente (CMK).
- Las cuentas de Cognitive Services deben habilitar el cifrado de datos con una clave administrada por el cliente (CMK)
- Las instancias de Container Registry se deben cifrar con una clave administrada por el cliente (CMK)
- Las instancias administradas de SQL deben usar claves administradas por el cliente para cifrar los datos en reposo
- Los servidores SQL deben usar claves administradas por el cliente para cifrar los datos en reposo
- Las cuentas de almacenamiento deben usar claves administradas por el cliente (CMK) para el cifrado

Obtenga información sobre qué recomendaciones se encuentran en cada control de seguridad en [Controles de seguridad y sus recomendaciones](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="11-azure-defender-alerts-deprecated"></a>11 alertas de Azure Defender se han puesto en desuso.

Las 11 alertas de Azure Defender que se enumeran a continuación han quedado en desuso.

- Las nuevas alertas reemplazarán estas dos y proporcionarán una mejor cobertura:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | VERSIÓN PRELIMINAR: Se detectó la ejecución de la función "Get-AzureDomainInfo" del kit de herramientas de MicroBurst. |
    | ARM_MicroBurstRunbook    | VERSIÓN PRELIMINAR: Se detectó la ejecución de la función "Get-AzurePasswords" del kit de herramientas de MicroBurst.  |
    |                          |                                                                          |

- Estas nueve alertas se refieren a un conector de Azure Active Directory Identity Protection (IPC) que ya está en desuso:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Propiedades de inicio de sesión desconocidas |
    | AnonymousLogin      | Dirección IP anónima          |
    | InfectedDeviceLogin | Dirección IP vinculada al malware     |
    | ImpossibleTravel    | Viaje atípico               |
    | MaliciousIP         | Dirección IP malintencionada          |
    | LeakedCredentials   | Credenciales con fugas            |
    | PasswordSpray       | Difusión de contraseñas                |
    | LeakedCredentials   | Inteligencia de Azure AD sobre amenazas  |
    | AADAI               | IA de Azure AD                   |
    |                     |                               |
 
    > [!TIP]
    > Estas nueve alertas de IPC nunca fueron alertas de Security Center. Forman parte del conector de Azure Active Directory Identity Protection que las envía a Security Center. Durante los últimos dos años, los únicos clientes que han estado viendo esas alertas son las organizaciones que han configurado la exportación (del conector a ASC) en 2019 o versiones anteriores. El conector de Azure Active Directory Identity Protection ha continuado mostrando sus propios sistemas de alertas y ha continuado estando disponible en Azure Sentinel. El único cambio es que ya no aparecen en Security Center.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Dos recomendaciones del control de seguridad "Aplicar actualizaciones del sistema" entraron en desuso. 

Las dos recomendaciones siguientes estaban en desuso y los cambios pueden dar lugar a un leve impacto en la puntuación de seguridad:

- **Las máquinas deben reiniciarse para aplicar las actualizaciones del sistema**
- **El agente de supervisión debe instalarse en las máquinas**. Esta recomendación solo está relacionada con máquinas locales y parte de su lógica se transferirá a otra recomendación, **Se deben resolver los problemas de estado del agente de Log Analytics en las máquinas**.

Se recomienda comprobar las configuraciones de exportación continua y de automatización del flujo de trabajo para ver si estas recomendaciones están incluidas en ellas. Además, los paneles u otras herramientas de supervisión que puedan estar usándolas se deben actualizar en consecuencia.

Más información sobre estas recomendaciones en la [página de referencia de las recomendaciones de seguridad](recommendations-reference.md).

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Azure Defender para SQL en el icono de la máquina se ha eliminado del panel de Azure Defender

El área de cobertura del panel de Azure Defender incluye iconos para los planes de Azure Defender pertinentes para su entorno. Debido a un problema con los informes de los números de recursos protegidos y no protegidos, hemos decidido quitar temporalmente el estado de cobertura de recursos para **Azure Defender para SQL en las máquinas** hasta que se resuelva el problema.


### <a name="21-recommendations-moved-between-security-controls"></a>Traslado de 21 recomendaciones entre controles de seguridad 

Las siguientes recomendaciones se han trasladado a otros controles de seguridad. Los controles de seguridad son grupos lógicos de recomendaciones de seguridad relacionadas y reflejan las superficies de ataque vulnerables. Este traslado garantiza que cada una de estas recomendaciones está en el control más adecuado para cumplir su objetivo.

Obtenga información sobre qué recomendaciones se encuentran en cada control de seguridad en [Controles de seguridad y sus recomendaciones](secure-score-security-controls.md#security-controls-and-their-recommendations).

|Recomendación |Cambio e impacto  |
|---------|---------|
|La evaluación de vulnerabilidades debe estar activada en sus servidores de SQL Server.<br>La evaluación de vulnerabilidad debe estar habilitada en las instancias administradas de SQL.<br>Las vulnerabilidades de sus bases de datos SQL deben remediarse ahora<br>Las vulnerabilidades de las bases de datos SQL en máquinas virtuales deben corregirse     |Cambia de Corregir vulnerabilidades (con un valor de 6 puntos)<br>a Corregir configuraciones de seguridad (con un valor de 4 puntos)<br>En función del entorno, estas recomendaciones tendrán menor impacto en la puntuación.|
|Debe haber más de un propietario asignado a su suscripción<br>Las variables de cuenta de automatización deben cifrarse<br> Dispositivos IoT: el proceso auditado dejó de enviar eventos.<br> Dispositivos IoT: error de validación de línea base del sistema operativo<br> Dispositivos IoT: es preciso actualizar el conjunto de cifrado de TLS.<br>Dispositivos IoT: puertos abiertos en el dispositivo.<br>Dispositivos IoT: se encontró una directiva de firewall permisiva en una de las cadenas.<br> Dispositivos IoT: se encontró una regla de firewall permisiva en la cadena de entrada.<br> Dispositivos IoT: se encontró una regla de firewall permisiva en la cadena de salida.<br>Los registros de diagnóstico de IoT Hub deben estar habilitados<br>Dispositivos IoT: mensajes infrautilizados de envío del agente.<br>Dispositivos IoT: la directiva de filtro de IP predeterminada debe ser Denegar.<br>Dispositivos IoT: intervalo de IP amplio de la regla del filtro de IP.<br>Dispositivos IoT: se deben ajustar tanto el tamaño como los intervalos de los mensajes de los agentes<br>Dispositivos IoT: credenciales de autenticación idénticas<br>Dispositivos IoT: el proceso auditado dejó de enviar eventos.<br>Dispositivos IoT: la configuración de la línea base del sistema operativo (SO) debe corregirse|Cambia a **Implementación de procedimientos recomendados de seguridad**.<br>Cuando una recomendación pasa al control de seguridad Implementar prácticas recomendadas de seguridad, que no vale ningún punto, la recomendación deja de afectar a la puntuación segura.|
|||


## <a name="march-2021"></a>Marzo de 2021

Las actualizaciones de marzo incluyen:

- [Administración de Azure Firewall integrada en Security Center](#azure-firewall-management-integrated-into-security-center)
- [La evaluación de vulnerabilidades de SQL ahora incluye la experiencia "Deshabilitación de regla" (versión preliminar)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Se integran en Security Center los libros de Azure Monitor y se proporcionan tres plantillas](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [El panel de cumplimiento normativo ahora incluye informes de auditoría de Azure (versión preliminar)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Los datos de recomendaciones se pueden ver en Azure Resource Graph con "Explore in ARG" (Explorar en ARG)](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Actualizaciones en las directivas para implementar la automatización de flujos de trabajo](#updates-to-the-policies-for-deploying-workflow-automation)
- [Dos recomendaciones heredadas dejan de escribir datos directamente en el registro de actividad de Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Mejoras de la página de recomendaciones](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Administración de Azure Firewall integrada en Security Center

Al abrir Azure Security Center, la primera página que aparece es la página de información general. 

Este panel interactivo proporciona una vista unificada de la posición de seguridad de las cargas de trabajo de la nube híbrida. Además, muestra alertas de seguridad, información de cobertura y mucho más.

Para ayudarle a ver el estado de seguridad desde una experiencia central, hemos integrado Azure Firewall Manager en este panel. Ahora puede comprobar el estado de cobertura del firewall en todas las redes y administrar de forma centralizada las directivas de Azure Firewall desde Security Center.

Obtenga más información sobre este panel en [Página de información general de Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Panel de información general de Security Center con un icono para Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>La evaluación de vulnerabilidades de SQL ahora incluye la experiencia "Deshabilitación de regla" (versión preliminar)

Security Center incluye un detector de vulnerabilidades integrado para ayudarle a detectar, realizar un seguimiento y corregir posibles vulnerabilidades de la base de datos. Los resultados de los exámenes de evaluación proporcionan información general sobre el estado de seguridad de las máquinas SQL y detalles de las conclusiones sobre seguridad.

Si tiene una necesidad organizativa de omitir un resultado, en lugar de corregirlo, tiene la opción de deshabilitarlo. Los resultados deshabilitados no afectan a la puntuación de seguridad ni generan ruido no deseado.

Obtenga más información en [Deshabilitación de resultados específicos](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Se integran en Security Center los libros de Azure Monitor y se proporcionan tres plantillas

En la conferencia Ignite de primavera de 2021, anunciamos la integración de los libros de Azure Monitor en Security Center.

Puede utilizar la nueva integración para empezar a usar las plantillas predefinidas de la galería de Security Center. Con las plantillas de libro, puede acceder y crear informes visuales y dinámicos para realizar un seguimiento de la posición de seguridad de su organización. Además, puede crear nuevos libros basados en los datos de Security Center o en cualquier otro tipo de datos admitido e implementar rápidamente libros de comunidad desde la comunidad de GitHub de Security Center.

Se proporcionan tres plantillas de informe:

- **Puntuación de seguridad a lo largo del tiempo**: puede realizar el seguimiento de las puntuaciones de las suscripciones y de los cambios en las recomendaciones sobre sus recursos.
- **Actualizaciones del sistema**: puede ver las actualizaciones del sistema que faltan por recursos, sistema operativo, gravedad, etc.
- **Conclusiones de la evaluación de vulnerabilidad**: puede ver los resultados de los exámenes de vulnerabilidades de los recursos de Azure.

Obtenga información sobre el uso de estos informes o sobre la creación de sus propios informes en [Creación de informes enriquecidos e interactivos con los datos de Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Informe de puntuación de seguridad a lo largo del tiempo.":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>El panel de cumplimiento normativo ahora incluye informes de auditoría de Azure (versión preliminar)

Ahora puede descargar informes de certificación de Azure y Dynamics desde la barra de herramientas del panel de cumplimiento normativo. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barra de herramientas del panel de cumplimiento normativo":::

Puede seleccionar la pestaña correspondiente a cada tipo de informe pertinente (PCI, SOC, ISO y otros) y usar filtros para buscar los informes específicos que necesita.

Más información sobre [cómo administrar los estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrado de la lista de informes de auditoría de Azure disponibles.":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Los datos de recomendaciones se pueden ver en Azure Resource Graph con "Explore in ARG" (Explorar en ARG)

Ahora, las páginas de detalles de la recomendación incluyen el botón de la barra de herramientas "Explore in ARG" (Explorar en ARG). Utilice este botón para abrir una consulta de Azure Resource Graph y explorar, exportar y compartir los datos de la recomendación.

Azure Resource Graph (ARG) proporciona acceso instantáneo a la información de los recursos en los entornos en la nube con funcionalidades sólidas para filtrar, agrupar y ordenar. Es una forma rápida y eficaz de consultar información en las suscripciones de Azure mediante programación o desde Azure Portal.

Más información sobre [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Exploración de los datos de recomendaciones en Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Actualizaciones en las directivas para implementar la automatización de flujos de trabajo

La automatización de los procesos de supervisión y respuesta ante incidentes de la organización puede mejorar considerablemente el tiempo necesario para investigar y mitigar los incidentes de seguridad.

Proporcionamos tres directivas "DeployIfNotExist" de Azure Policy que crean y configuran procedimientos de automatización de flujos de trabajo para que pueda implementar sus automatizaciones en toda la organización:

|Objetivo  |Directiva  |Id. de directiva  |
|---------|---------|---------|
|Automatización de flujos de trabajo para alertas de seguridad|[Implementar la automatización del flujo de trabajo para las alertas de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatización de flujos de trabajo para recomendaciones de seguridad|[Implementar la automatización del área de trabajo para las recomendaciones de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatización de flujos de trabajo para cambios de cumplimiento normativo|[Implementación de la automatización del flujo de trabajo para el cumplimiento normativo de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Hay dos novedades en las características de estas directivas:

- Cuando se asignan, permanecen habilitadas por cumplimiento.
- Ahora puede personalizar estas directivas y actualizar cualquiera de sus parámetros incluso después de que se hayan implementado. Por ejemplo, si un usuario desea agregar otra clave de evaluación o editar una clave de evaluación existente, puede hacerlo.

Empiece a usar las [plantillas de automatización de flujos de trabajo](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Puede obtener más información en [Automatización de respuestas a desencadenadores de Security Center](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Dos recomendaciones heredadas dejan de escribir datos directamente en el registro de actividad de Azure 

Security Center pasa los datos de casi todas las recomendaciones de seguridad a Azure Advisor que, a su vez, los escribe en el [registro de actividad de Azure](../azure-monitor/essentials/activity-log.md).

Para dos recomendaciones, los datos se escriben de forma simultánea directamente en el registro de actividad de Azure. Con este cambio, Security Center deja de escribir datos para estas recomendaciones de seguridad heredadas directamente en el registro de actividad. En su lugar, exportamos los datos a Azure Advisor como hacemos para las restantes recomendaciones.

Las dos recomendaciones heredadas son:
- Los problemas de estado de protección de puntos de conexión se deben resolver en las máquinas
- Se deben corregir las vulnerabilidades en la configuración de seguridad en las máquinas

Si ha accedido a la información de estas dos recomendaciones en la categoría "Recomendación de tipo TaskDiscovery" del registro de actividad, ya no estará disponible.


### <a name="recommendations-page-enhancements"></a>Mejoras de la página recomendaciones 

Hemos lanzado una versión mejorada de la lista de recomendaciones para presentar más información que se pueda consultar de un vistazo.

Ahora, en la página verá:

1. La puntuación máxima y la puntuación actual de cada control de seguridad.
1. Hay iconos que reemplazan etiquetas como **Corrección** y **Versión preliminar**.
1. Una columna nueva que muestra la [iniciativa de directiva](security-policy-concept.md) relacionada con cada recomendación: se puede ver cuando "Agrupar por controles" está deshabilitado.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Mejoras en la página de recomendaciones de Azure Security Center: marzo de 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Mejoras en la lista &quot;plana&quot; de recomendaciones de Azure Security Center: marzo de 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Más información en [Recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md)