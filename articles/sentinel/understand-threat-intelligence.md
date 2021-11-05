---
title: Información sobre la inteligencia sobre amenazas en Azure Sentinel | Microsoft Docs
description: Comprenda cómo las fuentes de inteligencia sobre amenazas se conectan, administran y usan en Azure Sentinel para analizar datos, detectar amenazas y enriquecer alertas.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9b8bafe6cbfb9f2351bcf3e8004fbed2a08e61b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055072"
---
# <a name="understand-threat-intelligence-in-azure-sentinel"></a>Descripción de la inteligencia sobre amenazas en Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="introduction-to-threat-intelligence"></a>Presentación de la inteligencia sobre amenazas

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

La inteligencia sobre ciberamenazas (CTI) es información que describe posibles amenazas o amenazas existentes conocidas a sistemas y usuarios. Este tipo de información tiene muchas formas, desde informes escritos que detallan las motivaciones, infraestructura y técnicas de un actor de amenaza determinado hasta observaciones específicas de direcciones IP, dominios y códigos hash de archivo asociados a ciberamenazas. Las organizaciones usan CTI para proporcionar contexto esencial a actividades inusuales, de modo que el personal de seguridad pueda tomar medidas rápidamente para proteger a sus usuarios y recursos. CTI puede proceder de muchos lugares, por ejemplo, fuentes de distribución de datos de código abierto, comunidades de uso compartido de inteligencia sobre amenazas e inteligencia local recopilada durante las investigaciones de seguridad realizadas dentro de una organización.

Dentro de una solución de administración de eventos e información de seguridad (SIEM) como Azure Sentinel, la forma de CTI más usada son los indicadores de amenazas, a los que se suele hacer referencia como indicadores de riesgo o IoC. Los indicadores de amenazas son datos que asocian artefactos observados como direcciones URL, códigos hash de archivo o direcciones IP con actividad de amenazas conocida como suplantación de identidad (phishing), redes de robots (botnet) o malware. Esta forma de inteligencia sobre amenazas suele llamarse *inteligencia sobre amenazas táctica*, ya que puede aplicarse a productos de seguridad y automatización a gran escala para proteger y detectar posibles amenazas a una organización. En Azure Sentinel, puede usar indicadores de amenazas para ayudar a detectar actividad malintencionada observada en el entorno y proporcionar contexto a investigadores de seguridad para ayudar a tomar decisiones de respuesta.

Integra la inteligencia sobre amenazas (TI) en Azure Sentinel a través de las actividades siguientes:

- **Importe inteligencia sobre amenazas** en Azure Sentinel habilitando **conectores de datos** en varias [plataformas](connect-threat-intelligence-tip.md) de TI y [fuentes](connect-threat-intelligence-taxii.md).

- **Vea y administre** la inteligencia sobre amenazas importada en **Registros** y en la nueva hoja **Inteligencia sobre amenazas** de Azure Sentinel.

- **Detecte las amenazas** y genere alertas e incidentes de seguridad utilizando las plantillas de reglas incorporadas de **Análisis** basadas en la inteligencia de amenazas importada.

- **Visualice información clave** sobre la inteligencia de amenazas importada en Azure Sentinel con el **libro de trabajo de inteligencia sobre amenazas**.

