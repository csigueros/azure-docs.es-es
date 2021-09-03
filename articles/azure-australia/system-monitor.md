---
title: Supervisión del sistema para la seguridad en Azure Australia
description: Guía sobre la configuración de la supervisión del sistema en las regiones de Australia para cumplir con los requisitos específicos de las directivas, reglamentos y legislación de la administración pública de Australia.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 775b2e366d335944205197aad97c1752b6ee7d04
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/07/2021
ms.locfileid: "117029152"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Supervisión del sistema para la seguridad en Azure Australia

Tener una sólida estrategia de seguridad que incluya la supervisión en tiempo real y las evaluaciones de seguridad rutinarias es fundamental para mejorar la seguridad operativa diaria de los entornos de TI, incluida la nube.

La seguridad en la nube es un esfuerzo conjunto entre el cliente y el proveedor de la nube. Hay cuatro servicios que Microsoft Azure proporciona para facilitar estos requisitos teniendo en cuenta las recomendaciones contenidas en los [controles del manual de seguridad (ISM) del centro para la ciberseguridad australiano (ACSC)](https://acsc.gov.au/infosec/ism/index.htm), en concreto, la implementación de un registro de eventos centralizado, la auditoría de registros de eventos y la valoración y administración de vulnerabilidades de seguridad. Los servicios de Microsoft Azure son:

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

El ACSC recomienda que utilice estos servicios para datos **PROTEGIDOS.** Mediante el uso de estos servicios, puede supervisar y analizar de forma proactiva los entornos de TI, y tomar decisiones informadas sobre cuál es la mejor ubicación para asignar los recursos. Cada uno de estos servicios forma parte de una solución combinada para proporcionarle la mejor visión, recomendaciones y protección posibles.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) proporciona una consola de administración de seguridad unificada que se usa para supervisar y mejorar la seguridad de los recursos de Azure y los datos hospedados. Azure Security Center incluye Puntuación de seguridad, una puntuación que está basada en un análisis del estado de la configuración de procedimientos recomendados de Azure Advisor y el cumplimiento general de Azure Policy.

Azure Security Center proporciona a los clientes de Azure las siguientes características:

* Directiva de seguridad, valoración y recomendaciones
* Búsqueda y recopilación de eventos de seguridad
* Controles de acceso y aplicación
* Detección de amenazas avanzada
* Control de acceso Just-in-Time a las máquinas virtuales
* Seguridad híbrida

El ámbito de los recursos supervisados por Azure Security Center se puede expandir para incluir los recursos locales admitidos en un entorno de nube híbrida. Esto incluye los recursos locales supervisados actualmente por una versión compatible de System Center Operations Manager.

El nivel "estándar" de Security Center también proporciona controles de seguridad basados en la nube que las [8 estrategias esenciales del ASD](https://acsc.gov.au/publications/protect/essential-eight-explained.htm) requieren. Estos controles incluyen el filtrado de aplicaciones y la restricción de privilegios administrativos mediante el acceso Just-in-Time.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../azure-monitor/overview.md) es la solución de registro centralizado para todos los recursos de Azure que incluye log Analytics y Application Insights. A partir de los recursos de Azure se recopilan dos tipos de datos clave: registros y métricas. Una vez recopilada en Azure Monitor, la información de registro se puede usar en una amplia gama de herramientas y con diferentes propósitos.

![Introducción a Azure Monitor](media/overview.png)

Azure Monitor también incluye el "registro de actividad de Azure". El registro SActivity almacena todos los eventos de nivel de suscripción que se han producido en Azure. Permite a los clientes de Azure ver los "quién, qué y cuándo" de las operaciones que se llevan a cabo en sus recursos de Azure. Tanto el registro basado en recursos que se envía a Azure Monitor como los eventos del registro de actividad de Azure se pueden analizar mediante el lenguaje de consulta integrado Kusto. Estos registros se pueden exportar, usar para crear vistas y paneles personalizados y configurarse para desencadenar alertas y notificaciones.

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) analiza recursos de Azure compatibles, archivos de registro generados por el sistema y configuraciones de recursos actuales dentro de la suscripción de Azure. El análisis proporcionado en Azure Advisor se genera en tiempo real y se basa en los procedimientos recomendados de Microsoft. Se analizarán todos los recursos de Azure admitidos que se agreguen a su entorno y se proporcionarán las recomendaciones adecuadas. Las recomendaciones de Azure Advisor se clasifican en cuatro categorías de procedimientos recomendados:

