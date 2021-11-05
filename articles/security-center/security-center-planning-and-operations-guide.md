---
title: Guía de planeamiento y operaciones de Defender for Cloud
description: Este documento ayuda a planear antes de adoptar Defender for Cloud y proporciona consideraciones sobre las operaciones diarias.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 30f42a41584c7897a288da22f11245608a7efbb8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009555"
---
# <a name="planning-and-operations-guide"></a>Guía de planeamiento y operaciones

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Esta guía está destinada a profesionales de las tecnologías de la información (TI), arquitectos de TI, analistas de seguridad de la información y administradores de la nube que planean usar Defender for Cloud.


## <a name="planning-guide"></a>Guía de planeamiento
Esta guía abarca una serie de tareas que se pueden seguir para optimizar el uso de Defender for Cloud en función de los requisitos de seguridad y el modelo de administración de la nube de la organización. Para poder beneficiarse plenamente de Defender for Cloud, es importante comprender cómo las distintas personas o equipos de la organización usarán el servicio para satisfacer las necesidades relativas al desarrollo y las operaciones seguros, la supervisión, la gobernanza y la respuesta a incidentes. Las áreas principales que se deben tener en cuenta al planear el uso de Defender for Cloud son:

* Roles de seguridad y controles de acceso
* Directivas de seguridad y recomendaciones
* Recopilación de datos y almacenamiento
* Incorporación de recursos que no son de Azure
* Supervisión continuada de la seguridad
* Respuesta a los incidentes

En la siguiente sección obtendrá información sobre cómo planear cada una de esas áreas y aplicar las recomendaciones según sus requisitos.


> [!NOTE]
> Consulte en [Preguntas más frecuentes sobre Defender for Cloud](faq-general.yml) una lista de preguntas habituales que también pueden ser útiles durante la fase de diseño y planeamiento.

## <a name="security-roles-and-access-controls"></a>Roles de seguridad y controles de acceso
Según el tamaño y la estructura de la organización, puede que varias personas y equipos usen Defender for Cloud para llevar a cabo diferentes tareas relacionadas con la seguridad. En el siguiente diagrama se ofrece un ejemplo de personas ficticias y sus respectivos roles y responsabilidades en cuanto a la seguridad:

