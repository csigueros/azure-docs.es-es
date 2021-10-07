---
title: Archivo de novedades de Azure Security Center
description: Una descripción de las novedades y los cambios en Azure Security Center desde hace seis meses y versiones anteriores.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 10/03/2021
ms.author: memildin
ms.openlocfilehash: ca1e75af5f1d5697b84742e3aec207a33a57b1c6
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407197"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archivo de novedades de Azure Security Center

La página principal de notas de la versión de [novedades de Azure Security Center](release-notes.md) contiene actualizaciones de los últimos seis meses, mientras que esta página contiene elementos más antiguos.

En este página se proporciona información acerca de lo siguiente:

- Nuevas características
- Corrección de errores
- Funciones obsoletas


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

## <a name="february-2021"></a>Febrero de 2021

Las actualizaciones de febrero incluyen:

- [La nueva página de alertas de seguridad de Azure Portal se ha publicado con disponibilidad general (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Las recomendaciones de protección de la carga de trabajo de Kubernetes se han publicado con disponibilidad general (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [La integración de Microsoft Defender para punto de conexión con Azure Defender ahora es compatible con Windows Server 2019 y Windows 10 Virtual Desktop (WVD) (en versión preliminar)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Vínculo directo a la directiva desde la página de detalles de la recomendación](#direct-link-to-policy-from-recommendation-details-page)
- [La recomendación de clasificación de datos SQL ya no afecta a la puntuación segura](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Las automatizaciones de los flujos de trabajo se pueden desencadenar mediante cambios en las evaluaciones de cumplimiento normativo (versión preliminar)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Mejoras en la página de inventario de recursos](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>La nueva página de alertas de seguridad de Azure Portal se ha publicado con disponibilidad general (GA)

La página de alertas de seguridad de Azure Security Center se ha rediseñado para proporcionar:

- **Mejora en la evaluación de prioridades en las alertas**: ahora es más fácil ayudar a reducir la fatiga de las alertas y centrarse en las amenazas más importantes, la lista incluye filtros personalizables y opciones de agrupación.
- **Más información en la lista de alertas**: por ejemplo las tácticas ATT y ACK de MITRE.
- **Botón para crear alertas de ejemplo**: para evaluar las funcionalidades de Azure Defender y probar la configuración de las alertas (para la integración de SIEM, las notificaciones por correo electrónico y las automatizaciones de los flujos de trabajo), puede crear alertas de ejemplo desde todos los planes de Azure Defender.
- **Alineación con la experiencia de incidentes de Azure Sentinel**: para los clientes que usan ambos productos, cambiar entre ellos ahora es más sencillo y es fácil que uno aprenda del otro.
- **Mejor rendimiento** de listas de alertas grandes.
- **Desplazamiento mediante el teclado** a través de la lista de alertas.
- **Alertas de Azure Resource Graph**: puede consultar las alertas en Azure Resource Graph, la API similar a Kusto para todos los recursos. Esto también resulta útil si va a crear sus propios paneles de alertas. [Más información sobre Azure Resource Graph](../governance/resource-graph/index.yml).
- **Característica Crear alertas de ejemplo**: para crear alertas de ejemplo en la nueva experiencia de alertas, consulte el apartado [Generación de alertas de ejemplo de Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de seguridad de Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Las recomendaciones de protección de la carga de trabajo de Kubernetes se han publicado con disponibilidad general (GA)

Nos complace anunciar la disponibilidad general (GA) del conjunto de recomendaciones para las protecciones de la carga de trabajo de Kubernetes.

Para que las cargas de trabajo de Kubernetes sean seguras de forma predeterminada, Security Center ha agregado recomendaciones de protección de nivel de Kubernetes, incluidas opciones de cumplimiento con el control de admisión de Kubernetes.

Cuando el complemento de Azure Policy para Kubernetes está instalado en el clúster de Azure Kubernetes Service (AKS), todas las solicitudes al servidor de la API de Kubernetes se supervisarán con el conjunto predefinido de procedimientos recomendados (que aparecen en forma de 13 recomendaciones de seguridad) antes de que se guarden en el clúster. Después, puede realizar la configurar para aplicar los procedimientos recomendados y exigirlos para futuras cargas de trabajo.

Por ejemplo, puede exigir que no se creen los contenedores con privilegios y que se bloqueen las solicitudes futuras para este fin.

Más información en [Procedimientos recomendados de protección de cargas de trabajo con el control de admisión de Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

> [!NOTE]
> Aunque las recomendaciones estaban en versión preliminar, no representaban los recursos de clúster de AKS en mal estado y no se incluían en los cálculos de la puntuación segura. Con este anuncio de disponibilidad general, se incluirán en el cálculo de puntuaciones. Si aún no ha hecho nada al respecto, esto podría afectar negativamente a su puntuación segura. Corríjalas siempre que sea posible como se describe en [Recomendaciones de corrección en Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>La integración de Microsoft Defender para punto de conexión con Azure Defender ahora es compatible con Windows Server 2019 y Windows 10 Virtual Desktop (WVD) (en versión preliminar)

Microsoft Defender para punto de conexión es una solución integral de seguridad de punto de conexión que se entrega en la nube. Proporciona funciones de administración y evaluación de vulnerabilidades basadas en riesgos, así como de detección y respuesta de puntos de conexión (EDR). Para obtener una lista completa de las ventajas del uso de Defender para punto de conexión junto con Azure Security Center, consulte [Proteja los puntos de conexión con la solución EDR integrada de Security Center: Microsoft Defender para punto de conexión](security-center-wdatp.md).

Cuando habilita Azure Defender para servidores en un servidor de Windows, el plan incluye una licencia de Defender para punto de conexión. Si ya ha habilitado Azure Defender para servidores y tiene servidores de Windows 2019 en la suscripción, los servidores recibirán automáticamente Defender para punto de conexión con esta actualización. No se requiere ninguna acción manual. 

Ahora se ha ampliado la compatibilidad para incluir Windows Server 2019 y [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Si va a habilitar Defender para punto de conexión en un equipo con Windows Server 2019, asegúrese de que cumple los requisitos previos descritos en [Habilitación de la integración de Microsoft Defender para punto de conexión](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Vínculo directo a la directiva desde la página de detalles de la recomendación

Cuando se revisan los detalles de una recomendación, a menudo resulta útil poder ver la directiva subyacente. Para cada recomendación admitida en una directiva, hay un vínculo nuevo en la página de detalles de la recomendación:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Vínculo a la página de Azure Policy de la directiva específica que admite una recomendación.":::

Use este vínculo para ver la definición de directiva y revisar la lógica de evaluación. 

Si va a revisar la lista de recomendaciones de la [guía de referencia de recomendaciones de seguridad](recommendations-reference.md), también verá vínculos a las páginas de definición de directiva:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Acceso a la página de Azure Policy de una directiva específica directamente desde la página de referencia de recomendaciones de Azure Security Center." lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>La recomendación de clasificación de datos SQL ya no afecta a la puntuación segura
La recomendación **Los datos confidenciales de las bases de datos SQL deben clasificarse** ya no afecta a la puntuación segura. Esta es la única recomendación del control de seguridad **Apply data classification** (Aplicar clasificación de datos), de modo que el control ahora tiene un valor de puntuación segura de 0.

Para obtener una lista completa de todos los controles de seguridad de Security Center, junto con sus puntuaciones y una lista de las recomendaciones de cada uno, consulte [Controles de seguridad y sus recomendaciones](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Las automatizaciones de los flujos de trabajo se pueden desencadenar mediante cambios en las evaluaciones de cumplimiento normativo (versión preliminar)
Hemos agregado un tercer tipo de datos a las opciones del desencadenador para las automatizaciones del flujo de trabajo: cambios en las evaluaciones de cumplimiento normativo.

Aprenda a usar las herramientas de automatización del flujo de trabajo en [Automatización de respuestas a desencadenadores de Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Uso de los cambios en las evaluaciones de cumplimiento normativo para desencadenar la automatización de un flujo de trabajo." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Mejoras en la página de inventario de recursos
La página de inventario de recursos de Security Center se ha mejorado como se indica a continuación:

- Los resúmenes de la parte superior de la página ahora incluyen **suscripciones no registradas**, que muestran el número de suscripciones sin Security Center habilitado.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Recuento de suscripciones no registradas en los resúmenes de la parte superior de la página de inventario de recursos.":::

- Se han expandido y mejorado los filtros, que incluyen:
    - **Recuentos**: cada filtro presenta el número de recursos que cumplen los criterios de cada categoría.

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Recuentos en los filtros de la página del inventario de recursos de Azure Security Center.":::

    - **Contiene filtro de exenciones** (opcional): restrinja los resultados a los recursos que tienen o no tienen exenciones. Este filtro no se muestra de forma predeterminada, pero se puede acceder a él el botón **Agregar filtro**.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Incorporación del filtro &quot;contiene exención&quot; en la página del inventario de recursos de Azure Security Center":::

Más información sobre los procedimientos para [explorar y administrar los recursos con el inventario de recursos](asset-inventory.md).



## <a name="january-2021"></a>Enero de 2021

Las actualizaciones de enero incluyen:

- [Azure Security Benchmark es ahora la iniciativa de directivas predeterminada para Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [La valoración de vulnerabilidades tanto para máquinas locales como las que están en varias nubes se ha publicado con disponibilidad general (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Puntuación segura para grupos de administración ya está disponible en versión preliminar](#secure-score-for-management-groups-is-now-available-in-preview)
- [La API de Puntuación segura se ha publicado en disponibilidad general (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Las protecciones de DNS pendientes se han agregado a Azure Defender para App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Los conectores de nubes múltiples se ha publicado con disponibilidad general (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Exclusión de recomendaciones completas de su puntuación segura para suscripciones y grupos de administración](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Los usuarios ya pueden solicitar visibilidad en todo el inquilino de su administrador global](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Se han agregado 35 recomendaciones en versión preliminar para aumentar la cobertura de Azure Security Benchmark](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).
- [Exportación de CSV de una lista filtrada de recomendaciones](#csv-export-of-filtered-list-of-recommendations)
- [Los recursos en estado "No aplicable" ahora se notifican como "Compatible" en las valoraciones de Azure Policy](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportación de instantáneas semanales de datos de cumplimiento normativo y puntuación segura con exportación continua (versión preliminar)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark es ahora la iniciativa de directivas predeterminada para Azure Security Center

Azure Security Benchmark es el conjunto de directrices específico de Azure creado por Microsoft para ofrecer los procedimientos recomendados de seguridad y cumplimiento basados en marcos de cumplimiento comunes. Este punto de referencia, que cuenta con un amplísimo respaldo, se basa en los controles del [Centro de seguridad de Internet (CIS)](https://www.cisecurity.org/benchmark/azure/) y del [Instituto Nacional de Normas y Tecnología (NIST)](https://www.nist.gov/), con un enfoque en seguridad centrada en la nube.

En los últimos meses, la lista de recomendaciones de seguridad integradas de Security Center ha crecido considerablemente para ampliar la cobertura de este punto de referencia.

A partir de esta versión, el punto de referencia es la base de las recomendaciones de Security Center y está totalmente integrado como iniciativa de directivas predeterminada. 

Todos los servicios de Azure tienen una página de línea base de seguridad en su documentación. Por ejemplo, [esta es la línea base de Security Center](security-baseline.md). Estas líneas base se basan en el Azure Security Benchmark.

Si usa el panel de cumplimiento normativo de Security Center, verá dos instancias del punto de referencia durante un período de transición:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Panel de cumplimiento normativo de Security Center que muestra Azure Security Benchmark":::

Las recomendaciones existentes no se ven afectadas y a medida que crezca el punto de referencia, los cambios se reflejarán automáticamente en Security Center. 

Para más información, consulte las páginas siguientes:

- [Más información sobre Azure Security Benchmark](/security/benchmark/azure/introduction)
- [Personalización del conjunto de estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>La valoración de vulnerabilidades tanto para máquinas locales como las que están en varias nubes se ha publicado con disponibilidad general (GA)

En octubre, se anunció una versión preliminar para examinar servidores con Azure Arc habilitado con el analizador de evaluación de vulnerabilidades integrado de [Azure Defender para servidores](defender-for-servers-introduction.md) (con tecnología de Qualys).

Ahora se ha publicado con disponibilidad general (GA).

Al habilitar Azure Arc en máquinas que no son de Azure, Security Center le ofrecerá la opción de implementar en ellas el analizador de vulnerabilidades integrado, manualmente y a escala.

Con esta actualización, puede aprovechar toda la capacidad de **Azure Defender para servidores** para consolidar su programa de administración de vulnerabilidades en todos sus recursos, tanto si son de Azure como si no.

Principales funcionalidades:

- Supervisión del estado de aprovisionamiento del analizador de evaluación de vulnerabilidades en máquinas de Azure Arc.
- Aprovisionamiento del agente de evaluación de vulnerabilidades integrado en máquinas de Azure Arc Windows y Linux (manualmente y a escala).
- Recepción y análisis de vulnerabilidades detectadas por agentes implementados (manualmente y a escala).
- Experiencia unificada para máquinas virtuales de Azure y máquinas de Azure Arc.

[Obtenga más información sobre la implementación del analizador de vulnerabilidades integrado en las máquinas híbridas](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Obtenga más información sobre los servidores habilitados para Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Puntuación segura para grupos de administración ya está disponible en versión preliminar

La página de puntuación segura ahora muestra las puntuaciones seguras agregadas para los grupos de administración, además del nivel de suscripción. Por consiguiente, ahora puede ver la lista de grupos de administración de la organización y la puntuación de cada uno de ellos.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Visualización de las puntuaciones seguras de los grupos de administración.":::

Obtenga más información sobre la [puntuación segura y los controles de seguridad en Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>La API de Puntuación segura se ha publicado en disponibilidad general (GA)

Ahora puede acceder a la puntuación a través de la [API de puntuación segura](/rest/api/securitycenter/securescores/). Los métodos de API proporcionan la flexibilidad necesaria para consultar los datos y crear su propio mecanismo de creación de informes de las puntuaciones seguras a lo largo del tiempo. Por ejemplo:

- use la API **Secure Scores** para obtener la puntuación de una suscripción específica.
- use la API **Secure Score Controls** para mostrar los controles de seguridad y la puntuación actual de las suscripciones.

Conozca las herramientas externas que son posibles gracias a la API de puntuación segura en el [área de puntuación segura de la comunidad de GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Obtenga más información sobre la [puntuación segura y los controles de seguridad en Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Las protecciones de DNS pendientes se han agregado a Azure Defender para App Service

Las adquisiciones de subdominios son una amenaza muy grave que se producen de forma común en las organizaciones. Una adquisición de subdominio puede producirse cuando se tiene un registro DNS que apunta a un sitio web desaprovisionado. Estos registros DNS también se conocen como entradas "DNS pendientes". Los registros CNAME son especialmente vulnerables a esta amenaza. 

Las adquisiciones de subdominios permiten a los actores de las amenazas redirigir el tráfico destinado al dominio de una organización a un sitio que realiza una actividad malintencionada.

Azure Defender para App Service ahora detecta las entradas DNS pendientes cuando se retira un sitio web de App Service. Este es el momento en el que la entrada DNS apunta a un recurso que no existe y su sitio web es vulnerable a la adquisición del subdominio. Estas protecciones están disponibles tanto si los dominios se administran con Azure DNS como con un registrador de dominios externo y se aplican a App Service en Windows y en Linux.

Más información:

- [Tabla de referencia de alertas de App Service](alerts-reference.md#alerts-azureappserv): incluye dos nuevas alertas de Azure Defender que se desencadenan cuando se detecta una entrada DNS pendiente
- [Evitar las entradas DNS pendientes y evitar la adquisición de subdominios](../security/fundamentals/subdomain-takeover.md): obtenga información sobre la amenaza de la adquisición de subdominios y el aspecto de los DNS pendientes
- [Introducción a Azure Defender para App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Los conectores de nubes múltiples se ha publicado con disponibilidad general (GA)

Las cargas de trabajo de nube abarcan normalmente varias plataformas de nube, por lo que los servicios de seguridad de la nube deben hacer lo mismo.

Azure Security Center protege las cargas de trabajo de Azure, Amazon Web Services (AWS) y Google Cloud Platform (GCP).

La conexión de las cuentas de AWS o GCP integra sus herramientas de seguridad nativas, como AWS Security Hub y GCP Security Center, en Azure Security Center.

Esta capacidad significa que Security Center proporciona visibilidad y protección en los principales entornos de nube. Algunas de las ventajas de esta integración son:

- Aprovisionamiento automático de agentes (Security Center usa Azure Arc para implementar el agente de Log Analytics en las instancias de AWS)
- Administración de directivas
- Administración de vulnerabilidades
- Detección y respuesta de puntos de conexión (EDR) integrados
- Detección de errores de configuración de seguridad
- Una vista única que muestra recomendaciones de seguridad de todos los proveedores de nube
- Incorporación de todos los recursos a los cálculos de puntuación segura de Security Center
- Evaluaciones de cumplimiento normativo de los recursos de AWS y GCP

En el menú de Security Center, seleccione **Multi cloud connectors** (Conectores en varias nubes) y verá las opciones para crear conectores:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Botón Add AWS account (Agregar cuenta de AWS) de la página Conectores multinube de Security Center":::

Puede encontrar más información en:
- [Conexión de las cuentas de AWS a Azure Security Center](quickstart-onboard-aws.md)
- [Conexión de las cuentas de GCP a Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Exclusión de recomendaciones completas de su puntuación segura para suscripciones y grupos de administración

Estamos ampliando la capacidad de exención para incluir recomendaciones completas. Proporcionar más opciones para ajustar las recomendaciones de seguridad que Security Center realiza para las suscripciones, el grupo de administración o los recursos.

En ocasiones, un recurso se mostrará como incorrecto cuando se sepa que el problema se ha resuelto mediante una herramienta de terceros que Security Center no ha detectado. O bien se mostrará una recomendación en un ámbito al que cree que no pertenece. La recomendación podría no ser adecuada para una suscripción concreta. O quizás la organización ha decidido aceptar los riesgos relacionados con la recomendación o el recurso específicos.

Con esta característica en vista previa (GB), ahora puede crear una exención para que una recomendación:

- **Excluya un recurso** para asegurarse de que no aparezca con los recursos incorrectos en el futuro y no afecte a la puntuación de seguridad. El recurso se mostrará como no aplicable y el motivo aparecerá como "exento" con la justificación específica que seleccione.

- **Excluir una suscripción o un grupo de administración** para asegurarse de que la recomendación no afecte a la puntuación de seguridad y no que no se muestre para la suscripción o el grupo de administración en el futuro. Esto se refiere a los recursos existentes y cualquiera que cree en el futuro. La recomendación se marcará con la justificación específica que seleccione para el ámbito que ha seleccionado.

Puede obtener más información en [Exención de recursos y recomendaciones de la puntuación de seguridad](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Los usuarios ya pueden solicitar visibilidad en todo el inquilino de su administrador global

Si algún usuario no tiene los permisos necesarios para ver los datos de Security Center, ahora verá un vínculo para solicitar permisos al administrador global de su organización. La solicitud incluye el rol que desean y la justificación de por qué es necesario.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner que informa a los usuarios de que pueden solicitar permisos para todo el inquilino.":::

Para más información, consulte [Solicitud de permisos para todo el inquilino cuando el suyo sea insuficiente](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Se han agregado 35 recomendaciones en versión preliminar para aumentar la cobertura de Azure Security Benchmark.

[Azure Security Benchmark](/security/benchmark/azure/introduction) es la iniciativa de directivas predeterminada de Azure Security Center. 

Para aumentar la cobertura del punto de referencia se han agregado a Security Center las siguientes 35 recomendaciones en versión preliminar.

> [!TIP]
> Las recomendaciones de la versión preliminar no representan un recurso incorrecto y no se incluyen en los cálculos de una puntuación segura. Corríjalas siempre que sea posible, de tal forma que, cuando finalice el período de versión preliminar, contribuyan a la puntuación. Puede encontrar más información sobre cómo responder a estas recomendaciones en [Recomendaciones de corrección en Azure Security Center](security-center-remediate-recommendations.md).

| Control de seguridad                     | Nuevas recomendaciones                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitación del cifrado de datos en reposo            | - Las cuentas de Azure Cosmos DB deben usar claves administradas por el cliente para cifrar los datos en reposo.<br>- Las áreas de trabajo de Azure Machine Learning deben cifrarse con una clave administrada por el cliente (CMK).<br>- La protección de datos de Bring Your Own Key debe estar habilitada para los servidores MySQL.<br>- La protección de datos de Bring Your Own Key debe estar habilitada para los servidores PostgreSQL.<br>- Las cuentas de Cognitive Services deben habilitar el cifrado de datos con una clave administrada por el cliente (CMK).<br>- Las instancias de Container Registry se deben cifrar con una clave administrada por el cliente (CMK).<br>- Las instancias administradas de SQL deben usar claves administradas por el cliente para cifrar los datos en reposo.<br>- Los servidores SQL deben usar claves administradas por el cliente para cifrar los datos en reposo.<br>- Las cuentas de almacenamiento deben usar la clave administrada por el cliente (CMK) para el cifrado.                                                                                                                                                              |
| Implementación de procedimientos recomendados de seguridad    | - Las suscripciones deben tener una dirección de correo electrónico de contacto para los problemas de seguridad.<br> - El aprovisionamiento automático del agente de Log Analytics debe estar habilitado en su suscripción.<br> - La opción para enviar notificaciones por correo electrónico para alertas de gravedad alta debe estar habilitada.<br> - La opción para enviar notificaciones por correo electrónico al propietario de la suscripción en relación a alertas de gravedad alta debe estar habilitada.<br> - Los almacenes de claves deben tener habilitada la protección contra operaciones de purga.<br> - Los almacenes de claves deben tener habilitada la eliminación temporal. |
| Administración de acceso y permisos        | - Las aplicaciones de funciones deben tener la opción "Certificados de cliente (certificados de cliente entrantes)" habilitada. |
| Protección de aplicaciones contra ataques DDoS | - Web Application Firewall (WAF) debe estar habilitado para Application Gateway.<br> - Web Application Firewall (WAF) debe estar habilitado para Azure Front Door Service. |
| Restricción de los accesos de red no autorizados | - El firewall debe estar habilitado en Key Vault.<br> - Se debe configurar un punto de conexión privado para Key Vault.<br> - App Configuration debe usar un vínculo privado.<br> - Azure Cache for Redis debe residir en una red virtual.<br> - Los dominios de Azure Event Grid deben usar un vínculo privado.<br> - Los temas de Azure Event Grid deben usa un vínculo privado.<br> - Las áreas de trabajo de Azure Machine Learning deben usar un vínculo privado.<br> Azure SignalR Service debe usar un vínculo privado.<br> - Azure Spring Cloud debe usar la inserción de red.<br> -Las instancias de Container Registry no deben permitir el acceso de red sin restricciones.<br> - Las instancias de Container Registry deben usar vínculo privado.<br> -El acceso a redes públicas debe estar deshabilitado para los servidores de MariaDB.<br> El acceso a las redes públicas debe estar deshabilitado para los servidores de MySQL<br> - El acceso a redes públicas debe estar deshabilitado para los servidores de PostgreSQL.<br> - La cuenta de almacenamiento debería utilizar una conexión de vínculo privado<br> - Las cuentas de almacenamiento deben restringir el acceso a la red mediante el uso de reglas de red virtual.<br> - Las plantillas de VM Image Builder deben usar un vínculo privado.|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Vínculos relacionados:

- [Más información sobre Azure Security Benchmark](/security/benchmark/azure/introduction)
- [Más información sobre Azure Database for MariaDB](../mariadb/overview.md)
- [Más información sobre Azure Database for MySQL](../mysql/overview.md)
- [Más información sobre Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Exportación de CSV de una lista filtrada de recomendaciones 

En noviembre de 2020, hemos agregado filtros a la página de recomendaciones (consulte [La lista de recomendaciones ahora incluye filtros](release-notes-archive.md#recommendations-list-now-includes-filters)). En diciembre, hemos expandido esos filtros ([La página Recomendaciones tiene nuevos filtros de entorno, gravedad y respuestas disponibles](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Con este anuncio, vamos a cambiar el comportamiento del botón **Descargar como CSV** para que la exportación de CSV solo incluya las recomendaciones que se muestran actualmente en la lista filtrada. 

Por ejemplo, en la imagen siguiente puede ver que la lista se ha filtrado a dos recomendaciones. El archivo CSV que se genera incluye los detalles de estado de cada recurso afectado por esas dos recomendaciones.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportación de las recomendaciones filtradas a un archivo .csv.":::

Más información en [Recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md)


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Los recursos en estado "No aplicable" ahora se notifican como "Compatible" en las valoraciones de Azure Policy

Anteriormente, los recursos que se evaluaban para una recomendación y se detectaba que eran **no aplicables** aparecían en in Azure Policy como "No compatible". Ninguna acción del usuario podría cambiar su estado a "Compatible". Con este cambio, se notificarán como "Compatible" para aumentar la claridad.

El único impacto se verá en Azure Policy, donde el número de recursos compatibles aumentará. Esto no afectará a la puntuación de seguridad de Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportación de instantáneas semanales de datos de cumplimiento normativo y puntuación segura con exportación continua (versión preliminar)

Hemos agregado una nueva característica en versión preliminar a las herramientas de [exportación continua](continuous-export.md) para exportar instantáneas semanales de datos de cumplimiento normativo y puntuación segura.

Al definir una exportación continua, establezca la frecuencia de exportación:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Elección de la frecuencia de la exportación continua.":::

- **Streaming**: las evaluaciones se enviarán cuando se actualice el estado de mantenimiento de un recurso (si no se produce ninguna actualización, no se enviarán datos).
- **Instantáneas**: se enviará una instantánea del estado actual de todas las evaluaciones de cumplimiento normativo cada semana (se trata de una característica en versión preliminar para las instantáneas semanales de datos de cumplimiento normativo y puntuaciones seguras).

Puede encontrar más información sobre las funcionalidades completas de esta característica en [Exportación continua de alertas y recomendaciones de seguridad](continuous-export.md).

## <a name="december-2020"></a>Diciembre de 2020

Las actualizaciones de diciembre incluyen:

- [Disponibilidad general de Azure Defender para servidores SQL Server en máquinas](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Compatibilidad de Azure Defender para SQL para el grupo de SQL dedicado de Azure Synapse Analytics disponible con carácter general](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Ahora, los administradores globales se pueden conceder a sí mismos permisos de nivel de inquilino](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dos nuevos planes de Azure Defender: Azure Defender para DNS y Azure Defender para Resource Manager (en versión preliminar)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nueva página de alertas de seguridad en Azure Portal (versión preliminar)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Experiencia de Security Center revitalizada en Azure SQL Database y SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Se han actualizado las herramientas y filtros para el inventario de recursos](#asset-inventory-tools-and-filters-updated)
- [La recomendación acerca de las aplicaciones web que solicitan certificados SSL ya no forma parte de la puntuación segura](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [La página Recomendaciones tiene nuevos filtros de entorno, gravedad y respuestas disponibles](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [La exportación continua obtiene nuevos tipos de datos y directivas deployifnotexist mejoradas](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Disponibilidad general de Azure Defender para servidores SQL Server en máquinas

Azure Security Center ofrece dos planes de Azure Defender para servidores SQL Server:

- **Azure Defender para servidores de bases de datos Azure SQL**: defiende los servidores SQL Server nativos de Azure. 
- **Azure Defender para servidores SQL Server en máquinas**: amplía las mismas protecciones a los servidores SQL Server en entornos híbridos, multinube y locales.

Con este anuncio, **Azure Defender para SQL** ahora protege las bases de datos y sus datos dondequiera que se encuentren.

Azure Defender para SQL incluye funcionalidades de evaluación de vulnerabilidades. La herramienta de evaluación de vulnerabilidades incluye las siguientes características avanzadas:

- La **configuración de línea de base** (nuevo) para limitar de forma inteligente los resultados de los análisis de vulnerabilidades a aquellas que podrían representar problemas de seguridad reales. Una vez establecido el estado de seguridad de la línea de base, la herramienta de evaluación de vulnerabilidades solo informa de las desviaciones con respecto a esa línea de base. Los resultados que coinciden con la línea de base se consideran como correctos en análisis posteriores. Esto le permite a usted y a sus analistas centrar la atención en lo que importa.
- **Información comparativa detallada** para ayudarle a *comprender* los hechos detectados y por qué se relacionan con los recursos.
- **Scripts de corrección** para ayudarle a mitigar los riesgos identificados.

Más información sobre [Azure Defender for SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Compatibilidad de Azure Defender para SQL para Azure Synapse Analytics disponible con carácter general

Azure Synapse Analytics (anteriormente SQL Data Warehouse) es un servicio de análisis que combina almacenamiento de datos empresariales con análisis de macrodatos. Los grupos de SQL dedicados son las características de almacenamiento de datos empresariales de Azure Synapse. Más información en [¿Qué es Azure Synapse Analytics (anteriormente SQL Data Warehouse)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Azure Defender para SQL protege los grupos de SQL dedicados con:

- **Protección contra amenazas avanzada** para detectar amenazas y ataques. 
- **Funcionalidades de evaluación de vulnerabilidades** para identificar y corregir configuraciones erróneas de seguridad.

La compatibilidad de Azure Defender para SQL con los grupos de SQL de Azure Synapse Analytics se agrega automáticamente al paquete de bases de datos SQL de Azure en Azure Security Center. Encontrará una nueva pestaña "Azure Defender para SQL" en la página del área de trabajo de Synapse en Azure Portal.

Más información sobre [Azure Defender for SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Ahora, los administradores globales se pueden conceder a sí mismos permisos de nivel de inquilino.

Un usuario con el rol de **administrador global** de Azure Active Directory puede tener responsabilidades en todo el inquilino, pero carecer de los permisos de Azure para ver la información de toda la organización en Azure Security Center. 

Para asignarse a sí mismo permisos de nivel de inquilino, siga las instrucciones que se indican en [Concederse a uno mismo permisos para todo el inquilino](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dos nuevos planes de Azure Defender: Azure Defender para DNS y Azure Defender para Resource Manager (en versión preliminar)

Se han agregado dos nuevas funcionalidades de protección contra amenazas con amplitud nativa de nube para el entorno de Azure.

Estas nuevas protecciones mejoran enormemente la resistencia frente a los ataques de actores de amenazas y aumentan considerablemente el número de recursos de Azure protegidos por Azure Defender.

- **Azure Defender para Resource Manager**: supervisa automáticamente todas las operaciones de administración de recursos realizadas en la organización. Para más información, consulte:
    - [Introducción a Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)
    - [Respuesta a las alertas de Azure Defender para Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista de alertas proporcionadas por Azure Defender para Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender para DNS**: supervisa continuamente todas las consultas de DNS de los recursos de Azure. Para más información, consulte:
    - [Introducción a Azure Defender para DNS](defender-for-dns-introduction.md)
    - [Respuesta a las alertas de Azure Defender para DNS](defender-for-dns-usage.md)
    - [Lista de alertas proporcionadas por Azure Defender para DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nueva página de alertas de seguridad en Azure Portal (versión preliminar)

La página de alertas de seguridad de Azure Security Center se ha rediseñado para proporcionar:

- **Mejora en la evaluación de prioridades en las alertas**: ahora es más fácil ayudar a reducir la fatiga de las alertas y centrarse en las amenazas más importantes, la lista incluye filtros personalizables y opciones de agrupación.
- **Más información en la lista de alertas**: por ejemplo las tácticas ATT y ACK de MITRE
- **Botón para crear alertas de ejemplo**: para evaluar las funcionalidades de Azure Defender y probar la configuración de las alertas (para la integración de SIEM, las notificaciones por correo electrónico y las automatizaciones de los flujos de trabajo), puede crear alertas de ejemplo desde todos los planes de Azure Defender.
- **Alineación con la experiencia de incidentes de Azure Sentinel**: para los clientes que usan ambos productos, cambiar entre ellos ahora es más sencillo y es fácil que uno aprenda del otro.
- **Mejor rendimiento** de listas de alertas grandes.
- **Desplazamiento mediante el teclado** a través de la lista de alertas.
- **Alertas de Azure Resource Graph**: puede consultar las alertas en Azure Resource Graph, la API similar a Kusto para todos los recursos. Esto también resulta útil si va a crear sus propios paneles de alertas. [Más información sobre Azure Resource Graph](../governance/resource-graph/index.yml).

Para acceder a la nueva experiencia, use el vínculo "Intentarlo ahora" del banner de la parte superior de la página de alertas de seguridad.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner con un vínculo a la versión preliminar de la nueva experiencia de alertas.":::

Para crear alertas de ejemplo a partir de la nueva experiencia de alertas, consulte el apartado [Generación de alertas de ejemplo de Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Experiencia de Security Center revitalizada en Azure SQL Database y SQL Managed Instance 

La experiencia de Security Center en SQL proporciona acceso a las siguientes características de Security Center y Azure Defender para SQL:

- **Recomendaciones de seguridad**: Azure Security Center analiza periódicamente el estado de seguridad de todos los recursos de Azure conectados para identificar posibles configuraciones erróneas de seguridad. A continuación, proporciona recomendaciones sobre cómo corregir esas vulnerabilidades y mejorar la postura de seguridad de las organizaciones.
- **Alertas de seguridad**: un servicio de detección que supervisa continuamente las actividades de Azure SQL para detectar amenazas tales como la inyección de código SQL, ataques por fuerza bruta y abuso de privilegios. Este servicio desencadena alertas de seguridad detalladas y orientadas a acciones en Security Center, y proporciona opciones para continuar las investigaciones con Azure Sentinel, la solución SIEM nativa de Microsoft Azure.
- **Conclusiones**: un servicio de evaluación de vulnerabilidades que supervisa continuamente las configuraciones de Azure SQL y ayuda a corregir las vulnerabilidades. Los análisis de evaluación proporcionan una visión general de los estados de seguridad de Azure SQL junto con conclusiones detalladas de seguridad.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Las características de seguridad de Azure Security Center para SQL están disponibles en Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Se han actualizado las herramientas y filtros para el inventario de recursos

La página Inventario de Azure Security Center se ha actualizado con los cambios siguientes:

- Se han agregado **guías y comentarios** a la barra de herramientas. Esto permite abrir un panel con vínculos a información y herramientas relacionados. 
- Se ha agregado un **filtro de suscripciones** a los filtros predeterminados disponibles para los recursos.
- Se ha agregado el enlace **Abrir consulta** para abrir las opciones de filtro actuales como una consulta de Azure Resource Graph (anteriormente se denominaba "Ver en Resource Graph Explorer").
- **Opciones de operador** para cada filtro. Ahora puede elegir entre más operadores lógicos adicionales distintos de "=". Por ejemplo, puede que desee buscar todos los recursos con recomendaciones activas cuyos títulos incluyan la cadena "Encrypt". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Controles para la opción de operador en los filtros de Asset Inventory":::

Más información acerca del inventario en [Exploración y administración de los recursos con Asset Inventory](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>La recomendación acerca de las aplicaciones web que solicitan certificados SSL ya no forma parte de la puntuación segura

La recomendación "Las aplicaciones web deben solicitar un certificado SSL a todas las solicitudes entrantes" se ha trasladado desde el control de seguridad **Administración de acceso y permisos** (con una puntuación máxima de 4 puntos) a **Implementación de procedimientos recomendados de seguridad** (con ningún punto). 

Asegurarse de que una aplicación web solicita un certificado ciertamente hace que sea más segura. Sin embargo, para las aplicaciones web de acceso público, esto es irrelevante. Si accede a su sitio a través de HTTP y no de HTTPS, no recibirá ningún certificado de cliente. Por lo tanto, si la aplicación requiere certificados de cliente, no debe permitir solicitudes a la aplicación mediante HTTP. Puede encontrar más información en [Configuración de la autenticación mutua de TLS en Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Con este cambio, la recomendación es ahora un procedimiento recomendado que no influye en la puntuación. 

Obtenga información sobre qué recomendaciones se encuentran en cada control de seguridad en [Controles de seguridad y sus recomendaciones](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>La página Recomendaciones tiene nuevos filtros de entorno, gravedad y respuestas disponibles

Azure Security Center supervisa todos los recursos conectados y genera recomendaciones de seguridad. Use estas recomendaciones para fortalecer su postura en la nube híbrida y realizar un seguimiento del cumplimiento de las directivas y los estándares importantes para su organización, sector y país.

A medida que Security Center amplía su cobertura y características, la lista de recomendaciones de seguridad crece cada mes. Por ejemplo, consulte [Se han agregado 29 recomendaciones en versión preliminar para aumentar la cobertura de Azure Security Benchmark](release-notes-archive.md#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Con una lista en continuo crecimiento, existe la necesidad de filtrar las recomendaciones para encontrar las de mayor interés. En noviembre, hemos agregado filtros a la página Recomendaciones (consulte [La lista de recomendaciones ahora incluye filtros](release-notes-archive.md#recommendations-list-now-includes-filters)).

Los filtros que se han agregado este mes proporcionan opciones para refinar la lista de recomendaciones según:

- El **entorno**: vea las recomendaciones para los recursos de AWS, GCP o Azure (o cualquier combinación).
- La **gravedad**: vea las recomendaciones según la clasificación de gravedad establecida por Security Center.
- **Acciones de respuesta**: consulte las recomendaciones según la disponibilidad de las opciones de respuesta de Security Center: corrección, denegación y aplicación

    > [!TIP]
    > El filtro de acciones de respuesta reemplaza al filtro **Solución rápida disponible (Sí/No)** . 
    > 
    > Puede aprender más información acerca de cada una de estas opciones de respuesta:
    > - [Botón Corregir](security-center-remediate-recommendations.md#fix-button)
    > - [Evitar errores de configuración con las recomendaciones Aplicar o Denegar](prevent-misconfigurations.md).

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Recomendaciones agrupadas por control de seguridad." lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>La exportación continua obtiene nuevos tipos de datos y directivas deployifnotexist mejoradas

Las herramientas de exportación continua de Azure Security Center permiten exportar las recomendaciones y alertas de Security Center para su uso con otras herramientas de supervisión de su entorno.

La exportación continua le permite personalizar completamente qué se exportará y a dónde irá. Para más información, consulte [Exportación continua de datos de Security Center](continuous-export.md).

Estas herramientas se han mejorado y ampliado de las siguientes maneras:

- **Se han mejorado las directivas deployifnotexist de la exportación continua**. Las directivas ahora:

    - **Comprueban si la configuración está habilitada.** Si no es así, la directiva aparecerá como no compatible y creará un recurso compatible. Obtenga más información acerca de las plantillas de Azure Policy proporcionadas en la pestaña "Implementación a gran escala con Azure Policy" de[Configuración de una exportación continua](continuous-export.md#set-up-a-continuous-export).

    - **Compatibilidad con la exportación de conclusiones de seguridad.** Cuando usa las plantillas de Azure Policy, puede configurar la exportación continua para que incluya conclusiones. Esto es importante cuando se exportan recomendaciones que incluyen "subrecomendaciones", como en el caso de los resultados de los escáneres de evaluación de vulnerabilidades o las actualizaciones específicas del sistema para la recomendación principal "Se deben instalar actualizaciones del sistema en las máquinas".
    
    - **Compatibilidad con la exportación de datos de puntuación de seguridad.**

- **Se han agregado datos de evaluación de cumplimiento normativo (en versión preliminar).** Ahora puede exportar continuamente las actualizaciones de las evaluaciones de cumplimiento normativo, incluidas las iniciativas personalizadas, a un área de trabajo Log Analytics o a un centro de eventos. Esta característica no está disponible en las nubes nacionales o soberanas.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Opciones para incluir información de evaluación conforme a la normativa con los datos de exportación continua.":::

## <a name="november-2020"></a>Noviembre de 2020

Las actualizaciones de noviembre incluyen:

- [Se han agregado 29 recomendaciones en versión preliminar para aumentar la cobertura de Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).
- [Se ha agregado NIST SP 800 171 R2 al panel de cumplimiento normativo de Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard).
- [La lista de recomendaciones ahora incluye filtros](#recommendations-list-now-includes-filters)
- [Mejora y ampliación de la experiencia de aprovisionamiento automático](#auto-provisioning-experience-improved-and-expanded)
- [La puntuación de seguridad ahora está disponible en la exportación continua (versión preliminar)](#secure-score-is-now-available-in-continuous-export-preview)
- [La recomendación "Las actualizaciones del sistema deben estar instaladas en las máquinas" ahora incluye recomendaciones secundarias](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [En la página de administración de directivas de Azure Portal ahora se muestra el estado de las asignaciones de directivas predeterminadas](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Se han agregado 29 recomendaciones en versión preliminar para aumentar la cobertura de Azure Security Benchmark.

Azure Security Benchmark es el conjunto de directrices específico de Azure creado por Microsoft para ofrecer los procedimientos recomendados de seguridad y cumplimiento basados en marcos de cumplimiento comunes. [Mas información sobre Azure Security Benchmark](/security/benchmark/azure/introduction).

Se han agregado a Security Center las siguientes 29 recomendaciones en versión preliminar para aumentar la cobertura de la prueba comparativa.

Las recomendaciones de la versión preliminar no representan un recurso incorrecto y no se incluyen en los cálculos de una puntuación segura. Corríjalas siempre que sea posible, de tal forma que, cuando finalice el período de versión preliminar, contribuyan a la puntuación. Puede encontrar más información sobre cómo responder a estas recomendaciones en [Recomendaciones de corrección en Azure Security Center](security-center-remediate-recommendations.md).

| Control de seguridad                     | Nuevas recomendaciones                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cifrado de los datos en tránsito              | - La aplicación de la conexión SSL debe estar habilitada para los servidores de base de datos PostgreSQL.<br>- La aplicación de la conexión SSL debe estar habilitada para los servidores de bases de datos MySQL.<br>- TLS debe actualizarse a la versión más reciente en la aplicación de API.<br>- TLS debe actualizarse a la versión más reciente en la aplicación de funciones.<br>- TLS debe actualizarse a la versión más reciente en la aplicación web.<br>- Es necesario exigir FTPS en la aplicación de API.<br>- Es necesario exigir FTPS en la aplicación de funciones.<br>- Es necesario exigir FTPS en la aplicación web.                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Administración de acceso y permisos        | - Las aplicaciones web deben solicitar un certificado SSL a todas las solicitudes entrantes.<br>- Se debe usar una identidad administrada en la aplicación de API.<br>- Se debe usar una identidad administrada en la aplicación de funciones.<br>- Se debe usar una identidad administrada en la aplicación web.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restricción de los accesos de red no autorizados | - El punto de conexión privado debe estar habilitado para servidores PostgreSQL.<br>- El punto de conexión privado debe estar habilitado para servidores MariaDB.<br>- El punto de conexión privado debe estar habilitado para servidores MySQL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Habilitar auditoría y registro          | - Los registros de diagnóstico de App Services deben estar habilitados                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementación de procedimientos recomendados de seguridad    | - Azure Backup debería habilitarse en las máquinas virtuales.<br>- La copia de seguridad con redundancia geográfica debe estar habilitada para Azure Database for MariaDB.<br>- La copia de seguridad con redundancia geográfica debe estar habilitada para Azure Database for MySQL.<br>- La copia de seguridad con redundancia geográfica debe estar habilitada para Azure Database for PostgreSQL.<br>- PHP debe actualizarse a la versión más reciente en la aplicación de API.<br>- PHP debe actualizarse a la versión más reciente en la aplicación web.<br>- Java debe actualizarse a la versión más reciente en la aplicación de API.<br>- Java debe actualizarse a la versión más reciente en la aplicación de funciones.<br>- Java debe actualizarse a la versión más reciente en la aplicación web.<br>- Python debe actualizarse a la versión más reciente en la aplicación de API.<br>- Python debe actualizarse a la versión más reciente en la aplicación de funciones.<br>- Python debe actualizarse a la versión más reciente en la aplicación web.<br>- La retención de la auditoría en los servidores SQL Server debe establecerse en 90 días, como mínimo. |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Vínculos relacionados:

- [Más información sobre Azure Security Benchmark](/security/benchmark/azure/introduction)
- [Más información sobre las aplicaciones de API de Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Más información sobre las aplicaciones de funciones de Azure](../azure-functions/functions-overview.md)
- [Más información sobre las aplicaciones web de Azure](../app-service/overview.md)
- [Más información sobre Azure Database for MariaDB](../mariadb/overview.md)
- [Más información sobre Azure Database for MySQL](../mysql/overview.md)
- [Más información sobre Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Se ha agregado NIST SP 800 171 R2 al panel de cumplimiento normativo de Security Center.

La norma NIST SP 800-171 R2 ahora está disponible como una iniciativa integrada para su uso con el panel de cumplimiento normativo de Azure Security Center. Las asignaciones de los controles se describen en [Detalles de la iniciativa integrada de cumplimiento normativo NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Para aplicar la norma a sus suscripciones y supervisar continuamente el estado de cumplimiento, siga las instrucciones que se describen en [Personalización del conjunto de normas del panel de cumplimiento normativo](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Norma NIST SP 800 171 R2 en el panel de cumplimiento normativo de Security Center":::

Para más información acerca de este estándar normativo, consulte [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>La lista de recomendaciones ahora incluye filtros

Ahora puede filtrar la lista de recomendaciones de seguridad de acuerdo con una gama de criterios. En el ejemplo siguiente, la lista de recomendaciones se ha filtrado para mostrar recomendaciones que:

- estén **disponibles con carácter general** (es decir, no en versión preliminar).
- sean para **cuentas de almacenamiento**.
- admitan las correcciones de **Corrección rápida**.

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtros de la lista de recomendaciones.":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Mejora y ampliación de la experiencia de aprovisionamiento automático

La característica de aprovisionamiento automático ayuda a reducir la sobrecarga de administración mediante la instalación de las extensiones necesarias en máquinas virtuales de Azure nuevas y existentes para que puedan beneficiarse de la protección de Security Center. 

A medida que crece Azure Security Center, se han desarrollado más extensiones y Security Center puede supervisar una lista más amplia de tipos de recursos. Las herramientas de aprovisionamiento automático ahora se han ampliado para admitir otras extensiones y tipos de recursos que aprovechan las funcionalidades de Azure Policy.

Ahora puede configurar el aprovisionamiento automático de:

- Agente de Log Analytics
- (Nuevo) Complemento de Azure Policy para Kubernetes
- (Nuevo) Microsoft Dependency Agent

Obtenga más información en [Aprovisionamiento automático de agentes y extensiones de Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>La puntuación de seguridad ahora está disponible en la exportación continua (versión preliminar)

Con la exportación continua de la puntuación de seguridad, puede transmitir los cambios de la puntuación en tiempo real a Azure Event Hubs o a un área de trabajo de Log Analytics. Use esta funcionalidad para:

- Realizar un seguimiento de la puntuación de seguridad en el tiempo con informes dinámicos
- Exportar datos de puntuación de seguridad a Azure Sentinel (o a cualquier otro SIEM)
- Integrar estos datos con cualquier proceso que ya esté usando para supervisar la puntuación de seguridad en la organización

Obtenga más información sobre la [Exportación continua de datos de Security Center](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>La recomendación "Las actualizaciones del sistema deben estar instaladas en las máquinas" ahora incluye recomendaciones secundarias

La recomendación **Las actualizaciones del sistema deben estar instaladas en las máquinas** se ha mejorado. La nueva versión incluye recomendaciones secundarias para cada actualización que falta y ofrece las siguientes mejoras:

- Una nueva experiencia en las páginas de Azure Security Center de Azure Portal. La página de detalles de la recomendación **Las actualizaciones del sistema deben estar instaladas en las máquinas** incluye la lista de conclusiones que se muestran a continuación. Al seleccionar una única búsqueda, se abre el panel de detalles con un vínculo a la información de corrección y una lista de los recursos afectados.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Apertura de una de las recomendaciones secundarias en la experiencia del portal para la recomendación actualizada.":::

- Datos enriquecidos para la recomendación de Azure Resource Graph (ARG). ARG es un servicio de Azure diseñado para proporcionar una exploración de recursos eficaz. Puede usar ARG para realizar consultas a escala para un conjunto determinado de suscripciones a fin de controlar eficazmente su entorno. 

    Para Azure Security Center, puede usar ARG y el [lenguaje de consulta Kusto (KQL)](/azure/data-explorer/kusto/query/) para consultar una amplia variedad de datos de posición de seguridad.

    Anteriormente, si consultaba esta recomendación en ARG, la única información disponible era que la recomendación debía corregirse en una máquina. La siguiente consulta de la versión mejorada devolverá las actualizaciones del sistema que faltan agrupadas por máquina.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>En la página de administración de directivas de Azure Portal ahora se muestra el estado de las asignaciones de directivas predeterminadas

Ahora puede ver si las suscripciones tienen la directiva de Security Center predeterminada asignada, en la página **Directiva de seguridad** de Security Center en Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="En la página de administración de directivas de Azure Security Center se muestran las asignaciones de directivas predeterminadas.":::



## <a name="october-2020"></a>Octubre de 2020

Las actualizaciones de octubre incluyen:
- [Evaluación de vulnerabilidades para máquinas locales y en varias nubes (versión preliminar)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Recomendación sobre Azure Firewall agregada (versión preliminar)](#azure-firewall-recommendation-added-preview)
- [La recomendación de que los intervalos IP autorizados deben definirse en los servicios de Kubernetes se ha actualizado con una corrección rápida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [El panel de cumplimiento normativo ahora incluye la opción para eliminar estándares](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabla Microsoft.Security/securityStatuses quitada de Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Evaluación de vulnerabilidades para máquinas locales y en varias nubes (versión preliminar)

El analizador de evaluación de vulnerabilidades integrado de [Azure Defender para servidores](defender-for-servers-introduction.md) (con tecnología de Qualys) ahora analiza los servidores habilitados para Azure Arc.

Al habilitar Azure Arc en máquinas que no son de Azure, Security Center le ofrecerá la opción de implementar en ellas el analizador de vulnerabilidades integrado, manualmente y a escala.

Con esta actualización, puede aprovechar toda la capacidad de **Azure Defender para servidores** para consolidar su programa de administración de vulnerabilidades en todos sus recursos, tanto si son de Azure como si no.

Principales funcionalidades:

- Supervisión del estado de aprovisionamiento del analizador de evaluación de vulnerabilidades en máquinas de Azure Arc.
- Aprovisionamiento del agente de evaluación de vulnerabilidades integrado en máquinas de Azure Arc Windows y Linux (manualmente y a escala).
- Recepción y análisis de vulnerabilidades detectadas por agentes implementados (manualmente y a escala).
- Experiencia unificada para máquinas virtuales de Azure y máquinas de Azure Arc.

[Obtenga más información sobre la implementación del analizador de vulnerabilidades integrado en las máquinas híbridas](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Obtenga más información sobre los servidores habilitados para Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Recomendación sobre Azure Firewall agregada (versión preliminar)

Se ha agregado una nueva recomendación para proteger todas las redes virtuales con Azure Firewall.

La recomendación **Azure Firewall debe proteger las redes virtuales** le aconseja restringir el acceso a las redes virtuales y evitar posibles amenazas mediante el uso de Azure Firewall.

Más información acerca de [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>La recomendación de que los intervalos IP autorizados deben definirse en los servicios de Kubernetes se ha actualizado con una corrección rápida

La recomendación **Authorized IP ranges should be defined on Kubernetes Services** (Los intervalos IP autorizados deben definirse en los servicios de Kubernetes) ahora tiene una opción de corrección rápida.

Para más información sobre esta y todas las demás recomendaciones de Security Center, consulte [Guía de referencia sobre las recomendaciones de seguridad](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Recomendación &quot;Los intervalos IP autorizados deben definirse en los servicios de Kubernetes&quot; con la opción de corrección rápida.":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>El panel de cumplimiento normativo ahora incluye la opción para eliminar estándares

En el panel de cumplimiento normativo de Security Center se proporciona información sobre su postura de cumplimiento en función de cómo cumple los requisitos y controles de cumplimiento específicos.

El panel incluye un conjunto predeterminado de estándares normativos. Si alguno de ellos no es pertinente para su organización, quitarlo de la interfaz de usuario ahora es un proceso sencillo en una suscripción. Los estándares se pueden quitar solo en el nivel de *suscripción*, no en el ámbito del grupo de administración.

Encontrará más información en [Eliminación de un estándar del panel](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabla Microsoft.Security/securityStatuses quitada de Azure Resource Graph (ARG)

Azure Resource Graph es un servicio de Azure diseñado para proporcionar una exploración de recursos eficaz con la capacidad de consultar a escala a través de un conjunto especificado de suscripciones para que pueda controlar eficazmente el entorno. 

Para Azure Security Center, puede usar ARG y el [lenguaje de consulta Kusto (KQL)](/azure/data-explorer/kusto/query/) para consultar una amplia variedad de datos de posición de seguridad. Por ejemplo:

- El inventario de recursos utiliza ARG.
- Hemos documentado una consulta de ARG de ejemplo sobre la [Identificación de cuentas sin Multi-Factor Authentication (MFA) habilitado](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled).

En ARG hay tablas de datos que se pueden usar en las consultas.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Explorador de Azure Resource Graph y tablas disponibles.":::

> [!TIP]
> La documentación de ARG muestra todas las tablas disponibles en la [tabla de Azure Resource Graph y la referencia de tipo de recurso](../governance/resource-graph/reference/supported-tables-resources.md).

A partir de esta actualización, se ha quitado la tabla **Microsoft.Security/securityStatuses**. La API securityStatuses sigue estando disponible.

La tabla Microsoft.Security/Assessments puede usar sustitución de datos.

La diferencia principal entre Microsoft.Security/securityStatuses y Microsoft.Security/Assessments es que, mientras que la primera muestra la agregación de las evaluaciones, la segunda contienen un único registro para cada una.

Por ejemplo, Microsoft.Security/securityStatuses devolvería un resultado con una matriz de dos policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Por su parte, Microsoft.Security/Assessments contendrá un registro para cada una de estas evaluaciones de directivas como se indica a continuación:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Ejemplo de conversión de una consulta de ARG existente usando securityStatuses para usar ahora la tabla Assessments:**

Consulta que hace referencia a SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Consulta de sustitución para la tabla Assessments:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Para más información, consulte los siguientes vínculos:
- [Creación de consultas con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Lenguaje de consulta de Kusto (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Septiembre de 2020

Las actualizaciones de septiembre incluyen:
- [Security Center tiene un nuevo aspecto](#security-center-gets-a-new-look)
- [Lanzamiento de Azure Defender](#azure-defender-released)
- [Azure Defender para Key Vault está disponible con carácter general](#azure-defender-for-key-vault-is-generally-available)
- [La protección de Azure Defender para Storage en archivos y ADLS Gen2 está disponible con carácter general](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Las herramientas para el inventario de recursos ya están disponibles con carácter general](#asset-inventory-tools-are-now-generally-available)
- [Deshabilitación de la detección de una vulnerabilidad específica para análisis de registros de contenedores y máquinas virtuales](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Exclusión de un recurso de una recomendación](#exempt-a-resource-from-a-recommendation)
- [Los conectores de AWS y GCP de Security Center aportan una experiencia multinube](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Conjunto de recomendaciones de protección de cargas de trabajo de Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Los resultados de la evaluación de vulnerabilidades ahora están disponibles en la exportación continua](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Imponer recomendaciones al crear recursos para prevenir errores de configuración de seguridad](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Mejoras de las recomendaciones del grupo de seguridad de red](#network-security-group-recommendations-improved)
- [La recomendación de AKS en versión preliminar "Se deben definir las directivas de seguridad de pod en los servicios de Kubernetes" está en desuso](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Notificaciones por correo electrónico mejoradas en Azure Security Center](#email-notifications-from-azure-security-center-improved)
- [La puntuación de seguridad no incluye recomendaciones en versión preliminar](#secure-score-doesnt-include-preview-recommendations)
- [Las recomendaciones incluyen ahora un indicador de gravedad y el intervalo de actualización](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center tiene un nuevo aspecto

Hemos lanzado una interfaz de usuario actualizada para las páginas del portal de Security Center. Las nuevas páginas incluyen una nueva página de información general, así como paneles para puntuación de seguridad, inventario de recursos y Azure Defender.

La página de información general renovada ahora incorpora un icono para acceder a los paneles de puntuación de seguridad, inventario de recursos y Azure Defender. También tiene un icono vinculado al panel de cumplimiento normativo.

Más información sobre la [página de información general](overview-page.md)


### <a name="azure-defender-released"></a>Lanzamiento de Azure Defender

**Azure Defender** es la plataforma de protección de cargas de trabajo en la nube (CWPP) integrada en Security Center para la protección avanzada e inteligente de las cargas de trabajo híbridas y de Azure. Reemplaza a la opción del plan de tarifa estándar de Security Center. 

Al habilitar Azure Defender en el área **Precios y configuración** de Azure Security Center, se habilitan a la vez los siguientes planes de Defender, que proporcionan defensas completas para las capas de proceso, datos y servicio de su entorno:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender para App Service](defender-for-app-service-introduction.md)
- [Azure Defender para Storage](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registros de contenedor](defender-for-container-registries-introduction.md)

Cada uno de estos planes se explica por separado en la documentación de Security Center.

Con su panel dedicado, Azure Defender proporciona alertas de seguridad y protección contra amenazas avanzada para máquinas virtuales, bases de datos SQL, contenedores, aplicaciones web, la red y mucho más.

[Más información sobre Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender para Key Vault está disponible con carácter general

Azure Key Vault es un servicio en la nube que protege las claves de cifrado y los secretos, como certificados, cadenas de conexión y contraseñas. 

**Azure Defender para Key Vault** ofrece protección frente a amenazas avanzada, nativa de Azure, para Azure Key Vault, que proporciona una capa adicional de inteligencia de seguridad. Por extensión, Azure Defender para Key Vault protege por consiguiente muchos de los recursos que dependen de las cuentas de Key Vault.

El plan opcional ahora está en disponibilidad general. Esta característica se encontraba en versión preliminar como "protección contra amenazas avanzada para Azure Key Vault".

Además, las páginas de Key Vault de Azure Portal ahora incluyen una página de **Seguridad** dedicada para las recomendaciones y las alertas de **Security Center**.

Más información en [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>La protección de Azure Defender para Storage en archivos y ADLS Gen2 está disponible con carácter general 

**Azure Defender para Storage** detecta actividades potencialmente dañinas en las cuentas de Azure Storage. Los datos se pueden proteger tanto si se almacenan como contenedores de blobs, recursos compartidos de archivos o lagos de datos.

La compatibilidad con [Azure Files](../storage/files/storage-files-introduction.md) y [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) ahora está disponible con carácter general.

A partir del 1 de octubre de 2020, empezaremos a cobrar por la protección de los recursos de estos servicios.

Más información en [Azure Defender para Storage](defender-for-storage-introduction.md)


### <a name="asset-inventory-tools-are-now-generally-available"></a>Las herramientas para el inventario de recursos ya están disponibles con carácter general

La página de inventario de recursos de Azure Security Center proporciona una sola página para ver la posición de seguridad de los recursos que se han conectado a Security Center.

Security Center analiza periódicamente el estado de seguridad de los recursos de Azure para identificar posibles puntos vulnerables de la seguridad. A continuación, se proporcionan recomendaciones sobre cómo corregir dichos puntos vulnerables.

Cuando algún recurso tenga recomendaciones pendientes, aparecerán en el inventario.

Más información en [Exploración y administración de los recursos con Asset Inventory](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Deshabilitación de la detección de una vulnerabilidad específica para análisis de registros de contenedores y máquinas virtuales

Azure Defender incluye detectores de vulnerabilidades para examinar imágenes en Azure Container Registry y en las máquinas virtuales.

Si tiene una necesidad organizativa de omitir un resultado, en lugar de corregirlo, tiene la opción de deshabilitarlo. Los resultados deshabilitados no afectan a la puntuación de seguridad ni generan ruido no deseado.

Cuando un resultado coincide con los criterios que ha definido en las reglas de deshabilitación, no aparecerá en la lista de resultados.

Esta opción está disponible en las páginas de detalles de recomendaciones para:

- **Se deben corregir las vulnerabilidades de las imágenes de Azure Container Registry**
- **Es necesario corregir las vulnerabilidades de las máquinas virtuales**

Obtenga más información en [Deshabilitación de resultados específicos para las imágenes de contenedor](defender-for-container-registries-usage.md#disable-specific-findings-preview) y [Deshabilitación de resultados específicos para las máquinas virtuales](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Exclusión de un recurso de una recomendación

En ocasiones, un recurso se mostrará como incorrecto en relación con una recomendación específica y, por lo tanto, se reduce la puntuación de seguridad, aunque crea que no debería ser así. Es posible que lo haya corregido un proceso del que Security Center no ha realizado un seguimiento. O, quizás, la organización ha decidido aceptar el riesgo de ese recurso específico. 

En tales casos, puede crear una regla de exención para asegurarse de que el recurso no aparezca entre los recursos incorrectos en el futuro. Estas reglas pueden incluir justificaciones documentadas, tal y como se describe a continuación.

Más información en [Exención de un recurso de las recomendaciones y la puntuación de seguridad](exempt-resource.md)


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Los conectores de AWS y GCP de Security Center aportan una experiencia multinube

Las cargas de trabajo de nube abarcan normalmente varias plataformas de nube, por lo que los servicios de seguridad de la nube deben hacer lo mismo.

Azure Security Center ahora protege las cargas de trabajo de Azure, Amazon Web Services (AWS) y Google Cloud Platform (GCP).

Al incorporar las cuentas de AWS y GCP a Security Center, se integran AWS Security Hub, el centro de comandos de seguridad GCP y Azure Security Center. 

Obtenga más información en [Conexión de las cuentas de AWS a Azure Security Center](quickstart-onboard-aws.md) y [Conexión de las cuentas de GCP a Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Conjunto de recomendaciones de protección de cargas de trabajo de Kubernetes

Para asegurarse de que las cargas de trabajo de Kubernetes son seguras de forma predeterminada, Security Center está agregando recomendaciones de protección en el nivel de Kubernetes, incluidas las opciones de cumplimiento con el control de admisión de Kubernetes.

Una vez instalado el complemento de Azure Policy para Kubernetes en el clúster de AKS, todas las solicitudes al servidor de la API de Kubernetes se supervisarán según el conjunto predefinido de procedimientos recomendados antes de que se guarden en el clúster. Después, puede realizar la configurar para aplicar los procedimientos recomendados y exigirlos para futuras cargas de trabajo.

Por ejemplo, puede exigir que no se creen los contenedores con privilegios y que se bloqueen las solicitudes futuras para este fin.

Más información en [Procedimientos recomendados de protección de cargas de trabajo con el control de admisión de Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Los resultados de la evaluación de vulnerabilidades ahora están disponibles en la exportación continua

Use la exportación continua para transmitir sus alertas y recomendaciones a Azure Event Hubs, áreas de trabajo de Log Analytics o Azure Monitor. A partir de ahí, puede integrar estos datos con SIEM (como Azure Sentinel, Power BI, Azure Data Explorer y mucho más).

Las herramientas de evaluación de vulnerabilidades integradas en Security Center devuelven resultados sobre los recursos, como recomendaciones útiles dentro de una recomendación "principal", tipo "Es necesario corregir las vulnerabilidades de las máquinas virtuales.". 

Los resultados de seguridad ahora están disponibles para la exportación mediante la exportación continua al seleccionar recomendaciones y habilitar la opción **include security findings** (Incluir resultados de seguridad).

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Control de alternancia Incluir resultados de seguridad en la configuración de la exportación continua." :::

Páginas relacionadas:

- [Solución de evaluación de vulnerabilidades integrada en Security Center para Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Solución de evaluación de vulnerabilidades integrada en Security Center para las imágenes de Azure Container Registry](defender-for-container-registries-usage.md)
- [Exportación continua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Imponer recomendaciones al crear recursos para prevenir errores de configuración de seguridad

Los errores de configuración de seguridad son una causa principal de los incidentes de seguridad. Security Center ofrece ahora la posibilidad de ayudar a *evitar* errores de configuración de los nuevos recursos con respecto a recomendaciones específicas. 

Esta característica puede ayudar a mantener las cargas de trabajo seguras y a estabilizar la puntuación de seguridad.

La aplicación de una configuración segura, basada en una recomendación específica, se ofrece mediante dos opciones:

- Con el efecto **Denegar** de Azure Policy, puede impedir que se creen recursos incorrectos.

- Con la opción **Aplicar**, puede sacar partido del efecto **DeployIfNotExist** de Azure Policy y corregir automáticamente los recursos no compatibles tras la creación.
 
Está disponible para las recomendaciones de seguridad seleccionadas y puede encontrarse en la parte superior de la página de detalles del recurso.

Más información en [Impedir errores de configuración con las recomendaciones Exigir/Denegar](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Mejoras de las recomendaciones del grupo de seguridad de red

Se han mejorado las siguientes recomendaciones de seguridad relacionadas con los grupos de seguridad de red para reducir algunas instancias de falsos positivos.

- Todos los puertos de red deben estar restringidos en el NSG asociado a la máquina virtual
- Se deben cerrar los puertos de administración en las máquinas virtuales
- Las máquinas virtuales accesibles desde Internet deben estar protegidas con grupos de seguridad de red
- Las subredes deben estar asociadas con un grupo de seguridad de red.


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>La recomendación de AKS en versión preliminar "Se deben definir las directivas de seguridad de pod en los servicios de Kubernetes" está en desuso

La recomendación en versión preliminar "Las directivas de seguridad de pod deben definirse en los servicios de Kubernetes" está en desuso, tal y como se describe en la documentación de [Azure Kubernetes Service](../aks/use-pod-security-policies.md).

La característica de la directiva de seguridad de pod (versión preliminar), está programada para quedar en desuso y dejará de estar disponible a partir del 15 de octubre de 2020 en favor de Azure Policy para AKS.

Una vez que la directiva de seguridad de pod (versión preliminar) haya quedado en desuso, deberá deshabilitar la característica en todos los clústeres existentes que la incluyan para realizar futuras actualizaciones de clústeres y seguir recibiendo el soporte técnico de Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Notificaciones por correo electrónico mejoradas en Azure Security Center

Se han mejorado las siguientes áreas de los correos electrónicos con respecto a las alertas de seguridad: 

- Se ha agregado la capacidad de enviar notificaciones por correo electrónico sobre las alertas de todos los niveles de gravedad.
- Se ha agregado la capacidad de notificar a los usuarios con diferentes roles de Azure en la suscripción.
- Se notifica de forma proactiva y predeterminada a los propietarios de la suscripción sobre las alertas de gravedad alta (con una probabilidad elevada de que se produzcan infracciones genuinas).
- Se ha eliminado el campo de número de teléfono de la página de configuración de notificaciones por correo electrónico.

Más información en [Configuración de notificaciones de alertas de seguridad por correo electrónico](security-center-provide-security-contact-details.md)


### <a name="secure-score-doesnt-include-preview-recommendations"></a>La puntuación de seguridad no incluye recomendaciones en versión preliminar 

Security Center evalúa continuamente los recursos, suscripciones y la organización en busca de problemas de seguridad. A continuación, agrega todos los resultados a una sola puntuación para que pueda conocer de un vistazo la situación de la seguridad actual: cuanto mayor sea la puntuación, menor será el nivel de riesgo identificado.

A medida que se detectan nuevas amenazas, se habilitan nuevos consejos de seguridad en Security Center mediante recomendaciones nuevas. Para evitar cambios inesperados en la puntuación de seguridad y ofrecer un período de gracia en el que pueda explorar nuevas recomendaciones antes de que afecten a las puntuaciones, las recomendaciones marcadas como **Versión preliminar** ya no se incluyen en los cálculos de la puntuación de seguridad. Aun así, deben corregirse siempre que sea posible, de tal forma que, cuando finalice el período de versión preliminar, contribuyan a la puntuación.

Además, las recomendaciones en **Versión preliminar** no representan un recurso "Incorrecto".

Un ejemplo de una recomendación en versión preliminar:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendación con la marca de versión preliminar.":::

[Más información sobre la puntuación de seguridad](secure-score-security-controls.md)


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Las recomendaciones incluyen ahora un indicador de gravedad y el intervalo de actualización

La página de detalles de las recomendaciones incluye ahora un indicador del intervalo de actualización (siempre que sea pertinente) y una visualización clara de la gravedad de la recomendación.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Página de recomendaciones en la que se muestran la actualización y la gravedad.":::


## <a name="august-2020"></a>Agosto de 2020

Las actualizaciones de agosto incluyen:

- [Inventario de recursos: nueva vista eficaz de la posición de seguridad de sus recursos](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Compatibilidad agregada con los valores predeterminados de seguridad de Azure Active Directory (para la autenticación multifactor)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Recomendación de entidades de servicio agregada](#service-principals-recommendation-added)
- [Evaluación de vulnerabilidades en máquinas virtuales: recomendaciones y directivas consolidadas](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Se han agregado nuevas directivas de seguridad de AKS a la iniciativa de ASC_default para su uso solo por parte de clientes de versión preliminar privada](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventario de recursos: nueva vista eficaz de la posición de seguridad de sus recursos

El inventario de recursos de Security Center (actualmente en versión preliminar) proporciona una manera de ver la posición de seguridad de los recursos que ha conectado a Security Center.

Security Center analiza periódicamente el estado de seguridad de los recursos de Azure para identificar posibles puntos vulnerables de la seguridad. A continuación, se proporcionan recomendaciones sobre cómo corregir dichos puntos vulnerables. Cuando algún recurso tenga recomendaciones pendientes, aparecerán en el inventario.

Puede usar la vista y sus filtros para explorar los datos de la posición de seguridad y realizar acciones adicionales en función de los resultados.

Obtenga más información sobre el [inventario de recursos](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Compatibilidad agregada con los valores predeterminados de seguridad de Azure Active Directory (para la autenticación multifactor)

Security Center ha agregado compatibilidad total con los [valores predeterminados de seguridad](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), las protecciones de seguridad de la identidad gratuitas de Microsoft.

Los valores predeterminados de seguridad proporcionan una configuración de seguridad de la identidad preconfigurada para proteger su organización frente a ataques habituales relacionados con la identidad. Los valores predeterminados de seguridad ya protegen más de 5 millones de inquilinos en total; 50 000 inquilinos también reciben la protección de Security Center.

Security Center ahora proporciona una recomendación de seguridad cada vez que identifica una suscripción de Azure sin valores predeterminados de seguridad habilitados. Hasta ahora, Security Center recomendaba habilitar la autenticación multifactor mediante el acceso condicional, que forma parte de la licencia Premium de Azure Active Directory (AD). Para los clientes que usen Azure AD de forma gratuita, ahora recomendamos que se habiliten los valores predeterminados de seguridad. 

Nuestro objetivo es animar a más clientes a proteger sus entornos en la nube con MFA y mitigar uno de los mayores riesgos que también es el que tiene más impacto en su [puntuación de seguridad](secure-score-security-controls.md).

Obtenga más información sobre los [valores predeterminados de seguridad](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Recomendación de entidades de servicio agregada

Se ha agregado una nueva recomendación para recomendar que los clientes de Security Center que usan certificados de administración para administrar sus suscripciones cambien a las entidades de servicio.

La recomendación, **Para proteger las suscripciones,deben usarse entidades de servicio en lugar de certificados de administración**, le aconseja usar entidades de servicio o Azure Resource Manager para administrar de forma más segura sus suscripciones. 

Obtenga más información sobre [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Evaluación de vulnerabilidades en máquinas virtuales: recomendaciones y directivas consolidadas

Security Center inspecciona las máquinas virtuales para detectar si ejecutan alguna solución de valoración de las vulnerabilidades. Si no encuentra ninguna solución, Security Center recomienda simplificar la implementación.

Si se encuentran vulnerabilidades, Security Center realiza una recomendación en la que se resumen los resultados que se deben investigar y corregir, en caso de que sea necesario.

Para garantizar una experiencia coherente para todos los usuarios, independientemente del tipo de analizador que usen, se han unificado cuatro recomendaciones en las dos siguientes:

|Recomendación unificada|Descripción del cambio|
|----|:----|
|**Debe habilitarse una solución de evaluación de vulnerabilidades en sus máquinas virtuales**|Reemplaza las dos recomendaciones siguientes:<br> ***** Habilitar la solución de evaluación de vulnerabilidades integrada en las máquinas virtuales (con tecnología de Qualys) (ahora en desuso) (se incluye con el nivel Estándar).<br> ***** La solución de evaluación de vulnerabilidades debe instalarse en sus máquinas virtuales (ahora en desuso) (niveles gratuito y Estándar).|
|**Es necesario corregir las vulnerabilidades de las máquinas virtuales**|Reemplaza las dos recomendaciones siguientes:<br>***** Corregir las vulnerabilidades que se encontraron en las máquinas virtuales (con tecnología de Qualys) (ahora en desuso).<br>***** Se deben corregir las vulnerabilidades mediante una solución de evaluación de vulnerabilidades (ahora en desuso).|
|||

Ahora usará la misma recomendación para implementar la extensión de evaluación de vulnerabilidades de Security Center o una solución con licencia privada ("BYOL") de un asociado como Qualys o Rapid7.

Además, cuando se detectan vulnerabilidades y se notifican a Security Center, una sola recomendación le avisará de los hallazgos, independientemente de la solución de evaluación de vulnerabilidades que los haya identificado.

#### <a name="updating-dependencies"></a>Actualización de dependencias

Si tiene scripts, consultas o automatizaciones que hacen referencia a las recomendaciones anteriores o a nombres o claves de directivas, use las tablas siguientes para actualizar las referencias:

##### <a name="before-august-2020"></a>Antes de agosto de 2020

| Recomendación|Ámbito|
|----|:----|
|**Habilitar la solución de evaluación de vulnerabilidades integrada en las máquinas virtuales (con tecnología de Qualys)**<br>Clave: 550e890b-e652-4d22-8274-60b3bdb24c63|Integrada|
|**Corregir las vulnerabilidades que se encontraron en las máquinas virtuales (con tecnología de Qualys)**<br>Clave: 1195afff-c881-495e-9bc5-1486211ae03f|Integrada|
|**La solución de evaluación de vulnerabilidades debe instalarse en sus máquinas virtuales**<br>Clave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Se deben corregir las vulnerabilidades mediante una solución de evaluación de vulnerabilidades**<br>Clave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|Directiva|Ámbito|
|----|:----|
|**La evaluación de vulnerabilidades debe estar habilitada en las máquinas virtuales**<br>Id. de directiva: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integrada|
|**Se deben corregir las vulnerabilidades mediante una solución de evaluación de vulnerabilidades**<br>Id. de directiva: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>Desde agosto de 2020

|Recomendación|Ámbito|
|----|:----|
|**Debe habilitarse una solución de evaluación de vulnerabilidades en sus máquinas virtuales**<br>Clave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Integrada + BYOL|
|**Es necesario corregir las vulnerabilidades de las máquinas virtuales**<br>Clave: 1195afff-c881-495e-9bc5-1486211ae03f|Integrada + BYOL|
|||

|Directiva|Ámbito|
|----|:----|
|[**La evaluación de vulnerabilidades debe estar habilitada en las máquinas virtuales**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Id. de directiva: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Integrada + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Se han agregado nuevas directivas de seguridad de AKS a la iniciativa de ASC_default para su uso solo por parte de clientes de versión preliminar privada

Para asegurarse de que las cargas de trabajo de Kubernetes son seguras de forma predeterminada, Security Center está agregando directivas en el nivel de Kubernetes y recomendaciones de protección, incluidas las opciones de cumplimiento con el control de admisión de Kubernetes.

La fase temprana de este proyecto incluye una versión preliminar privada y la adición de nuevas directivas (deshabilitadas de forma predeterminada) a la iniciativa ASC_default.

Puede omitir estas directivas con seguridad y no habrá ningún impacto en el entorno. Si desea habilitarlas, regístrese para obtener la versión preliminar en https://aka.ms/SecurityPrP y seleccione una de las opciones siguientes:

1. **Versión preliminar única**: para unirse solo a esta versión preliminar privada. Mencione explícitamente "ASC Continuous SCAN" como la versión preliminar a la que le gustaría unirse.
1. **Programa continuo**: para agregarse a esta y a futuras versiones preliminares privadas. Deberá completar un perfil y un contrato de privacidad.


## <a name="july-2020"></a>Julio de 2020

Las actualizaciones de julio incluyen:
- [Evaluación de vulnerabilidades de las máquinas virtuales disponible ahora para las imágenes que no son de Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Protección contra amenazas para Azure Storage expandido con el fin de incluir Azure Files y Azure Data Lake Storage Gen2 (versión preliminar)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Ocho nuevas recomendaciones para habilitar las características de protección contra amenazas](#eight-new-recommendations-to-enable-threat-protection-features)
- [Mejoras en la seguridad de los contenedores: análisis del registro más rápido y documentación actualizada](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Controles de aplicaciones adaptables actualizados con una nueva recomendación y compatibilidad para caracteres comodín en reglas de ruta de acceso](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Novedades de Azure Security Center](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Evaluación de vulnerabilidades de las máquinas virtuales disponible ahora para las imágenes que no son de Marketplace

Anteriormente, al implementar una solución de evaluación de vulnerabilidades, Security Center realizaba una comprobación de validación antes de la implementación. La comprobación se realizaba para confirmar la SKU de Marketplace de la máquina virtual de destino. 

A partir de esta actualización, se ha eliminado esta comprobación y así ahora se pueden implementar herramientas de evaluación de vulnerabilidades en máquinas Windows y Linux personalizadas. Las imágenes personalizadas son aquellas que se han modificado a partir de los valores predeterminados de Marketplace.

Aunque ahora puede implementar la extensión de evaluación de vulnerabilidades integrada (con tecnología de Qualys) en muchas más máquinas, la compatibilidad solo está disponible si usa uno de los sistemas operativos que se indica en [Implementación del detector de vulnerabilidades integrado en VM de nivel estándar](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

Más información sobre el [Detector de vulnerabilidades integrado para máquinas virtuales (requiere Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)

Más información sobre el uso de su propia solución de evaluación de vulnerabilidades con licencia privada de Qualys o Rapid7 en [Implementación de una solución de examen de vulnerabilidades de asociados](deploy-vulnerability-assessment-vm.md)


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Protección contra amenazas para Azure Storage expandido con el fin de incluir Azure Files y Azure Data Lake Storage Gen2 (versión preliminar)

La protección contra amenazas para Azure Storage detecta actividades potencialmente dañinas en las cuentas de Azure Storage. Security Center muestra alertas cuando detecta intentos de acceder a las cuentas de almacenamiento o de aprovecharlas. 

Los datos se pueden proteger tanto si se almacenan como contenedores de blobs, recursos compartidos de archivos o lagos de datos.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Ocho nuevas recomendaciones para habilitar las características de protección contra amenazas

Se han agregado ocho nuevas recomendaciones para proporcionar una manera sencilla de habilitar las características de protección contra amenazas de Azure Security Center para los siguientes tipos de recursos: máquinas virtuales, planes de App Service, servidores de Azure SQL Database, servidores SQL Server en máquinas, cuentas de Azure Storage, clústeres de Azure Kubernetes Service, registros de Azure Container Registry y almacenes de Azure Key Vault.

Las nuevas recomendaciones son:

- **Advanced Data Security debe estar habilitado en los servidores de Azure SQL Database**
- **Advanced Data Security debe estar habilitado en los servidores SQL Server en las máquinas**
- **Advanced Threat Protection debe estar habilitado en los planes de Azure App Service**
- **Advanced Threat Protection debe estar habilitado en los registros de Azure Container Registry**
- **Advanced Threat Protection debe estar habilitado en los almacenes de Azure Key Vault**
- **Advanced Threat Protection debe estar habilitado en los clústeres de Azure Kubernetes Service**
- **Advanced Threat Protection debe estar habilitado en las cuentas de almacenamiento de Azure**
- **Advanced Threat Protection debe estar habilitado en Virtual Machines**

Estas nuevas recomendaciones pertenecen al control de seguridad **Habilitar Azure Defender**.

Las recomendaciones también incluyen la funcionalidad de corrección rápida. 

> [!IMPORTANT]
> La corrección de cualquiera de estas recomendaciones dará lugar a cargos por la protección de los recursos pertinentes. Estos cargos se iniciarán de forma inmediata si tiene recursos relacionados en la suscripción actual. O en el futuro, si los agrega en una fecha posterior.
> 
> Por ejemplo, si no tiene ningún clúster de Azure Kubernetes Service en su suscripción y habilita la protección contra amenazas, no se aplicarán cargos. Si, en el futuro, agrega un clúster a la misma suscripción, este se protegerá automáticamente y a partir de ese momento, se iniciarán los cargos.

Más información sobre cada una de estas recomendaciones en la [página de referencia de las recomendaciones de seguridad](recommendations-reference.md).

Más información sobre la [protección contra amenazas en Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Mejoras en la seguridad de los contenedores: análisis del registro más rápido y documentación actualizada

Como parte de las inversiones continuas en el dominio de la seguridad de los contenedores, nos complace compartir una mejora significativa del rendimiento en los análisis dinámicos de las imágenes de contenedor de Security Center que se almacenan en Azure Container Registry. Los exámenes se completan ahora en aproximadamente dos minutos. En algunos casos, pueden tardar hasta 15 minutos.

Para mejorar la claridad y la orientación con respecto a las funcionalidades de seguridad de los contenedores de Azure Security Center, también hemos actualizado las páginas de documentación sobre la seguridad de los contenedores. 

Más información sobre la seguridad de los contenedores de Security Center en los artículos siguientes:

- [Información general sobre las características de seguridad de los contenedores de Azure Security Center](container-security.md)
- [Detalles de la integración con Azure Container Registry](defender-for-container-registries-introduction.md)
- [Detalles de la integración con Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Procedimientos para el examen de los registros y la protección de los hosts de Docker](container-security.md)
- [Alertas de seguridad de las características de protección contra amenazas para clústeres de Azure Kubernetes Service](alerts-reference.md#alerts-k8scluster)
- [Alertas de seguridad de las características de protección contra amenazas para hosts de Azure Kubernetes Service](alerts-reference.md#alerts-containerhost)
- [Recomendaciones de seguridad para contenedores](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Controles de aplicaciones adaptables actualizados con una nueva recomendación y compatibilidad para caracteres comodín en reglas de ruta de acceso

La característica de controles de aplicaciones adaptables ha recibido dos actualizaciones importantes:

* Una nueva recomendación identifica un comportamiento potencialmente legítimo que no se ha permitido previamente. La nueva recomendación, **Se deben actualizar las reglas de la lista de permitidos de la directiva de controles de aplicaciones adaptables**, le pedirá que agregue nuevas reglas a la directiva existente para reducir el número de falsos positivos en las alertas de infracción de controles de aplicaciones adaptables.

* Las reglas de ruta de acceso ahora admiten caracteres comodín. A partir de esta actualización, puede configurar las reglas de ruta de acceso permitidas mediante caracteres comodín. Se admiten dos escenarios:

    * El uso de un carácter comodín al final de una ruta de acceso para permitir todos los ejecutables dentro de esta carpeta y subcarpetas

    * El uso de un carácter comodín en medio de una ruta de acceso para habilitar un nombre ejecutable conocido con un nombre de carpeta cambiante (por ejemplo, carpetas de usuario personales con un archivo ejecutable conocido, nombres de carpeta generados automáticamente, etc.).


[Más información acerca de los controles de aplicación adaptables](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Seis directivas para la seguridad avanzada de datos de SQL en desuso

Seis directivas relacionadas con la seguridad avanzada de datos para máquinas SQL están en desuso:

- Los tipos de Advanced Threat Protection deben definirse como "Todos" en la configuración de Advanced Data Security para SQL Managed Instance.
- Los tipos de Advanced Threat Protection deben definirse como "Todos" en la configuración de Advanced Data Security del servidor SQL Server.
- La configuración de seguridad de datos avanzada para una instancia administrada SQL debe contener una dirección de correo electrónico para recibir alertas de seguridad
- La configuración de seguridad de datos avanzada para SQL Server debe contener una dirección de correo electrónico para recibir alertas de seguridad
- Las notificaciones a los administradores y a los propietarios de la suscripción deben estar habilitadas en la configuración de seguridad avanzada de datos de la instancia administrada de SQL
- Las notificaciones por correo electrónico para administradores y propietarios de suscripciones deben estar habilitadas en la configuración de seguridad avanzada de datos de SQL Server

Obtenga más información sobre las [directivas integradas](./policy-reference.md).



## <a name="june-2020"></a>Junio de 2020

Las actualizaciones de junio incluyen:

- [API de puntuación segura (versión preliminar)](#secure-score-api-preview)
- [Advanced Data Security para máquinas SQL (Azure, otras nubes e instancias locales) (versión preliminar)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Dos nuevas recomendaciones para implementar el agente de Log Analytics en máquinas de Azure Arc (versión preliminar)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nuevas directivas para crear configuraciones de exportación continua y de automatización de flujos de trabajo a escala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nueva recomendación para usar NSG para proteger las máquinas virtuales sin conexión a Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nuevas directivas para habilitar la protección contra amenazas y Advanced Data Security](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API de puntuación segura (versión preliminar)

Ahora puede acceder a la puntuación a través de la [API de puntuación segura](/rest/api/securitycenter/securescores/) (actualmente en versión preliminar). Los métodos de API proporcionan la flexibilidad necesaria para consultar los datos y crear su propio mecanismo de creación de informes de las puntuaciones seguras a lo largo del tiempo. Por ejemplo, puede usar la API **Secure Scores** para obtener la puntuación de una suscripción específica. Además, puede usar la API **Secure Score Controls** para mostrar los controles de seguridad y la puntuación actual de las suscripciones.

Para ver ejemplos de herramientas externas que son posibles gracias a la API de puntuación segura, vea el [área de puntuación segura de la comunidad de GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Obtenga más información sobre la [puntuación segura y los controles de seguridad en Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Advanced Data Security para máquinas SQL (Azure, otras nubes e instancias locales) (versión preliminar)

Ahora, Advanced Data Security de Azure Security Center para máquinas SQL protege los servidores SQL Server hospedados en Azure, en otros entornos en la nube e incluso en máquinas locales. Esto amplía las protecciones de los servidores SQL Server nativos de Azure, de modo que se admitan totalmente los entornos híbridos.

Advanced Data Security proporciona una evaluación de vulnerabilidades y protección contra amenazas avanzada para máquinas SQL dondequiera que se encuentren.

La configuración conlleva dos pasos:

1. Implementación del agente de Log Analytics en la máquina host de SQL Server para proporcionar la conexión a la cuenta de Azure.

1. Habilitación del conjunto de productos opcional en la página de precios y configuración de Security Center.

Obtenga más información sobre [Advanced Data Security para máquinas SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dos nuevas recomendaciones para implementar el agente de Log Analytics en máquinas de Azure Arc (versión preliminar)

Se han agregado dos nuevas recomendaciones para ayudar a implementar el [agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md) en las máquinas de Azure Arc y asegurarse de que están protegidas por Azure Security Center:

- **El agente de Log Analytics debe estar instalado en las máquinas de Azure Arc basadas en Windows (versión preliminar)** .
- **El agente de Log Analytics debe estar instalado en las máquinas de Azure Arc basadas en Linux (versión preliminar)** .

Estas nuevas recomendaciones aparecerán en los mismos cuatro controles de seguridad que la recomendación existente (relacionada) **El agente de supervisión debe estar instalado en las máquinas**: corrija las configuraciones de seguridad, aplique el control de aplicaciones adaptativo, aplique actualizaciones del sistema y habilite Endpoint Protection.

Las recomendaciones también incluyen la función de corrección rápida para ayudar a acelerar el proceso de implementación. 

Obtenga más información sobre estas dos nuevas recomendaciones en la tabla [Recomendaciones de proceso y aplicación](recommendations-reference.md#recs-compute).

Obtenga más información sobre la manera en que Azure Security Center usa el agente en [¿Qué es el agente de Log Analytics?](./faq-data-collection-agents.yml#what-is-the-log-analytics-agent-)

Obtenga más información sobre las [extensiones para máquinas de Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nuevas directivas para crear configuraciones de exportación continua y de automatización de flujos de trabajo a escala

La automatización de los procesos de supervisión y respuesta ante incidentes de la organización puede mejorar considerablemente el tiempo necesario para investigar y mitigar los incidentes de seguridad.

Para implementar las configuraciones de automatización en la organización, use estas directivas de Azure "DeployIfdNotExist" integradas para crear y configurar los procedimientos de [exportación continua](continuous-export.md) y [automatización de flujos de trabajo](workflow-automation.md):

Las definiciones de directiva se pueden encontrar en Azure Policy:


|Objetivo  |Directiva  |Id. de directiva  |
|---------|---------|---------|
|Exportación continua al centro de eventos|[Implementar la exportación al centro de eventos para las alertas y recomendaciones de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Exportación continua a las áreas de trabajo de Log Analytics|[Implementar la exportación al área de trabajo de Log Analytics para las alertas y recomendaciones de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatización de flujos de trabajo para alertas de seguridad|[Implementar la automatización del flujo de trabajo para las alertas de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatización de flujos de trabajo para recomendaciones de seguridad|[Implementar la automatización del área de trabajo para las recomendaciones de Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Empiece a usar las [plantillas de automatización de flujos de trabajo](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Para más información sobre el uso de las dos directivas de exportación, consulte las secciones sobre la [configuración de la automatización de flujos de trabajo a escala mediante las directivas suministradas](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) y sobre la [configuración de una exportación continua](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nueva recomendación para usar NSG para proteger las máquinas virtuales sin conexión a Internet

El control de seguridad "implementar prácticas recomendadas de seguridad" incluye ahora la siguiente recomendación nueva:

- **Las máquinas virtuales sin conexión a Internet deben protegerse con grupos de seguridad de red**

Una recomendación existente (**Las máquinas virtuales sin conexión a Internet deben protegerse con grupos de seguridad de red**) no distinguía entre las máquinas virtuales con y sin conexión a Internet. En ambos casos, se generaba una recomendación de alta gravedad si una máquina virtual no estaba asignada a un grupo de seguridad de red. Esta nueva recomendación separa las máquinas sin conexión a Internet para reducir los falsos positivos y evitar alertas innecesarias de alta gravedad.

Obtenga más información en la tabla [Recomendaciones de red](recommendations-reference.md#recs-networking).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nuevas directivas para habilitar la protección contra amenazas y Advanced Data Security

Las nuevas definiciones de directiva que se indican a continuación se agregaron a la iniciativa predeterminada de ASC y están diseñadas para ayudar a habilitar la protección contra amenazas o Advanced Data Security para los tipos de recursos pertinentes.

Las definiciones de directiva se pueden encontrar en Azure Policy:


| Directiva                                                                                                                                                                                                                                                                | Id. de directiva                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Advanced Data Security debe estar habilitado en los servidores de Azure SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security debe estar habilitado en los servidores SQL Server en las máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection debe estar habilitado en las cuentas de almacenamiento de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection debe estar habilitado en los almacenes de Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection debe estar habilitado en los planes de Azure App Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection debe estar habilitado en los registros de Azure Container Registry](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection debe estar habilitado en los clústeres de Azure Kubernetes Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Advanced Threat Protection debe estar habilitado en Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Obtenga más información sobre la [protección contra amenazas en Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Mayo de 2020

Las actualizaciones de mayo incluyen:
- [Reglas de supresión de alertas (versión preliminar)](#alert-suppression-rules-preview)
- [La evaluación de vulnerabilidades de la máquina virtual ya está disponible con carácter general](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Cambios en el acceso a la máquina virtual (VM) Just-in-Time (JIT)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Las recomendaciones personalizadas se han migrado a un control de seguridad independiente](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Alternancia agregada para ver las recomendaciones en controles o como una lista plana](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Control de seguridad expandido "implementar prácticas recomendadas de seguridad"](#expanded-security-control-implement-security-best-practices)
- [Las directivas personalizadas con metadatos personalizados ya están disponibles con carácter general](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migración de funcionalidades de análisis de volcado de memoria a detección de ataques sin archivos](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Reglas de supresión de alertas (versión preliminar)

Esta nueva característica (actualmente en versión preliminar) ayuda a reducir la fatiga de la alerta. Use reglas para ocultar automáticamente las alertas que se sabe que son inocuas o relacionadas con las actividades normales de su organización. Esto le permite centrarse en las amenazas más importantes. 

Todavía se generarán alertas que coincidan con las reglas de supresión habilitadas, pero su estado se establecerá en descartado. Puede ver el estado en el Azure Portal o tener acceso a las alertas de seguridad de Security Center.

Las reglas de supresión definen los criterios para los que se deben descartar automáticamente las alertas. Normalmente, se usaría una regla de supresión para:

- Eliminar las alertas identificadas como falsos positivos

- suprimir las alertas que se desencadenan con demasiada frecuencia para ser útiles

Más información sobre la [supresión de alertas de protección contra amenazas de Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>La evaluación de vulnerabilidades de la máquina virtual ya está disponible con carácter general

El nivel estándar de Security Center ahora incluye una evaluación de vulnerabilidades integrada para máquinas virtuales sin precio adicional. Esta extensión está basada en Qualys pero notifica sus hallazgos directamente de nuevo a Security Center. No se necesita ninguna licencia ni cuenta de Qualys, ya que todo se administra sin problemas en Security Center.

La nueva solución puede examinar continuamente las máquinas virtuales para encontrar vulnerabilidades y presentar las conclusiones en Security Center. 

Para implementar la solución, use la nueva recomendación de seguridad:

"Habilitar la solución de evaluación de vulnerabilidades integrada en las máquinas virtuales (con tecnología de Qualys)"

Más información sobre la [evaluación de vulnerabilidades integradas de Security Center para máquinas virtuales](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Cambios en el acceso a la máquina virtual (VM) Just-in-Time (JIT)

Security Center incluye una característica opcional para proteger los puertos de administración de las máquinas virtuales. Esto proporciona una defensa contra la forma más común de ataques por fuerza bruta.

Esta actualización lleva a cabo los siguientes cambios en esta característica:

- Se ha cambiado el nombre de la recomendación que le aconseja habilitar JIT en una máquina virtual. Anteriormente, "el control de acceso a red Just-in-Time se debe aplicar a las máquinas virtuales" ahora es: "Los puertos de administración de las máquinas virtuales deben protegerse con el control de acceso de red Just-In-Time".

- La recomendación se desencadena solo si hay puertos de administración abiertos.

Más información acerca de [la característica de acceso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Las recomendaciones personalizadas se han migrado a un control de seguridad independiente

Uno de los controles de seguridad incluidos con la puntuación segura mejorada fue "Implementación de procedimientos recomendados de seguridad". Las recomendaciones personalizadas creadas para las suscripciones se colocan automáticamente en ese control. 

Para que sea más fácil encontrar las recomendaciones personalizadas, las hemos pasado a un control de seguridad dedicado, "recomendaciones personalizadas". Este control no afecta a la puntuación segura.

Más información sobre los controles de seguridad en [puntuación de seguridad mejorada (versión preliminar) de Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Alternancia agregada para ver las recomendaciones en controles o como una lista plana

Los controles de seguridad son grupos lógicos de recomendaciones de seguridad relacionadas. Reflejan las superficies de ataque vulnerables. Un control es un conjunto de recomendaciones de seguridad con instrucciones que le ayudan a implementar esas recomendaciones.

Para ver de inmediato cómo protege su organización cada superficie de ataque individual, revise las puntuaciones de cada control de seguridad.

De forma predeterminada, las recomendaciones se muestran en los controles de seguridad. A partir de esta actualización también se pueden mostrar como una lista. Para verlos como una lista simple ordenada por el estado de mantenimiento de los recursos afectados, use el nuevo comando de alternancia "agrupar por controles". El comando de alternancia está encima de la lista en el portal.

Los controles de seguridad y esta alternancia forman parte de la nueva experiencia de puntuación segura. No se olvide de enviarnos sus comentarios desde dentro del portal.

Más información sobre los controles de seguridad en [puntuación de seguridad mejorada (versión preliminar) de Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Control de alternancia Agrupar por controles para recomendaciones.":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Control de seguridad expandido "implementar prácticas recomendadas de seguridad" 

Uno de los controles de seguridad que incluye con la puntuación segura mejorada es "Implementación de procedimientos recomendados de seguridad". Cuando una recomendación está en este control, no afecta a la puntuación segura. 

Con esta actualización, se han sacado tres recomendaciones de los controles en los que se colocaron originalmente y en este control de prácticas recomendadas. Hemos realizado este paso porque hemos determinado que el riesgo de estas tres recomendaciones es menor de lo que se pensó inicialmente.

Además, se han incorporado dos nuevas recomendaciones y se han agregado a este control.

Las tres recomendaciones que se movieron son:

- **MFA debe estar habilitada en las cuentas con permisos de lectura en la suscripción** (originalmente en el control "habilitar MFA")
- **Las cuentas externas con permisos de lectura deben quitarse de la suscripción** (originalmente en el control "administrar acceso y permisos")
- **Se debe designar un máximo de 3 propietarios para la suscripción** (originalmente en el control "administrar acceso y permisos")

Las dos nuevas recomendaciones que se han agregado al control son:

- **La extensión de configuración de invitado debe instalarse en máquinas virtuales Windows (versión preliminar):** el uso de la [configuración de invitado de Azure Policy](../governance/policy/concepts/guest-configuration.md) proporciona visibilidad dentro de las máquinas virtuales a la configuración de servidor y de aplicación (solo Windows).

- **Protección contra vulnerabilidades de seguridad de Windows Defender debe estar habilitada en las máquinas (versión preliminar)** : Protección contra vulnerabilidades de seguridad de Windows Defender aprovecha el agente de configuración de invitado de Azure Policy. La protección contra vulnerabilidades de seguridad tiene cuatro componentes diseñados para bloquear dispositivos en una amplia variedad de vectores de ataque y comportamientos de bloque utilizados habitualmente en ataques de malware, al tiempo que permiten a las empresas equilibrar los requisitos de productividad y riesgo de seguridad (solo Windows).

Más información sobre la protección contra vulnerabilidades de seguridad de Windows Defender en [Crear e implementar una directiva de protección contra vulnerabilidades](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Obtenga más información sobre los controles de seguridad en [Puntuación de seguridad mejorada (versión preliminar)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Las directivas personalizadas con metadatos personalizados ya están disponibles con carácter general

Las directivas personalizadas ahora forman parte de la experiencia de recomendaciones de Security Center, la puntuación segura y el panel de normas de cumplimiento normativo. Esta característica ya está disponible con carácter general y permite ampliar la cobertura de evaluación de seguridad de la organización en Security Center. 

Cree una iniciativa personalizada en Azure Policy, agréguele directivas e incorpórela a Azure Security Center, y visualícela como recomendaciones.

Ahora también hemos agregado la opción para editar los metadatos de recomendación personalizados. Las opciones de metadatos incluyen gravedad, pasos de corrección, información de amenazas y mucho más.  

Más información sobre [mejora de las recomendaciones personalizadas con información detallada](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migración de funcionalidades de análisis de volcado de memoria a detección de ataques sin archivos 

Estamos integrando las capacidades de detección del análisis de volcado de memoria de Windows (CDA) en [detección de ataques sin archivos](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). El análisis de detección de ataques sin archivos proporciona versiones mejoradas de las siguientes alertas de seguridad para máquinas Windows: Inserción de código detectada, Detectado módulo de Windows enmascarado, Shellcode detectado y Segmento de código sospechoso.

Algunas de las ventajas de esta transición son:

- **Detección de malware proactiva y oportuna**: el enfoque de CDA implicaba esperar a que se produjera un bloqueo y, después, ejecutar el análisis para encontrar artefactos malintencionados. El uso de la detección de ataques sin archivos proporciona una identificación proactiva de las amenazas en memoria mientras se ejecutan. 

- **Alertas enriquecidas**: las alertas de seguridad de la detección de ataques sin archivos no están disponibles en CDA, como la información de las conexiones de red activas. 

- **Agregación de alertas**: cuando CDA detectó varios patrones de ataque en un solo volcado de memoria, desencadenó varias alertas de seguridad. Detección de ataques sin archivos combina todos los patrones de ataque identificados del mismo proceso en una única alerta, lo que elimina la necesidad de poner en correlación varias alertas.

- **Requisitos reducidos en el área de trabajo de Log Analytics**: los volcados que contienen datos potencialmente confidenciales ya no se cargarán en el área de trabajo de Log Analytics.






## <a name="april-2020"></a>Abril de 2020

Las actualizaciones de abril incluyen:
- [Los paquetes de cumplimiento dinámico ya están disponibles con carácter general](#dynamic-compliance-packages-are-now-generally-available)
- [Las recomendaciones de identidad ahora están incluidas en el nivel gratuito de Azure Security Center](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Los paquetes de cumplimiento dinámico ya están disponibles con carácter general

El panel de cumplimiento normativo de Azure Security Center ahora incluye **paquetes de cumplimiento dinámico** (ya disponible con carácter general) para realizar un seguimiento de los estándares de la industria y normativas adicionales.

Los paquetes de cumplimiento dinámico se pueden agregar a su suscripción o grupo de administración desde la página de la directiva de seguridad de Security Center. Cuando haya incorporado un estándar o una prueba comparativa, el estándar aparece en el panel de cumplimiento normativo con todos los datos de cumplimiento asociados asignados como evaluaciones. Podrá descargar un informe de resumen para cualquiera de los estándares que se hayan incorporado.

Ahora, puede agregar estándares como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official y UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nuevo)** (que es una representación más completa de Azure CIS 1.1.0)

Además, recientemente hemos agregado el [Azure Security Benchmark](/security/benchmark/azure/introduction), las instrucciones específicas de Azure creadas por Microsoft para las prácticas recomendadas de seguridad y cumplimiento basadas en los marcos de cumplimiento comunes. Se admitirán estándares adicionales en el panel a medida que estén disponibles.  
 
Más información sobre [cómo personalizar el conjunto de estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendaciones de identidad ahora incluidas en el nivel gratuito de Azure Security Center

Las recomendaciones de seguridad para la identidad y el acceso en el nivel gratuito de Azure Security Center ya están disponibles con carácter general. Esto forma parte del esfuerzo de hacer que las características de la administración de posturas de seguridad en la nube (CSPM) sean gratuitas. Hasta ahora, estas recomendaciones solo estaban disponibles en el plan de tarifa estándar.

Los ejemplos de recomendaciones de identidad y acceso incluyen:

- "La autenticación multifactor debe estar habilitada en las cuentas con permisos de propietario en la suscripción".
- "Debe designar un máximo de tres propietarios para la suscripción".
- "Las cuentas en desuso deben quitarse de la suscripción".

Si tiene suscripciones en el plan de tarifa gratuito, su puntuación de seguridad se verá afectada por este cambio ya que nunca se evaluó su identidad y seguridad de acceso.

Más información sobre [recomendaciones de identidad y acceso](recommendations-reference.md#recs-identityandaccess).

Obtenga más información cómo [administrar la aplicación de la autenticación multifactor (MFA) en las suscripciones](security-center-identity-access.md).



## <a name="march-2020"></a>Marzo de 2020

Las actualizaciones de marzo incluyen:

- [La automatización del flujo de trabajo ya está disponible con carácter general](#workflow-automation-is-now-generally-available)
- [Integración de Azure Security Center con Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Protección para Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Experiencia Just-in-Time mejorada](#improved-just-in-time-experience)
- [Dos recomendaciones de seguridad para las aplicaciones web en desuso](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>La automatización del flujo de trabajo ya está disponible con carácter general

La característica de automatización del flujo de trabajo de Azure Security Center ahora está disponible con carácter general. Úsela para desencadenar automáticamente una instancia de Logic Apps sobre alertas y recomendaciones de seguridad. Además, los desencadenadores manuales están disponibles para las alertas y todas las recomendaciones que tienen la opción corrección rápida disponible.

Cada programa de seguridad incluye varios flujos de trabajo para la respuesta a incidentes. Estos procesos pueden incluir notificaciones para las partes interesadas competentes, iniciar un proceso de administración de cambios y aplicar pasos de corrección específicos. Los expertos en seguridad recomiendan automatizar tantos pasos de esos procedimientos como sea posible. La automatización reduce la sobrecarga y puede mejorar la seguridad asegurándose de que los pasos del proceso se realizan de forma rápida, coherente y según sus requisitos predefinidos.

Para más información acerca de las capacidades de Security Center automática y manual para ejecutar los flujos de trabajo, consulte [ automatización de flujos de trabajo](workflow-automation.md).

Más información acerca de la [creación de Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integración de Azure Security Center con Windows Admin Center

Ahora es posible migrar los servidores de Windows locales desde Windows Admin Center directamente a Azure Security Center. Security Center, a continuación, se convierte en el único panel para ver la información de seguridad de todos los recursos de Windows Admin Center, incluidos los servidores locales, las máquinas virtuales y las cargas de trabajo PaaS adicionales.

Después de mover un servidor de Windows Admin Center a Azure Security Center, podrá realizar lo siguiente:

- Ver alertas y recomendaciones de seguridad en la extensión de Security Center de Windows Admin Center.
- Ver la postura de seguridad y recuperar información detallada adicional de los servidores administrados de Windows Admin Center en Security Center, mediante Azure Portal (o a través de una API).

Más información sobre [cómo integrar Azure Security Center con Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Protección para Azure Kubernetes Service

Azure Security Center está expandiendo sus características de seguridad de contenedor para proteger Azure Kubernetes Service (AKS).

La popular plataforma de código abierto Kubernetes se ha adoptado tan ampliamente que ahora es un estándar del sector para la orquestación de contenedores. A pesar de esta implementación generalizada, todavía hay una falta de comprensión sobre cómo proteger un entorno de Kubernetes. Defender las superficies de ataque de una aplicación en contenedores requiere conocimientos para asegurarse de que la infraestructura esté configurada de forma segura y se supervise constantemente para detectar posibles amenazas.

La defensa de Security Center incluye:

- **Detección y visibilidad**: detección continua de instancias de AKS administradas dentro de las suscripciones registradas de Security Center.
- **Recomendaciones de seguridad**: recomendaciones útiles para ayudarle a cumplir los procedimientos recomendados de seguridad para AKS. Estas recomendaciones se incluyen en su puntuación segura para asegurarse de que se ven como parte de la postura de seguridad de su organización. Un ejemplo de una recomendación relacionada con AKS que podría ver es que "se debe usar el control de acceso basado en roles para restringir el acceso a un clúster de servicio de Kubernetes".
- **Protección contra amenazas**: gracias al análisis continuo de la implementación de AKS, Security Center le alerta de las amenazas y actividades malintencionadas que se detectan en el host y el nivel de clúster de AKS.

Más información acerca de [integración de Azure Kubernetes Services con Security Center](defender-for-kubernetes-introduction.md).

Más información sobre [las características de seguridad de los contenedores en Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Experiencia Just-in-Time mejorada

Las características, la operación y la interfaz de usuario de las herramientas Just-in-Time de Azure Security Center que protegen los puertos de administración se han mejorado de la manera siguiente: 

- **Campo de justificación**: al solicitar acceso a una máquina virtual (VM) a través de la página Just-in-Time del Azure Portal, hay un nuevo campo opcional disponible para especificar una justificación para la solicitud. Se puede realizar un seguimiento de la información especificada en este campo en el registro de actividad. 
- **Limpieza automática de reglas Just-In-Time (JIT) redundantes**: siempre que se actualiza una directiva JIT, se ejecuta automáticamente una herramienta de limpieza para comprobar la validez de todo el conjunto de reglas. La herramienta busca discrepancias entre las reglas de la directiva y las reglas del grupo de seguridad de red. Si la herramienta de limpieza encuentra una discrepancia, determina la causa y, cuando es seguro hacerlo, quita las reglas integradas que ya no son necesarias. El limpiador no elimina nunca las reglas que ha creado. 

Más información acerca de [la característica de acceso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dos recomendaciones de seguridad para las aplicaciones web en desuso

Dos recomendaciones de seguridad relacionadas con las aplicaciones web están en desuso: 

- Se deben proteger las reglas de las aplicaciones web en los grupos de seguridad de red de IaaS.
    (Directiva relacionada: Se deben proteger las reglas de NSG para las aplicaciones web en IaaS).

- El acceso a App Services debe estar restringido.
    (Directiva relacionada: El acceso a App Services debe estar restringido [versión preliminar])

Estas recomendaciones ya no aparecerán en la lista de Security Center de recomendaciones. Las directivas relacionadas ya no se incluirán en la iniciativa denominada "valor predeterminado de Security Center".

Más información sobre las [recomendaciones de seguridad](recommendations-reference.md).




## <a name="february-2020"></a>Febrero de 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Detección de ataques sin archivos para Linux (versión preliminar)

A medida que los atacantes aumentan el empleo de métodos stealthier para evitar la detección, Azure Security Center está ampliando la detección de ataques no archivados para Linux, además de Windows. Los ataques sin archivos no aprovechan las vulnerabilidades del software, insertan cargas malintencionadas en procesos benignos del sistema y se ocultan en la memoria. Estas técnicas:

- minimiza o eliminan seguimientos de malware en disco
- reducen considerablemente las posibilidades de detección mediante soluciones de análisis de malware basadas en disco

Para contrarrestar esta amenaza, Azure Security Center publicó la detección de ataques sin archivos para Windows en octubre de 2018 y ahora ha extendido la detección de ataques sin archivos en Linux. 



## <a name="january-2020"></a>Enero de 2020

### <a name="enhanced-secure-score-preview"></a>Puntuación de seguridad mejorada (versión preliminar)

Ahora hay disponible una versión mejorada de la característica de puntuación segura de Azure Security Center en versión preliminar. En esta versión, se agrupan varias recomendaciones en controles de seguridad que reflejan mejor las superficies de ataque vulnerables (por ejemplo, restringir el acceso a los puertos de administración).

Familiarícese con los cambios de puntuación segura durante la fase de versión preliminar y determine otras correcciones que le ayudarán a proteger más su entorno.

Obtenga más información sobe la [puntuación de seguridad mejorada (versión preliminar)](secure-score-security-controls.md).



## <a name="november-2019"></a>Noviembre de 2019

Las actualizaciones de noviembre incluyen:
 - [Protección contra amenazas para Azure Key Vault en regiones de Norteamérica (versión preliminar)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [La protección contra amenazas para Azure Storage incluye el filtrado de reputación de malware](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatización del flujo de trabajo con Logic Apps (versión preliminar)](#workflow-automation-with-logic-apps-preview)
 - [Corrección rápida de los recursos en masa disponibles con carácter general](#quick-fix-for-bulk-resources-generally-available)
 - [Examen de imágenes de contenedor para detectar vulnerabilidades (versión preliminar)](#scan-container-images-for-vulnerabilities-preview)
 - [Estándares de cumplimiento normativo adicionales (versión preliminar)](#additional-regulatory-compliance-standards-preview)
 - [Protección contra amenazas para Azure Kubernetes Service (versión preliminar)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Evaluación de vulnerabilidades de máquinas virtuales (versión preliminar)](#virtual-machine-vulnerability-assessment-preview)
 - [Advanced Data Security para servidores SQL Server en Azure Virtual Machines (versión preliminar)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Compatibilidad con directivas personalizadas (versión preliminar)](#support-for-custom-policies-preview)
 - [Ampliación de la cobertura de Azure Security Center con la plataforma para la comunidad y los asociados](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Integraciones avanzadas con la exportación de recomendaciones y alertas (versión preliminar)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Incorporación de servidores locales a Security Center desde Windows Admin Center (versión preliminar)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Protección contra amenazas para Azure Key Vault en regiones de Norteamérica (versión preliminar)

Azure Key Vault es un servicio esencial para proteger los datos y mejorar el rendimiento de las aplicaciones en la nube, ya que ofrece la posibilidad de administrar de forma centralizada claves, secretos, claves criptográficas y directivas en la nube. Como Azure Key Vault almacena datos confidenciales y críticos para la empresa, requiere la máxima seguridad para los almacenes de claves y los datos almacenados en ellos.

El soporte de Azure Security Center para Threat Protection para Azure Key Vault proporciona un nivel adicional de inteligencia de seguridad que detecta intentos poco habituales y potencialmente peligrosos de acceder a los almacén de claves o vulnerarlos. Esta nueva capa de protección permite a los clientes afrontar las amenazas contra sus almacenes sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de la seguridad. La característica está en versión preliminar pública en regiones Norteamérica.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>La protección contra amenazas para Azure Storage incluye el filtrado de reputación de malware

La protección contra amenazas para Azure Storage ofrece nuevas detecciones basadas en la inteligencia sobre amenazas de Microsoft para detectar cargas de malware en Azure Storage mediante el análisis de reputación de hash y accesos sospechosos desde un nodo de salida de Tor activo (un proxy de anonimización). Ahora puede ver el malware detectado en todas las cuentas de almacenamiento mediante Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatización del flujo de trabajo con Logic Apps (versión preliminar)

Organizaciones con seguridad administrada centralmente y de TI/operaciones que implementan procesos de flujo de trabajo internos para impulsar la acción necesaria dentro de la organización cuando se detectan discrepancias en sus entornos. En muchos casos, estos flujos de trabajo son procesos repetibles y la automatización puede optimizar considerablemente los procesos dentro de la organización.

Hoy estamos presentando una nueva funcionalidad en Security Center que permite a los clientes crear configuraciones de automatización que aprovechan Azure Logic Apps y crear directivas que las desencadenarán automáticamente en función de determinados resultados de ASC, como recomendaciones o alertas. La aplicación lógica de Azure se puede configurar para realizar cualquier acción personalizada compatible con la amplia comunidad de conectores de aplicaciones lógicas, o bien usar una de las plantillas proporcionadas por Security Center como enviar un correo electrónico o abrir un vale de ServiceNow&trade;.

Para más información acerca de las capacidades de Security Center automática y manual para ejecutar los flujos de trabajo, consulte [ automatización de flujos de trabajo](workflow-automation.md).

Para información acerca de la creación de Logic Apps, consulte [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Corrección rápida de los recursos en masa disponibles con carácter general

Con las muchas tareas que se proporcionan a un usuario como parte de la puntuación segura, la capacidad de corregir los problemas de forma eficaz en una flota de gran tamaño puede resultar complicada.

Para simplificar la corrección de las configuraciones erróneas de seguridad y poder corregir rápidamente las recomendaciones en una gran parte de los recursos y mejorar la puntuación segura, use la corrección rápida.

Esta operación le permitirá seleccionar los recursos a los que desea aplicar la corrección e iniciar una acción correctiva que configurará la configuración en su nombre.

La corrección rápida está disponible con carácter general para los clientes de hoy en día como parte de la página de recomendaciones de Security Center.

Vea qué recomendaciones tiene habilitada la corrección rápida en la [guía de referencia para recomendaciones de seguridad](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Examinar imágenes de contenedor para detectar vulnerabilidades (versión preliminar)

Ahora Azure Security Center puede examinar imágenes de contenedor en Azure Container Registry para detectar vulnerabilidades.

El análisis de imágenes funciona mediante el análisis del archivo de imagen del contenedor y la comprobación de la existencia de alguna vulnerabilidad conocida (con la tecnología Qualys).

El propio análisis se desencadena automáticamente al insertar nuevas imágenes de contenedor en Azure Container Registry. Las vulnerabilidades detectadas se verán como recomendaciones de Security Center y se incluirán en la puntuación segura de Azure junto con información sobre cómo aplicarles revisiones para reducir la superficie expuesta a los ataques que permitían.


### <a name="additional-regulatory-compliance-standards-preview"></a>Estándares de cumplimiento normativo adicionales (versión preliminar)

En el panel de cumplimiento normativo se ofrece información sobre la postura de cumplimiento basado en las evaluaciones de Security Center. En el panel se muestra cómo el entorno cumple los controles y requisitos designados por estándares normativos específicos y pruebas comparativas del sector, y proporciona recomendaciones prescriptivas para abordar estos requisitos.

Por lo tanto, el panel de cumplimiento normativo ha admitido cuatro estándares integrados:  Azure CIS 1.1.0, PCI-DSS, ISO 27001, y SOC-TSP. Ahora anunciamos la versión preliminar pública de estándares admitidos adicionales: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM y UK Official junto con UK NHS. También lanzamos una versión actualizada de Azure CIS 1.1.0, que cubre más controles del estándar y la mejora de la extensibilidad.

[Obtenga más información sobre cómo personalizar el conjunto de estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Protección contra amenazas para Azure Kubernetes Service (versión preliminar)

Kubernetes se convierte rápidamente en el nuevo estándar para implementar y administrar software en la nube. Pocas personas tienen una amplia experiencia con Kubernetes y muchos solo se centran en la ingeniería y administración generales y pasan por alto el aspecto de seguridad. El entorno de Kubernetes debe configurarse cuidadosamente para que sea seguro, asegurándose de que no se deja abierta ninguna puerta expuesta a ataques de contenedor para los atacantes. Security Center está expandiendo su compatibilidad en el espacio del contenedor a uno de los servicios en crecimiento más rápido de Azure: Azure Kubernetes Service (AKS).

Las nuevas capacidades de esta versión preliminar pública incluyen:

- **Detección y visibilidad**: detección continua de instancias de AKS administradas dentro de las suscripciones registradas de Security Center.
- **Recomendaciones de puntuación de seguridad**: Elementos procesables para ayudar a los clientes a cumplir con los procedimientos recomendados de seguridad para AKS y aumentar su puntuación de seguridad. Las recomendaciones incluyen elementos como "El control de acceso basado en rol debe usarse para restringir el acceso a un clúster de servicio de Kubernetes".
- **Detección de amenazas**: análisis basados en host y en clúster, como "Se ha detectado un contenedor con privilegios".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Evaluación de vulnerabilidades de máquinas virtuales (versión preliminar)

Las aplicaciones que se instalan en máquinas virtuales a menudo pueden tener vulnerabilidades que podrían provocar una infracción de la máquina virtual. Estamos anunciando que el nivel estándar de Security Center incluye una evaluación de vulnerabilidades integrada para máquinas virtuales sin costo adicional. La evaluación de vulnerabilidades, con la tecnología de Qualys en la versión preliminar pública, le permitirá examinar continuamente todas las aplicaciones instaladas en una máquina virtual para encontrar aplicaciones vulnerables y presentar las conclusiones en la experiencia del portal de Security Center. Security Center se encarga de todas las operaciones de implementación para que no sea necesario ningún trabajo adicional por parte del usuario. En adelante, tenemos previsto proporcionar opciones de evaluación de vulnerabilidades para satisfacer las necesidades empresariales únicas de nuestros clientes.

[Más información acerca de evaluaciones de vulnerabilidades para Azure Virtual Machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security para servidores SQL Server en Azure Virtual Machines (versión preliminar)

La compatibilidad de Azure Security Center para la protección contra amenazas y la evaluación de vulnerabilidades en las bases de SQL que se ejecutan en máquinas virtuales IaaS está ahora en versión preliminar.

[Evaluación de vulnerabilidades](../azure-sql/database/sql-vulnerability-assessment.md) es un servicio fácil de configurar que puede detectar, realizar un seguimiento y ayudarle a corregir posibles puntos vulnerables de una base de datos. Permite ver la postura de seguridad como parte de la puntuación segura de Azure e incluye los pasos para resolver problemas de seguridad y mejorar las defensas de su base de datos.

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) detecta actividades anómalas que indican intentos inusuales y potencialmente perjudiciales de acceder a su servidor SQL Server o de aprovechar sus vulnerabilidades. Supervisa de forma constante la base de datos en busca de actividad sospechosa y proporciona alertas de seguridad sobre patrones de acceso a la base de datos anómalos para que pueda tomar medidas. Las alertas proporcionan detalles de la actividad sospechosa y recomiendan acciones para investigar y mitigar la amenaza.


### <a name="support-for-custom-policies-preview"></a>Compatibilidad con directivas personalizadas (versión preliminar)

Azure Security Center admite ahora directivas personalizadas (en versión preliminar).

Nuestros clientes han estado intentando ampliar su cobertura de evaluaciones de seguridad actual en Security Center con sus propias evaluaciones de seguridad basadas en las directivas que crean en Azure Policy. Gracias a la compatibilidad con las directivas personalizadas, ahora es posible.

Estas nuevas directivas formarán parte de la experiencia de recomendaciones de Security Center, la puntuación segura y el panel de normas de cumplimiento normativo. Con la compatibilidad con las directivas personalizadas, ahora puede crear una iniciativa personalizada en Azure Policy y, a continuación, agregarla como una directiva en Security Center y visualizarla como una recomendación.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Ampliación de la cobertura de Azure Security Center con la plataforma para la comunidad y los asociados

Use Security Center para recibir recomendaciones no solo de Microsoft, sino también de soluciones existentes de asociados como Check Point, Tenable y CyberArk con muchas más integraciones.  El flujo de incorporación simple de Security Center puede conectar las soluciones existentes a Security Center, lo que permite ver las recomendaciones de seguridad en un único lugar, ejecutar informes unificados y aprovechar todas las funcionalidades de Security Center con las recomendaciones integradas y de asociados. También puede exportar las recomendaciones de Security Center a productos de asociados.

[Más información sobre Intelligent Security Association.](https://www.microsoft.com/security/partnerships/intelligent-security-association)



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integraciones avanzadas con la exportación de recomendaciones y alertas (versión preliminar)

Con el fin de habilitar escenarios de nivel empresarial sobre Security Center, ahora es posible consumir alertas y recomendaciones de Security Center en otros lugares, excepto Azure Portal o la API. Estos se pueden exportar directamente a un centro de eventos y áreas de trabajo de Log Analytics. Estos son algunos de los flujos de trabajo que puede crear en torno a estas nuevas capacidades:

- Con la exportación al área de trabajo de Log Analytics, puede crear paneles personalizados con Power BI.
- Con la exportación al Centro de eventos, podrá exportar alertas y recomendaciones de Security Center a los SIEM de terceros, a una solución de terceros o a Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Incorporación de servidores locales a Security Center desde Windows Admin Center (versión preliminar)

Windows Admin Center es un portal de administración para los servidores de Windows que no están implementados en Azure y que ofrecen varias funcionalidades de administración de Azure, como copias de seguridad y actualizaciones del sistema. Recientemente hemos agregado una capacidad para incorporar estos servidores que no son de Azure para que ASC los proteja directamente de la experiencia de Windows Admin Center.

Con esta nueva experiencia, los usuarios van a incorporar un servidor de WAC a Azure Security Center y habilitar la visualización de las alertas y recomendaciones de seguridad directamente en la experiencia de Windows Admin Center.


## <a name="september-2019"></a>Septiembre de 2019

Las actualizaciones de septiembre incluyen:

 - [Administración de reglas con mejoras de controles de aplicación adaptables](#managing-rules-with-adaptive-application-controls-improvements)
 - [Control de la recomendación de seguridad del contenedor mediante Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Administrar reglas con mejoras de controles de aplicación adaptables

Se ha mejorado la experiencia de administración de reglas para máquinas virtuales mediante controles de aplicación adaptables. Los controles de aplicación adaptables de Azure Security Center ayudan a controlar qué aplicaciones se pueden ejecutar en las máquinas virtuales. Además de una mejora general en la administración de reglas, una nueva ventaja le permite controlar qué tipos de archivo se protegerán cuando agregue una nueva regla.

[Más información acerca de los controles de aplicación adaptables](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Controlar la recomendación de seguridad del contenedor mediante Azure Policy

La recomendación de Azure Security Center para corregir vulnerabilidades en la seguridad de los contenedores ahora puede habilitarse o deshabilitarse mediante Azure Policy.

Para ver las directivas de seguridad habilitadas, en Security Center abra la página Directiva de seguridad.


## <a name="august-2019"></a>Agosto de 2019

Las actualizaciones de agosto incluyen:

 - [Acceso a máquina virtual Just-in-Time (JIT) para Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Corrección de un solo clic para impulsar la postura de seguridad (versión preliminar)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Administración entre inquilinos](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Acceso a máquina virtual Just-in-Time (JIT) para Azure Firewall 

El acceso a máquina virtual Just-in-Time (JIT) para Azure Firewall ya está disponible con carácter general. Úselo para proteger sus entornos protegidos con Azure Firewall además de los entornos protegidos con NSG.

El acceso de máquina virtual JIT reduce la exposición a los ataques volumétricos de red al proporcionar acceso controlado a las máquinas virtuales solo cuando sea necesario, mediante el uso de las reglas de NSG y Azure Firewall.

Al habilitar JIT para las máquinas virtuales, se crea una directiva que determina los puertos que se van a proteger, cuánto tiempo deben permanecer abiertos los puertos y las direcciones IP aprobadas desde donde se puede tener acceso a estos puertos. Esta directiva le ayuda a mantener el control de lo que los usuarios pueden hacer cuando solicitan acceso.

Las solicitudes se registran en el registro de actividad de Azure para que pueda supervisar y auditar fácilmente el acceso. La página Just-in-Time también le ayuda a identificar rápidamente las máquinas virtuales existentes que tienen habilitado JIT y las máquinas virtuales en las que se recomienda JIT.

[Más información acerca de Azure Firewall](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Corrección de un solo clic para impulsar la postura de seguridad (versión preliminar)

La puntuación segura es una herramienta que le ayuda a evaluar la postura de seguridad de las cargas de trabajo. Revisa las recomendaciones de seguridad y les asigna una prioridad, para que usted sepa qué recomendaciones realizar primero. De esta manera, puede encontrar las vulnerabilidades de seguridad más graves para dar prioridad a la investigación.

Con el fin de simplificar la corrección de configuraciones incompletas de seguridad y ayudarle a mejorar rápidamente su puntuación segura, hemos agregado una nueva funcionalidad que le permite corregir una recomendación en una gran parte de los recursos con un solo clic.

Esta operación le permitirá seleccionar los recursos a los que desea aplicar la corrección e iniciar una acción correctiva que configurará la configuración en su nombre.

Vea qué recomendaciones tiene habilitada la corrección rápida en la [guía de referencia para recomendaciones de seguridad](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Administración entre inquilinos

Security Center admite ahora escenarios de administración entre inquilinos como parte de Azure Lighthouse. Esto le permite obtener visibilidad y administrar la postura de seguridad de varios inquilinos en Security Center. 

[Más información sobre las experiencias de administración entre inquilinos](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Julio de 2019

### <a name="updates-to-network-recommendations"></a>Actualizaciones de recomendaciones sobre redes

Azure Security Center (ASC) ha lanzado nuevas recomendaciones de redes y ha mejorado algunas existentes. Ahora, el uso de Security Center garantiza una mayor protección de la red para los recursos. 

[Más información sobre las recomendaciones de redes](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>Junio de 2019

### <a name="adaptive-network-hardening---generally-available"></a>Protección de redes adaptable: disponible con carácter general

Una de las superficies de ataque más importantes para las cargas de trabajo que se ejecutan en la nube pública son las conexiones hacia y desde la red pública de Internet. Nuestros clientes saben que es difícil saber qué reglas del grupo de seguridad de red (NSG) deben estar en vigor para asegurarse de que las cargas de trabajo de Azure solo están disponibles para los intervalos de origen necesarios. Con esta característica, Security Center aprende los patrones de conectividad y el tráfico de red de las cargas de trabajo de Azure y proporciona recomendaciones de reglas de NSG, para las máquinas virtuales orientadas a Internet. Esto ayuda a nuestro cliente a configurar mejor las directivas de acceso a la red y limitar su exposición a los ataques. 

[Más información sobre la protección de red adaptable](security-center-adaptive-network-hardening.md).
