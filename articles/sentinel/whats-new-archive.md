---
title: Archivo de novedades en Azure Sentinel
description: Una descripción de las novedades y los cambios en Azure Sentinel en los últimos seis meses, e incluso antes.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 06/15/2021
ms.openlocfilehash: 979855c0ad6a19c03c0c2c23cde935bc03cba157
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179348"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Archivo de novedades en Azure Sentinel

La página principal de notas de la versión de [Novedades de Azure Sentinel](whats-new.md) contiene actualizaciones de los últimos seis meses, mientras que esta página contiene elementos más antiguos.

Para más información sobre las características anteriores, consulte los [blogs de Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Actualmente, las características indicadas están en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.


> [!TIP]
> Nuestros equipos de búsqueda de amenazas en Microsoft aportan consultas, cuadernos de estrategias, libros y cuadernos a la [comunidad de Azure Sentinel](https://github.com/Azure/Azure-Sentinel), lo que incluye [consultas de búsqueda](https://github.com/Azure/Azure-Sentinel) concretas que sus equipos pueden adaptar y usar.
>
> Usted también puede contribuir. Únase a nosotros en la [comunidad de GitHub de cazadores de amenazas de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="february-2021"></a>Febrero de 2021

- [Libro de certificación del modelo de madurez de ciberseguridad (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Conectores de datos de terceros](#third-party-data-connectors)
- [Información de UEBA en la página de entidades (versión preliminar pública)](#ueba-insights-in-the-entity-page-public-preview)
- [Búsqueda de incidentes mejorada (versión preliminar pública)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Libro de certificación del modelo de madurez de ciberseguridad (CMMC)

El libro de CMMC de Azure Sentinel proporciona un mecanismo para ver las consultas de registros alineadas con los controles de CMMC en la cartera de Microsoft, incluidas las ofertas de seguridad de Microsoft, Office 365, Teams, Intune, Windows Virtual Desktop y muchas más.

Gracias al libro de CMMC, los arquitectos de seguridad, ingenieros, analistas de operaciones de seguridad, administradores y profesionales de TI consiguen una visibilidad que les permite tomar conciencia de la situación de la posición de seguridad de las cargas de trabajo en la nube. También incluye recomendaciones para seleccionar, diseñar, implementar y configurar ofertas de Microsoft para adaptarse a los requisitos y las prácticas de CMMC correspondientes.

Incluso si no necesita cumplir con CMMC, el libro de CMMC resulta útil para crear centros de operaciones de seguridad, desarrollar alertas, visualizar amenazas y tomar conciencia de la situación de las cargas de trabajo.

Acceda al libro CMMC en el área **Libros** de Azure Sentinel. Seleccione **Plantilla** y, después, busque **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Grabación de GIF de la guía del libro C M M C activada y desactivada." lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Para más información, consulte:

- [Libro de certificación del modelo de madurez de ciberseguridad (CMMC) de Azure Sentinel](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Visualizar y supervisar los datos](monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Conectores de datos de terceros

Nuestra colección de integraciones con terceros sigue creciendo. En los dos últimos meses hemos agregado treinta conectores más. Esta es la lista:

- [Agari Phishing Defense y Agari Brand Protection](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Alsid para Active Directory](connect-alsid-active-directory.md)
- [Servidor HTTP de Apache](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (anteriormente, G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Servicio de Salesforce en la nube](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Información de UEBA en la página de entidades (versión preliminar pública)

Las páginas de detalles de la entidad de Azure Sentinel proporcionan un [panel de información](identify-threats-with-entity-behavior-analytics.md#entity-insights), que muestra datos sobre el comportamiento de la entidad y ayuda a identificar rápidamente las anomalías y las amenazas de seguridad.

Si tiene [UEBA habilitado](ueba-enrichments.md) y ha seleccionado un período de tiempo de al menos cuatro días, este panel de información ahora incluirá también las siguientes secciones nuevas para ofrecer la información sobre UEBA:

|Sección  |Descripción  |
|---------|---------|
|**UEBA Insights** (Información de UEBA)     | Resume las actividades de usuario anómalas: <br>- Entre ubicaciones geográficas, dispositivos y entornos<br>- Entre los horizontes de tiempo y frecuencia, en comparación con el propio historial del usuario <br>- En comparación con el comportamiento de sus pares <br>- En comparación con el comportamiento de la organización     |
|**User Peers Based on Security Group Membership** (Pares del usuario en función de la pertenencia al grupo de seguridad)     |   Enumera los pares del usuario en función de la pertenencia a grupos de seguridad de Azure AD, lo que proporciona a los equipos de operaciones de seguridad una lista de otros usuarios que comparten permisos similares.  |
|**User Access Permissions to Azure Subscription** (Permisos de acceso del usuario a las suscripciones de Azure)     |     Muestra los permisos de acceso del usuario a las suscripciones de Azure a las que puede acceder directamente o a través de grupos o entidades de servicio de Azure AD.   |
|**Threat Indicators Related to The User** (Indicadores de amenaza relacionados con el usuario)     |  Enumera una colección de amenazas conocidas relacionadas con las direcciones IP representadas en las actividades del usuario. Las amenazas se enumeran por tipo y familia de amenazas, y las enriquece el servicio de inteligencia sobre amenazas de Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Búsqueda de incidentes mejorada (versión preliminar pública)

Hemos mejorado la experiencia de búsqueda de incidentes de Azure Sentinel, lo que le permite navegar más rápido por los incidentes a medida que investiga una amenaza específica.

Ahora puede buscar incidentes en Azure Sentinel utilizando los siguientes detalles del incidente:

- Id.
- Título
- Producto
- Propietario
- Etiqueta

## <a name="january-2021"></a>Enero de 2021

- [Asistente para reglas de análisis: experiencia mejorada de edición de consultas (versión preliminar pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo de PowerShell Az.SecurityInsights (versión preliminar pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector de SQL Database](#sql-database-connector)
- [Conector de Dynamics 365 (versión preliminar pública)](#dynamics-365-connector-public-preview)
- [Mejora en los comentarios de incidentes](#improved-incident-comments)
- [Clústeres de Log Analytics dedicados](#dedicated-log-analytics-clusters)
- [Identidades administradas de Logic Apps](#logic-apps-managed-identities)
- [Mejora en el ajuste de reglas con los grafos en versión preliminar de las reglas de análisis](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Asistente para reglas de análisis: mejora en la edición de consultas (versión preliminar pública)

Ahora, el Asistente para reglas de análisis programado de Azure Sentinel ofrece las siguientes mejoras para escribir y editar consultas:

-   Una ventana de edición que se puede expandir y que proporciona más espacio en la pantalla para ver la consulta.
-   Resaltado de palabras clave en el código de la consulta.
-   Compatibilidad con Autocompletar expandida.
-   Validaciones de consultas en tiempo real. Los errores en la consulta ahora se muestran en forma de bloque rojo en la barra de desplazamiento y en forma de punto rojo en el nombre de la pestaña **Establecer la lógica de la regla**. Además, las consultas que tengan errores no se pueden guardar.

Para obtener más información, consulte [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo de PowerShell Az.SecurityInsights (versión preliminar pública)

Azure Sentinel ya admite el nuevo módulo de PowerShell [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

El módulo **Az.SecurityInsights** admite casos de uso comunes de Azure Sentinel, como la interacción con incidentes para cambiar los estados, la gravedad, el propietario, etc., la incorporación de comentarios y etiquetas a incidentes, y la creación de marcadores.

Aunque se recomienda usar las plantillas de [Azure Resource Manager](../azure-resource-manager/templates/index.yml) para la canalización de CI/CD, el módulo **Az.SecurityInsights** es útil para las tareas posteriores a la implementación y está destinado a la automatización de SOC.  Por ejemplo, la automatización de SOC puede incluir los pasos necesarios para configurar conectores de datos, crear reglas de análisis o agregar acciones de automatización a las reglas de análisis.

Para más información, incluida una lista completa de los cmdlets disponibles y una descripción de los mismos, las descripciones y ejemplos de parámetros, consulte la [documentación de Az.SecurityInsights de PowerShell](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector de SQL Database

Azure Sentinel ahora incluye el conector de Azure SQL Database, que permite transmitir en secuencias los registros de auditoría y diagnóstico de bases de datos a Azure Sentinel, así como supervisar continuamente la actividad en todas las instancias.

Azure SQL es un motor de base de datos de plataforma como servicio (PaaS) totalmente administrado que se encarga de la mayoría de las funciones de administración de bases de datos, como actualizar, aplicar revisiones, crear copias de seguridad y supervisar sin la intervención del usuario.

Para más información, consulte [Conexión de los registros de auditoría y diagnóstico de bases de datos de Azure SQL](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Conector de Dynamics 365 (versión preliminar pública)

Ahora Azure Sentinel proporciona un conector para Microsoft Dynamics 365, que le permite recopilar los registros de actividad de los usuarios, administradores y de soporte técnico de las aplicaciones Dynamics 365 en Azure Sentinel. Puede usar estos datos para ayudarle a auditar la totalidad de las acciones de procesamiento de datos que se realizan y analizarlas para localizar posibles infracciones de seguridad.

Para obtener más información, consulte [Conexión de los registros de actividad de Dynamics 365 a Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Mejora en los comentarios de incidentes

Los analistas usan estos comentarios para colaborar en los incidentes, así como documentar procesos y pasos manualmente o como parte de un cuaderno de estrategias. 

La mejora en nuestra experiencia en comentarios de incidentes permite no solo dar formato a los comentarios, sino también editar o eliminar los comentarios existentes.

Para más información, consulte [Creación automática de incidentes a partir de alertas de seguridad de Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clústeres de Log Analytics dedicados

Azure Sentinel ahora admite clústeres de Log Analytics dedicados como opción de implementación. Se recomienda considerar la posibilidad de usar un clúster dedicado si:

- **Ingiere más de 1 TB al día** en el área de trabajo de Azure Sentinel.
- **Tiene varias áreas de trabajo de Azure Sentinel** en una inscripción de Azure.

Los clústeres dedicados le permiten usar características como claves administradas por el cliente, caja de seguridad, cifrado doble y consultas entre áreas de trabajo rápidas cuando varias áreas de trabajo están en el mismo clúster.

Para más información, consulte [Clústeres dedicados de registros de Azure Monitor](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Identidades administradas de Logic Apps

Azure Sentinel ahora admite identidades administradas para el conector de Logic Apps de Azure Sentinel, lo que permite conceder permisos directamente a un cuaderno de estrategias específico para que funcione en Azure Sentinel, en lugar de crear identidades adicionales.

- **Sin una identidad administrada**, el conector de Logic Apps requiere una identidad independiente con un rol RBAC de Azure Sentinel para ejecutarse en Azure Sentinel. Esta identidad independiente puede ser un usuario de Azure AD o una entidad de servicio, como una aplicación registrada de Azure AD.

- **Al activar la compatibilidad con identidades administradas en una aplicación lógica**, la aplicación lógica se registra en Azure AD y se proporciona un identificador de objeto. Use el identificador de objeto en Azure Sentinel para asignar la aplicación lógica con un rol RBAC de Azure en el área de trabajo de Azure Sentinel. 

Para más información, consulte:

- [Autenticación con Identidad administrada en Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Documentación del conector de Logic Apps de Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Mejora en el ajuste de reglas con los grafos de versión preliminar de reglas de análisis (versión preliminar pública)

Azure Sentinel le permite ahora ajustar mejor las reglas de análisis, lo que le ayuda a aumentar su precisión y reducir el ruido.

Después de editar una regla de análisis en la pestaña **Establecer la lógica de la regla**, busque el área **Results simulation** (Simulación de resultados) de la derecha. 

Seleccione **Test with current data** (Probar con datos actuales) para que Azure Sentinel ejecute una simulación de las últimas 50 ejecuciones de una regla de análisis. Se genera un grafo para mostrar el número promedio de alertas que habría generado la regla, en función de los datos de eventos sin procesar que se han evaluado. 

Para más información, consulte [Definición de la lógica de consulta de regla y configuración de los valores](detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Diciembre de 2020

- [80 nuevas consultas de búsqueda integradas](#80-new-built-in-hunting-queries)
- [Mejoras en el agente de Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nuevas consultas de búsqueda integradas
 
Las consultas de búsqueda integradas de Azure Sentinel permiten a los analistas de SOC reducir las lagunas en la cobertura de detección actual y lograr nuevos clientes potenciales de búsqueda.

Esta actualización de Azure Sentinel incluye nuevas consultas de búsqueda que proporcionan cobertura en la matriz de la plataforma MITRE ATT&CK:

- **Colección**
- **Comando y control**
- **Acceso de credenciales**
- **Detección**
- **Ejecución**
- **Exfiltración**
- **Impacto**
- **Acceso inicial**
- **Persistencia**
- **Elevación de privilegios**

Las consultas de búsqueda agregadas están diseñadas para ayudarle a encontrar actividades sospechosas en su entorno. Aunque pueden devolver actividades legítimas y actividades potencialmente malintencionadas, pueden resultar útiles como guía para la búsqueda. 

Si, después de ejecutar estas consultas, confía en los resultados, puede convertirlos en reglas de análisis, o bien agregar resultados de búsqueda a los incidentes nuevos o existentes.

Todas las consultas agregadas están disponibles a través de la página de búsqueda de Azure Sentinel. Para más información, consulte [Búsqueda de amenazas con Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Mejoras en el agente de Log Analytics

Los usuarios de Azure Sentinel se benefician de las siguientes mejoras en el agente de Log Analytics:

- **Compatibilidad con más sistemas operativos**, entre los que se incluyen CentOS 8, RedHat 8 y SUSE Linux 15.
- **Compatibilidad con Python 3**, además de con Python 2

Azure Sentinel usa el agente de Log Analytics para enviar eventos al área de trabajo, lo que incluye eventos de seguridad de Windows, los eventos de Syslog, los registros de CEF, etc.

> [!NOTE]
> Al agente de Log Analytics, a veces se le denomina Agente de OMS o Microsoft Monitoring Agent (MMA). 
> 

Para más información, consulte la [documentación de Log Analytics](../azure-monitor/agents/log-analytics-agent.md) y las [notas de la versión del agente de Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Noviembre de 2020

- [Supervise el estado de los cuadernos de estrategias en Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Conector de Microsoft 365 Defender (versión preliminar pública)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Supervise el estado de los cuadernos de estrategias en Azure Sentinel

Los cuadernos de estrategias de Azure Sentinel se basan en flujos de trabajo integrados en [Azure Log Apps](../logic-apps/index.yml), un servicio en la nube que le ayuda a programar, automatizar y organizar tareas, procesos empresariales y flujos de trabajo. Los cuadernos de estrategias se pueden invocar automáticamente al crear un incidente o al evaluar y usar incidentes. 

Para proporcionar información sobre el estado, el rendimiento y el uso de los cuadernos de estrategias, se ha agregado un [libro](../azure-monitor/visualize/workbooks-overview.md) denominado **Supervisión del estado de los cuadernos de estrategias**. 

El libro **Playbooks health monitoring** (Supervisión de estado de cuadernos de estrategias) se usa para supervisar el estado de los cuadernos de estrategias o buscar anomalías en la cantidad de ejecuciones correctas o con errores. 

El libro **Playbooks health monitoring** (Supervisión de estado de cuadernos de estrategias) ya está disponible en la galería de plantillas de Azure Sentinel:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Libro Playbooks health monitoring (Supervisión de estado de cuadernos de estrategias) de ejemplo":::

Para más información, consulte:

- [Documentación de Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentación sobre Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Conector de Microsoft 365 Defender (versión preliminar pública)
 
El conector de Microsoft 365 Defender para Azure Sentinel permite transmitir en secuencias registros de búsqueda avanzada (un tipo de datos de eventos sin procesar) desde Microsoft 365 Defender hasta Azure Sentinel. 

Con la integración de [Microsoft Defender para punto de conexión (MDATP)](/windows/security/threat-protection/) en el paraguas de seguridad de [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection), ahora puede recopilar los eventos de búsqueda avanzada de Microsoft Defender para punto de conexión mediante el conector Microsoft 365 Defender y transmitirlos en secuencia directamente a nuevas tablas creadas específicamente en el área de trabajo de Azure Sentinel. 

Las tablas de Azure Sentinel se basan en el mismo esquema que se usa en el portal de Microsoft 365 Defender y proporcionan acceso pleno al conjunto completo de registros de búsqueda avanzada. 

Para más información, consulte [Conexión de datos de Microsoft 365 Defender con Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender se conocía antes como Microsoft Threat Protection o MTP. Microsoft Defender para Endpoint se conocía anteriormente como Protección contra amenazas avanzada de Microsoft Defender o MDATP.
> 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Incorporación de Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtención de visibilidad sobre las alertas](get-visibility.md)