![Roles](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Defender for Cloud hace posible que estos usuarios cumplan estas diversas responsabilidades. Por ejemplo:

**Jeff (Propietario de la carga de trabajo)**

* Administración de una carga de trabajo de nube y sus recursos relacionados
* Responsable de la implementación y el mantenimiento de las protecciones de acuerdo con la directiva de seguridad de la empresa

**Ellen (CISO/CIO)**

* Responsable de todos los aspectos de seguridad de la empresa
* Desea comprender la postura de la empresa en materia de seguridad con respecto a las cargas de trabajo de nube
* Se le debe informar de los riesgos y ataques principales

**David (Seguridad de TI)**

* Define directivas de seguridad de la empresa para garantizar que existen las protecciones adecuadas
* Supervisa el cumplimiento con directivas
* Genera informes para la dirección ejecutiva o los auditores

**Judy (Operaciones de seguridad)**

* Supervisa y responde a alertas de seguridad las 24 horas, los 7 días de la semana
* Transfiere el problema al propietario de la carga de trabajo de nube o al analista de seguridad de TI

**Sam (Analista de seguridad)**

* Investigación de los ataques
* Trabajar con el propietario de la carga de trabajo de nube para aplicar correcciones

Defender for Cloud usa el [control de acceso basado en rol de Azure (RBAC de Azure)](../role-based-access-control/role-assignments-portal.md), que proporciona [roles integrados](../role-based-access-control/built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios de Azure. Cuando un usuario abre Defender for Cloud, solo ve la información relacionada con los recursos a los que tiene acceso. Esto significa que al usuario se le asigna el rol de Propietario, Colaborador o Lector para la suscripción o el grupo de recursos a los que pertenece un recurso. Además de estos roles, hay dos roles específicos de Defender for Cloud:

- **Lector de seguridad**: un usuario que pertenece a este rol solo puede ver las configuraciones de Defender for Cloud, que incluyen recomendaciones, alertas, directivas y estados, pero no puede realizar cambios.
- **Administrador de seguridad**: igual que el lector de seguridad pero también puede actualizar la directiva de seguridad o descartar recomendaciones y alertas.

Los roles de Defender for Cloud descritos anteriormente no tienen acceso a otras áreas de servicio de Azure, como Storage, Web y móvil o Internet de las cosas.

Con las personas que se explican en el diagrama anterior, sería necesario el siguiente control de acceso basado en rol de Azure:

**Jeff (Propietario de la carga de trabajo)**

* Propietario o colaborador del grupo de recursos

**Ellen (CISO/CIO)**

* Propietario o colaborador de la suscripción o administrador de seguridad

**David (Seguridad de TI)**

* Propietario o colaborador de la suscripción o administrador de seguridad

**Judy (Operaciones de seguridad)**

* Lector de suscripción o lector de seguridad para ver alertas
* Debe ser propietario o colaborador de la suscripción o administrador de seguridad para descartar alertas

**Sam (Analista de seguridad)**

* Lector de la suscripción para ver las alertas
* Debe ser propietario o colaborador de la suscripción para descartar alertas
* Puede requerir acceso al área de trabajo

Otra información importante que se debe tener en cuenta:

* Solo los propietarios o colaboradores de la suscripción y los administradores de seguridad pueden editar una directiva de seguridad
* Los únicos que pueden aplicar recomendaciones de seguridad para un recurso son los propietarios y los colaboradores de la suscripción y del grupo de recursos.

Cuando planee el control de acceso mediante RBAC de Azure para Defender for Cloud, asegúrese de comprender qué personas de la organización van a usar Defender for Cloud. Planee también los tipos de tareas que realizarán y luego configure RBAC de Azure en consecuencia.

> [!NOTE]
> Es recomendable que asigne el rol de menos permisos que los usuarios necesiten para realizar sus tareas. Por ejemplo, a los usuarios que solo necesiten ver información sobre el estado de seguridad de los recursos, pero no llevar a cabo acciones como aplicar recomendaciones o editar directivas, se les debe asignar el rol Lector.
>
>

## <a name="security-policies-and-recommendations"></a>Directivas de seguridad y recomendaciones
Una directiva de seguridad define la configuración deseada de las cargas de trabajo. Además, ayuda a garantizar el cumplimiento de los requisitos de seguridad normativos o de la empresa. En Defender for Cloud, puede definir directivas para las suscripciones de Azure, que se pueden adaptar al tipo de carga de trabajo o la confidencialidad de los datos.

Las directivas de Defender for Cloud contienen los siguientes componentes:
- [Recopilación de datos](enable-data-collection.md): configuración de colección de datos y aprovisionamiento de agente.
- [Directiva de seguridad](tutorial-security-policy.md): instancia de [Azure Policy](../governance/policy/overview.md) que determina los controles que supervisa y recomienda Defender for Cloud. También puede usar Azure Policy para crear definiciones, definir directivas adicionales y asignar directivas en los grupos de administración.
- [Notificaciones por correo electrónico](configure-email-notifications.md): configuración de contactos de seguridad y notificaciones.
- [Plan de tarifa](enhanced-security-features-overview.md): con o sin las características de seguridad mejoradas de Microsoft Defender for Cloud, lo que determina qué características de Defender for Cloud están disponibles para los recursos del ámbito (se puede especificar para suscripciones y áreas de trabajo o para grupos de recursos mediante la API).

> [!NOTE]
> Al especificar un contacto de seguridad se garantiza que Azure puede llegar a la persona adecuada de su organización si se produce un incidente de seguridad. Consulte [Configuración de notificaciones de alertas de seguridad por correo electrónico](configure-email-notifications.md) para más información sobre cómo habilitar esta recomendación.

### <a name="security-policies-definitions-and-recommendations"></a>Definiciones y recomendaciones de directivas de seguridad
Defender for Cloud crea automáticamente una directiva de seguridad predeterminada para cada una de las suscripciones de Azure. Puede modificar la directiva en Defender for Cloud o usar Azure Policy para crear nuevas definiciones, definir directivas adicionales y asignar directivas entre grupos de administración (que pueden representar una organización entera, una unidad de negocio de ella, etc.) y supervisar el cumplimiento de estas directivas en todos estos ámbitos.

Antes de configurar las directivas de seguridad, revise cada una de las [recomendaciones de seguridad](review-security-recommendations.md)y determine si son adecuadas para los diversos grupos de recursos y suscripciones. También es importante entender qué acción debe realizarse para abordar las recomendaciones de seguridad y qué persona de su organización será responsable de supervisar las nuevas recomendaciones y llevar a cabo los pasos necesarios.

## <a name="data-collection-and-storage"></a>Recopilación de datos y almacenamiento
Defender for Cloud utiliza el agente de Log Analytics, que es el mismo agente que usa el servicio Azure Monitor, para recopilar datos de seguridad de las máquinas virtuales. Los [datos recopilados](enable-data-collection.md) por este agente se almacenan en las áreas de trabajo de Log Analytics.

### <a name="agent"></a>Agente

Cuando el aprovisionamiento automático está habilitado en la directiva de seguridad, el agente de Log Analytics (para [Windows](../azure-monitor/agents/agent-windows.md) o [Linux](../azure-monitor/vm/monitor-virtual-machine.md)) se instala en todas las máquinas virtuales de Azure admitidas y en las nuevas que se creen. Si la máquina virtual o el equipo ya tienen instalado el agente de Log Analytics, Defender for Cloud aprovechará el agente instalado actualmente. El proceso del agente está diseñado para que no sea invasivo y tenga un impacto mínimo sobre el rendimiento de la máquina virtual.

El agente de Log Analytics para Windows requiere el uso del puerto TCP 443. Para más información, consulte el [artículo de solución de problemas](troubleshooting-guide.md).

Si en algún momento desea deshabilitar la recopilación de datos, puede desactivarla en la directiva de seguridad. Sin embargo, como el agente de Log Analytics se puede usar en otros servicios de administración y supervisión de Azure, el agente no se desinstalará automáticamente cuando desactive la recopilación de datos en Defender for Cloud. En caso necesario, puede desinstalar el agente manualmente.

> [!NOTE]
> Para encontrar una lista de las máquinas virtuales admitidas, consulte [Preguntas frecuentes: preguntas sobre máquinas virtuales](faq-vms.yml).

### <a name="workspace"></a>Área de trabajo

Un área de trabajo es un recurso de Azure que actúa como contenedor de los datos. Tanto usted como otros miembros de la organización pueden usar varias áreas de trabajo para administrar diferentes conjuntos de datos, recopilados a partir de toda la infraestructura de TI o de algunos de sus componentes.

Los datos recopilados por el agente de Log Analytics (en nombre de Defender for Cloud) se almacenarán en las áreas de trabajo de Log Analytics existentes asociadas a la suscripción de Azure o en nuevas áreas de trabajo, según la región geográfica de la máquina virtual.

En Azure Portal, puede realizar una exploración para ver una lista de las áreas de trabajo de Log Analytics, incluidas las creadas por Defender for Cloud. Se creará un grupo de recursos relacionado para las nuevas áreas de trabajo. Ambas siguen esta convención de nomenclatura:

* Área de trabajo: *DefaultWorkspace-[subscription-ID]-[geo]*
* Grupo de recursos: *DefaultResourceGroup-[geo]*

En el caso de las áreas de trabajo creadas por Defender for Cloud, los datos se conservan durante 30 días. En las áreas de trabajo existentes, la retención se basa en el plan de tarifa del área de trabajo. Si lo desea, también puede usar un área de trabajo existente.

> [!NOTE]
> Microsoft está totalmente comprometido con la protección de la privacidad y la seguridad de estos datos. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio. Para más información sobre el control de datos y la privacidad, consulte [Seguridad de datos de Microsoft Defender for Cloud](data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Incorporación de recursos que no son de Azure

Defender for Cloud puede supervisar la posición de seguridad de los equipos que no son de Azure, pero para ello antes hay que incorporar esos recursos. Para más información sobre cómo incorporar recursos que no son de Azure, lea [Incorporar equipos que no son de Azure](quickstart-onboard-machines.md).

## <a name="ongoing-security-monitoring"></a>Supervisión continuada de la seguridad
Después de la configuración inicial y la aplicación de las recomendaciones de Defender for Cloud, el siguiente paso es considerar los procesos operativos de Defender for Cloud.

La información general de Defender for Cloud proporciona una vista unificada de la seguridad en todos los recursos de Azure y los recursos que no son de Azure que ha conectado. En el ejemplo siguiente se muestra un entorno con muchos problemas que se deben solucionar:

![panel.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Defender for Cloud no interferirá en los procedimientos operativos normales, sino que supervisará de forma pasiva las implementaciones y proporcionará recomendaciones basadas en las directivas de seguridad que se hayan habilitado.

Cuando elige por primera vez usar Defender for Cloud para su entorno actual de Azure, debe asegurarse de revisar todas las recomendaciones, lo cual se puede hacer en la página **Recomendaciones**.

Planee la visita a la opción de inteligencia sobre amenazas como parte de las operaciones de seguridad diarias. Ahí puede identificar las amenazas de seguridad en el entorno, como determinar si un determinado equipo es parte de una red de robots (botnet).

### <a name="monitoring-for-new-or-changed-resources"></a>Supervisión de recursos nuevos o modificados

La mayoría de los entornos de Azure son dinámicos; en ellos se crean recursos, se activan o desactivan, se reconfiguran y se cambian. Defender for Cloud ayuda a garantizar la visibilidad del estado de seguridad de estos nuevos recursos.

Cuando agregue nuevos recursos (máquinas virtuales, bases de datos SQL) a su entorno de Azure, Defender for Cloud los detectará automáticamente y empezará a supervisar su seguridad. Esto también incluye los roles web de PaaS y los roles de trabajo. Si la recopilación de datos está habilitada en la [directiva de seguridad](tutorial-security-policy.md), se habilitarán automáticamente funcionalidades de supervisión adicionales para las máquinas virtuales.

También debería supervisar periódicamente los recursos existentes en busca de cambios de configuración que podrían haber creado riesgos de seguridad, desviaciones de las bases de referencia recomendadas y alertas de seguridad. 

### <a name="hardening-access-and-applications"></a>Protección de acceso y aplicaciones

Como parte de las operaciones de seguridad, también debe adoptar medidas preventivas para restringir el acceso a las máquinas virtuales y controlar las aplicaciones que se ejecutan en ellas. Si bloquea el tráfico entrante a las máquinas virtuales de Azure, reduce la exposición a los ataques y, al mismo tiempo, proporciona un acceso fácil para conectarse a ellas cuando es necesario. Use la característica [Acceso a VM Just-In-Time](just-in-time-access-usage.md) para proteger el acceso a las máquinas virtuales.

Puede usar [controles de aplicación adaptables](adaptive-application-controls.md) para limitar las aplicaciones que se pueden ejecutar en las máquinas virtuales que se encuentran en Azure. Entre otras ventajas, esto ayuda a proteger las máquinas virtuales frente al malware. Mediante el aprendizaje automático, Defender for Cloud analiza los procesos que se ejecutan en la máquina virtual para ayudarle a crear reglas de inclusión en la lista de permitidos.


## <a name="incident-response"></a>Respuesta a los incidentes
Defender for Cloud detecta amenazas y alerta sobre ellas a medida que se producen. Las organizaciones deben estar al tanto de las nuevas alertas de seguridad y tomar medidas según sea necesario para investigarlas o solucionar el ataque. Para más información sobre cómo funciona la protección contra amenazas de Defender for Cloud, consulte [¿Cómo detecta las amenazas Defender for Cloud?](alerts-overview.md#detect-threats).

Aunque el objetivo de este artículo no es ayudarle a crear su propio plan de respuesta a incidentes, vamos a usar las respuestas de seguridad de Microsoft Azure en el ciclo de vida de la nube como base para las fases de la respuesta a incidentes. Estas fases se muestran en el diagrama siguiente:

![Fases de la respuesta a incidentes en el ciclo de vida de la nube.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Puede usar la guía [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) sobre el tratamiento de los incidentes de seguridad informática del National Institute of Standards and Technology (NIST) de EE. UU. como ayuda para crear el suyo propio.
>

Puede usar las alertas de Defender for Cloud durante las siguientes fases:

* **Detectar**: identifique una actividad sospechosa en uno o varios recursos.
* **Evaluar**: realice la evaluación inicial para más información acerca de la actividad sospechosa.
* **Diagnosticar**: siga los pasos de corrección para llevar a cabo el procedimiento técnico para solucionar el problema.

Cada alerta de seguridad proporciona información que sirve para comprender la naturaleza del ataque y sugerir posibles mitigaciones. Algunas alertas también proporcionan vínculos a más datos o a otras fuentes de información dentro de Azure. Puede usar la información proporcionada para proseguir la investigación y para iniciar la mitigación, y también puede buscar datos relacionados con la seguridad que se almacenan en el área de trabajo.

En el ejemplo siguiente se muestra una actividad sospechosa de RDP:

![Actividad sospechosa.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

En esta página se muestran los detalles relacionados con la hora en que ocurrió el ataque, el nombre del host de origen, la máquina virtual de destino y también pasos recomendados. En algunas circunstancias, la información de origen del ataque puede estar vacía. Consulte [Falta de información de origen en las alertas de Defender for Cloud](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) para más información acerca de este tipo de comportamiento.

Una vez identificado el sistema en riesgo, puede ejecutar una [automatización de flujos de trabajo](workflow-automation.md) creada anteriormente. Se trata de una colección de procedimientos que se pueden ejecutar desde Defender for Cloud una vez que se desencadene mediante una alerta.

En el vídeo [Uso de Defender for Cloud y Microsoft Operations Management Suite para dar respuesta a incidentes](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703), puede ver algunas demostraciones que muestran cómo se puede usar Defender for Cloud en cada una de estas fases.

> [!NOTE]
> Para más información acerca de cómo usar las funcionalidades de Defender for Cloud para ayudarle durante el proceso de respuesta ante incidentes, consulte [Administración y respuesta a las alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md).
>
>

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a planear la adopción de Defender for Cloud. Para más información sobre Defender for Cloud, consulte las páginas siguientes:

* [Administración y respuesta a las alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md)
* [Integración de soluciones de seguridad en Microsoft Defender for Cloud](./partner-integration.md): obtenga información sobre cómo supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes: preguntas generales sobre Defender for Cloud](faq-general.yml): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](/archive/blogs/azuresecurity/) :  encuentre entradas de blog sobre el cumplimiento y la seguridad en Azure.