* Seguridad
* Alta disponibilidad
* Rendimiento
* Coste

Las recomendaciones de seguridad generadas por Azure Advisor forman parte del análisis de seguridad general proporcionado por Azure Security Center.

La información recopilada por Azure Advisor proporciona a los administradores:

* Información acerca de la configuración de recursos que no cumpla el procedimiento recomendado
* Guía sobre acciones de corrección específicas que se deben llevar a cabo
* Clasificaciones que indican cuáles son las acciones correctivas que se deben realizar con prioridad alta

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) proporciona la capacidad de aplicar reglas que controlan los tipos de recursos de Azure y su configuración permitida. Policy se puede usar para controlar la creación y configuración de recursos, o para auditar la configuración en un entorno. Estos resultados de auditoría se pueden usar para formar la base de las actividades de corrección. Azure Policy difiere del control de acceso basado en rol (RBAC) de Azure; Azure Policy se usa para restringir los recursos y su configuración, mientras que Azure RBAC se utiliza para restringir el acceso con privilegios a los usuarios de Azure.

Tanto si se está aplicando la directiva específica como si se está auditando el efecto de la misma, el cumplimiento de la directiva se supervisa continuamente y se proporciona información de cumplimiento general y específica del recurso a los administradores. Los datos de cumplimiento de Azure Policy se proporcionan a Azure Security Center y forman parte de la Puntuación de seguridad.

## <a name="key-design-considerations"></a>Consideraciones clave sobre el diseño

Al implementar una estrategia de registro de eventos, el ISM del ACSC destaca las siguientes consideraciones:

* Instalaciones de registro centralizado
* Eventos específicos que se registrarán
* Protección del registro de eventos
* Retención del registro de eventos
* Auditoría de registros de eventos

Además de recopilar y administrar registros, el ISM también recomienda la evaluación rutinaria de vulnerabilidades del entorno de TI de una organización.

### <a name="centralised-logging"></a>Registro centralizado

Cualquier solución de registro debería, siempre que sea posible, consolidar los registros capturados en un único repositorio de datos. Esto no solo reduce la complejidad operativa y evita la creación de varios silos de datos, también permite analizar juntos los datos recopilados de varios orígenes, lo que permite identificar eventos correlacionados. Esto es fundamental para detectar y administrar el ámbito de cualquier incidente de ciberseguridad.

Este requisito se cumple para todos los clientes de Azure con Azure Monitor. Esta oferta no solo proporciona un repositorio de registro centralizado en Azure para todos los recursos de Azure, sino que también permite transmitir los datos a un centro de eventos de Azure. Azure Event Hubs proporciona un servicio de ingesta de datos en tiempo real y totalmente administrado. Una vez que los datos de Azure Monitor se transmiten a un centro de eventos de Azure, los datos también se pueden conectar fácilmente con los repositorios de Administración de eventos e información de seguridad (SIEM) ya existentes y con otras herramientas de supervisión de terceros.

