---
title: Trabajo con indicadores de amenazas en Microsoft Sentinel | Microsoft Docs
description: En este artículo se explica cómo ver, crear, administrar, visualizar y detectar amenazas con indicadores de inteligencia sobre amenazas de Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 29a464e27b6ee276d0fe2bc313a87ae718453067
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519864"
---
# <a name="work-with-threat-indicators-in-microsoft-sentinel"></a>Uso de los indicadores de amenazas de Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Puede integrar la inteligencia sobre amenazas (TI) en Microsoft Sentinel a través de las actividades siguientes:

- **Importe inteligencia sobre amenazas** en Microsoft Sentinel habilitando **conectores de datos** en varias [plataformas](connect-threat-intelligence-tip.md) y [fuentes](connect-threat-intelligence-taxii.md) de TI.

- **Vea y administre** la inteligencia sobre amenazas importada en **Registros** y en la página **Inteligencia sobre amenazas** de Microsoft Sentinel.

- **Detecte las amenazas** y genere alertas e incidentes de seguridad utilizando las plantillas de reglas incorporadas de **Análisis** basadas en la inteligencia de amenazas importada.

- **Visualice información clave** sobre la inteligencia de amenazas importada en Microsoft Sentinel con el **libro de inteligencia sobre amenazas**.

## <a name="view-your-threat-indicators-in-microsoft-sentinel"></a>Visualización de los indicadores de amenazas de Microsoft Sentinel

### <a name="find-and-view-your-indicators-in-logs"></a>Búsqueda y visualización de indicadores en Registros

En este procedimiento se describe cómo ver los indicadores de amenazas importados en el área **Registros** de Microsoft Sentinel, junto con otros datos de eventos de Microsoft Sentinel, independientemente de la fuente de origen o el conector usado.

Los indicadores de amenazas importados se enumeran en la tabla **Microsoft Sentinel > ThreatIntelligenceIndicator**, que es la base para que las consultas de inteligencia sobre amenazas se ejecuten en otra parte de Microsoft Sentinel, como en análisis o libros.

**Para ver los indicadores de inteligencia sobre amenazas en registros:**

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Microsoft Sentinel**.

1. Seleccione el área de trabajo al que ha importado indicadores de amenazas mediante el conector de datos de inteligencia sobre amenazas.

1. Seleccione **Registros** en la sección **General** del menú de Microsoft Sentinel.

1. La tabla **ThreatIntelligenceIndicator** se encuentra en el grupo **Microsoft Sentinel**.

1. Seleccione el icono **Vista previa de los datos** (el ojo) junto al nombre de la tabla y seleccione el botón **Ver en el editor de consultas** para ejecutar una consulta que mostrará los registros de esta tabla.

Los resultados deben tener un aspecto similar al indicador de amenazas de ejemplo que se muestra a continuación:

:::image type="content" source="media/work-with-threat-indicators/threat-intel-sample-query.png" alt-text="Datos de consulta de ejemplo":::

### <a name="find-and-view-your-indicators-in-the-threat-intelligence-page"></a>Búsqueda y visualización de indicadores en la página Inteligencia sobre amenazas

En este procedimiento se describe cómo ver y administrar los indicadores en la nueva página **Inteligencia sobre amenazas**, accesible desde el menú principal de Microsoft Sentinel. Use la página **Inteligencia sobre amenazas** para ordenar, filtrar y buscar los indicadores de amenazas importados sin escribir una consulta de Log Analytics.

**Para ver los indicadores de inteligencia sobre amenazas en la página Inteligencia sobre amenazas**:

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Microsoft Sentinel**.

1. Seleccione el área de trabajo a la que importó los indicadores de amenazas mediante los conectores o cuadernos de reproducción o para la que ha creado datos de inteligencia sobre amenazas.

1. En la sección **Administración de amenazas** de la izquierda, seleccione la página **Inteligencia sobre amenazas**.

1. En la cuadrícula, seleccione el indicador para el que quiere ver más detalles. Los detalles del indicador aparecen a la derecha, con información como niveles de confianza, etiquetas, tipos de amenazas, etc.

    Microsoft enriquece cada indicador con datos adicionales de GeoLocation y WhoIs, que proporcionan más contexto para las investigaciones en las que se encuentra el indicador seleccionado.

    Por ejemplo:

    :::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="Captura de pantalla de la página Inteligencia sobre amenazas con un indicador que muestra los datos de GeoLocation y WhoIs." lightbox="media/whats-new/geolocation-whois-ti.png":::

