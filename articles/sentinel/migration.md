---
title: Migre a Azure Sentinel desde una solución de SIEM existente.
description: Aprenda la mejor manera de migrar desde una solución de SIEM existente a Azure Sentinel para realizar análisis de seguridad escalables e inteligentes en toda la organización.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 07/04/2021
ms.author: bagol
ms.openlocfilehash: 525def5e69317597664b54bc68505ae3f19d228e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179371"
---
# <a name="migrate-to-azure-sentinel-from-an-existing-siem"></a>Migración a Azure Sentinel desde una solución de SIEM existente

El equipo del centro de operaciones de seguridad (SOC) usará soluciones centralizadas de administración de eventos e información de seguridad (SIEM) y de orquestación de seguridad, automatización y respuesta (SOAR) para proteger el patrimonio digital, cada vez más descentralizado.

Las soluciones de SIEM heredadas suelen ser locales y pueden mantener una buena cobertura de los recursos locales. Sin embargo, las arquitecturas locales pueden tener una cobertura insuficiente para los recursos en la nube, como Azure, Microsoft 365, AWS o Google Cloud Platform (GCP). Por su parte, Azure Sentinel puede ingerir datos de recursos locales y en la nube, lo que garantiza la cobertura de todo el patrimonio.

En este artículo se describe cómo migrar de una solución de SIEM heredada existente a Azure Sentinel, ya sea en una configuración en paralelo o mediante la transición a una implementación completa de Azure Sentinel.

## <a name="plan-your-migration"></a>Planeación de la migración

Es posible que haya decidido iniciar una transición directa o gradual a Azure Sentinel, en función de sus necesidades empresariales y los recursos disponibles.

Querrá planear la migración correctamente para asegurarse de que la transición no introduce brechas en la cobertura, lo que podría poner en peligro la seguridad de la organización.

Para empezar, identifique las funcionalidades principales clave y los requisitos de máxima prioridad. Evalúe los casos de uso clave que cubre la solución de SIEM actual y decida en qué detecciones y funcionalidades debe seguir proporcionando cobertura Azure Sentinel.

