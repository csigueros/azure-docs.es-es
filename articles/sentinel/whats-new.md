---
title: Novedades de Azure Sentinel
description: En este artículo se describen las nuevas características de Azure Sentinel de los últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: 7c4a2958f8629b224cecf1e92fd0efcff6b1fdd6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182182"
---
# <a name="whats-new-in-azure-sentinel"></a>Novedades de Azure Sentinel

En este artículo se enumeran las características que se han agregado recientemente a Azure Sentinel y las nuevas características de servicios relacionados que proporcionan una experiencia de usuario mejorada en Azure Sentinel.

Si busca elementos de más de 6 meses, puede encontrarlos en las [Archivo de novedades de Azure Sentinel](whats-new-archive.md). Para más información sobre las características anteriores, consulte los [blogs de Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

> [!IMPORTANT]
> Actualmente, las características indicadas están en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Nuestros equipos de búsqueda de amenazas en Microsoft aportan consultas, cuadernos de estrategias, libros y cuadernos a la [comunidad de Azure Sentinel](https://github.com/Azure/Azure-Sentinel), lo que incluye [consultas de búsqueda](https://github.com/Azure/Azure-Sentinel) concretas que sus equipos pueden adaptar y usar.
>
> Usted también puede contribuir. Únase a nosotros en la [comunidad de GitHub de cazadores de amenazas de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="august-2021"></a>Agosto de 2021

- [Búsqueda de incidentes mejorada (versión preliminar pública)](#advanced-incident-search-public-preview)
- [Detección de Fusion para ransomware (versión preliminar pública)](#fusion-detection-for-ransomware-public-preview)
- [Plantillas de lista de control para datos UEBA](#watchlist-templates-for-ueba-data-public-preview)
- [Esquema de normalización de eventos de archivo (versión preliminar pública)](#file-event-normalization-schema-public-preview)
- [Novedades de la documentación: Guía de procedimientos recomendados](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>Búsqueda de incidentes mejorada (versión preliminar pública)

De forma predeterminada, las búsquedas de incidentes se ejecutan solo en los valores de **Id. de incidente**, **Título**, **Etiquetas**, **Propietario** y **Nombre de producto**. Azure Sentinel ahora proporciona [opciones de búsqueda avanzada](investigate-cases.md#search-for-incidents) para buscar en más datos, incluidos los detalles de alertas, las descripciones, las entidades, las tácticas y mucho más.

Por ejemplo:

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="Captura de pantalla de las opciones de búsqueda avanzada de la página Incidentes.":::

Para obtener más información, consulte [Búsqueda de incidentes](investigate-cases.md#search-for-incidents).

### <a name="fusion-detection-for-ransomware-public-preview"></a>Detección de Fusion para ransomware (versión preliminar pública)

Azure Sentinel ahora proporciona nuevas detecciones de Fusion para posibles actividades de ransomware y genera incidentes con el título **Varias alertas posiblemente relacionadas con la actividad de ransomware detectada**.

Los incidentes se generan para las alertas que posiblemente están asociadas a actividades de ransomware, cuando se producen durante un período de tiempo específico, y se asocian a las fases de ejecución y evasión defensiva de un ataque. Puede usar las alertas enumeradas en el incidente para analizar las técnicas que posiblemente hayan utilizado los atacantes para poner en peligro un host o dispositivo y para evitar la detección.

Entre los conectores de datos admitidos, se incluyen los siguientes:

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender para punto de conexión](connect-microsoft-defender-advanced-threat-protection.md)
- [Microsoft Defender for Identity](connect-azure-atp.md)
- [Microsoft Cloud App Security](connect-cloud-app-security.md)
- [Reglas de análisis programadas de Azure Sentinel](detect-threats-built-in.md#scheduled)

Para obtener más información, vea [Varias alertas posiblemente relacionadas con la actividad de ransomware detectada](fusion.md#multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview).

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>Plantillas de lista de control para datos UEBA (versión preliminar pública)

Azure Sentinel ahora proporciona plantillas de lista de control integradas para los datos UEBA, que puede personalizar para su entorno y usar durante las investigaciones.

Una vez que las plantillas de lista de control UEBA se rellenen con los datos, podrá correlacionar esos datos con reglas de análisis, verlos en las páginas de la entidad y en los gráficos de investigación como conclusiones, crear usos personalizados, como el seguimiento de usuarios VIP o confidenciales, etc.

Actualmente, entre las plantillas de la lista de control, se incluyen las siguientes:

- **Usuarios VIP**. Lista de cuentas de usuario de empleados que tienen un valor de alto impacto en la organización.
- **Empleados con el contrato finalizado**. Lista de cuentas de usuario de empleados cuyo contrato con la empresa ha finalizado o está a punto de finalizar.
- **Cuentas de servicio**. Lista de cuentas de servicio y sus propietarios.
- **Correlación de identidad**. Lista de cuentas de usuario relacionadas que pertenecen a la misma persona.
- **Recursos de alto valor**. Lista de dispositivos, recursos u otros recursos que tienen un valor crítico en la organización.
- **Asignación de red**. Lista de subredes IP y sus respectivos contextos organizativos.

Para obtener más información, consulte [Creación de una lista de control mediante una plantilla](watchlists.md#create-a-new-watchlist-using-a-template-public-preview) y [Esquemas de listas de control integrados](watchlist-schemas.md).



### <a name="file-event-normalization-schema-public-preview"></a>Esquema de normalización de eventos de archivo (versión preliminar pública)

El modelo de información de Azure Sentinel (ASIM) admite ahora un esquema de normalización de eventos de archivo, que se usa para describir la actividad de archivo, como la creación, la modificación o la eliminación de archivos o documentos. Los sistemas operativos, los sistemas de almacenamiento de archivos, como Azure Files, y los sistemas de administración de documentos, como Microsoft SharePoint, notifican los eventos de archivo.

Para más información, consulte:

- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Normalización y el modelo de información de Azure Sentinel (ASIM)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>Novedades de la documentación: Guía de procedimientos recomendados

En respuesta a varias solicitudes de clientes y nuestros equipos de soporte técnico, hemos agregado una serie de instrucciones de procedimientos recomendados a nuestra documentación.

Para más información, consulte:

- [Requisitos previos para la implementación de Azure Sentinel](prerequisites.md)
- [Procedimientos recomendados para Azure Sentinel](best-practices.md)
- [Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel](best-practices-workspace-architecture.md)
- [Diseño de una arquitectura de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md)
- [Diseños de ejemplo de áreas de trabajo de Azure Sentinel](sample-workspace-designs.md)
- [Procedimientos recomendados para la recopilación de datos](best-practices-data.md)

> [!TIP]
> Puede encontrar más instrucciones agregadas en nuestra documentación en los artículos conceptuales y de instrucciones pertinentes. Para obtener más información, consulte las [referencias de procedimientos adicionales](best-practices.md#additional-best-practice-references).
>

## <a name="july-2021"></a>Julio de 2021

- [Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft (versión preliminar pública)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Uso de los datos de Azure AD con la tabla IdentityInfo de Azure Sentinel (versión preliminar pública)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [Enriquecimiento de entidades con datos de geolocalización mediante API (versión preliminar pública)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [Compatibilidad con consultas entre recursos de ADX (versión preliminar pública)](#support-for-adx-cross-resource-queries-public-preview)
- [Las listas de control están disponibles de forma general](#watchlists-are-in-general-availability)
- [Compatibilidad con la residencia de datos en más ubicaciones geográficas](#support-for-data-residency-in-more-geos)
- [Sincronización bidireccional en el conector de Azure Defender (versión preliminar pública)](#bidirectional-sync-in-azure-defender-connector-public-preview)


### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft (versión preliminar pública)

Azure Sentinel proporciona ahora la regla integrada de **Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft**, que coincide con los datos de inteligencia sobre amenazas generados por Microsoft con los registros. Esta regla genera alertas e incidentes de alta fidelidad con gravedades adecuadas en función del contexto de los registros detectados. Cuando se detecta una coincidencia, el indicador también se publica en el repositorio de inteligencia sobre amenazas de Azure Sentinel.

La regla de **Análisis de coincidencias de Inteligencia sobre amenazas de Microsoft** coincide actualmente con los indicadores de dominio con los siguientes orígenes de registro:

- [CEF](connect-common-event-format.md)
- [DNS](connect-dns.md)
- [Syslog](connect-syslog.md)

Para obtener más información, consulte [Detección de amenazas mediante análisis de coincidencias (versión preliminar pública)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview).

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Uso de los datos de Azure AD con la tabla IdentityInfo de Azure Sentinel (versión preliminar pública)

Como los atacantes suelen usar las propias cuentas de usuario y servicio de la organización, los datos sobre esas cuentas de usuario, incluidos la identificación y los privilegios de usuario, son fundamentales para los analistas en el proceso de una investigación.

Ahora, tener la opción [UEBA habilitada](enable-entity-behavior-analytics.md) en su espacio de trabajo de Azure Sentinel también sincroniza los datos de Azure AD en la nueva tabla **IdentityInfo** en Log Analytics. Las sincronizaciones entre Azure AD y la tabla **IdentifyInfo** crean una instantánea de los datos del perfil de usuario que incluye metadatos de usuario, información de grupo y los roles de Azure AD asignados a cada usuario.

Use la tabla **IdentityInfo** durante las investigaciones y al ajustar las reglas de análisis de su organización para reducir los falsos positivos.

Para obtener más información, vea la [tabla IdentityInfo](ueba-enrichments.md#identityinfo-table-public-preview) en la referencia de enriquecimientos de UEBA y [use los datos de UEBA para analizar los falsos positivos](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives).

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>Enriquecimiento de entidades con datos de geolocalización mediante API (versión preliminar pública)

Azure Sentinel ahora ofrece una API para enriquecer los datos con información de geolocalización. Después, los datos de geolocalización se pueden usar para analizar e investigar incidentes de seguridad.

Para obtener más información, vea [Enriquecimiento de entidades en Azure Sentinel con datos de geolocalización mediante la API REST (versión preliminar pública)](geolocation-data-api.md) y [Clasificación y análisis de datos mediante entidades en Azure Sentinel](entities-in-azure-sentinel.md).


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>Compatibilidad con consultas entre recursos de ADX (versión preliminar pública)

La experiencia de búsqueda en Azure Sentinel ahora admite consultas [entre recursos de ADX](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).
 
Aunque Log Analytics sigue siendo la ubicación de almacenamiento de datos principal para realizar análisis con Azure Sentinel, hay casos en los que se requiere ADX para almacenar datos debido al costo, los períodos de retención u otros factores. Esta funcionalidad permite a los clientes buscar en un conjunto más amplio de datos y ver los resultados en las [ experiencias de búsqueda de Azure Sentinel ](hunting.md), incluidas las consultas de búsqueda, la [transmisión en vivo](livestream.md) y la página de búsqueda de Log Analytics.

Para consultar los datos almacenados en clústeres de ADX, use la función adx() para especificar el clúster de ADX, el nombre de la base de datos y la tabla que prefiera. Después, podrá consultar el resultado como lo haría con cualquier otra tabla. Puede obtener más información en las páginas vinculadas anteriormente.




### <a name="watchlists-are-in-general-availability"></a>Las listas de control están disponibles de forma general

La característica de [listas de control](watchlists.md) ya está disponible de forma general. Use listas de control para enriquecer las alertas con datos empresariales, crear listas de permitidos o listas de bloqueados con las que comprobar los eventos de acceso y ayudar a investigar las amenazas y reducir el agotamiento que provocan las alertas.

### <a name="support-for-data-residency-in-more-geos"></a>Compatibilidad con la residencia de datos en más ubicaciones geográficas

Azure Sentinel ahora admite la residencia de datos completa en las siguientes ubicaciones geográficas adicionales:

Brasil, Noruega, Sudáfrica, Corea del Sur, Alemania, Emiratos Árabes Unidos (EAU) y Suiza.

Consulte la [lista completa de ubicaciones geográficas admitidas](quickstart-onboard.md#geographical-availability-and-data-residency) para la residencia de datos.

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Sincronización bidireccional en el conector de Azure Defender (versión preliminar pública)

El conector de Azure Defender ahora admite la sincronización bidireccional del estado de las alertas entre Defender y Azure Sentinel. Al cerrar un incidente de Sentinel que contiene una alerta de Defender, esta se cerrará automáticamente también en el portal de Defender.

Consulte esta [descripción completa del conector de Azure Defender actualizado](connect-azure-security-center.md).

## <a name="june-2021"></a>Junio de 2021

- [Actualizaciones de la normalización y el modelo de información de Azure Sentinel](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [Conectores de servicio a servicio actualizados](#updated-service-to-service-connectors)
- [Exportar e importar reglas de análisis (versión preliminar pública)](#export-and-import-analytics-rules-public-preview)
- [Enriquecimiento de alertas: detalles de alerta (versión preliminar pública)](#alert-enrichment-alert-details-public-preview)
- [Más ayuda para los cuadernos de estrategias](#more-help-for-playbooks)
- [Nueva organización de la documentación](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>Actualizaciones de la normalización y el modelo de información de Azure Sentinel

El modelo de información de Azure Sentinel permite usar y crear contenido independiente del origen, lo que simplifica el análisis de los datos en el área de trabajo de Azure Sentinel.

En la actualización de este mes, hemos mejorado nuestra documentación de normalización y hemos proporcionado nuevos niveles de detalle y esquemas de normalización de autenticación, eventos de proceso y DNS completos.

Para más información, consulte:

- [Normalización y modelo de información de Azure Sentinel (ASIM)](normalization.md) (actualizado)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md) (novedad)
- [Referencia del esquema de normalización de datos de Azure Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel (versión preliminar pública)](dns-normalization-schema.md) (novedad)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel (versión preliminar pública)](process-events-normalization-schema.md) (novedad)
- [Referencia del esquema de normalización de eventos de registro de Azure Sentinel (versión preliminar pública)](registry-event-normalization-schema.md) (novedad)


### <a name="updated-service-to-service-connectors"></a>Conectores de servicio a servicio actualizados

Dos de nuestros conectores más utilizados se han beneficiado de importantes actualizaciones.

- El [conector de eventos de seguridad de Windows (versión preliminar pública)](connect-windows-security-events.md) se basa ahora en el nuevo agente de Azure Monitor (AMA), lo que le permite tener mucha más flexibilidad a la hora de elegir qué datos ingerir y le proporciona la máxima visibilidad al mínimo costo.

- El [conector de registros de actividad de Azure](connect-azure-activity.md) se basa ahora en la canalización de configuración de diagnóstico, lo que proporciona datos más completos, un retraso en la ingesta considerablemente reducido y un rendimiento y una confiabilidad mayores.

Las actualizaciones no son automáticas. Se recomienda a los usuarios de estos conectores que habiliten las nuevas versiones.

### <a name="export-and-import-analytics-rules-public-preview"></a>Exportación e importación de reglas de análisis (versión preliminar pública)

Ahora puede exportar reglas de análisis a archivos de plantilla de Azure Resource Manager (ARM) con formato JSON e importar reglas de estos archivos, como parte de la administración y el control de las implementaciones de Azure Sentinel como código. Cualquier tipo de [regla de análisis](detect-threats-built-in.md), no solo el tipo **Programada**, se puede exportar a una plantilla de ARM. El archivo de plantilla incluye toda la información de la regla, desde su consulta a sus tácticas MITRE ATT&CK asignadas.

Para obtener más información, vea [Exportación e importación de reglas de análisis hacia y desde plantillas de ARM](import-export-analytics-rules.md).

### <a name="alert-enrichment-alert-details-public-preview"></a>Enriquecimiento de alertas: detalles de alerta (versión preliminar pública)

Además de enriquecer el contenido de la alerta con la asignación de entidades y los detalles personalizados, ahora puede personalizar la forma en que se presentan y se muestran las alertas (y, por extensión, los incidentes), en función de su contenido concreto. Al igual que las otras características de enriquecimiento de alertas, esto se puede configurar en el [asistente para reglas de análisis](detect-threats-custom.md).

Para obtener más información, vea [Personalización de los detalles de las alertas de Azure Sentinel](customize-alert-details.md).


### <a name="more-help-for-playbooks"></a>Más ayuda para los cuadernos de estrategias

Dos nuevos documentos pueden ayudarle a comenzar o sentir mayor comodidad con la creación y el uso de libros de estrategias.
- El artículo [Autenticación de cuadernos de estrategias en Azure Sentinel](authenticate-playbooks-to-sentinel.md) le ayuda a comprender los distintos métodos de autenticación mediante los cuales los cuadernos de estrategias basados en Logic Apps pueden conectarse a la información de Azure Sentinel y acceder a ella, así como a comprender cuándo es adecuado usar cada uno de estos métodos.
- En el artículo [Uso de desencadenadores y acciones en los cuadernos de estrategias](playbook-triggers-actions.md), se explica la diferencia entre el **desencadenador de incidentes** y el **desencadenador de alertas** y cuándo usar cada uno. También se le muestran algunas de las diferentes acciones que puede realizar en los cuadernos de estrategias en respuesta a los incidentes, incluido el procedimiento para acceder a la información en [detalles personalizados](playbook-triggers-actions.md#work-with-custom-details).

En la documentación sobre cuadernos de estrategias, también se aborda explícitamente el escenario de MSSP multiinquilino.

### <a name="new-documentation-reorganization"></a>Nueva organización de la documentación

Este mes hemos reorganizado nuestra [documentación de Azure Sentinel](index.yml) y la hemos reestructurado en categorías intuitivas que siguen los recorridos comunes de los clientes. Use la búsqueda de documentos filtrados y la página de aterrizaje actualizada para navegar por los documentos de Azure Sentinel.

:::image type="content" source="media/whats-new/new-docs.png" alt-text="Nueva organización de la documentación de Azure Sentinel" lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>Mayo de 2021

- [Módulo de PowerShell para Azure Sentinel](#azure-sentinel-powershell-module)
- [Mejoras en la agrupación de alertas](#alert-grouping-enhancements)
- [Soluciones de Azure Sentinel (versión preliminar pública)](#azure-sentinel-solutions-public-preview)
- [Supervisión continua de amenazas para la solución SAP (versión preliminar pública)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [Integraciones de inteligencia sobre amenazas (versión preliminar pública)](#threat-intelligence-integrations-public-preview)
- [Fusión a través de alertas programadas (versión preliminar pública)](#fusion-over-scheduled-alerts-public-preview)
- [Anomalías de SOC-ML (versión preliminar pública)](#soc-ml-anomalies-public-preview)
- [Página de entidad de IP (versión preliminar pública)](#ip-entity-page-public-preview)
- [Personalización de actividades (versión preliminar pública)](#activity-customization-public-preview)
- [Panel de búsqueda (versión preliminar pública)](#hunting-dashboard-public-preview)
- [Equipos de incidentes: Colaboración en Microsoft Teams (versión preliminar pública)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [Libro Confianza cero (TIC3.0)](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Módulo de PowerShell para Azure Sentinel

El módulo oficial de PowerShell para Azure Sentinel para automatizar las tareas operativas diarias se ha publicado con disponibilidad general.

Puede descargarlo aquí: [Galería de PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Para más información, consulte la documentación de PowerShell: [Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="alert-grouping-enhancements"></a>Mejoras en la agrupación de alertas

Ahora puede configurar la regla de análisis para agrupar las alertas en un único incidente, no solo cuando coinciden con un tipo de entidad específico, sino también cuando coinciden con un nombre de alerta, gravedad u otros detalles personalizados específicos de una entidad configurada. 

En la pestaña **Configuración de incidentes** del asistente para reglas de análisis, active la agrupación de alertas y, a continuación, seleccione la opción **Agrupar alertas en un solo incidente si los tipos de entidad seleccionados y los detalles coinciden**. 

A continuación, seleccione el tipo de entidad y los detalles pertinentes que desea que coincidan:

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="Agrupación de alertas por los detalles de entidad que coinciden.":::

Para más información, consulte [Agrupación de alertas](detect-threats-custom.md#alert-grouping).

### <a name="azure-sentinel-solutions-public-preview"></a>Soluciones de Azure Sentinel (versión preliminar pública)

Azure Sentinel ofrece ahora [soluciones](sentinel-solutions-catalog.md) de **contenido empaquetado** que incluyen combinaciones de uno o varios conectores de datos, libros, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizadores, listas de reproducción y otros componentes para Azure Sentinel.

Las soluciones proporcionan una mejor detectabilidad en el producto, una implementación de un solo paso y escenarios de producto completos. Para más información, consulte [Detección e implementación de soluciones de Azure Sentinel](sentinel-solutions-deploy.md).

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Supervisión continua de amenazas para la solución SAP (versión preliminar pública)

Las soluciones de Azure Sentinel incluyen ahora la **supervisión continua de amenazas para SAP** lo que le permite supervisar los sistemas SAP en busca de amenazas sofisticadas en el nivel empresarial y el de la aplicación.

El conector de datos de SAP genera una gran cantidad de registros de 14 aplicaciones de todo el conjunto del sistema SAP y recopila registros de Advanced Business Application Programming (ABAP) mediante llamadas a NetWeaver RFC y datos de almacenamiento de archivos mediante la interfaz de control de OSSAP. El conector de datos de SAP se agrega a la capacidad de Azure Sentinel para supervisar la infraestructura subyacente de SAP.

Para ingerir registros de SAP en Azure Sentinel, debe tener instalado el conector de datos de Azure Sentinel para SAP en el entorno de SAP. Una vez implementado el conector de datos de SAP, implemente el variado contenido de seguridad de la solución de SAP para obtener información fácilmente sobre el entorno de SAP de su organización y mejorar la funcionalidad de todas las operaciones de seguridad relacionadas.

Para más información, consulte [Tutorial: Implementación de la solución Azure Sentinel para SAP (versión preliminar pública)](sap-deploy-solution.md)

### <a name="threat-intelligence-integrations-public-preview"></a>Integraciones de inteligencia sobre amenazas (versión preliminar pública)

Azure Sentinel proporciona varias maneras diferentes de [usar fuentes de inteligencia sobre amenazas](./understand-threat-intelligence.md) para mejorar la capacidad de los analistas de seguridad de detectar y priorizar las amenazas conocidas.

Ahora puede usar uno de los muchos productos de la plataforma de inteligencia sobre amenazas integrada (TIP) que se acaban de publicar, conectarse a servidores TAXII para aprovechar cualquier origen de inteligencia sobre amenazas compatible con STIX y usar cualquier solución personalizada que pueda comunicarse directamente con la [API Security tiIndicators de Microsoft Graph](/graph/api/resources/tiindicator).

También puede conectarse a orígenes de inteligencia sobre amenazas a partir de cuadernos de estrategias, con el fin de enriquecer los incidentes con información de TI que puede ayudar a dirigir acciones de investigación y respuesta.

Para más información, consulte [Integración de inteligencia sobre amenazas en Azure Sentinel](threat-intelligence-integration.md).

### <a name="fusion-over-scheduled-alerts-public-preview"></a>Fusión a través de alertas programadas (versión preliminar pública)

El motor de correlación de aprendizaje automático de **Fusion** ahora puede detectar ataques de varias fases mediante las alertas que genera un conjunto de [reglas de análisis programadas](detect-threats-custom.md) en sus correlaciones, además de las alertas importadas desde otros orígenes de datos.

Para más información, consulte [Detección avanzada de ataques de varias fases en Azure Sentinel](fusion.md).

### <a name="soc-ml-anomalies-public-preview"></a>Anomalías de SOC-ML (versión preliminar pública)

Las anomalías basadas en aprendizaje automático de SOC-ML de Azure Sentinel pueden identificar comportamientos inusuales que, de lo contrario, podrían pasar desapercibidos.

SOC-ML usa plantillas de reglas de análisis que pueden funcionar de forma inmediata. Aunque las anomalías no indican necesariamente un comportamiento malintencionado o incluso sospechoso por sí mismas, se pueden usar para mejorar la fidelidad de las detecciones, las investigaciones y la búsqueda de amenazas.

Para más información, consulte [Uso de anomalías de SOC-ML para detectar amenazas en Azure Sentinel](soc-ml-anomalies.md).

### <a name="ip-entity-page-public-preview"></a>Página de entidad de IP (versión preliminar pública)

Azure Sentinel es compatible ahora con la entidad de dirección IP y ya puede ver la información sobre esta en la nueva página de la entidad.

Al igual que las páginas de las entidades de usuario y host, la página de la entidad de dirección IP incluye información general sobre la dirección IP, una lista de las actividades de las que se ha encontrado que la dirección IP forma parte, etc., lo que le proporciona un almacén de información cada vez más completo para mejorar la investigación sobre los incidentes de seguridad.

Para más información, consulte [Páginas de entidad](identify-threats-with-entity-behavior-analytics.md#entity-pages).

### <a name="activity-customization-public-preview"></a>Personalización de actividades (versión preliminar pública)

Hablando de páginas de entidad, ahora puede crear nuevas actividades personalizadas para las entidades, de las que se realizará un seguimiento y se mostrarán en sus respectivas páginas de entidad junto con las actividades listas para usar que ha visto allí hasta ahora.

Para más información, consulte [Personalización de actividades en escalas de tiempo de páginas de entidad](customize-entity-activities.md).

### <a name="hunting-dashboard-public-preview"></a>Panel de búsqueda (versión preliminar pública)

La hoja **Búsqueda** se ha actualizado. El nuevo panel le permite ejecutar todas las consultas, o un subconjunto seleccionado, en un solo clic.

Identifique dónde desea empezar la búsqueda. Para ello, debe examinar el recuento de resultados, los picos o el cambio en el recuento de resultados en un período de 24 horas. También puede ordenar y filtrar por favoritos, origen de datos, táctica y técnica MITRE ATT&CK, resultados o diferencias de resultados. Vea las consultas que aún no tienen los orígenes de datos necesarios conectados y obtenga recomendaciones necesarias para habilitar estas consultas.

Para más información, consulte [Búsqueda de amenazas con Azure Sentinel](hunting.md).

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Equipo de incidentes de Azure Sentinel: Colaboración en Microsoft Teams (versión preliminar pública)

Azure Sentinel admite ahora una integración directa con Microsoft Teams, lo que le permite colaborar sin problemas en toda la organización y con las partes interesadas externas.

Directamente desde el incidente en Azure Sentinel, cree un nuevo *equipo de incidente* para usarlo para la comunicación central y la coordinación.

Los equipos de incidentes son especialmente útiles cuando se usan como un puente de conferencia dedicado para incidentes continuos de alta gravedad. Las organizaciones que ya usan Microsoft Teams para la comunicación y la colaboración pueden usar la integración de Azure Sentinel para aportar datos de seguridad directamente a sus conversaciones y al trabajo diario.

En Microsoft Teams, la pestaña de la **página Incidente** del nuevo equipo siempre tiene los datos más actualizados y recientes de Azure Sentinel, lo que garantiza que los equipos tengan a mano los datos más importantes.

[ ![Página Incidente en Microsoft Teams.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

Para más información, consulte [Colaboración en Microsoft Teams (versión preliminar pública)](collaborate-in-microsoft-teams.md).

### <a name="zero-trust-tic30-workbook"></a>Libro Confianza cero (TIC3.0)

El nuevo libro Confianza cero (TIC3.0) de Azure Sentinel proporciona una visualización automatizada de los principios de [Confianza cero](/security/zero-trust/), que se cruzan con el marco de [conexiones a Internet de confianza](https://www.cisa.gov/trusted-internet-connections) (TIC).

Sabemos que el cumplimiento no es solo un requisito anual, y las organizaciones deben supervisar las configuraciones a lo largo del tiempo como punto fuerte. El libre Confianza cero de Azure Sentinel usa toda la gama de ofertas de seguridad de Microsoft en Azure, Office 365, Teams, Intune, Windows Virtual Desktop y muchas más.

[ ![Libro Confianza cero](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**Libro Confianza cero**:

- Permite a los implementadores, analistas de SecOps, evaluadores, responsables de la toma de decisiones de seguridad y cumplimiento, MSSP y otros usuarios conocer la situación de la posición de seguridad de las cargas de trabajo en la nube.
- Ofrece más de 75 tarjetas de control, alineadas con las funcionalidades de seguridad de TIC 3.0, con botones de GUI seleccionables para la navegación.
- Está diseñado para dotar al personal de automatización, inteligencia artificial, aprendizaje automático, generación de consultas y alertas, visualizaciones, recomendaciones adaptadas y referencias a la documentación correspondiente.

Para obtener más información, consulte [Visualización y supervisión de los datos](monitor-your-data.md).

## <a name="april-2021"></a>Abril de 2021

- [Conectores de datos basados en Azure Policy](#azure-policy-based-data-connectors)
- [Escala de tiempo del incidente (versión preliminar pública)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Conectores de datos basados en Azure Policy

Azure Policy permite aplicar un conjunto común de configuraciones de registros de diagnóstico a todos los recursos (actuales y futuros) de un tipo determinado cuyos registros quiera ingerir en Azure Sentinel.

Para continuar con nuestros esfuerzos para llevar la eficacia de [Azure Policy](../governance/policy/overview.md) a la tarea de configuración de recopilación de datos, ahora ofrecemos otro recopilador de datos mejorado por Azure Policy para los recursos de la [cuenta de Azure Storage](connect-azure-storage-account.md), publicado en versión preliminar pública.

Además, dos de nuestros conectores en versión preliminar, para [Azure Key Vault](connect-azure-key-vault.md) y [Azure Kubernetes Service](connect-azure-kubernetes-service.md), ahora se han publicado para disponibilidad general (GA) y se han unido al conector de [Azure SQL Database](connect-azure-sql-logs.md).

### <a name="incident-timeline-public-preview"></a>Escala de tiempo del incidente (versión preliminar pública)

La primera pestaña de la página de detalles del incidente es ahora **Escala de tiempo**, que muestra una escala de tiempo de las alertas y los marcadores del incidente. La escala de tiempo de un incidente puede ayudarle a entenderlo mejor y a reconstruir la escala temporal de la actividad del atacante con las alertas y los marcadores relacionados.

- Seleccione un elemento de la escala de tiempo para ver los detalles correspondientes, sin salir del contexto del incidente.
- Filtre el contenido de la escala de tiempo para mostrar solo las alertas o los marcadores, o bien los elementos con una táctica de MITRE o gravedad específica.
- Puede seleccionar el vínculo **Id. de alerta del sistema** para ver el registro completo o el vínculo **Eventos** para ver los eventos relacionados en el área **Registros**.

Por ejemplo:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Pestaña de escala de tiempo del incidente":::

Para más información, consulte el [Tutorial: Investigación de incidentes con Azure Sentinel](investigate-cases.md).

## <a name="march-2021"></a>Marzo de 2021

- [Configurar los libros para que se actualicen automáticamente en modo de vista](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nuevas detecciones para Azure Firewall](#new-detections-for-azure-firewall)
- [Reglas de automatización y cuadernos de estrategias desencadenados por incidentes (versión preliminar pública)](#automation-rules-and-incident-triggered-playbooks-public-preview), que incluye documentación de los nuevos cuadernos de estrategias.
- [Nuevos enriquecimientos de alertas: asignación de entidades mejorada y detalles personalizados (versión preliminar pública)](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [Imprimir los libros de Azure Sentinel o guardarlos como PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Los filtros de incidentes y las preferencias de ordenación ahora se guardan en la sesión (versión preliminar pública)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integración de incidentes de Microsoft 365 Defender (versión preliminar pública)](#microsoft-365-defender-incident-integration-public-preview)
- [Nuevos conectores de servicio de Microsoft con Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Configurar los libros para que se actualicen automáticamente en modo de vista

Los usuarios de Azure Sentinel ahora pueden usar la nueva [funcionalidad de Azure Monitor](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) para actualizar automáticamente los datos del libro durante una sesión de visualización.

En cada libro o plantilla de libro, seleccione :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Actualización automática** para mostrar las opciones de intervalo. Seleccione la opción que quiera usar para la sesión de visualización actual y, luego, seleccione **Aplicar**.

- Los intervalos de actualización admitidos oscilan entre los **cinco minutos** y **un día**.
- De manera predeterminada, la actualización automática está desactivada. Para optimizar el rendimiento, la actualización automática también se desactiva cada vez que se cierra un libro, y no se ejecuta en segundo plano. Vuelva a activar la actualización automática según sea necesario la próxima vez que abra el libro.
- La actualización automática se pausa mientras edita un libro y los intervalos de actualización automática se reinician cada vez que pasa al modo de vista desde el modo de edición.

    También se reinician los intervalos si actualiza manualmente el libro al seleccionar el botón :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Actualizar**.

Para obtener más información, vea [Visualización y supervisión de los datos](monitor-your-data.md) y la [documentación de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nuevas detecciones para Azure Firewall

Se han agregado varias detecciones integradas para Azure Firewall al área de [Análisis](./understand-threat-intelligence.md) en Azure Sentinel. Estas nuevas detecciones permiten a los equipos de seguridad recibir alertas si las máquinas de la red interna intentan consultar o conectarse a nombres de dominio de Internet o direcciones IP asociadas a indicadores de riesgo conocidos, tal como se define en la consulta de la regla de detección.

Las nuevas detecciones incluyen:

- [Señal de red Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Códigos hash y dominios GALLIUM conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Known IRIDIUM IP (Dirección IP de IRIDIUM conocida)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [IP/dominios de grupo de Phosphorus conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Dominios de THALLIUM incluidos en el ataque de DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Código de hash de maldoc relacionado con ZINC conocido](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Dominios de grupo de STRONTIUM conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM, indicadores de riesgo de dominio e IP: marzo de 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Las detecciones de Azure Firewall se agregan continuamente a la galería de plantillas integrada. Para recibir las detecciones más recientes de Azure Firewall, en **Plantillas de reglas**, filtre **Orígenes de datos** por **Azure Firewall**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nuevas detecciones en el libro de eficiencia de Análisis":::

Para obtener más información, consulte [Nuevas detecciones para Azure Firewall en Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>Reglas de automatización y cuadernos de estrategias desencadenados por incidentes (versión preliminar pública)

Las reglas de automatización son un nuevo concepto de Azure Sentinel que permite administrar de forma centralizada la automatización del control de incidentes. Además de permitirle asignar cuadernos de estrategias a incidentes (no solo a las alertas, como hasta ahora), las reglas de automatización también permiten automatizar las respuestas de varias reglas de análisis a la vez, etiquetar, asignar o cerrar incidentes automáticamente sin necesidad de cuadernos de estrategias y controlar el orden de las acciones que se ejecutan. Las reglas de automatización agilizarán el uso de la automatización en Azure Sentinel y le permitirán simplificar flujos de trabajo complejos de los procesos de orquestación de incidentes.

Consulte esta [explicación completa de las reglas de automatización](automate-incident-handling-with-automation-rules.md) para obtener más información.

Como hemos mencionado anteriormente, los cuadernos de estrategias ahora se pueden activar con el desencadenador de incidentes además del desencadenador de alertas. El desencadenador de incidentes proporciona a los cuadernos de estrategias un conjunto mayor de entradas con las que trabajar (ya que el incidente incluye también todos los datos de la entidad y la alerta), lo que ofrece más capacidad y flexibilidad en los flujos de trabajo de respuesta. Los cuadernos de estrategias desencadenados por incidentes se activan con una llamada desde las reglas de automatización.

Obtenga más información sobre las [funcionalidades mejoradas de los cuadernos de estrategias](automate-responses-with-playbooks.md) y descubra cómo [diseñar un flujo de trabajo de respuesta](tutorial-respond-threats-playbook.md) al combinar los cuadernos de estrategias y las reglas de automatización.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>Nuevos enriquecimientos de alertas: asignación de entidades mejorada y detalles personalizados (versión preliminar pública)

Puede enriquecer las alertas de dos formas nuevas para que sean más informativas y más fáciles de usar.

Comience por llevar la asignación de entidades al siguiente nivel. Ahora puede asignar casi 20 tipos de entidades, usuarios, hosts y direcciones IP a archivos y procesos, a buzones de correo, a recursos de Azure y a dispositivos IoT. También puede usar varios identificadores para cada entidad y fortalecer así su identificación única. Esto le ofrece un conjunto de datos mucho más completo en los incidentes, proporciona una correlación más amplia y una investigación más eficaz. [Conozca la nueva forma de asignar entidades](map-data-fields-to-entities.md) en las alertas.

[Obtenga más información sobre las entidades](entities-in-azure-sentinel.md) y consulte la [lista completa de entidades disponibles y sus identificadores](entities-reference.md).

Mejore aún más sus funcionalidades de investigación y respuesta mediante la personalización de las alertas para mostrar los detalles de los eventos sin procesar. Ahora puede visibilizar el contenido del evento en los incidentes y aumentar de este modo su capacidad y flexibilidad para investigar amenazas de seguridad y responder a ellas. [Obtenga información sobre cómo exponer los detalles personalizados](surface-custom-details-in-alerts.md) en las alertas.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Imprimir los libros de Azure Sentinel o guardarlos como PDF

Ahora puede imprimir los libros de Azure Sentinel, exportarlos a archivos PDF y guardarlos de forma local o compartida.

En el libro, seleccione el menú de opciones > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimir contenido**. A continuación, seleccione la impresora o elija **Guardar como PDF**, según necesite.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Imprima el libro o guárdelo como PDF.":::

Para obtener más información, consulte [Visualización y supervisión de los datos](monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Los filtros de incidentes y las preferencias de ordenación ahora se guardan en la sesión (versión preliminar pública)

Ahora los filtros y la ordenación de incidentes se guardan en la sesión de Azure Sentinel, incluso si navega a otras áreas del producto.
Si no cambia de sesión, cuando regrese al área [Incidentes](investigate-cases.md) de Azure Sentinel se mostrarán los filtros y la ordenación tal como los dejó.

> [!NOTE]
> Los filtros y la ordenación de incidentes no se guardan si sale de Azure Sentinel o si actualiza el explorador.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integración de incidentes de Microsoft 365 Defender (versión preliminar pública)

La integración de incidentes de [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) de Azure Sentinel le permite transmitir todos los incidentes de M365D a Azure Sentinel y mantenerlos sincronizados entre ambos portales. Los incidentes de M365D (anteriormente conocidos como Protección contra amenazas de Microsoft o MTP) incluyen todas las alertas, entidades e información pertinente asociadas, lo que le proporciona un contexto suficiente para realizar la evaluación de errores y una investigación preliminar en Azure Sentinel. Una vez en Sentinel, los incidentes permanecerán sincronizados de manera bidireccional con M365D, lo que le permite aprovechar las ventajas de ambos portales en su investigación de los incidentes.

El uso conjunto de Azure Sentinel y Microsoft 365 Defender ofrece lo mejor de ambos mundos. Consigue la amplitud de detalles que proporciona un sistema SIEM en todo el ámbito de los recursos de información de la organización, así como la profundidad de la potencia investigadora personalizada y adaptada que ofrece un sistema XDR para proteger los recursos de Microsoft 365, y todo ello coordinado y sincronizado para lograr un funcionamiento sin problemas del centro de operaciones de seguridad.

Para más información, consulte [Integración de Microsoft 365 Defender en Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nuevos conectores de servicio de Microsoft con Azure Policy

[Azure Policy](../governance/policy/overview.md) es un servicio de Azure que permite usar directivas para aplicar y controlar las propiedades de un recurso. El uso de directivas garantiza que los recursos sigan siendo compatibles con los estándares de gobernanza de TI.

Entre las propiedades de los recursos que se pueden controlar con directivas están la creación y control de los registros de diagnóstico y auditoría. Ahora, Azure Sentinel usa Azure Policy para que pueda aplicar un conjunto común de valores de registros de diagnóstico a todos los recursos (actuales y futuros) de un tipo determinado cuyos registros quiera ingerir en Azure Sentinel. Gracias a Azure Policy, ya no tendrá que establecer la configuración de registros de diagnóstico para cada recurso.

Los conectores basados en Azure Policy ahora están disponibles para los siguientes servicios de Azure:
- [Azure Key Vault](connect-azure-key-vault.md) (versión preliminar pública)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (versión preliminar pública)
- [Servidores e instancias de Azure SQL Database](connect-azure-sql-logs.md) (disponibilidad general)

Los clientes podrán seguir enviando manualmente los registros de instancias específicas y no tendrán que usar el motor de directivas.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Incorporación de Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtención de visibilidad sobre las alertas](get-visibility.md)