La página **Inteligencia sobre amenazas** también permite crear indicadores de amenazas directamente dentro de la interfaz de Microsoft Sentinel, y realizar dos de las tareas administrativas de inteligencia sobre amenazas más comunes: el etiquetado de indicadores y la creación de nuevos indicadores relacionados con investigaciones de seguridad.

> [!IMPORTANT]
> El enriquecimiento de GeoLocation y WhoIs está actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

#### <a name="create-a-new-indicator"></a>Creación de un nuevo indicador

1. En [Azure Portal](https://portal.azure.com/), vaya al servicio **Microsoft Sentinel**.

1. Elija el **área de trabajo** al que ha importado indicadores de amenazas mediante el conector de datos de inteligencia sobre amenazas.

1. Seleccione **Inteligencia sobre amenazas** en la sección Administración de amenazas del menú de Microsoft Sentinel.

1. Seleccione el botón **Agregar nuevo** en la barra de menús de la parte superior de la página.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-add-new-indicator.png" alt-text="Incorporación de un nuevo indicador de amenazas" lightbox="media/work-with-threat-indicators/threat-intel-add-new-indicator.png":::

1. Elija el tipo de indicador y rellene el formulario en el panel **Nuevo indicador**. Los campos obligatorios están marcados con un asterisco rojo (*).

1. Seleccione **Aplicar**. El indicador se agrega a la lista Indicadores y se envía también a la tabla *ThreatIntelligenceIndicator* en **Registros**.

#### <a name="tag-threat-indicators"></a>Indicadores de amenazas de etiqueta

El etiquetado de indicadores de amenazas es una forma sencilla de agruparlos para que sean más fáciles de encontrar. Normalmente, podría aplicar una etiqueta a los indicadores relacionados con un incidente concreto, o bien a aquellos que representan amenazas de un actor conocido determinado o una campaña de ataques conocida. Puede etiquetar indicadores de amenazas de forma individual o realizar una selección múltiple de los indicadores y etiquetarlos todos a la vez. A continuación se muestra un ejemplo de etiquetado de varios indicadores con un identificador de incidente. Dado que el etiquetado es de forma libre, una práctica recomendada es crear convenciones de nomenclatura estándar para las etiquetas del indicador de amenazas. Puede aplicar varias etiquetas a cada indicador.

:::image type="content" source="media/work-with-threat-indicators/threat-intel-tagging-indicators.png" alt-text="Aplicación de etiquetas a indicadores de amenazas" lightbox="media/work-with-threat-indicators/threat-intel-tagging-indicators.png":::

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Detección de amenazas con análisis basados en indicadores de amenazas

El caso de uso más importante para los indicadores de amenazas en soluciones SIEM como Microsoft Sentinel es la activación de reglas de análisis de detección de amenazas. Estas reglas basadas en indicadores comparan eventos sin formato de los orígenes de datos con sus indicadores de amenazas para determinar la presencia de amenazas de seguridad en la organización. En **Análisis** de Microsoft Sentinel, crea reglas de análisis que se ejecutan de forma programada y genera alertas de seguridad. Las reglas están controladas mediante consultas, junto con configuraciones que determinan con qué frecuencia debe ejecutarse la regla, qué clase de resultados de consulta deben generar alertas de seguridad e incidentes, y qué automatizaciones deben desencadenarse como respuesta.

Aunque siempre puede crear nuevas reglas de análisis desde cero, Microsoft Sentinel proporciona un conjunto de plantillas de reglas integradas, creadas por ingenieros de seguridad de Microsoft, que puede usar tal cual o modificar para satisfacer sus necesidades. Puede identificar fácilmente las plantillas de reglas que usan indicadores de amenazas, ya que todas tienen un título que empieza por "*TI map*…". Todas estas plantillas de reglas funcionan de forma similar, siendo la única diferencia el tipo de indicadores de amenazas que se usan (dominio, correo electrónico, hash de archivo, dirección IP o dirección URL) y el tipo de evento que se va a comparar. En cada plantilla se muestran los orígenes de datos requeridos necesarios para el funcionamiento de la regla, de modo que puede ver a simple vista si tiene ya importados los eventos necesarios en Microsoft Sentinel. Al editar y guardar una plantilla de regla existente o crear una nueva regla, se habilita de forma predeterminada.

### <a name="configure-a-rule-to-generate-security-alerts"></a>Configuración de una regla para generar alertas de seguridad

A continuación se puede ver un ejemplo de cómo habilitar y configurar una regla para generar alertas de seguridad mediante los indicadores de amenazas que ha importado en Microsoft Sentinel. En este ejemplo, se ysa la plantilla de regla llamada **TI map IP entity to AzureActivity**. Esta regla coincidirá con cualquier indicador de amenazas de tipo de dirección IP con todos sus eventos de actividad de Azure. Cuando se encuentre una coincidencia, se generará una **alerta**, y un **incidente** correspondiente para que lo investigue el equipo de operaciones de seguridad. Esta regla de análisis funcionará correctamente solo si ha habilitado uno de los conectores de datos de **inteligencia sobre amenazas** o ambos (para importar indicadores de amenazas), y el conector de datos **Actividad de Azure** (para importar los eventos de nivel de suscripción de Azure).

1. En [Azure Portal](https://portal.azure.com/), vaya al servicio **Microsoft Sentinel**.

1. Elija el **área de trabajo** al que importó indicadores de amenazas mediante los conectores de datos de **inteligencia sobre amenazas** y los datos de actividad de Azure mediante el conector de datos **Actividad de Azure**.

1. Seleccione **Análisis** en la sección **Configuración** del menú de Microsoft Sentinel.

1. Seleccione la pestaña **Plantillas de reglas** para ver la lista de plantillas de reglas de análisis disponibles.

1. Localice a la regla titulada **TI map IP entity to AzureActivity** y asegúrese de que ha conectado todos los orígenes de datos necesarios como se muestra a continuación.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-required-data-sources.png" alt-text="Orígenes de datos necesarios":::

1. Seleccione la regla **TI map IP entity to AzureActivity** y luego seleccione **Crear regla** para abrir un asistente de configuración de reglas. Configure los valores en el asistente y, después, seleccione **Siguiente: Establecer la lógica de la regla >** .

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-create-analytics-rule.png" alt-text="Creación de una regla de análisis":::

1. La parte lógica de la regla del asistente se ha rellenado previamente con los siguientes elementos:
    - La consulta que se usará en la regla.

    - Asignaciones de entidades, que indican a Microsoft Sentinel cómo reconocer entidades como Cuentas, Direcciones IP y Direcciones URL, de modo que tanto **incidentes** como **investigaciones** entiendan cómo trabajar con los datos en cualquier alerta de seguridad generada por esta regla.

    - La programación para ejecutar esta regla.

    - El número de resultados de la consulta necesarios antes de que se genere una alerta de seguridad.

    La configuración predeterminada de la plantilla es:
    - Ejecute una vez por hora.

    - Haga que coincida cualquier indicador de amenazas de direcciones IP de la tabla **ThreatIntelligenceIndicator** con cualquier dirección IP encontrada en la última hora de eventos de la tabla **AzureActivity**.

    - Genere una alerta de seguridad si los resultados de la consulta son mayores que cero, es decir, si se encuentra alguna coincidencia.

    Puede dejar la configuración predeterminada o cambiarla para satisfacer sus requisitos, y puede definir la configuración de generación de incidentes en la pestaña **Configuración del incidente**. Para más información, consulte [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md). Cuando haya terminado, seleccione la pestaña **Respuesta automática**.

1. Configure cualquier automatización que desee desencadenar al generarse una alerta de seguridad a partir de esta regla de análisis. La automatización en Microsoft Sentinel se realiza mediante combinaciones de **reglas de automatización** y **cuadernos de estrategias**, con la tecnología de Azure Logic Apps. Para obtener más información, consulte el [Tutorial: Uso de cuadernos de estrategias con reglas de automatización en Microsoft Sentinel](./tutorial-respond-threats-playbook.md). Cuando haya terminado, seleccione el botón **Siguiente: Revisar >** para continuar.

1. Cuando vea el mensaje que indica que la validación de la regla se ha superado, seleccione el botón **Crear** y habrá terminado.

Encontrará la regla habilitada en la pestaña **Reglas activas** de la sección **Análisis** de Microsoft Sentinel. Puede editar, habilitar, deshabilitar, duplicar o eliminar la regla activa desde allí. La nueva regla se ejecuta inmediatamente tras la activación y, desde ese momento, se ejecutará en su programación definida.

Según la configuración predeterminada, cada vez que se ejecute la regla en su programación, cualquier resultado encontrado generará una alerta de seguridad. Las alertas de seguridad de Microsoft Sentinel se pueden ver en la sección **Registros** de Microsoft Sentinel, en la tabla **SecurityAlert** del grupo **Microsoft Sentinel**.

En Microsoft Sentinel, las alertas generadas a partir de las reglas de análisis también generan incidentes de seguridad que se encuentran en **Incidentes** en **Administración de amenazas** en el menú de Microsoft Sentinel. Los incidentes son aquello que los equipos de operaciones de seguridad investigarán y cuyas prioridades evaluarán para determinar las acciones de respuesta adecuadas. Puede encontrar información detallada en [Tutorial: Investigación de incidentes con Microsoft Sentinel](./investigate-cases.md).

## <a name="detect-threats-using-matching-analytics-public-preview"></a>Detección de amenazas mediante análisis de coincidencia (versión preliminar pública)

> [!IMPORTANT]
> El análisis de coincidencias está actualmente en VERSIÓN PRELIMINAR. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>

[Cree una regla](detect-threats-built-in.md#use-built-in-analytics-rules) mediante la plantilla de regla de **Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft** integrada para que Microsoft Sentinel coincida con los datos de inteligencia sobre amenazas generados por Microsoft con los registros que ha ingerido para Microsoft Sentinel.

La coincidencia de los datos de inteligencia sobre amenazas con los registros ayuda a generar alertas e incidentes de alta fidelidad, con las gravedades adecuadas aplicadas. Cuando se encuentra una coincidencia, las alertas generadas se agrupan en incidentes.

Las alertas se agrupan según la observación durante un período de tiempo de 24 horas. Por ejemplo, todas las alertas generadas en un período de tiempo de 24 horas que coinciden con el dominio `abc.com` se agrupan en un único incidente.

### <a name="triage-through-an-incident-generated-by-matching-analytics"></a>Análisis de la evaluación de prioridades a través de un incidente generado por análisis de coincidencias

Si se encuentra una coincidencia, las alertas generadas se agrupan en incidentes.

Use los pasos siguientes para realizar una evaluación de prioridades de los incidentes generados por la regla de **Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft**:

1. En el área de trabajo de Microsoft Sentinel en el que ha activado la regla de **Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft**, seleccione **Incidentes** y busque **Análisis de Inteligencia sobre amenazas de Microsoft**.

    Los incidentes encontrados se muestran en la cuadrícula.

1. Seleccione **Ver detalles completos** para ver las entidades y otros detalles sobre el incidente, como las alertas específicas.

    Por ejemplo:

    :::image type="content" source="media/work-with-threat-indicators/matching-analytics.png" alt-text="Detalles de análisis coincidentes de ejemplo.":::

Cuando se encuentra una coincidencia, el indicador también se publica en Log Analytics **ThreatIntelligenceIndicators**, y se muestra en la página **Inteligencia sobre amenazas**. Para los indicadores publicados a partir de esta regla, el origen se define como **Análisis de Inteligencia sobre amenazas de Microsoft**.

Por ejemplo, en el registro **ThreatIntelligenceIndicators**:

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-logs.png" alt-text="Análisis de coincidencia que se muestra en el registro ThreatIntelligenceIndicators.":::

En la página **Inteligencia sobre amenazas**:

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-threat-intelligence.png" alt-text="Análisis de coincidencia que se muestra en la página Inteligencia sobre amenazas":::.

### <a name="supported-log-sources-for-matching-analytics"></a>Orígenes de registro admitidos para el análisis de coincidencias

La regla de **Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft** coincide actualmente con los indicadores de dominio con los siguientes orígenes de registro:

| Origen del registro | Descripción |
| --------- | --------- |
| [CEF](connect-common-event-format.md) | La coincidencia se realiza para todos los registros CEF que se ingieren en la tabla **CommonSecurityLog** de Log Analytics, excepto aquellos en los que el `DeviceVendor` aparece como `Cisco`. <br><br>Para hacer coincidir la inteligencia sobre amenazas generada por Microsoft con los registros CEF, asegúrese de asignar el dominio en el campo `RequestURL` del registro CEF. |
| [DNS](./data-connectors-reference.md#windows-dns-server-preview) | La coincidencia se realiza para todos los registros DNS que son consultas de DNS de búsqueda de clientes a servicios DNS (`SubType == "LookupQuery"`). Las consultas de DNS se procesan solo para consultas IPv4 (`QueryType=”A”`) e IPv6 (`QueryType=” AAAA”`).<br><br>Para hacer coincidir la inteligencia sobre amenazas generada por Microsoft con los registros DNS, no se necesita ninguna asignación manual de columnas Windows, ya que todas las columnas son estándar de un servidor DNS y los dominios estarán en la columna `Name` de forma predeterminada. |
| [Syslog](connect-syslog.md) | Actualmente, la coincidencia solo se realiza para los eventos de Syslog donde `Facility` es `cron`. <br><br>Para hacer coincidir la inteligencia sobre amenazas generada por Microsoft con Syslog, no se necesita ninguna asignación manual de columnas. Los detalles se incluyen en el campo `SyslogMessage` de Syslog de forma predeterminada y la regla analizará el dominio directamente desde SyslogMessage. |
|  |  |


## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Los libros proporcionan información sobre la inteligencia sobre amenazas

Puede usar un libro de Microsoft Sentinel especialmente diseñado para visualizar información clave sobre la inteligencia sobre amenazas en Microsoft Sentinel y puede personalizar fácilmente los libros según sus necesidades empresariales.

Aquí puede saber cómo buscar el libro de inteligencia sobre amenazas proporcionado en Microsoft Sentinel y un ejemplo de cómo realizar ediciones en el libro para personalizarlo.

1. En [Azure Portal](https://portal.azure.com/), vaya al servicio **Microsoft Sentinel**.

1. Elija el **área de trabajo** al que ha importado indicadores de amenazas mediante el conector de datos de inteligencia sobre amenazas.

1. Seleccione **Workbooks** en la sección **Administración de amenazas** del menú de Microsoft Sentinel.

1. Localice el libro titulado **Inteligencia sobre amenazas** y compruebe que tiene datos en la tabla **ThreatIntelligenceIndicator** como se muestra a continuación.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-verify-data.png" alt-text="Comprobación de datos":::

1. Seleccione el botón **Guardar** y elija una ubicación de Azure para almacenar el libro. Este paso es necesario si va a modificar el libro de cualquier manera y guardar los cambios.

1. Ahora, seleccione el botón **Ver libro guardado** para abrir el libro a fin de verlo y editarlo.

1. Ahora debería ver los gráficos predeterminados proporcionados por la plantilla. Para modificar el gráfico, seleccione el botón **Editar** en la parte superior de la página para especificar el modo de edición del libro.

1. Agregue un nuevo gráfico de indicadores de amenazas por tipo de amenaza. Desplácese a la parte inferior de la página y seleccione **Agregar consulta**.

1. Agregue el siguiente texto al cuadro de texto **Consulta de registro del área de trabajo de Log Analytics**:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. En la lista desplegable **Visualización**, seleccione **Gráfico de barras**.

1. Seleccione el botón **Edición finalizada**. Ha creado un nuevo gráfico para el libro.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-bar-chart.png" alt-text="Gráfico de barras":::

Los libros proporcionan paneles interactivos eficaces que proporcionan información sobre todos los aspectos de Microsoft Sentinel. Hay muchas cosas que puede hacer con los libros y, aunque las plantillas proporcionadas son un buen punto de partida, es probable que quiera profundizar en estas plantillas y personalizarlas, o bien crear nuevos paneles que combinen muchos orígenes de datos diferentes para que pueda visualizar los datos de maneras únicas. Dado que los libros de Microsoft Sentinel se basan en los libros de Azure Monitor, ya hay una amplia documentación disponible y muchas más plantillas. Un buen punto de partida es este artículo sobre cómo [crear informes interactivos con los libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). 

También hay una completa comunidad de [libros de Azure Monitor en GitHub](https://github.com/microsoft/Application-Insights-Workbooks) donde puede descargar más plantillas y aportar las suyas.

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido todas las formas en que puede trabajar con indicadores de inteligencia sobre amenazas en Microsoft Sentinel. Para más información sobre la inteligencia sobre amenazas en Microsoft Sentinel, consulte los siguientes artículos:
- [Descripción de la inteligencia sobre amenazas en Microsoft Azure Sentinel](understand-threat-intelligence.md).
- Conexión de Microsoft Sentinel a las [fuentes de inteligencia sobre amenazas STIX/TAXII](./connect-threat-intelligence-taxii.md).
- [Conexión de plataformas de inteligencia sobre amenazas](./connect-threat-intelligence-tip.md) con Microsoft Sentinel.
- Consulte qué [plataformas TIP, fuentes TAXII y enriquecimientos](threat-intelligence-integration.md) se pueden integrar fácilmente con Microsoft Sentinel.