Agregará más planeamiento en proceso en cada paso del proceso de migración, a medida que tenga en cuenta los orígenes de datos exactos y las reglas de detección que desea migrar. Para más información, consulte [Migración de los datos](#migrate-your-data) y [Migración de las reglas de análisis](#migrate-analytics-rules).

> [!TIP]
> La solución de SIEM actual puede tener un número abrumador de detecciones y casos de uso. Decida cuáles son más útiles para su negocio y determine cuáles no es necesario migrar. Por ejemplo, compruebe qué detecciones generaron resultados en el último año.
>

### <a name="compare-your-legacy-siem-to-azure-sentinel"></a>Comparación de la solución de SIEM heredada con Azure Sentinel

Compare la solución de SIEM heredada con Azure Sentinel para ayudar a refinar los criterios de finalización de la migración y descubrir en qué áreas le aporta más valor Azure Sentinel.

Por ejemplo, evalúe las siguientes áreas clave:

|Área de evaluación |Descripción  |
|---------|---------|
|**Cobertura de detección de ataques**     | Compare la capacidad de cada solución de SIEM para detectar toda la gama de ataques mediante [MITRE ATT&CK](https://attack.mitre.org/) o un marco similar.        |
|**Capacidad de respuesta**     |   Mida el tiempo medio de confirmación (MTTA); es decir, el tiempo entre que aparece una alerta en la solución de SIEM y un analista empieza a trabajar en ella. Este tiempo probablemente será similar entre las soluciones de SIEM.      |
|**Tiempo medio para corregir (MTTR)**     |    Compare el MTTR de los incidentes investigados por cada solución de SIEM, suponiendo que los analistas tienen niveles de aptitud equivalentes.     |
|**Velocidad y agilidad de la búsqueda**     | Mida la rapidez con la que los equipos pueden buscar, empezando por una hipótesis totalmente formada hasta consultar los datos, hasta obtener los resultados en cada plataforma de SIEM.        |
|**Fricción de crecimiento de la capacidad**     |  Compare el nivel de dificultad para agregar capacidad a medida que crece el uso. Tenga en cuenta que los servicios y las aplicaciones en la nube tienden a generar más datos de registro que las cargas de trabajo locales tradicionales.       |
|     |         |

Si tiene una inversión limitada o ninguna en una solución de SIEM local existente, el traslado a Azure Sentinel puede ser una implementación directa y sencilla. Sin embargo, las empresas que han invertido mucho en una solución de SIEM heredada suelen requerir un proceso en varias fases para dar cabida a las tareas de transición.

Aunque Azure Sentinel proporciona datos extendidos y respuesta tanto para el entorno local como de nube, es posible que desee iniciar la migración lentamente ejecutando Azure Sentinel y su solución de SIEM heredada en [paralelo](#select-a-side-by-side-approach-and-method). En una arquitectura en paralelo, los recursos locales pueden usar los recursos de la solución de SIEM local y de la nube, y las nuevas cargas de trabajo usan los análisis basados en la nube.

A menos que elija una configuración en paralelo a largo plazo, realice la migración a una implementación completa de Azure Sentinel para conseguir menores costos de infraestructura, análisis de amenazas en tiempo real y escalabilidad en la nube.

## <a name="select-a-side-by-side-approach-and-method"></a>Selección de un enfoque y un método en paralelo

Use una arquitectura en paralelo como una fase de transición a corto plazo que conduce a una solución de SIEM completamente hospedada en la nube o como un modelo operativo a medio y largo plazo, en función de las necesidades de SIEM de su organización.

Por ejemplo, aunque lo que se recomienda es usar una arquitectura en paralelo el tiempo suficiente como para completar la migración, es posible que su organización quiera permanecer con la configuración en paralelo durante más tiempo, por ejemplo, si no está listo para dejar su solución de SIEM heredada. Normalmente, las organizaciones que usan una configuración en paralelo a largo plazo usan Azure Sentinel para analizar solo sus datos en la nube.

Tenga en cuenta las ventajas y desventajas de cada enfoque a la hora de decidir cuál usar en la migración.

> [!NOTE]
> Muchas organizaciones evitan ejecutar varias soluciones de análisis locales debido al costo y la complejidad.
>
> Azure Sentinel proporciona [precios de pago por uso](azure-sentinel-billing.md) y una infraestructura flexible, lo que da a los equipos de SOC el tiempo suficiente para adaptarse al cambio. Migre y pruebe el contenido al ritmo que funcione mejor para su organización.
>
### <a name="short-term-approach"></a>Enfoque a corto plazo

:::row:::
   :::column span="":::
      **Ventajas**

        - Concede al personal de SOC tiempo para adaptarse a los nuevos procesos a medida que se migran las cargas de trabajo y los análisis.

        - Consigue una amplia correlación entre todos los orígenes de datos para los escenarios de búsqueda.

        - Elimina la necesidad de realizar análisis entre las soluciones de SIEM, crear reglas de reenvío y cerrar investigaciones en dos lugares.

        - Permite que el equipo de SOC cambie rápidamente las soluciones de SIEM heredadas a una versión anterior, lo que elimina costos de infraestructura y licencias.
   :::column-end:::
   :::column span="":::
      **Desventajas**

        - Puede implicar una rápida curva de aprendizaje para el personal de SOC.
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>Enfoque a medio y largo plazo

:::row:::
   :::column span="":::
      **Ventajas**

        - Permite usar las ventajas clave Azure Sentinel, como la inteligencia artificial, ML y las funcionalidades de investigación, sin abandonar por completo la solución de SIEM heredada.

        - Ahorra dinero en comparación con la solución de SIEM heredada, mediante el análisis de los datos en la nube o de Microsoft en Azure Sentinel.
   :::column-end:::
   :::column span="":::
      **Desventajas**

        - Aumenta la complejidad ya que separa los análisis entre distintas bases de datos.

        - Divide la administración de casos y las investigaciones de los incidentes que se han producido en varios entornos.

        - Incurre en mayores costos de personal e infraestructura.

        - Requiere que el personal del SOC tenga conocimientos sobre dos soluciones de SIEM diferentes.
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-azure-sentinel-recommended"></a>Envío de alertas desde una solución de SIEM heredada a Azure Sentinel (recomendado)

Envíe alertas, o indicadores de actividad anómala, desde la solución de SIEM heredada a Azure Sentinel.

- Realice la ingesta y el análisis de datos en la nube en Azure Sentinel.
- Use la solución de SIEM heredada para analizar los datos locales y generar alertas.
- Reenvíe las alertas de la solución de SIEM local a Azure Sentinel para establecer una única interfaz.

Por ejemplo, reenvíe alertas con [Logstash](connect-logstash.md), [API](/rest/api/securityinsights/) o [Syslog](connect-syslog.md) y almacénelas en formato [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) en el [área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md) en Azure Sentinel.

Al enviar alertas desde la solución de SIEM heredada a Azure Sentinel, el equipo puede hacer la correlación cruzada de esas alertas e investigarlas en Azure Sentinel. El equipo todavía puede acceder a la solución de SIEM heredada para una investigación más profunda si es necesario. Mientras tanto, puede seguir migrando orígenes de datos durante un período de transición ampliado.

Este método de migración en paralelo recomendado ofrece todo el valor de Azure Sentinel y la capacidad de migrar orígenes de datos al ritmo adecuado para su organización. Este enfoque evita duplicar los costos de almacenamiento e ingesta de datos mientras mueve los orígenes de datos.

Para más información, consulte:

- [Migración de las infracciones de QRadar a Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043)
- [Exportación de datos de Splunk a Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237)


### <a name="send-alerts-and-enriched-incidents-from-azure-sentinel-to-a-legacy-siem"></a>Envío de alertas e incidentes enriquecidos de Azure Sentinel a una solución de SIEM heredada

Analice algunos datos de Azure Sentinel, como los datos en la nube, y, a continuación, envíe las alertas generadas a una solución de SIEM heredada. Use la solución de SIEM *heredada* como interfaz única para realizar una correlación cruzada con las alertas que ha generado Azure Sentinel. Puede seguir usando Azure Sentinel para una investigación más profunda de las alertas generadas en esta solución.

Esta configuración es rentable, ya que puede mover el análisis de datos en la nube a Azure Sentinel sin duplicar los costos ni pagar dos veces por los datos. Sigue teniendo la libertad de migrar a su propio ritmo. A medida que continúa desplazando los orígenes de datos y las detecciones a Azure Sentinel, resulta más fácil migrar a Azure Sentinel como interfaz principal. Sin embargo, el simple reenvío de incidentes enriquecidos a una solución de SIEM heredada limita el valor que obtiene de las funcionalidades de investigación, búsqueda y automatización de Azure Sentinel.

Para más información, consulte:

- [Envío de alertas enriquecidas de Azure Sentinel a la solución de SIEM heredada](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [Envío de alertas enriquecidas de Azure Sentinel a IBM QRadar](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [Ingesta de alertas de Azure Sentinel en Splunk](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>Otros métodos

En la tabla siguiente se describen las configuraciones en paralelo que *no* se recomiendan, con detalles sobre las razones:

|Método  |Descripción  |
|---------|---------|
|**Envío de registros de Azure Sentinel a la solución de SIEM heredada**     |  Con este método, seguirá enfrentándose a los desafíos de costo y escalado de la solución de SIEM local. <br><br>Pagará por la ingesta de datos en Azure Sentinel, junto con los costos de almacenamiento en la solución de SIEM heredada, y no podrá aprovechar las detecciones de SIEM y SOAR, el análisis, el análisis de comportamiento de usuarios y entidades (UEBA), la inteligencia artificial o las herramientas de investigación y automatización de Azure Sentinel.       |
|**Envío de registros de una solución de SIEM heredada a Azure Sentinel**     |   Aunque este método proporciona toda la funcionalidad de Azure Sentinel, la organización sigue pagando por dos orígenes de ingesta de datos diferentes. Además de aumentar la complejidad arquitectónica, este modelo puede dar lugar a costos mayores.     |
|**Uso de Azure Sentinel y la solución de SIEM heredada como dos soluciones completamente independientes**     |  Puede usar Azure Sentinel para analizar algunos orígenes de datos, como los datos en la nube, y seguir usando la solución de SIEM local para otros orígenes. Esta configuración permite establecer límites claros respecto a cuándo usar cada solución y evita la duplicación de costos. <br><br>Sin embargo, la correlación cruzada se vuelve difícil y no se pueden diagnosticar completamente los ataques que afectan a ambos conjuntos de orígenes de datos. En el panorama actual, en el que las amenazas a menudo se mueven lateralmente a través de una organización, estas brechas de visibilidad pueden suponer importantes riesgos para la seguridad.       |
|     |         |



## <a name="migrate-your-data"></a>Migración de los datos

Asegúrese de migrar solo los datos que representen los casos de uso clave actuales.

1. Determine los datos necesarios para admitir cada uno de los casos de uso.

1. Determine si los orígenes de datos actuales proporcionan datos valiosos.

1. Identifique las brechas de visibilidad en la solución de SIEM actual y cómo puede evitarlas.

1. Para cada origen de datos, determine si necesita ingerir registros sin procesar, lo que puede resultar costoso, o si las alertas enriquecidas proporcionan suficiente contexto para los casos de uso clave.

      Por ejemplo, puede ingerir datos enriquecidos de productos de seguridad en toda la organización y usar Azure Sentinel para correlacionarlos entre sí, sin tener que ingerir registros sin procesar de los propios orígenes de datos.

1. Use cualquiera de los siguientes recursos para ingerir datos:

    - Use **[conectores de datos integrados](connect-data-sources.md) de Azure Sentinel** para empezar a ingerir datos. Por ejemplo, puede que desee iniciar una [evaluación gratuita](azure-sentinel-billing.md#free-trial) con los datos en la nube o que quiera usar [conectores de datos gratuitos](azure-sentinel-billing.md#free-data-sources) para ingerir datos de otros productos de Microsoft.

    - Use **[Syslog](connect-data-sources.md#syslog), el [formato de evento común (CEF)](connect-data-sources.md#common-event-format-cef) o las [API REST](connect-data-sources.md#rest-api-integration)** para conectar otros orígenes de datos.

        Para obtener más información, consulte [Conectores de datos de asociados de Azure Sentinel](partner-data-connectors.md) y el [catálogo de soluciones de Azure Sentinel](sentinel-solutions-catalog.md).

> [!TIP]
> - Si solo utiliza orígenes de datos gratuitos puede limitar la capacidad de realizar pruebas con datos que son importantes para usted. Al realizar pruebas, considere la posibilidad de una ingesta de datos limitada de conectores de datos gratuitos y de pago para sacar el máximo partido de los resultados.
>
> - A medida que migre las detecciones y cree casos de uso en Azure Sentinel, no deje de ser consciente de los datos que ingiere y confirme el valor que tienen para sus prioridades clave. Vuelva a revisar las conversaciones sobre recopilación de datos para garantizar la profundidad y la amplitud de los datos en todos los casos de uso.
>

## <a name="migrate-analytics-rules"></a>Migración de las reglas de análisis

Azure Sentinel utiliza análisis de aprendizaje automático para crear incidentes de alta fidelidad y acción; algunas de las detecciones que ya tiene pueden ser redundantes en Azure Sentinel. Por lo tanto, no migre todas las reglas de detección y análisis sin estudiarlas:

- Asegúrese de seleccionar los casos de uso que justifican la migración de reglas, teniendo en cuenta la prioridad empresarial y la eficacia.

- Revise las [reglas de análisis integradas](detect-threats-built-in.md) que puede que ya aborden sus casos de uso. En Azure Sentinel, vaya al separador **Configuration > Analytics > Rule templates** (Configuración > Análisis > Plantillas de reglas) para crear reglas basadas en plantillas integradas.

- Revise las reglas que no hayan desencadenado ninguna alerta en los últimos 6 a 12 meses y determine si siguen siendo pertinentes.

- Elimine las amenazas o alertas de bajo nivel que omite de forma rutinaria.

**Para migrar las reglas de análisis a Azure Sentinel**:

1. Compruebe que cuenta con un sistema de pruebas para cada regla que desea migrar.

    1. **Prepare un proceso de validación** para las reglas migradas, incluidos escenarios y scripts de prueba completos.

    1. **Asegúrese de que el equipo tiene recursos útiles** para probar las reglas migradas.

    1. **Confirme que tiene los orígenes de datos necesarios conectados** y revise los métodos de conexión de datos.

1. Compruebe si las detecciones están disponibles como plantillas integradas en Azure Sentinel:

    - **Si las reglas integradas son suficientes**, use plantillas de reglas integradas para crear reglas para su propia área de trabajo.

        En Azure Sentinel, vaya a la pestaña **Configuration > Analytics > Rule templates** (Configuración > Análisis > Plantillas de reglas) y cree y actualice cada regla de análisis pertinente.

        Para más información, consulte [Detección de amenazas integrada](detect-threats-built-in.md).

    - **Si tiene detecciones que no están cubiertas por las reglas integradas de Azure Sentinel**, pruebe un convertidor de consultas en línea, como [Uncoder.io](https://uncoder.io/) para convertir las consultas a KQL.

        Identifique la condición del desencadenador y la acción de regla y, a continuación, construya y revise la consulta KQL.

    - **Si ni las reglas integradas ni un convertidor de reglas en línea son suficientes**, deberá crear la regla manualmente. En tal caso, siga estos pasos para empezar a crear la regla:

        1. **Identifique los orígenes de datos que desea usar en la regla**. Querrá crear una tabla de asignación entre los orígenes de datos y las tablas de datos de Azure Sentinel para identificar las tablas que desea consultar.

        1. **Identifique los atributos, campos o entidades** en los datos que desea usar en las reglas.

        1. **Identifique los criterios y la lógica de la regla**. En esta fase, puede que desee usar plantillas de regla como ejemplos para construir las consultas KQL.

            Considere la posibilidad de usar filtros, reglas de correlación, listas activas, conjuntos de referencias, listas de control, anomalías de detección, agregaciones, etcétera. Puede usar las referencias proporcionadas por la solución de SIEM heredada para entender cómo asignar mejor la sintaxis de consulta.

            Por ejemplo, consulte:

            - [Asignación de reglas de ejemplo entre ArcSight/QRadar y Azure Sentinel](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [Ejemplos de asignación de SPL a KQL](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. **Identifique la condición del desencadenador y la acción de regla y, a continuación, construya y revise la consulta KQL**. Al revisar la consulta, considere la posibilidad de usar los recursos de guía de optimización de KQL.

1. Pruebe la regla con cada uno de los casos de uso pertinentes. Si no consigue los resultados esperados, es posible que quiera revisar la consulta de KQL y probar de nuevo.

1. Cuando esté satisfecho, puede considerar la regla migrada. Cree un cuaderno de estrategias para la acción de regla según sea necesario. Para más información, consulte [Automatización de la respuesta a amenazas con cuadernos de estrategias en Azure Sentinel](automate-responses-with-playbooks.md).

**Para más información, consulte**:

- [**Creación de reglas de análisis personalizadas para detectar amenazas**](detect-threats-custom.md) Use la [agrupación de alertas](detect-threats-custom.md#alert-grouping) para reducir el exceso de alertas agrupando las que se producen en un período de tiempo determinado.
- [**Asignación de campos de datos a entidades en Azure Sentinel**](map-data-fields-to-entities.md) para permitir que los ingenieros de SOC definan entidades como parte de la evidencia para realizar un seguimiento durante una investigación. La asignación de entidades también permite a los analistas de SOC aprovechar un [gráfico de investigación (investigate-cases.md#use-the-investigation-graph-to-deep-dive) intuitivo que puede ayudar a reducir el tiempo y el esfuerzo.
- [**Investigación de incidentes con datos de UEBA**](investigate-with-ueba.md) a modo de ejemplo de cómo usar evidencias para mostrar eventos, alertas y marcadores asociados a un incidente determinado en el panel de vista previa del incidente.
- [**Lenguaje de consulta Kusto (KQL)** ](/azure/data-explorer/kusto/query/), que puede usar para enviar solicitudes de solo lectura a la base de datos de [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) para procesar datos y devolver resultados. KQL también se usa en otras servicios Microsoft, como [Microsoft Defender para punto de conexión](https://www.microsoft.com/microsoft-365/security/endpoint-defender) y [Application Insights](../azure-monitor/app/app-insights-overview.md).

## <a name="use-automation-to-streamline-processes"></a>Uso de la automatización para optimizar los procesos

Use flujos de trabajo automatizados para agrupar y clasificar por orden de prioridad las alertas de un incidente común y para modificar su prioridad.

Para más información, consulte:

- [Orquestación de seguridad, automatización y respuesta (SOAR) en Azure Sentinel](automation-in-azure-sentinel.md)
- [Automatización de la respuesta a amenazas con cuadernos de estrategias en Azure Sentinel](automate-responses-with-playbooks.md)
- [Automatización del control de incidentes en Azure Sentinel con las reglas de automatización](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>Retirada de la solución de SIEM heredada

Use la siguiente lista de control para asegurarse de que ha realizado la migración completa a Azure Sentinel y está listo para retirar la solución de SIEM heredada:


|Área de preparación  |Detalles  |
|---------|---------|
|**Preparación de la tecnología**     | **Comprobar los datos críticos**: asegúrese de que todos los orígenes y alertas están disponibles en Azure Sentinel. <br><br>**Archivar todos los registros**: guarde los registros críticos de incidentes y casos anteriores, opcionalmente los datos sin procesar, para conservar el historial institucional.   |
|**Preparación del proceso**     |  **Cuadernos de estrategias**: actualice los [procesos de investigación y búsqueda](investigate-cases.md) en Azure Sentinel.<br><br>**Métricas**: asegúrese de que puede obtener todas las métricas clave de Azure Sentinel.<br><br>**Libros**: cree [libros personalizados](monitor-your-data.md) o use plantillas de libro integradas para obtener información rápidamente en cuanto se [conecte a los orígenes de datos](connect-data-sources.md).<br><br>**Incidentes**: asegúrese de transferir todos los incidentes actuales al nuevo sistema, incluidos los datos de origen necesarios.        |
|**Preparación de las personas**     |  **Analistas de SOC**: asegúrese de que todos los miembros del equipo tienen formación sobre Azure Sentinel y se sienten cómodos dejando la solución de SIEM heredada.   |
|     |         |
## <a name="next-steps"></a>Pasos siguientes

Después de la migración, explore los recursos de Azure Sentinel de Microsoft para ampliar sus aptitudes y sacar el máximo partido de esta solución.

Considere también la posibilidad de aumentar la protección contra amenazas usando Azure Sentinel junto con [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) y [Azure Defender](../security-center/azure-defender.md) para la [protección contra amenazas integrada](https://www.microsoft.com/security/business/threat-protection). Aproveche la amplitud de visibilidad que ofrece Azure Sentinel, a la vez que profundiza en el análisis detallado de amenazas.

Para más información, consulte:

- [Procedimientos recomendados para la migración de reglas](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [Seminario web: Procedimientos recomendados para convertir reglas de detección](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Orquestación de seguridad, automatización y respuesta (SOAR) en Azure Sentinel](automation-in-azure-sentinel.md)
- [Mejora en la administración de SOC con métricas de incidentes](manage-soc-with-incident-metrics.md)
- [Ruta de aprendizaje de Azure Sentinel](/learn/paths/security-ops-sentinel/)
- [Examen SC-200: Microsoft Security Operations Analyst](/learn/certifications/exams/sc-200)
- [Formación en Ninja de Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [Investigación de un ataque en un entorno híbrido con Azure Sentinel](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)