Microsoft también ofrece su propia solución de SIEM nativa de Azure, Azure Sentinel. Azure Sentinel admite una amplia variedad de conectores de datos y se puede usar para supervisar eventos de seguridad en toda la empresa. Mediante la combinación de los datos de los [conectores de datos](../sentinel/connect-data-sources.md) admitidos, el aprendizaje automático integrado de Azure Sentinel y el lenguaje de consulta Kusto, se le proporciona a los administradores de seguridad una única solución para la detección de alertas, la visibilidad de amenazas, búsqueda proactiva y respuesta a amenazas. Azure Sentinel también proporciona una característica de búsqueda y de cuaderno que permite a los administradores de seguridad registrar todos los pasos realizados como parte de una investigación de seguridad en un cuaderno de estrategias reutilizable que se puede compartir dentro de una organización. Los administradores de seguridad pueden incluso usar el [análisis de usuario](../sentinel/overview.md) integrado para investigar las acciones de un único usuario designado.

### <a name="logged-events-and-log-detail"></a>Eventos registrados y detalles de registro

ISM proporciona una lista detallada de los tipos de registro de eventos que se deben incluir en cualquier estrategia de registro. Los registros capturados tiene que contener detalles suficientes para ser de uso práctico en la realización de análisis e investigaciones.

Los registros recopilados en Azure se encuentran en una de las tres categorías siguientes:

* **Registros de control y administración**: proporcionan información sobre las operaciones CREATE, UPDATE y DELETE de Azure Resource Manager.

* **Registro del plano de datos**: contienen eventos generados como parte de la utilización de recursos de Azure. Incluye orígenes como registros de eventos de Windows, incluidos los registros de sistema, seguridad y aplicación.

* **Eventos procesados**: estos eventos contienen información sobre eventos y alertas que Azure ha procesado de manera automática en nombre del cliente. Un ejemplo de evento procesado son las alertas de Azure Security Center.

La supervisión de la máquina virtual de Azure se ha mejorado mediante la implementación del agente de máquina virtual tanto para Windows como para Linux. Esto aumenta notablemente la amplitud de información de registro recopilada. La implementación de este agente puede configurarse para que se produzca automáticamente mediante Azure Security Center.

Microsoft proporciona información detallada sobre los registros específicos de recursos de Azure y sus [esquemas](../security/fundamentals/log-audit.md).

### <a name="log-retention-and-protection"></a>Retención y protección de registros

 Los registros de eventos se tienen que almacenar de forma segura durante el período de retención requerido. El ISM aconseja que los registros se conserven durante un período mínimo de siete años. Azure proporciona una serie de medios para garantizar la larga duración de los registros recopilados. De forma predeterminada los eventos de Azure Log se almacenan durante 90 días. Los datos de registro capturados por Azure Monitor pueden moverse y almacenarse en una cuenta de Azure Storage según sea necesario para la retención a largo plazo. Los registros de actividad almacenados en una cuenta de Azure Storage se pueden conservar durante un número de días establecido, o de forma indefinida si es necesario.

Las cuentas de Azure Storage que se usan para almacenar los eventos de Azure Log pueden incluir redundancia geográfica y se puede realizar una copia de seguridad con Azure Backup. Una vez que se capturan con Azure Backup, cualquier eliminación de copias de seguridad que contengan registros, requiere la aprobación administrativa, aún así, las copias de seguridad marcadas para su eliminación se conservan durante 14 días, lo que permite su recuperación. Azure Backup permite 9999 copias de una instancia protegida, lo que proporciona más de 27 años de copias de seguridad diarias.

El control de acceso basado en rol (Azure RBAC) se debe usar para controlar el acceso a los recursos utilizados para el registro de Azure. Azure Monitor, las cuentas de Azure Storage y las copias de Azure Backup se deben configurar con Azure RBAC para garantizar la seguridad de los datos incluidos en los registros.

### <a name="log-auditing"></a>Auditoría de registros

El verdadero valor de los registros se materializa una vez que se analizan. El uso del análisis automatizado y del manual, así como la familiarización con las herramientas disponibles, le ayudará a detectar y administrar las infracciones de la directiva de seguridad de la organización y los incidentes de ciberseguridad. Azure Monitor proporciona un amplio conjunto de herramientas para analizar los registros recopilados. El resultado de este análisis se puede compartir entre sistemas, visualizar o difundir en varios formatos.