Microsoft enriquece todos los indicadores de inteligencia sobre amenazas importados con [datos de GeoLocation y WhoIs](#view-your-geolocation-and-whois-data-enrichments-public-preview), que se muestran junto con otros detalles del indicador.

> [!TIP]
> Inteligencia sobre amenazas también proporciona un contexto útil dentro de otras experiencias de Azure Sentinel, como la **búsqueda** y los **cuadernos**. Para obtener más información, vea [Uso de Jupyter Notebook en Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) y [Tutorial: Introducción a los cuadernos de Jupyter y MSTICPy en Azure Sentinel](notebook-get-started.md).
>

## <a name="import-threat-intelligence-with-data-connectors"></a>Importación de inteligencia sobre amenazas con conectores de datos

Al igual que todos los demás datos de eventos en Azure Sentinel, los indicadores de amenazas se importan mediante conectores de datos. Hay dos conectores de datos en Azure Sentinel proporcionados de forma específica para los indicadores de amenazas, **Inteligencia sobre amenazas: TAXII**, para las fuentes STIX/TAXII estándar sel sector, y **Plataformas de inteligencia sobre amenazas**, para las fuentes TI integradas y seleccionadas. Puede usar el conector de datos solo o ambos conectores juntos, dependiendo del origen de los indicadores de amenazas en la organización.

Consulte este catálogo de [integraciones de la inteligencia sobre amenazas](threat-intelligence-integration.md) disponibles con Azure Sentinel.

### <a name="add-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Incorporación de indicadores de amenazas a Azure Sentinel con el conector de datos Plataformas de inteligencia sobre amenazas

Muchas organizaciones usan soluciones de la plataforma de inteligencia sobre amenazas (TIP) para agregar fuentes de indicadores de amenazas desde diferentes orígenes, mantener los datos dentro de la plataforma y, a continuación, elegir qué indicadores de amenazas se aplicarán a diferentes soluciones de seguridad como dispositivos de red, soluciones de EDR/XDR o SIEM como Azure Sentinel. Si su organización usa una [solución TIP integrada](connect-threat-intelligence-tip.md), el **conector de datos de plataformas de inteligencia sobre amenazas** permite usar estas soluciones para importar indicadores de amenazas en Azure Sentinel.

Como el conector de datos TIP trabaja con la [API tiIndicators de Microsoft Graph Security](/graph/api/resources/tiindicator) para lograrlo, cualquier plataforma de inteligencia sobre amenazas personalizada puede usar el conector para aprovechar las ventajas de la API tiIndicators para enviar indicadores a Azure Sentinel (y a otras soluciones de seguridad de Microsoft como Microsoft 365 Defender).

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="Ruta de acceso de importación de inteligencia sobre amenazas":::

Para obtener más información sobre las soluciones TIP integradas con Azure Sentinel, consulte [Productos de la plataforma de inteligencia sobre amenazas integrada](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products).

**Para importar indicadores de amenazas a Azure Sentinel desde su TIP integrada o plataforma de inteligencia sobre amenazas personalizada**:

1. Obtenga un **identificador de la aplicación** y un **secreto de cliente** en Azure Active Directory

1. Escriba esta información en la solución TIP o en la aplicación personalizada

1. Habilite el conector de datos Plataformas de inteligencia sobre amenazas en Azure Sentinel

Para más información, consulte [Conexión de la plataforma de inteligencia sobre amenazas a Azure Sentinel](connect-threat-intelligence-tip.md).

### <a name="add-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Incorporación de indicadores de amenazas a Azure Sentinel con el conector de datos Inteligencia sobre amenazas: TAXII

El estándar del sector más ampliamente adoptado para la transmisión de la inteligencia sobre amenazas es una [combinación del formato de datos STIX y el protocolo TAXII](https://oasis-open.github.io/cti-documentation/). Si la organización obtiene indicadores de amenazas de soluciones que admiten la versión de STIX/TAXII actual (2.0 o 2.1), puede usar el conector de datos **Inteligencia sobre amenazas: TAXII** para traer los indicadores de amenazas a Azure Sentinel. El conector de datos Inteligencia sobre amenazas: TAXII habilita un cliente de TAXII integrado en Azure Sentinel para importar la inteligencia sobre amenazas desde servidores TAXII 2.x.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Ruta de acceso de importación de TAXII":::

**Para importar indicadores de amenazas con formato STIX a Azure Sentinel desde un servidor TAXII**:

1. Obtenga el identificador de colección y raíz de API del servidor TAXII

1. Habilite el conector de datos Inteligencia sobre amenazas: TAXII en Azure Sentinel

Para más información, consulte [Conexión de Azure Sentinel a las fuentes de inteligencia sobre amenazas STIX/TAXII](connect-threat-intelligence-taxii.md).

## <a name="view-and-manage-your-threat-indicators"></a>Visualización y administración de los indicadores de amenazas

Puede ver los indicadores de amenazas importados correctamente, independientemente de la fuente de origen o el conector usado, en la tabla **ThreatIntelligenceIndicator** (bajo el grupo **Azure Sentinel)** en **Registros**, que es donde se almacenan todos los datos de eventos de Azure Sentinel. Esta tabla es la base de las consultas de inteligencia sobre amenazas realizadas por otras características de Azure Sentinel como Análisis y Libros.

Los resultados deben tener un aspecto similar al indicador de amenazas de ejemplo que se muestra a continuación:

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="Datos de consulta de ejemplo":::

También puede ver y administrar los indicadores en la nueva hoja **Inteligencia sobre amenazas**, accesible desde el menú principal de Azure Sentinel. Puede ordenar, filtrar y buscar los indicadores de amenazas importados sin siquiera escribir una consulta de Log Analytics. Esta característica también permite crear indicadores de amenazas directamente dentro de la interfaz de Azure Sentinel, así como realizar dos de las tareas administrativas de inteligencia sobre amenazas más comunes: el etiquetado de indicadores y la creación de nuevos indicadores relacionados con investigaciones de seguridad.

El etiquetado de indicadores de amenazas es una forma sencilla de agruparlos para que sean más fáciles de encontrar. Normalmente, podría aplicar una etiqueta a los indicadores relacionados con un incidente concreto, o bien a aquellos que representan amenazas de un actor conocido determinado o una campaña de ataques conocida. Puede etiquetar indicadores de amenazas de forma individual o realizar una selección múltiple de los indicadores y etiquetarlos todos a la vez. A continuación se muestra un ejemplo de etiquetado de varios indicadores con un identificador de incidente. Dado que el etiquetado es de forma libre, una práctica recomendada es crear convenciones de nomenclatura estándar para las etiquetas del indicador de amenazas. Puede aplicar varias etiquetas a cada indicador.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Aplicación de etiquetas a indicadores de amenazas" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

Para obtener más información sobre cómo ver y administrar los indicadores de amenazas, consulte [Trabajo con los indicadores de amenazas en Azure Sentinel](work-with-threat-indicators.md#view-your-threat-indicators-in-azure-sentinel).

### <a name="view-your-geolocation-and-whois-data-enrichments-public-preview"></a>Visualización de los enriquecimientos de datos de GeoLocation y WhoIs (versión preliminar pública)

Microsoft enriquece cada indicador con datos adicionales de GeoLocation y WhoIs, que proporcionan más contexto para las investigaciones en las que se encuentra el indicador de compromiso (IOC) seleccionado.

Puede ver los datos de geolocalización y WHOIS en el panel **Inteligencia sobre amenazas** para cada indicador de riesgo que haya importado en Azure Sentinel.

Por ejemplo, use datos de geolocalización para encontrar información como *Organización* o *País* para el indicador, y datos WHOIS para encontrar información como *Registrador* y *Creación de registros*.

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Detección de amenazas con análisis basados en indicadores de amenazas

El caso de uso más importante para los indicadores de amenazas en soluciones SIEM como Azure Sentinel es la activación de reglas de análisis de detección de amenazas. Estas reglas basadas en indicadores comparan eventos sin formato de los orígenes de datos con sus indicadores de amenazas para detectar amenazas de seguridad en la organización. En **Análisis** de Azure Sentinel, crea reglas de análisis que se ejecutan de forma programada y genera alertas de seguridad. Las reglas están controladas mediante consultas, junto con configuraciones que determinan con qué frecuencia debe ejecutarse la regla, qué clase de resultados de consulta deben generar alertas de seguridad e incidentes, y qué automatizaciones deben desencadenarse como respuesta.

Aunque siempre puede crear nuevas reglas de análisis desde cero, Azure Sentinel proporciona un conjunto de plantillas de reglas integradas, creadas por ingenieros de seguridad de Microsoft, que puede usar tal cual o modificar para satisfacer sus necesidades. Puede identificar fácilmente las plantillas de reglas que usan indicadores de amenazas, ya que todas tienen un título que empieza por "**TI map**…". Todas estas plantillas de reglas funcionan de forma similar, siendo la única diferencia el tipo de indicadores de amenazas que se usan (dominio, correo electrónico, hash de archivo, dirección IP o dirección URL) y el tipo de evento que se va a comparar. En cada plantilla se muestran los orígenes de datos requeridos necesarios para el funcionamiento de la regla, de modo que puede ver a simple vista si tiene ya importados los eventos necesarios en Azure Sentinel. Al editar y guardar una plantilla de regla existente o crear una nueva regla, se habilita de forma predeterminada.

Encontrará la regla habilitada en la pestaña **Reglas activas** de la sección **Análisis** de Azure Sentinel. Puede editar, habilitar, deshabilitar, duplicar o eliminar la regla activa desde allí. La nueva regla se ejecuta inmediatamente tras la activación y, desde ese momento, se ejecutará en su programación definida.

Según la configuración predeterminada, cada vez que se ejecute la regla en su programación, cualquier resultado encontrado generará una alerta de seguridad. Las alertas de seguridad de Azure Sentinel se pueden ver en la sección **Registros** de Azure Sentinel, en la tabla **SecurityAlert** del grupo **Azure Sentinel**.

En Azure Sentinel, las alertas generadas a partir de las reglas de análisis también generan incidentes de seguridad que se encuentran en **Incidentes** en **Administración de amenazas** en el menú de Azure Sentinel. Los incidentes son aquello que los equipos de operaciones de seguridad investigarán y cuyas prioridades evaluarán para determinar las acciones de respuesta adecuadas. Encontrará información detallada en este [Tutorial: Investigación de incidentes con Azure Sentinel](./investigate-cases.md).

Para obtener más información sobre usar los indicadores de amenazas, consulte [Trabajo con los indicadores de amenazas en Azure Sentinel](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Los libros proporcionan información sobre la inteligencia sobre amenazas

Los libros proporcionan paneles interactivos eficaces que proporcionan información sobre todos los aspectos de Azure Sentinel, y la inteligencia sobre amenazas no es una excepción. Puede usar el **libro de inteligencia sobre amenazas** integrado para visualizar información clave sobre la inteligencia sobre amenazas, y puede personalizar fácilmente los libros según sus necesidades empresariales. Incluso puede crear nuevos paneles que combinen muchos orígenes de datos diferentes para que pueda visualizar los datos de maneras únicas. Dado que los libros de Azure Sentinel se basan en los libros de Azure Monitor, ya hay una amplia documentación disponible y muchas más plantillas. Un buen punto de partida es este artículo sobre cómo [crear informes interactivos con los libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

También hay una completa comunidad de [libros de Azure Monitor en GitHub](https://github.com/microsoft/Application-Insights-Workbooks) donde puede descargar plantillas adicionales y aportar sus propias plantillas.

Para obtener más información sobre el uso y la personalización del libro de inteligencia sobre amenazas, consulte [Trabajo con los indicadores de amenazas en Azure Sentinel](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence).

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido acerca de las funcionalidades de inteligencia sobre amenazas de Azure Sentinel, incluida la hoja Inteligencia sobre amenazas. Para obtener instrucciones prácticas sobre el uso de las funcionalidades de inteligencia sobre amenazas de Azure Sentinel, consulte los siguientes artículos:

- Conexión de Azure Sentinel a las [fuentes de inteligencia sobre amenazas STIX/TAXII](./connect-threat-intelligence-taxii.md).
- [Conexión de plataformas de inteligencia sobre amenazas](./connect-threat-intelligence-tip.md) con Azure Sentinel.
- Consulte qué [plataformas TIP, fuentes TAXII y enriquecimientos](threat-intelligence-integration.md) se pueden integrar fácilmente con Azure Sentinel.
- [Trabaje con indicadores de amenazas](work-with-threat-indicators.md) al usar Azure Sentinel.
- Detecte amenazas con reglas de análisis [integradas](./detect-threats-built-in.md) o [personalizadas](./detect-threats-custom.md) en Azure Sentinel.
- [Investigue incidentes](./investigate-cases.md) en Azure Sentinel.
