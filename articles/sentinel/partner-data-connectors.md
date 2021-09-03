---
title: Conectores de datos de asociados para Azure Sentinel | Microsoft Docs
description: Obtenga información sobre todos los conectores de datos de asociados para Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 25b09b496ff505bb37452b80355cae705af8936d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723520"
---
# <a name="azure-sentinel-partner-data-connectors"></a>Conectores de datos de asociados de Azure Sentinel

En este artículo se enumeran los conectores de datos que Azure Sentinel admite para organizaciones de asociados que no son de Microsoft, en orden alfabético. Si sabe el conector que busca, desplácese hasta este o busque su nombre en esta página. También puede usar los vínculos de la derecha o la parte superior del artículo.

- Para habilitar y configurar los conectores enumerados, consulte [Conectar orígenes de datos](connect-data-sources.md) y los vínculos de las listas individuales.
- Para obtener información sobre los conectores de datos de servicio a servicio y creados por Microsoft, vea [Integración de servicio a servicio](connect-data-sources.md#service-to-service-integration).
- Para obtener información sobre los modelos de compatibilidad de Azure Sentinel y el campo **Compatible con**, vea [Compatibilidad con conectores de datos](connect-data-sources.md#data-connector-support).

> [!IMPORTANT]
> Muchos de los siguientes conectores de datos de Azure Sentinel están actualmente en **versión preliminar**. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense y Brand Protection (versión preliminar)

El conector de las soluciones Agari Phishing Defense y Brand Protection conecta los registros de dichas soluciones a Azure Sentinel.

Para obtener más información, consulte el [sitio para desarrolladores de Agari](https://developers.agari.com/agari-platform) y el artículo [Conectar Agari Phishing Defense y Brand Protection a Azure Sentinel](connect-agari-phishing-defense.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support)

## <a name="ai-analyst-aia-by-darktrace-preview"></a>AI Analyst (AIA) de Darktrace (versión preliminar)

El conector de Darktrace envía las infracciones del modelo y los incidentes de AIA a Azure Sentinel. Explore los datos de forma interactiva a través de visualizaciones en el libro AIA de Darktrace, que incluye gráficos de información general con limpieza de tiempo. Puede ver desgloses detallados sobre infracciones e incidentes específicos y, a continuación, ver los incidentes en la interfaz de usuario de Darktrace para una exploración más profunda.

AIA envía datos de infracción a la tabla CommonSecurityLog de Azure Sentinel en Azure Log Analytics. La tabla CommonSecurityLog admite la intercalación y agregación de consultas fácilmente en diferentes conectores de asociados.

**Método de ingesta de datos:** [formato de evento común (CEF)](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Darktrace](https://customerportal.darktrace.com/)

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect (versión preliminar)

El conector de datos de AI Vectra Detect lleva los datos de AI Vectra Detect a Azure Sentinel. Con el libro AI Vectra, puede empezar a investigar los ataques de red directamente desde Sentinel. Puede ver hosts, cuentas, campañas y detecciones críticos, y supervisar el estado del sistema y los registros de auditoría de Vectra.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de AI Vectra Detect a Azure Sentinel](connect-ai-vectra-detect.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Vectra](https://www.vectra.ai/support)

## <a name="akamai-security-events-preview"></a>Akamai Security Events (versión preliminar)

El conector de datos de Akamai Security Events ingiere [eventos de seguridad de Akamai](https://www.akamai.com/us/en/products/security/) en Azure Sentinel. Para obtener más información, consulte el tema sobre la [integración de Akamai SIEM para Splunk y CEF Syslog](https://developer.akamai.com/tools/integrations/siem).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Akamai Security Events a Azure Sentinel](connect-akamai-security-events.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [Akamai](https://www.akamai.com/us/en/support/)

## <a name="alcide-kaudit-preview"></a>Alcide kAudit (versión preliminar)

El conector Alcide kAudit exporta automáticamente los registros de auditoría del clúster de Kubernetes a Azure Sentinel en tiempo real. El conector kAudit proporciona visibilidad y observabilidad mejoradas en los registros de auditoría de Kubernetes. Alcide kAudit ofrece sólidas funcionalidades de seguridad y supervisión con fines de análisis forense.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Alcide kAudit a Azure Sentinel](connect-alcide-kaudit.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** [Alcide](https://www.alcide.io/company/contact-us/)

## <a name="alsid-for-active-directory-preview"></a>Alsid para Active Directory (versión preliminar)

El conector Alsid para Active Directory exporta los registros indicadores de exposiciones de Alsid, el flujo de registro e indicadores de ataques a Azure Sentinel en tiempo real. El conector también proporciona un analizador de datos para ayudar a manipular los registros. Libros proporciona visualizaciones de datos y supervisión de Active Directory. Las plantillas analíticas ayudan a automatizar las respuestas a eventos, exposiciones y ataques.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Alsid para Active Directory a Azure Sentinel](connect-alsid-active-directory.md).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [Alsid](https://www.alsid.com/contact-us/)

## <a name="amazon-web-services"></a>Amazon Web Services

El conector de datos de AWS importa eventos de administración de AWS CloudTrail en Azure Sentinel. Para habilitar este conector, consulte [Conexión de Azure Sentinel a AWS CloudTrail](connect-aws.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** Microsoft

## <a name="apache-http-server-preview"></a>Apache HTTP Server (versión preliminar)

El conector de datos de Apache HTTP Server ingiere eventos de Apache HTTP Server en Azure Sentinel. Para más información, consulte la [documentación de registros de Apache](https://httpd.apache.org/docs/2.4/logs.html).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="apache-tomcat-preview"></a>Apache Tomcat (versión preliminar)

El conector de datos de Apache Tomcat ingiere eventos de [Apache Tomcat](http://tomcat.apache.org/) en Azure Sentinel. Para más información, consulte la [documentación de Apache Tomcat](http://tomcat.apache.org/tomcat-10.0-doc/logging.html).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (versión preliminar)

El conector Aruba ClearPass conecta los registros de auditoría, sesión, sistema y conclusiones de Aruba ClearPass a Azure Sentinel. Para obtener más información sobre cómo configurar la solución Aruba ClearPass para reenviar syslog, vea [Adición de un filtro de exportación de Syslog](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Aruba ClearPass a Azure Sentinel](connect-aruba-clearpass.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit (versión preliminar)

El conector de datos de auditoría de [Atlassian Confluence](https://www.atlassian.com/software/confluence) ingiere registros de auditoría de Confluence. El conector puede obtener eventos para examinar posibles riesgos de seguridad, analizar la colaboración del equipo y diagnosticar problemas de configuración, entre otros. Para más información, consulte [Visualización del registro de auditoría](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit (versión preliminar)

El conector de datos de Atlassian Jira Audit ingiere eventos de registros de auditoría de Jira en Azure Sentinel. El conector puede obtener eventos para examinar posibles riesgos de seguridad, analizar la colaboración del equipo y diagnosticar problemas de configuración, entre otros. Para obtener más información, consulte [Registros de auditoría](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="barracuda-cloudgen-firewall-cgfw"></a>Barracuda CloudGen Firewall (CGFW)

El conector Barracuda CGFW conecta los registros de Barracuda CGFW a Azure Sentinel. Para configurar el streaming de syslog para Barracuda CGFW, consulte [Cómo configurar el streaming de Syslog](https://aka.ms/sentinel-barracudacloudfirewall-connector).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Barracuda CloudGen Firewall a Azure Sentinel](connect-barracuda-cloudgen-firewall.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [Barracuda](https://www.barracuda.com/support)

## <a name="barracuda-web-application-firewall-waf"></a>Firewall de aplicaciones web (WAF) de Barracuda

El conector WAF de Barracuda conecta los registros WAF de Barracuda a Azure Sentinel. Para más información, consulte [Configuración del Firewall de aplicaciones web de Barracuda](https://aka.ms/asi-barracuda-connector).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de WAF de Barracuda a Azure Sentinel](connect-barracuda.md).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs).

**Compatible con:** [Barracuda](https://www.barracuda.com/support)

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (versión preliminar)

El conector BETTER MTD permite a las empresas conectar sus instancias de Better MTD a Azure Sentinel. Este conector proporciona información sobre los dispositivos móviles de una organización y la posición de seguridad móvil actual, lo que mejora las funcionalidades generales de SecOps. Para más información, consulte la [documentación de instalación de Azure Sentinel](https://mtd-docs.bmobi.net/integrations/how-to-setup-azure-sentinel-integration#mtd-integration-configuration) de BETTER Mobile.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de BETTER Mobile Threat Defense a Azure Sentinel](connect-better-mtd.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** BETTER Mobile

## <a name="beyond-security-besecure-preview"></a>beSECURE de Beyond Security (versión preliminar)

El conector beSECURE de Beyond Security conecta los eventos de análisis, los resultados del examen y el registro de auditoría de beSECURE de Beyond Security a Azure Sentinel. Para obtener más información, consulte la [página de información general](https://beyondsecurity.com/solutions/besecure.html) de beSECURE.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de beSECURE de Beyond Security a Azure Sentinel](connect-besecure.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Beyond Security](https://beyondsecurity.freshdesk.com/support/home)

## <a name="blackberry-cylanceprotect-preview"></a>Blackberry CylancePROTECT (versión preliminar)

El conector Blackberry CylancePROTECT conecta los registros de auditoría, amenazas, control de aplicaciones, dispositivo, protección de memoria y clasificación de amenazas de CylancePROTECT a Azure Sentinel. Para configurar CylancePROTECT para reenviar syslog, consulte la [Guía de Syslog de Cylance](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP) (versión preliminar)

El conector Broadcom Symantec DLP conecta los desencadenadores de reglas de directiva/respuesta de Symantec DLP a Azure Sentinel. Con este conector, puede crear paneles y alertas personalizados para ayudar a la investigación. Para configurar Symantec DLP para reenviar syslog, consulte [Configuring the Log to a Syslog Server action](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) (Configuración del registro en una acción de servidor de Syslog).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Broadcom Symantec DLP a Azure Sentinel](connect-broadcom-symantec-dlp.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="check-point"></a>Punto de comprobación

El conector de firewalls de Check Point conecta los registros de Check Point a Azure Sentinel. Para configurar el producto Check Point para exportar registros, consulte [Exportador de registros: exportación de registros de Check Point](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Check Point a Azure Sentinel](connect-checkpoint.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Check Point](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.checkpoint.com%2Fsupport-services%2Fcontact-support%2F&data=04%7C01%7CNayef.Yassin%40microsoft.com%7C9965f53402ed44988a6c08d913fc51df%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637562796697084967%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Av1vTkeYoEXzRKO%2FotZLtMMexIQI%2FIKjJGnPQsbhqmE%3D&reserved=0)

## <a name="cisco-asa"></a>Cisco ASA

El conector de firewall de Cisco ASA conecta los registros de Cisco ASA a Azure Sentinel. Para configurar la conexión, siga las instrucciones de la [guía de configuración de la CLI de Cisco ASA Series](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Cisco ASA a Azure Sentinel](connect-cisco.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** Microsoft

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (versión preliminar)

El conector de Cisco Firepower eStreamer conecta los registros de eStreamer a Azure Sentinel. Cisco Firepower eStreamer es una API cliente-servidor diseñada para la solución Cisco Firepower NGFW. Para obtener más información, consulte la guía [eStreamer eNcore for Sentinel Operations Guide](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Cisco](https://www.cisco.com/c/en/us/support/index.html)

## <a name="cisco-meraki-preview"></a>Cisco Meraki (versión preliminar)

El conector de Cisco Meraki conecta los registros de eventos, las direcciones URL, los flujos, las alertas de IDS, los eventos de seguridad y los eventos de Airmarshal de Cisco Meraki (MX/MR/MS) a Azure Sentinel. Para configurar dispositivos Meraki para el reenvío de syslog, siga las instrucciones de [Meraki Device Reporting - Syslog, SNMP, and API](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) (Informes de dispositivo Meraki: Syslog, SNMP y API).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Cisco Meraki a Azure Sentinel](connect-cisco-meraki.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (versión preliminar)

El conector de Cisco UCS conecta los registros de auditoría, eventos y errores de Cisco UCS a Azure Sentinel. Para configurar Cisco UCS para el reenvío de syslog, siga las instrucciones de [Pasos para configurar Syslog en un servidor de Syslog remoto](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Cisco Unified Computing System (UCS) a Azure Sentinel](connect-cisco-ucs.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (versión preliminar)

El conector de datos de Cisco Umbrella ingiere eventos de Cisco Umbrella almacenados en Amazon S3, como DNS, proxy y registros IP, en Azure Sentinel. El conector de datos de Cisco Umbrella usa la API de REST de Amazon S3. Para configurar el registro y obtener credenciales, consulte [Registro en Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Cisco Umbrella a Azure Sentinel](connect-cisco-umbrella.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="citrix-analytics-security"></a>Citrix Analytics (Security)

El conector de datos de Citrix Analytics (Security) exporta datos de eventos de riesgo de Citrix Analytics (Security) a Azure Sentinel. Puede crear paneles personalizados, analizar datos de otros orígenes junto con los de Citrix Analytics (Security) y crear flujos de trabajo personalizados de Logic Apps para supervisar y mitigar los eventos de seguridad. Para más información, consulte [Integración con Microsoft Azure Sentinel](https://docs.citrix.com/en-us/security-analytics/getting-started-security/azure-sentinel-integration.html).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Citrix Analytics (Security) a Azure Sentinel](connect-citrix-analytics.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Citrix](https://www.citrix.com/support/)

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (versión preliminar)

El conector de datos de Citrix WAF conecta los registros de Citrix WAF a Azure Sentinel. Citrix WAF mitiga las amenazas contra los recursos de acceso público, incluidos sitios web, aplicaciones y API.
- Para configurar WAF, consulte [Support WIKI - WAF Configuration with NetScaler](https://support.citrix.com/article/CTX234174) (Compatibilidad de WIKI: configuración de WAF con NetScaler).
- Para configurar los registros de CEF, consulte [CEF Logging Support in the Application Firewall](https://support.citrix.com/article/CTX136146) (Compatibilidad con el registro de CEF en el firewall de aplicaciones).
- Para reenviar los registros al proxy, consulte el tema sobre la [configuración de dispositivos Citrix ADC para el registro de auditoría](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Citrix WAF a Azure Sentinel](connect-citrix-waf.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Citrix](https://www.citrix.com/support/)

## <a name="cognni-preview"></a>Cognni (versión preliminar)

El conector de datos de Cognni ofrece una integración rápida y sencilla a Azure Sentinel. Puede usar Cognni para asignar de forma autónoma información importante no clasificada previamente y detectar incidentes relacionados. Cognni le ayuda a reconocer riesgos para su información importante, comprender la gravedad de los incidentes e investigar los detalles que debe corregir, lo suficientemente rápido como para marcar la diferencia.

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Cognni](https://cognni.ai/contact-support/)

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>Eventos de CyberArk Enterprise Password Vault (EPV) (versión preliminar)

El conector de datos de CyberArk ingiere mensajes de Syslog XML de CyberArk EPV para las acciones realizadas en el almacén. EPV convierte los mensajes XML al formato estándar CEF a través del traductor *Sentinel.xsl* y los envía a un servidor de ensayo de Syslog de su elección (syslog-ng, rsyslog). El agente de Log Analytics en el servidor de ensayo de Syslog importa los mensajes en Log Analytics. Para obtener más información, vea el artículo sobre las [aplicaciones de administración de eventos e información de seguridad (SIEM)](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) en la documentación de CyberArk.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de CyberArk Enterprise Password Vault a Azure Sentinel](connect-cyberark.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [CyberArk](https://www.cyberark.com/customer-support/)

## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs (versión preliminar)

El conector de datos de Cyberpion Security Logs ingiere registros del sistema Cyberpion directamente en Azure Sentinel. Para más información, consulte [Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) en la documentación de Cyberpion.

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** Cyberpion

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (versión preliminar)

El conector de datos de ESET Enterprise Inspector ingiere las detecciones de ESET Enterprise Inspector mediante la API de REST proporcionada en la versión 1.4 y posteriores de ESET Enterprise Inspector. Para más información, consulte [API de REST](https://help.eset.com/eei/1.5/en-US/api.html) en la documentación de ESET Enterprise Inspector.

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** [ESET](https://support.eset.com/en)

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (versión preliminar)

El conector de datos de ESET SMC ingiere eventos de amenazas, registros de auditoría, eventos de firewall y filtros de sitios web de ESET SMC en Azure Sentinel. Para más información, consulte [Servidor de Syslog](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) en la documentación de ESET SMC.

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs).

**Compatible con:** [ESET](https://support.eset.com/en)

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (versión preliminar)

El conector de datos de Exabeam Advanced Analytics ingiere eventos de Exabeam Advanced Analytics, como los de estado del sistema, sesiones notables/anomalías, análisis avanzado y estado del trabajo en Azure Sentinel. Para enviar registros de Exabeam Advanced Analytics a través de Syslog, siga las instrucciones del apartado sobre la [configuración de notificaciones de actividad del sistema Advanced Analytics](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

El conector de datos ExtraHop Reveal(x) conecta el sistema Reveal(x) a Azure Sentinel. La integración con Azure Sentinel requiere ExtraHop Detection SIEM Connector. Para instalar SIEM Connector en el sistema Reveal(x), siga las instrucciones de [ExtraHop Detection SIEM Connector](https://aka.ms/asi-syslog-extrahop-forwarding).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de ExtraHop Reveal(x) a Azure Sentinel](connect-extrahop.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [ExtraHop](https://www.extrahop.com/support/)

## <a name="f5-big-ip"></a>F5 BIG-IP 

El conector F5 BIG-IP conecta los registros de F5 LTM, del sistema y de ASM a Azure Sentinel. Para obtener más información, consulte [Integración de F5 BIGIP con Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de F5 BIG-IP a Azure Sentinel](connect-f5-big-ip.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** Microsoft

## <a name="f5-networks"></a>Redes F5

El conector de firewall F5 conecta los eventos de seguridad de aplicaciones de F5 a Azure Sentinel. Para configurar el registro remoto, consulte el artículo sobre la [configuración del registro de eventos de seguridad de las aplicaciones](https://aka.ms/asi-syslog-f5-forwarding).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de F5 ASM a Azure Sentinel](connect-f5.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** Microsoft

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB) (versión preliminar)

El conector de datos Forcepoint CASB exporta automáticamente los registros y eventos de CASB a Azure Sentinel en tiempo real. Para más información, consulte [Forcepoint CASB y Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de productos de Forcepoint a Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (versión preliminar)

El conector de datos de Forcepoint CSG exporta automáticamente los registros de CSG a Azure Sentinel. CSG es un servicio de seguridad en la nube convergente que proporciona visibilidad, control y protección contra amenazas para los usuarios y los datos, estén donde estén. Para obtener más información, consulte [Forcepoint Cloud Security Gateway y Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de productos de Forcepoint a Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (versión preliminar)

El conector de datos de Forcepoint DLP exporta automáticamente los datos de incidentes de DLP de Forcepoint DLP a Azure Sentinel en tiempo real. Para obtener más información, vea [Forcepoint Data Loss Prevention y Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Forcepoint DLP a Azure Sentinel](connect-forcepoint-dlp.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (versión preliminar)

El conector de datos de Forcepoint NGFW exporta automáticamente los registros de Forcepoint NGFW definidos por el usuario a Azure Sentinel en tiempo real. Para obtener más información, consulte [Forcepoint Next-Gen Firewall y Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de productos de Forcepoint a Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) para CEF (versión preliminar)

ForgeRock Identity Platform proporciona un único marco de auditoría común para que pueda realizar un seguimiento holístico de los datos de registro. Puede extraer y agregar datos de registro en toda la plataforma con controladores de eventos CAUD e identificadores únicos. El conector CAUD para CEF es abierto y extensible, y puede usar sus funcionalidades de registro de auditoría e informes para la integración con Azure Sentinel. Para obtener más información, consulte [ForgeRock Common Audit (CAUD) para Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [ForgeRock](https://www.forgerock.com/support)

## <a name="fortinet"></a>Fortinet 

El conector del firewall de Fortinet conecta los registros de Fortinet a Azure Sentinel. Para obtener más información, diríjase a la [biblioteca de documentos de Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary), elija su versión y use los documentos PDF *Handbook* y *Log Message Reference*.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Fortinet a Azure Sentinel](connect-fortinet.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Fortinet](https://support.fortinet.com/)

## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (versión preliminar)

El conector de datos de Google Workspace ingiere eventos de actividad de Google Workspace en Azure Sentinel a través de la API de REST. La capacidad de ingerir eventos le ayuda a:
- Examinar riesgos de seguridad potenciales.
- Analizar la colaboración del equipo.
- Diagnosticar problemas de configuración.
- Realizar un seguimiento de quién inicia sesión y cuándo.
- Analizar la actividad del administrador.
- Comprender cómo crean y comparten contenido los usuarios.
- Revisar los eventos de la organización.

Para obtener las credenciales, siga las instrucciones de [Perform Google Workspace Domain-Wide Delegation of Authority](https://developers.google.com/admin-sdk/reports/v1/guides/delegation) (Delegación de autoridad de todo el dominio de Google Workspace).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Google Workspace (anteriormente G Suite) a Azure Sentinel](connect-google-workspace.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS) (versión preliminar)

El conector de Illusive AMS comparte datos de análisis de la superficie de ataques y registros de incidentes de Illusive con Azure Sentinel. Puede ver esta información en los paneles dedicados. El libro de ASM ofrece información sobre el riesgo de la superficie de ataque de la organización y el libro de ADS realiza un seguimiento del movimiento lateral no autorizado en la red de la organización. Para más información, consulte la [guía de administración de Illusive Networks](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Illusive Networks AMS a Azure Sentinel](connect-illusive-attack-management-system.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Illusive Networks](https://www.illusivenetworks.com/technical-support/)

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (versión preliminar)

El conector de Imperva conecta las alertas de Imperva WAF Gateway a Azure Sentinel. Para más información, consulte los [pasos para habilitar el registro de alertas de Imperva WAF Gateway a Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Imperva WAF Gateway a Azure Sentinel](connect-imperva-waf-gateway.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Imperva](https://www.imperva.com/support/technical-support/)

## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (versión preliminar)

El conector de Infoblox NIOS conecta los registros de Infoblox NIOS a Azure Sentinel. Para habilitar el reenvío de syslog de registros de Infoblox NIOS, consulte la [guía de implementación de SNMP y Syslog de NIOS](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Infoblox NIOS a Azure Sentinel](connect-infoblox.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="juniper-srx-preview"></a>Juniper SRX (versión preliminar)

El conector de Juniper SRX conecta los registros de Juniper SRX a Azure Sentinel. Para reenviar los registros de tráfico, consulte el artículo sobre la [configuración del registro de tráfico (registros de directivas de seguridad) para dispositivos de rama SRX](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA). Para reenviar registros del sistema, consulte [Configuración del registro del sistema](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Juniper SRX a Azure Sentinel](connect-juniper-srx.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [Juniper Networks](https://support.juniper.net/support/)

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (versión preliminar)

El conector de datos de Morphisec para Azure Sentinel integra información fundamental de los productos de seguridad. Puede expandir las funcionalidades analíticas con búsqueda y correlación, inteligencia sobre amenazas y alertas personalizadas. El conector de datos de Morphisec proporciona visibilidad de amenazas avanzadas, tales como ataques sin archivos sofisticados, vulnerabilidades en memoria y ataques de día cero. Con una vista única entre productos, puede tomar decisiones basadas en datos en tiempo real para proteger sus recursos más importantes.

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Morphisec

## <a name="netskope-preview"></a>Netskope (versión preliminar)

El conector de la plataforma de seguridad en la nube de Netskope ingiere registros y eventos de Netskope en Azure Sentinel. Para más información, consulte [La plataforma de seguridad en la nube de Netskope](https://www.netskope.com/platform).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (versión preliminar)

El conector de datos del NGINX HTTP Server ingiere eventos de NGINX HTTP Server en Azure Sentinel. Para obtener más información, consulte [Módulo ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) en macOS (Preview)

El conector de datos NXLog BSM en macOS usa Sun BSM Auditing API para capturar eventos de auditoría directamente desde el kernel en la plataforma macOS. Este conector de datos puede exportar eficazmente eventos de auditoría de macOS en Azure Sentinel en tiempo real. Consulte la [Guía de usuario de NXLog de Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para más información.

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-dns-logs-preview"></a>Registros DNS de NXLog (versión preliminar)

El conector de datos de registros DNS de NXLog usa el seguimiento de eventos para Windows (ETW) para recopilar eventos de servidor DNS de análisis y auditoría. Para obtener la máxima eficacia, el módulo im_etw de NXLog lee los datos de seguimiento de eventos directamente, sin tener que capturar el seguimiento de eventos en un archivo *.etl*. Este conector de la API de REST puede reenviar eventos de servidor DNS a Azure Sentinel en tiempo real. Consulte la [Guía de usuario de NXLog de Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para más información.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de registros DNS de NXLog (Windows) a Azure Sentinel](connect-nxlog-dns.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (versión preliminar)

El conector de datos de NXLog LinuxAudit admite reglas de auditoría personalizadas y recopila registros sin usar AuditD ni ningún otro software de usuario. El conector resuelve las direcciones IP y los id. de grupo/usuario en sus respectivos nombres, lo que permite que los registros de auditoría de Linux sean más inteligibles. Este conector de la API de REST puede exportar eventos de seguridad de Linux a Azure Sentinel en tiempo real. Consulte la [Guía de usuario de NXLog de Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para más información.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de NXLog LinuxAudit a Azure Sentinel](connect-nxlog-linuxaudit.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [NXLog](https://nxlog.co/community-forum)

## <a name="okta-single-sign-on-preview"></a>Inicio de sesión único de Okta (versión preliminar)

El conector de datos de inicio de sesión único (SSO) de Okta ingiere registros de eventos y auditoría de la API de Okta en Azure Sentinel. Para crear un token de API, siga las instrucciones de [Creación del token](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión del inicio de sesión único de Okta a Azure Sentinel](connect-okta-single-sign-on.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="onapsis-platform-preview"></a>Plataforma Onapsis (versión preliminar)

El conector de datos de Onapsis exporta las alarmas desencadenadas en la plataforma Onapsis a Azure Sentinel en tiempo real.

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Onapsis](https://onapsis.force.com/s/login/)

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (versión preliminar)

El conector de datos de Sentinel CEF One Identity Safeguard mejora el conector CEF estándar con los paneles específicos de Safeguard for Privileged Sessions. Este conector usa eventos de dispositivo para la visualización, las alertas, las investigaciones, etc. Para obtener más información, consulte la [guía de administración de One Identity Safeguard for Privileged Sessions](https://aka.ms/sentinel-cef-oneidentity-forwarding).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de One Identity Safeguard a Azure Sentinel](connect-one-identity.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [One Identity](https://support.oneidentity.com/)

## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (versión preliminar)

El conector de datos de OracleWebLogicServer ingiere eventos de OracleWebLogicServer en Azure Sentinel. Para obtener más información, consulte la [documentación de Oracle WebLogic Server](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="orca-security-alert-preview"></a>Alerta de Orca Security (versión preliminar)

El conector de alertas de Orca Security exporta automáticamente los registros de alertas a Azure Sentinel. Para más información, consulte [Integración con Azure Sentinel](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Orca Security a Azure Sentinel](connect-orca-security-alerts.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Orca Security](http://support.orca.security/)

## <a name="ossec-preview"></a>OSSEC (versión preliminar)

El conector de datos de OSSEC ingiere eventos de OSSEC en Azure Sentinel. Para obtener más información, consulte la [documentación de OSSEC](https://www.ossec.net/docs/). Para configurar el envío de alertas de OSSEC a través de Syslog, siga las instrucciones de [Envío de alertas a través de syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** Microsoft

## <a name="palo-alto-networks"></a>Palo Alto Networks

El conector de datos de firewall de Palo Alto Networks conecta los registros de Palo Alto Networks a Azure Sentinel. Para obtener más información, consulte las [guías de configuración del formato de evento común (CEF)](https://aka.ms/asi-syslog-paloalto-forwarding) y el artículo de [configuración de la supervisión de Syslog](https://aka.ms/asi-syslog-paloalto-configure).

El conector de datos de Palo Alto Networks ingiere registros en Azure Sentinel [CEF](connect-common-event-format.md) a través de Syslog.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Palo Alto Networks a Azure Sentinel](connect-paloalto.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support)

## <a name="perimeter-81-activity-logs-preview"></a>Registros de actividad de Perimeter 81 (versión preliminar)

El conector de datos registros de actividad de Perimeter 81 conecta los registros de actividad de Perimeter 81 a Azure Sentinel. Para más información, consulte la documentación de [Azure Sentinel](https://support.perimeter81.com/docs/360012680780) de Perimeter 81.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de registros de Perimeter 81 a Azure Sentinel](connect-perimeter-81-logs.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Perimeter 81](https://support.perimeter81.com/)

## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security (versión preliminar)

El conector de datos POD Email Security obtiene los datos de POD Email Protection. Con este conector, puede comprobar la rastreabilidad de los mensajes y supervisar la actividad de correo electrónico, las amenazas y la filtración de datos por parte de atacantes y usuarios malintencionados. Puede revisar los eventos organizativos de forma acelerada y obtener registros de eventos en incrementos por hora para la actividad reciente. Para obtener información sobre cómo habilitar y comprobar la API de registros de POD, inicie sesión en [Proofpoint Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public) y consulte [Proofpoint-on-Demand-Pod-Log-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Proofpoint On Demand (POD) Email Security a Azure Sentinel](connect-proofpoint-pod.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (versión preliminar)

El conector de Proofpoint TAP ingiere registros y eventos de Proofpoint TAP en Azure Sentinel. El conector proporciona visibilidad sobre los eventos Message y Click en Azure Sentinel.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Proofpoint TAP a Azure Sentinel](connect-proofpoint-tap.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (versión preliminar)

El conector de Pulse Connect Secure conecta los registros de Pulse Connect Secure a Azure Sentinel. Para habilitar el streaming de syslog de Pulse Connect Secure, siga las instrucciones de [Configuración de Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Pulse Connect Secure a Azure Sentinel](connect-pulse-connect-secure.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="qualys-vulnerability-management-vm-knowledgebase-kb-preview"></a>Knowledge Base (KB) de Qualys Vulnerability Management (VM) (versión preliminar)

El conector de datos de Qualys VM KB ingiere los datos de vulnerabilidad más recientes de qualys KB en Azure Sentinel. Puede usar estos datos para correlacionar y enriquecer las detecciones de vulnerabilidad mediante el conector de datos de Qualys VM.

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="qualys-vm-preview"></a>Qualys VM (versión preliminar)

El conector de datos de Qualys VM ingiere datos de detección de host de vulnerabilidad en Azure Sentinel a través de la API de Qualys. El conector proporciona visibilidad de los datos de detección de host de los exámenes de vulnerabilidad.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Qualys VM a Azure Sentinel](connect-qualys-vm.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (versión preliminar)

El conector de datos de Salesforce Service Cloud ingiere información sobre los eventos operativos de Salesforce en Azure Sentinel a través de la API de REST. Con este conector, puede revisar los eventos organizativos de forma acelerada y obtener registros de eventos en incrementos por hora de la actividad reciente. Para obtener más información y credenciales, consulte la [Guía para desarrolladores de la API de REST](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm) de Salesforce.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Salesforce Service Cloud a Azure Sentinel](connect-salesforce-service-cloud.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="sentinelone-preview"></a>SentinelOne (versión preliminar)

El conector de datos de SentinelOne ingiere eventos de SentinelOne en Azure Sentinel. Los eventos incluyen objetos de servidor como amenazas, agentes, aplicaciones, actividades, directivas, grupos, etc. El conector puede obtener eventos para examinar posibles riesgos de seguridad, analizar la colaboración del equipo y diagnosticar problemas de configuración, entre otros.

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall (versión preliminar)

El conector de datos de SonicWall Firewall conecta los registros de firewall a Azure Sentinel. Para obtener más información, vea [Registro > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [SonicWall](https://www.sonicwall.com/support/)

## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (versión preliminar)

El conector de datos de Sophos Cloud Optix conecta los registros de Sophos Cloud Optix a Azure Sentinel. Para obtener más información, en la configuración de Cloud Optix, consulte la [página Integraciones](https://optix.sophos.com/#/integrations/sentinel) de Azure Sentinel.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Sophos Cloud Optix a Azure Sentinel](connect-sophos-cloud-optix.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Sophos](https://secure2.sophos.com/en-us/support.aspx)

## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (versión preliminar)

Sophos XG Firewall conecta los registros de Sophos XG Firewall a Azure Sentinel. Para más información, consulte el artículo sobre la [adición de un servidor de Syslog](https://docs.sophos.com/nsg/sophos-firewall/18.0/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Sophos XG a Azure Sentinel](connect-sophos-xg-firewall.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

El conector de datos de Squadra Technologies secRMM inserta datos de eventos de seguridad del almacenamiento extraíble USB en Azure Sentinel. Para obtener más información, consulte la [guía del administrador de Azure Sentinel secRMM](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Squadra Technologies secRMM a Azure Sentinel](connect-squadra-secrmm.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx)

## <a name="squid-proxy-preview"></a>Squid Proxy (versión preliminar)

El conector de datos de [Squid Proxy](http://www.squid-cache.org/) conecta registros de Squid Proxy a Azure Sentinel.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Squid Proxy a Azure Sentinel](connect-squid-proxy.md).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

El conector de datos Symantec ICDx conecta los registros de soluciones de seguridad de Symantec a Azure Sentinel. Para obtener más información, consulte [Conexión del dispositivo Symantec ICDx](connect-symantec.md).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Symantec ICDx a Azure Sentinel](connect-symantec.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Broadcom Symantec](https://support.broadcom.com/security)

## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (versión preliminar)

El conector de datos de Symantec ProxySG conecta los registros de Symantec ProxySG a Azure Sentinel. Para obtener más información, consulte [Envío de registros de acceso a un servidor de Syslog](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html).

El conector de datos ProxySG de Symantec ingiere registros en Azure Sentinel a través de [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Symantec ProxySG a Azure Sentinel](connect-symantec-proxy-sg.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="symantec-vip-preview"></a>Symantec VIP (versión preliminar)

El conector de datos de Symantec VIP conecta los registros de Symantec VIP a Azure Sentinel. Para más información, consulte [Configuración de syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US).

El conector de datos VIP de Symantec ingiere registros en Azure Sentinel a través de [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Symantec VIP a Azure Sentinel](connect-symantec-vip.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (versión preliminar)

El conector de datos de Thycotic Secret Server conecta los registros de Secret Server a Azure Sentinel. Para más información, consulte [Registro seguro de Syslog/CEF](https://docs.thycotic.com/ss/10.9.0/events-and-alerts/secure-syslog-cef).

El conector de datos de Thycotic ingiere registros en Azure Sentinel [CEF](connect-common-event-format.md) a través de Syslog.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Thycotic Secret Server to Azure Sentinel](connect-thycotic-secret-server.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Thycotic](https://thycotic.force.com/support/s/)

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

El conector de datos de Trend Micro Deep Security conecta los registros de Deep Security a Azure Sentinel. Para obtener más información, consulte [Reenvío de eventos de Deep Security a un servidor Syslog o SIEM](https://help.deepsecurity.trendmicro.com/12_0/on-premise/siem-syslog-forwarding-secure.html?redirected=true&Highlight=Configure%20Syslog#Protection_modules_DSM).

El conector de datos de Trend Micro Deep Security ingiere registros en Azure Sentinel [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Trend Micro Deep Security a Azure Sentinel](connect-trend-micro.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (versión preliminar)

El conector de datos de Trend Micro TippingPoint conecta los eventos de TippingPoint SMS IPS a Azure Sentinel.

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Trend Micro TippingPoint a Azure Sentinel](connect-trend-micro-tippingpoint.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog. El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-xdr-preview"></a>Trend Micro XDR (versión preliminar)

El conector de datos de Trend Micro XDR ingiere alertas de Workbench de la API de Trend Micro XDR en Azure Sentinel. Para crear una cuenta y un token de autenticación de API, siga las instrucciones de [Obtención de claves de API para el acceso de terceros](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (versión preliminar)

El conector de datos de VMware Carbon Black Endpoint Standard ingiere datos de Carbon Black Endpoint Standard en Azure Sentinel. Para crear una clave de API, siga las instrucciones de [Creación de una clave de API](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de VMware Carbon Black Cloud Endpoint Standard a Azure Sentinel](connect-vmware-carbon-black.md).

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con:** Microsoft

## <a name="vmware-esxi-preview"></a>VMware ESXi (versión preliminar)

El conector de datos de VMware ESXi conecta registros de VMware ESXi a Azure Sentinel. Para configurar el conector de VMware ESXi para el reenvío de syslog, consulte [Habilitación de syslog en ESXi 3.5 y 4.x](https://kb.vmware.com/s/article/1016621) y [Configuración de Syslog en hosts ESXi](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de VMware ESXi a Azure Sentinel](connect-vmware-esxi.md).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (versión preliminar)

El conector de datos WatchGuard Firebox ingiere registros de firewall en Azure Sentinel. Para más información, consulte la [guía de integración de Microsoft Azure Sentinel](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html).

**Método de ingesta de datos:** [Syslog](connect-syslog.md). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview)

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (versión preliminar)

El conector de datos de WireX Systems permite a los profesionales de seguridad realizar la integración con Azure Sentinel para enriquecer las investigaciones de análisis forense. El conector no solo abarca el contenido contextual que ofrece WireX, sino que también puede analizar datos de otros orígenes. Puede crear paneles y flujos de trabajo personalizados para ofrecer la imagen más completa durante una investigación de análisis forense. Para más información y soporte técnico de configuración, póngase en contacto con el [soporte técnico de WireX](https://wirexsystems.com/contact-us/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de WireX Network Forensics Platform a Azure Sentinel](connect-wirex-systems.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** WireX

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (versión preliminar)

El conector de datos de Workplace ingiere eventos comunes de Workplace en Azure Sentinel a través de Webhooks. Los webhooks permiten que las aplicaciones de integración personalizadas se suscriban a eventos en Workplace y reciban actualizaciones en tiempo real. Cuando se produce un cambio, Workplace envía una solicitud HTTPS POST con información del evento a una dirección URL del conector de datos de devolución de llamada. Para obtener más información, consulte la documentación de Webhooks. El conector puede obtener eventos para examinar posibles riesgos de seguridad, analizar la colaboración del equipo y diagnosticar problemas de configuración, entre otros.

**Método de ingesta de datos:** [Azure Functions y la API de REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Compatible con**: Microsoft

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (versión preliminar)

El conector de datos de Zimperium Mobile Threat Defense conecta el registro de amenazas de Zimperium a Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. Este conector le ofrece más información sobre el panorama de amenazas móviles de su organización y mejora las capacidades de las operaciones de seguridad. Para obtener más instrucciones, consulte el [portal de soporte al cliente de Zimperium](https://support.zimperium.com/).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Zimperium to Azure Sentinel](connect-zimperium-mtd.md).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** [Zimperium](https://www.zimperium.com/support)

## <a name="zoom-reports-preview"></a>Informes de Zoom (versión preliminar)

El conector de datos de informes de Zoom ingiere eventos de informes de Zoom en Azure Sentinel. El conector puede obtener eventos para examinar posibles riesgos de seguridad, analizar la colaboración del equipo y diagnosticar problemas de configuración, entre otros. Para obtener las credenciales, siga las instrucciones de [JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) (JWT con Zoom).

**Método de ingesta de datos:** [API de REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Compatible con:** Microsoft

## <a name="zscaler"></a>Zscaler 

El conector de datos de Zscaler conecta los registros de Zscaler Internet Access (XIA) a Azure Sentinel. El uso de Zscaler en Azure Sentinel proporciona información sobre el uso de Internet de su organización y mejora las funcionalidades de las operaciones de seguridad. Para más información, consulte la [guía de implementación de Zscaler y Microsoft Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).

Para obtener más información sobre cómo conectarse a Azure Sentinel, consulte [Conexión de Zscaler a Azure Sentinel](connect-zscaler.md).

**Método de ingesta de datos:** [CEF](connect-common-event-format.md) a través de Syslog.

**Compatible con:** [Zscaler](https://help.zscaler.com/submit-ticket-links)

## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (versión preliminar)

El conector de datos de ZPA ingiere eventos de Zscaler Private Access en Azure Sentinel. Para más información, consulte la [documentación de Zscaler Private Access](https://help.zscaler.com/zpa).

**Método de ingesta de datos:** [registros personalizados del agente de Log Analytics](connect-data-sources.md#custom-logs). El conector también usa un analizador de registros basado en una función de Kusto.

**Compatible con:** Microsoft