Los datos de registro almacenados en Azure Monitor se guardan en un área de trabajo de Log Analytics. Todos los análisis comienzan con una consulta. Las consultas de Azure Monitor se escriben en el lenguaje de consulta Kusto. Las consultas constituyen la base de todas las salidas de Azure Monitor, desde los paneles de Azure a las reglas de alerta.

![Introducción a las consultas de Azure Log](media/queries-overview.png)

La auditoría de registros se puede mejorar mediante el uso de soluciones de supervisión. Se trata de soluciones integradas que contienen vistas de lógica de recopilación, consultas y visualización de datos. Microsoft [ofrece](../azure-monitor/monitor-reference.md) una serie de soluciones de supervisión y soluciones adicionales de proveedores de productos que se pueden encontrar en Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Evaluación y administración de vulnerabilidades

El ISM indica que la valoración y administración rutinarias de las vulnerabilidades son esenciales. Su entorno de TI evoluciona constantemente y las amenazas de seguridad externas están perennemente cambiando. Con Azure Security Center puede realizar evaluaciones de vulnerabilidades automatizadas y obtener una guía sobre cómo planear y realizar actividades de corrección.

La Puntuación de seguridad en Azure Security Center ofrece una lista de recomendaciones cuya aplicación mejorará la seguridad de su entorno. La lista se ordena por el impacto en la Puntuación de seguridad general de mayor a menor. Ordenar la lista por impacto le permite centrarse en las recomendaciones de prioridad más alta que presentan el mayor valor para mejorar la seguridad.

Azure Policy también desempeña una parte fundamental en la evaluación de vulnerabilidades continuada. Los tipos de directivas disponibles en Azure Policy van desde la aplicación de etiquetas y valores de recursos para restringir las regiones de Azure en las que se pueden crear recursos, hasta el bloqueo de la creación de tipos de recursos concretos de forma conjunta. Un conjunto de directivas de Azure se puede agrupar en iniciativas. Las iniciativas se usan para combinar directivas de Azure relacionadas que, cuando se aplican juntas como un grupo, forman la base de un objetivo de seguridad o cumplimiento específico.

Azure Policy tiene una biblioteca de definiciones de directivas integradas que crece constantemente. Azure Portal también le ofrece la opción de crear sus propias definiciones personalizadas de Azure Policy. Una vez que encuentre una directiva en la biblioteca existente o cree una nueva, puede asignarla a los recursos de Azure. El [ámbito](../governance/policy/tutorials/create-and-manage.md) de estas asignaciones se puede definir a varios niveles en la jerarquía de administración de recursos. La asignación de directivas es heredada, lo que significa que todos los recursos secundarios dentro de un ámbito reciben la misma asignación de directiva. Los recursos también se pueden excluir de la asignación de directivas de un ámbito según sea necesario.

Todas las directivas de Azure implementadas contribuyen a la Puntuación de seguridad de una organización. En un entorno con un alto nivel de personalización, se pueden crear e implementar definiciones de Azure Policy personalizadas para proporcionar información de auditoría adaptada a cargas de trabajo específicas.

## <a name="getting-started"></a>Introducción

Para empezar con Azure Security Center y hacer uso completo de Azure Monitor, Advisor y Policy, Microsoft recomienda los siguientes pasos iniciales:

* Habilitación de Azure Security Center
* Actualización al nivel Estándar
* Habilitación del aprovisionamiento automático de Microsoft Monitoring Agent para admitir Azure Virtual Machines
* Revisión, priorización y mitigación de las recomendaciones y alertas de seguridad en el panel del centro de seguridad

## <a name="next-steps"></a>Pasos siguientes

Lea [Azure Policy y Azure Blueprints](azure-policy.md) para más información sobre la implementación de la gobernanza y el control sobre los recursos de Azure Australia para garantizar el cumplimiento normativo y de directivas.