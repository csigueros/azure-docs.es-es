---
title: Búsqueda del conector de datos de Azure Sentinel | Microsoft Docs
description: Obtenga información sobre los pasos de configuración específicos conectores de datos de Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: reference
ms.date: 08/12/2021
ms.author: bagol
ms.openlocfilehash: 8cbd8861e7dc01e8615225dd88960b581fd4c2f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755091"
---
# <a name="find-your-azure-sentinel-data-connector"></a>Búsqueda del conector de datos de Azure Sentinel

En este artículo se describe cómo implementar conectores de datos en Azure Sentinel, se enumeran todos los conectores de datos integrados compatibles, junto con vínculos a procedimientos de implementación genéricos y pasos adicionales necesarios para conectores específicos.

## <a name="how-to-use-this-guide"></a>Cómo utilizar esta guía

1. En primer lugar, busque y seleccione el conector del producto, servicio o dispositivo en el menú de títulos de la derecha.

    El primer fragmento de información que verá para cada conector es su **método de ingesta de datos**. El método que aparece tendrá un vínculo a uno de los siguientes procedimientos de implementación genéricos, que contienen la mayor parte de la información que necesitará para conectar los orígenes de datos a Azure Sentinel:

    | Método de ingesta de datos | Artículo vinculado con instrucciones |
    | --- | --- |
    | **Integración entre servicios de Azure** | [Conexión a servicios de Azure, Windows, Microsoft y Amazon](connect-azure-windows-microsoft-services.md) |
    | **Formato de evento común (CEF) sobre Syslog** | [Obtención de registros con formato CEF del dispositivo en Azure Sentinel](connect-common-event-format.md) |
    | **Data Collector API de Azure Sentinel** | [Conexión del origen de datos a Data Collector API de Azure Sentinel para ingerir datos](connect-rest-api-template.md) |
    | **Azure Functions y la API REST** | [Uso de Azure Functions para conectar Azure Sentinel al origen de datos](connect-azure-functions-template.md) |
    | **Syslog** | [Recopilación de datos de orígenes basados en Linux mediante Syslog](connect-syslog.md) |
    | **Registros personalizados** | [Recopilación de datos en formatos de registro personalizados para Azure Sentinel con el agente de Log Analytics](connect-custom-logs.md) |
    |

    > [!NOTE]
    > El método de ingesta de datos de **integración entre servicios de Azure** se vincula a tres secciones diferentes de su artículo, en función del tipo de conector. En la sección siguiente de cada conector se especifica la sección de ese artículo a la que se vincula.

1. Al implementar un conector específico, elija el artículo adecuado vinculado a su **método de ingesta de datos** y use la información y las instrucciones adicionales de la sección correspondiente para complementar la información de ese artículo.

> [!TIP]
> - Muchos conectores de datos también se pueden implementar como parte de una [solución de Azure Sentinel](sentinel-solutions.md), junto con reglas de análisis, libros y cuadernos de estrategias relacionados. Para obtener más información, vea [Catálogo de soluciones de Azure Sentinel](sentinel-solutions-catalog.md).
>
>
> - La comunidad de Azure Sentinel proporciona más conectores de datos y se pueden encontrar en Azure Marketplace. La documentación de los conectores de datos de la comunidad es responsabilidad de la organización que ha creado el conector.
>
>
> - Si tiene un origen de datos que no aparece en la lista o actualmente se admite, también puede crear un conector personalizado propio. Para obtener más información, vea [Recursos para crear conectores personalizados de Azure Sentinel](create-custom-connector.md).
>

> [!IMPORTANT]
> Los conectores de datos anotados con Azure Sentinel están actualmente en **versión preliminar**. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense y Brand Protection (versión preliminar)

| Atributo del conector | Descripción|
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) <br><br>**Antes de la implementación**: [ habilite Security Graph API (opcional)](#enable-the-security-graph-api-optional). <br>**Después de la implementación**: [asigne los permisos necesarios a la aplicación de funciones](#assign-necessary-permissions-to-your-function-app)|
| **Tabla de Log Analytics** | agari_bpalerts_log_CL<br>agari_apdtc_log_CL<br>agari_apdpolicy_log_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-agari-functionapp |
| **Credenciales de API** | <li>Id. de cliente<li>Secreto del cliente<li>(Opcional: id. de inquilino de Graph, id. de cliente de Graph, secreto de cliente de Graph) |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Inicio rápido](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Sitio para desarrolladores de Agari](https://developers.agari.com/agari-platform) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Configuración de la aplicación** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (true/false)<li>enablePhishingResponseAPI (true/false)<li>enablePhishingDefenseAPI (true/false)<li>resGroup (escriba el grupo de recursos)<li>functionName<li>subId (escriba el id. de suscripción)<li>enableSecurityGraphSharing (true/false; vea a continuación)<br>Obligatorio si enableSecurityGraphSharing está establecido en true (vea a continuación):<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri (opcional) |
| **Compatible con** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
| | |

### <a name="enable-the-security-graph-api-optional"></a>Habilite Security Graph API (opcional)

> [!IMPORTANT]
> Si realiza este paso, haga esto antes de implementar el conector de datos.
>
La aplicación de funciones de Agari permite compartir la inteligencia sobre amenazas con Azure Sentinel a través de Security Graph API. Para usar esta característica, no solo es preciso habilitar el conector [Sentinel Threat Intelligence Platforms](./connect-threat-intelligence-tip.md), sino también [registrar una aplicación](/graph/auth-register-app-v2) en Azure Active Directory.

Este proceso proporcionará tres datos que se usarán al [implementar la aplicación de funciones](connect-azure-functions-template.md): el **id. de inquilino de Graph**, el **id. de cliente de Graph** y el **secreto de cliente de Graph** (vea la *configuración de la aplicación* en la tabla anterior).

### <a name="assign-necessary-permissions-to-your-function-app"></a>Asigne los permisos necesarios a la aplicación de funciones

El conector de Agari usa una variable de entorno para almacenar las marcas de tiempo de acceso al registro. Para que la aplicación escriba en esta variable, es preciso asignar permisos a la identidad asignada por el sistema.

1. En Azure Portal, vaya a **Aplicación de funciones**.
1. En la hoja **Aplicación de funciones**, seleccione una aplicación de funciones de la lista y, después, seleccione **Identidad** en la opción **Configuración** del menú de navegación de Aplicación de funciones.
1. En la pestaña **Asignado por el sistema**, en **Estado** seleccione **Activado**.
1. Seleccione **Guardar** y aparecerá el botón **Asignaciones de roles de Azure**. Selecciónela.
1. En la pantalla **Asignaciones de roles de Azure**, seleccione **Agregar asignación de rol**. En **Ámbito**, seleccione **Suscripción**, seleccione la suscripción en la lista desplegable **Suscripción** y en **Rol**, seleccione **App Configuration Data Owner** (Propietario de datos de la configuración de la aplicación).
1. Seleccione **Guardar**.


## <a name="ai-analyst-aia-by-darktrace-preview"></a>AI Analyst (AIA) de Darktrace (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** <br><br>[Configuración del reenvío de registros CEF para AI Analyst](#configure-cef-log-forwarding-for-ai-analyst) |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Compatible con** | [Darktrace](https://customerportal.darktrace.com/) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-analyst"></a>Configuración del reenvío de registros CEF para AI Analyst

Configure Darktrace para reenviar mensajes de Syslog en formato CEF al área de trabajo de Azure por medio del agente de Log Analytics.

1. En el visualizador de amenazas de Darktrace, vaya a la página **System Config** (Configuración del sistema) en el menú principal bajo **Admin** (Administración).
1. En el menú de la izquierda, seleccione **Modules** (Módulos) y elija **Azure Sentinel** en las **Workflow Integrations** (Integraciones de flujo de trabajo) disponibles.
1. Se abrirá una ventana de configuración. Busque **Azure Sentinel Syslog CEF** y seleccione **New** (Nuevo) para mostrar los valores de configuración, a menos que ya se muestren.
1. En el campo **Server configuration** (Configuración del servidor), escriba la ubicación del reenviador de registros y, opcionalmente, modifique el puerto de comunicación. Asegúrese de que el puerto seleccionado está establecido en 514 y que lo permiten los firewalls intermedios.
1. Configure los umbrales de alerta, los desplazamientos de tiempo o los valores adicionales según sea necesario.
1. Revise las opciones de configuración adicionales que quiera habilitar para modificar la sintaxis de Syslog.
1. Habilite **Send Alerts** (Enviar alertas) y guarde los cambios.

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** <br><br>[Configuración del reenvío de registros CEF para AI Vectra Detect](#configure-cef-log-forwarding-for-ai-vectra-detect)|
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Compatible con** | [Vectra AI](https://www.vectra.ai/support) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-vectra-detect"></a>Configuración del reenvío de registros CEF para AI Vectra Detect

Configure el agente de Vectra (serie X) para reenviar mensajes de Syslog en formato CEF al área de trabajo de Azure Sentinel por medio del agente de Log Analytics.

En la interfaz de Vectra, vaya a Configuración > Notificaciones y, después, elija Editar configuración de Syslog. Siga estas instrucciones para configurar la conexión:

- Agregue un nuevo destino (el nombre de host del reenviador de registros)
- Establezca el puerto como **514**.
- Establezca el protocolo como **UDP**.
- Establezca el formato en **CEF**.
- Establezca los tipos de registro (seleccione todos los tipos disponibles).
- Seleccione **Guardar**.

Puede seleccionar el botón **Test** (Probar) para forzar el envío de algunos eventos de prueba al reenviador de registros.

Para obtener más información, consulte Cognito Detect Syslog Guide (Guía de Syslog de detección de Cognito), que se puede descargar desde la página de recursos de Detect UI.

## <a name="akamai-security-events-preview"></a>Akamai Security Events (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | AkamaiSIEMEvent |
| **URL de función de Kusto:** | https://aka.ms/sentinel-akamaisecurityevents-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de la integración de SIEM](https://developer.akamai.com/tools/integrations/siem)<br>[Configure un conector CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). |
| **Compatible con** | [Akamai](https://www.akamai.com/us/en/support/) |
| | |

## <a name="alcide-kaudit"></a>Alcide kAudit

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | alcide_kaudit_activity_1_CL: registros de actividad de Alcide kAudit<br>alcide_kaudit_detections_1_CL: detecciones de Alcide kAudit<br>alcide_kaudit_selections_count_1_CL: recuentos de actividad de cAlcide kAudit<br>alcide_kaudit_selections_details_1_CL: detalles de actividad de Alcide kAudit |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de instalación de Alcide kAudit](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit) |
| **Compatible con** | [Alcide](https://www.alcide.io/company/contact-us/) |
| | |

## <a name="alsid-for-active-directory"></a>Alsid para Active Directory

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) <br><br>[Configuración adicional para Alsid](#extra-configuration-for-alsid)|
| **Tabla de Log Analytics** | AlsidForADLog_CL |
| **Alias de función de Kusto:** | afad_parser |
| **URL de función de Kusto:** | https://aka.ms/sentinel-alsidforad-parser |
| **Compatible con** | [Alsid](https://www.alsid.com/contact-us/) |
| | |

### <a name="extra-configuration-for-alsid"></a>Configuración adicional para Alsid

1. **Configuración del servidor de Syslog**

    Primero necesitará un servidor **Syslog de Linux** al que Alsid para AD enviará los registros. Normalmente, puede ejecutar **rsyslog** en **Ubuntu**. 

    Después, puede configurar este servidor como prefiera, pero se recomienda que pueda generar registros de AFAD en un archivo independiente. Como alternativa, puede usar una [plantilla de inicio rápido](https://azure.microsoft.com/resources/templates/alsid-syslog-proxy/) para implementar el servidor de Syslog y el agente de Microsoft automáticamente. Si usa la plantilla, puede omitir las [instrucciones de instalación del agente](connect-custom-logs.md#install-the-log-analytics-agent).

1. **Configuración de Alsid para enviar registros al servidor de Syslog**

    En el portal de **Alsid para AD**, vaya a **Sistema**, **Configuración** y, a continuación, **Syslog**. Desde allí puede crear una nueva alerta de Syslog para el servidor de Syslog.

    Cuando haya terminado, compruebe que los registros se recopilan correctamente en el servidor en un archivo independiente (para ello, puede usar el botón *Test the configuration* (Probar la configuración) en la configuración de alertas de Syslog en AFAD). Si ha usado la plantilla de inicio rápido, el servidor de Syslog escuchará de forma predeterminada en el puerto 514 en UDP y en 1514 en TCP, sin TLS.

## <a name="amazon-web-services---cloudtrail"></a>Amazon Web Services: CloudTrail

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexión de AWS CloudTrail a Azure Sentinel](connect-aws.md)** (Artículo de conector principal) |
| **Tabla de Log Analytics** | AWSCloudTrail |
| **Compatible con** | Microsoft |
| | |

## <a name="apache-http-server"></a>Servidor HTTP de Apache

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) |
| **Tabla de Log Analytics** | ApacheHTTPServer_CL |
| **Alias de función de Kusto:** | ApacheHTTPServer |
| **URL de función de Kusto:** | https://aka.ms/sentinel-apachehttpserver-parser |
| **Archivo de ejemplo de registro personalizado:** | access.log o error.log |
| | |

## <a name="apache-tomcat"></a>Apache Tomcat

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) |
| **Tabla de Log Analytics** | Tomcat_CL |
| **Alias de función de Kusto:** | TomcatEvent |
| **URL de función de Kusto:** | https://aka.ms/sentinel-ApacheTomcat-parser |
| **Archivo de ejemplo de registro personalizado:** | access.log o error.log |
| | |

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | ArubaClearPass |
| **URL de función de Kusto:** | https://aka.ms/sentinel-arubaclearpass-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | Siga las instrucciones de Aruba para [configurar ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). |
| **Compatible con** | Microsoft |
| | |

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | Confluence_Audit_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-confluenceauditapi-functionapp |
| **Credenciales de API** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de la API](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[Requisitos e instrucciones para obtener credenciales](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[Visualización del registro de auditoría](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | ConfluenceAudit |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-confluenceauditapi-parser |
| **Configuración de la aplicación** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | Jira_Audit_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-jiraauditapi-functionapp |
| **Credenciales de API** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de API: registros de auditoría](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[Requisitos e instrucciones para obtener credenciales](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | JiraAudit |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-jiraauditapi-parser |
| **Configuración de la aplicación** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-active-directory"></a>Azure Active Directory

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexión de datos de Azure Active Directory a Azure Sentinel](connect-azure-active-directory.md)** (Artículo de conector principal) |
| **Requisitos previos de licencia/<br>Información de costos** | <li>Licencia de Azure Active Directory P1 o P2 para registros de inicio de sesión<li>Cualquier licencia de Azure AD (Free/O365/P1/P2) para otros tipos de registro<br>Es posible que se apliquen cargos adicionales |
| **Tabla de Log Analytics** | SigninLogs<br>AuditLogs<br>AADNonInteractiveUserSignInLogs<br>AADServicePrincipalSignInLogs<br>AADManagedIdentitySignInLogs<br>AADProvisioningLogs<br>ADFSSignInLogs |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Requisitos previos de licencia/<br>Información de costos** | [Suscripción a Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)<br>Es posible que se apliquen cargos adicionales |
| **Tabla de Log Analytics** | SecurityAlert |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-activity"></a>Actividad de Azure

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico, administradas por Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)**<br><br>[Actualización al nuevo conector de actividad de Azure](#upgrade-to-the-new-azure-activity-connector) |
| **Tabla de Log Analytics** | AzureActivity |
| **Compatible con** | Microsoft |
| | |

### <a name="upgrade-to-the-new-azure-activity-connector"></a>Actualización al nuevo conector de actividad de Azure

#### <a name="data-structure-changes"></a>Cambios en la estructura de datos

Este conector ha cambiado recientemente su mecanismo de back-end para recopilar eventos del registro de actividad. Ahora usa la canalización de **configuración de diagnóstico**. Si todavía usa el método heredado para este conector, *se recomienda encarecidamente actualizar* a la nueva versión, que proporciona una mejor funcionalidad y una mayor coherencia con los registros de recursos. Consulte las instrucciones a continuación.

El método de **configuración de diagnóstico** envía los mismos datos que el método heredado que se envía desde el servicio Registro de actividad, aunque ha habido algunos [cambios en la estructura](../azure-monitor/essentials/activity-log.md#data-structure-changes) de la tabla **AzureActivity**.

Estas son algunas de las mejoras clave resultantes del pasaje a la canalización de configuración de diagnóstico:
- Se mejoró la latencia de ingesta (ingesta de eventos de entre 2 y 3 minutos desde la aparición en lugar de 15 o 20 minutos).
- Se mejoró la confiabilidad.
- Mejor rendimiento.
- Compatibilidad con todas las categorías de eventos registrados por el servicio Registro de actividad (el mecanismo heredado solo admite un subconjunto; por ejemplo, no admite eventos de Service Health).
- Administración a gran escala con Azure Policy.

Vea la [documentación de Azure Monitor](../azure-monitor/logs/data-platform-logs.md) para obtener un tratamiento más detallado del [Registro de actividad de Azure](../azure-monitor/essentials/activity-log.md) y la [canalización de configuración de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md).

#### <a name="disconnect-from-old-pipeline"></a>Desconexión de la canalización antigua

Antes de configurar el nuevo conector del registro de actividad de Azure, debe desconectar las suscripciones existentes del método heredado.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**. En la lista de conectores, seleccione **Actividad de Azure** y, a continuación, haga clic en el botón **Abrir página del conector** en la parte inferior derecha.

1. En la pestaña **Instrucciones**, en la sección **Configuración**, en el paso 1, revise la lista de suscripciones existentes que están conectadas al método heredado (para saber cuáles se van a agregar al nuevo) y desconéctelas todas a la vez con el botón **Desconectar todo** que aparece a continuación.

1. Siga configurando el nuevo conector con las [instrucciones vinculadas en la tabla anterior](connect-azure-windows-microsoft-services.md#diagnostic-settings-based-connections).

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Requisitos previos de licencia/<br>Información de costos** | <li>Debe tener configurado un [plan de protección de Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).<li>Debe tener configurada una [red virtual con Azure DDoS Standard habilitado](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).<br>Es posible que se apliquen cargos adicionales |
| **Tabla de Log Analytics** | AzureDiagnostics |
| **Diagnósticos recomendados** | DDoSProtectionNotifications<br>DDoSMitigationFlowLogs<br>DDoSMitigationReports |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-defender"></a>Azure Defender

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexión de alertas de Azure Defender desde Azure Security Center](connect-azure-security-center.md)** (Artículo de conector principal) |
| **Tabla de Log Analytics** | SecurityAlert |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-defender-for-iot"></a>Azure Defender para IoT

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Tabla de Log Analytics** | SecurityAlert |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-firewall"></a>Azure Firewall

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Tabla de Log Analytics** | AzureDiagnostics |
| **Diagnósticos recomendados** | AzureFirewallApplicationRule<br>AzureFirewallNetworkRule<br>AzureFirewallDnsProxy |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-information-protection"></a>Azure Information Protection

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Integración entre servicios de Azure**](connect-azure-windows-microsoft-services.md) |
| **Tabla de Log Analytics** | InformationProtectionLogs_CL |
| **Compatible con** | Microsoft |
| | |

Para obtener más información, vea la [documentación de Azure Information Protection](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries).

## <a name="azure-key-vault"></a>Azure Key Vault

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico, administradas por Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Tabla de Log Analytics** | KeyVaultData |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico, administradas por Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Tabla de Log Analytics** | kube-apiserver<br>kube-audit<br>kube-audit-admin<br>kube-controller-manager<br>kube-scheduler<br>cluster-autoscaler<br>guard |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-sql-databases"></a>Azure SQL Database

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico, administradas por Azure Policy](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Tabla de Log Analytics** | SQLSecurityAuditEvents<br>SQLInsights<br>AutomaticTuning<br>QueryStoreWaitStatistics<br>Errors<br>DatabaseWaitStatistics<br>Tiempos de espera<br>Blocks<br>Interbloqueos<br>Básico<br>InstanceAndAppAdvanced<br>WorkloadManagement<br>DevOpsOperationsAudit |
| **Compatible con** | Microsoft |
| | |

## <a name="azure-storage-account"></a>Cuenta de Azure Storage

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)**<br><br>[Notas sobre la configuración de diagnóstico de la cuenta de almacenamiento](#notes-about-storage-account-diagnostic-settings-configuration) |
| **Tabla de Log Analytics** | StorageBlobLogs<br>StorageQueueLogs<br>StorageTableLogs<br>StorageFileLogs |
| **Diagnósticos recomendados** | **Recurso de cuenta**<li>Transacción<br>**Recursos de blob, cola, tabla o archivo**<br><li>StorageRead<li>StorageWrite<li>StorageDelete<li>Transacción |
| **Compatible con** | Microsoft |
| | |

### <a name="notes-about-storage-account-diagnostic-settings-configuration"></a>Notas sobre la configuración de diagnóstico de la cuenta de almacenamiento

El recurso de cuenta de almacenamiento (primario) contiene otros recursos (secundarios) para cada tipo de almacenamiento: archivos, tablas, colas y blobs.

Al configurar diagnósticos para una cuenta de almacenamiento, debe seleccionar y configurar lo siguiente, por orden:
- El recurso de la cuenta primaria, y exportar la métrica **Transacción**.
- Cada uno de los recursos de tipo de almacenamiento secundario, y exportar todos los registros y las métricas (vea la tabla anterior).

Solo verá los tipos de almacenamiento para los que realmente ha definido recursos.

## <a name="azure-web-application-firewall-waf"></a>Firewall de aplicaciones web (WAF) de Azure

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en configuración de diagnóstico](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Tabla de Log Analytics** | AzureDiagnostics |
| **Diagnósticos recomendados** | **Application Gateway**<br><li>ApplicationGatewayAccessLog<li>ApplicationGatewayFirewallLog<br>**Front Door**<li>FrontdoorAccessLog<li>FrontdoorWebApplicationFirewallLog<br>**Directiva WAF de CDN**<li>WebApplicationFirewallLogs |
| **Compatible con** | Microsoft |
| | |


## <a name="barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | CGFWFirewallActivity |
| **URL de función de Kusto:** | https://aka.ms/sentinel-barracudacloudfirewall-function |
| **Documentación del proveedor/<br>instrucciones de instalación** | https://aka.ms/sentinel-barracudacloudfirewall-connector |
| **Compatible con** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="barracuda-waf"></a>Firewall de aplicaciones web Barracuda

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) |
| **Tabla de Log Analytics** | syslog |
| **Documentación del proveedor/<br>instrucciones de instalación** | https://aka.ms/asi-barracuda-connector |
| **Compatible con** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | BetterMTDDeviceLog_CL<br>BetterMTDIncidentLog_CL<br>BetterMTDAppLog_CL<br>BetterMTDNetflowLog_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación de BETTER MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration)<br><br>Configuración de la directiva de amenazas, que define los incidentes que se notifican a Azure Sentinel:<br><ol><li>En **Better MTD Console** (Consola de Better MTD), seleccione **Policies** (Directivas) en la barra lateral.<li>Seleccione el botón **Edit** (Editar) de la directiva que use.<li>Para cada tipo de incidente que quiera registrar, vaya al campo **Send to Integrations** (Enviar a integraciones) y seleccione **Sentinel**. |
| **Compatible con** | [Better Mobile](mailto:support@better.mobi) |
| | |


## <a name="beyond-security-besecure"></a>Beyond Security beSECURE

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | beSECURE_ScanResults_CL<br>beSECURE_ScanEvents_CL<br>beSECURE_Audit_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | Acceda al menú **Integration** (Integración):<br><ol><li>Seleccione la opción de menú **More** (Más).<li>Seleccione **Server** (Servidor).<li>Seleccione **Integration** (Integración).<li>Habilitación de Azure Sentinel<li>Pegue los valores **Workspace ID** (Id. de área de trabajo) y **Primary Key** (Clave principal) en la configuración de beSECURE.<li>Seleccione **Modificar**. |
| **Compatible con** | [Beyond Security](https://beyondsecurity.freshdesk.com/support/home) |
| | |


## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | CylancePROTECT |
| **URL de función de Kusto:** | https://aka.ms/sentinel-cylanceprotect-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de Syslog de Cylance](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf) |
| **Compatible con** | Microsoft |
| | |

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | SymantecDLP |
| **URL de función de Kusto:** | https://aka.ms/sentinel-symantecdlp-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de la acción Registrar en un servidor de Syslog](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) |
| **Compatible con** | Microsoft |
| | |

## <a name="check-point"></a>Punto de comprobación

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Exportador de registros: Check Point Log Export](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323) |
| **Compatible con** | [Check Point](https://www.checkpoint.com/support-services/contact-support/) |
| | |

## <a name="cisco-asa"></a>Cisco ASA

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Cisco ASA Series CLI Configuration Guide](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html) (Guía de configuración de la serie Cisco ASA mediante CLI) |
| **Compatible con** | Microsoft |
| | |

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** <br><br>[Configuración adicional para Cisco Firepower eStreamer](#extra-configuration-for-cisco-firepower-estreamer)|
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de operaciones de eStreamer eNcore for Sentinel](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html) |
| **Compatible con** | [Cisco](https://www.cisco.com/c/en/us/support/index.html)
| | |

### <a name="extra-configuration-for-cisco-firepower-estreamer"></a>Configuración adicional para Cisco Firepower eStreamer

1. **Instalación del cliente de Firepower eNcore**  
Instale y configure el cliente de eStreamer de Firepower eNcore. Para obtener más información, vea la [guía completa de instalación de Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html).

1. **Descarga de Firepower Connector desde GitHub**  
Descargue la versión más reciente del conector Firepower eNcore para Azure Sentinel desde el [repositorio de Cisco en GitHub](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector). Si planea usar python3, use el [conector eStreamer python3](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector/tree/python3).

1. **Creación de un archivo pkcs12 con la dirección IP de la máquina virtual o Azure**  
Cree un certificado pkcs12 mediante la dirección IP pública de la instancia de máquina virtual en Firepower en **System > Integration > eStreamer** (Sistema > Integración > eStreamer). Para obtener más información, vea la [guía de instalación](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049443).

1. **Prueba de la conectividad entre el cliente de Azure o la máquina virtual, y FMC**  
Copie el archivo pkcs12 de FMC a la instancia de Azure o VM, y ejecute la utilidad de prueba (./encore.sh test) para asegurarse de que se puede establecer una conexión. Para obtener más información, vea la [guía de configuración](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049430).

1. **Configuración de eNcore para transmitir datos al agente**  
Configure eNcore para transmitir datos mediante TCP al agente de Log Analytics. Esto se debe habilitar de forma predeterminada. Pero se pueden configurar puertos adicionales y protocolos de streaming en función de la posición de seguridad de red. También es posible guardar los datos en el sistema de archivos. Para obtener más información, vea [Configuración de eNcore](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049433).

## <a name="cisco-meraki-preview"></a>Cisco Meraki (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | CiscoMeraki |
| **URL de función de Kusto:** | https://aka.ms/sentinel-ciscomeraki-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación de Meraki Device Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) |
| **Compatible con** | Microsoft |
| | |

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | Cisco_Umbrella_dns_CL<br>Cisco_Umbrella_proxy_CL<br>Cisco_Umbrella_ip_CL<br>Cisco_Umbrella_cloudfirewall_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-CiscoUmbrellaConn-functionapp |
| **Credenciales de API** | <li>Id. de la clave de acceso de AWS<li>Clave de acceso del secreto de AWS<li>Nombre del depósito de AWS S3 |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Registro en Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | Cisco_Umbrella |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-ciscoumbrella-function |
| **Configuración de la aplicación** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | CiscoUCS |
| **URL de función de Kusto:** | https://aka.ms/sentinel-ciscoucs-function |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de Syslog para Cisco UCS: Cisco](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) |
| **Compatible con** | Microsoft |
| | |

## <a name="citrix-analytics-security"></a>Citrix Analytics (Security)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | CitrixAnalytics_SAlerts_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Conexión de Citrix a Azure Sentinel](https://aka.ms/Sentinel-Citrix-Connector) |
| **Compatible con** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | Para configurar WAF, vea [Support WIKI - WAF Configuration with NetScaler](https://support.citrix.com/article/CTX234174) (WIKI de soporte técnico: configuración de WAF con NetScaler).<br><br>Para configurar los registros de CEF, vea [CEF Logging Support in the Application Firewall](https://support.citrix.com/article/CTX136146) (Compatibilidad con el registro de CEF en el firewall de aplicaciones).<br><br>Para reenviar los registros al proxy, vea [Configuring Citrix ADC appliance for audit logging](https://docs.citrix.com/en-us/citrix-adc/current-release/system/audit-logging/configuring-audit-logging.html) (Configuración de dispositivos Citrix ADC para el registro de auditoría). |
| **Compatible con** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="cognni-preview"></a>Cognni (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | CognniIncidents_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | **Conexión a Cognni**<br><ol><li>Vaya a la [página de integraciones de Cognni](https://intelligence.cognni.ai/integrations).<li>Seleccione **Connect** (Conectar) en el cuadro Azure Sentinel.<li>Pegue los valores **workspaceId** y **sharedKey** en los campos de la pantalla de integraciones de Cognni.<li>Seleccione el botón **Connect** (Conectar) para completar la configuración. |
| **Compatible con** | [Cognni](https://cognni.ai/contact-support/)
| | |

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>Eventos de CyberArk Enterprise Password Vault (EPV) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Aplicaciones de administración de eventos e información de seguridad (SIEM)](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) |
| **Compatible con** | [CyberArk](https://www.cyberark.com/customer-support/) |
| | |


## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | CyberpionActionItems_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Obtención de una suscripción a Cyberpion](https://azuremarketplace.microsoft.com/en/marketplace/apps/cyberpion1597832716616.cyberpion)<br>[Integración de alertas de seguridad de Cyberpion en Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) |
| **Compatible con** | [Cyberpion](https://www.cyberpion.com/) |
| | |

## <a name="domain-name-server"></a>Servidor de nombres de dominio

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en el agente de Log Analytics](connect-azure-windows-microsoft-services.md#log-analytics-agent-based-connections)** |
| **Tabla de Log Analytics** | DnsEvents<br>DnsInventory |
| **Compatible con** | Microsoft |
| | |

## <a name="dynamics-365"></a>Dynamics 365

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Requisitos previos de licencia/<br>Información de costos** | <li>[Licencia de producción de Microsoft Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). No disponible para entornos de espacio aislado.<li>Para realizar el registro de actividad se necesita una suscripción a Microsoft 365 Enterprise [E3 o E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements).<br>Es posible que se apliquen cargos adicionales |
| **Tabla de Log Analytics** | Dynamics365Activity |
| **Compatible con** | Microsoft |
| | |

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md)<br><br>[Creación de un usuario de API](#create-an-api-user) |
| **Tabla de Log Analytics** | ESETEnterpriseInspector_CL |
| **Credenciales de API** | <li>Nombre de usuario de EEI<li>Contraseña de EEI<li>URL base |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de la API REST de ESET Enterprise Inspector](https://help.eset.com/eei/1.5/en-US/api.html) |
| **Instrucciones de implementación del conector** | [Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM) |
| **Compatible con** | [ESET](https://support.eset.com/en) |
| | |
### <a name="create-an-api-user"></a>Creación de un usuario de API

1. Inicie sesión en la consola ESET Security Management Center / ESET PROTECT con una cuenta de administrador, seleccione la pestaña **More** (Más) y la subpestaña **Users** (Usuarios).
1. Seleccione el botón **ADD NEW** (AGREGAR NUEVO) y agregue un **usuario nativo**.
1. Cree un usuario para la cuenta de API. **Opcional:** seleccione un **Home group** (grupo Inicio) distinto de **All** (Todos) para limitar las detecciones que se ingieren.
1. En la pestaña **Permission Sets** (Conjuntos de permisos), asigne el conjunto de permisos **Enterprise Inspector reviewer** (Revisor de Enterprise Inspector).
1. Cierre la sesión de la cuenta de administrador, inicie sesión en la consola con las nuevas credenciales de API para la validación y, después, cierre la sesión de la cuenta de API.

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md)<br><br>[Configuración de los registros de ESET SMC que se van a recopilar](#configure-the-eset-smc-logs-to-be-collected) <br>[Configuración del agente de OMS para pasar los datos de Eset SMC en formato de API](#configure-oms-agent-to-pass-eset-smc-data-in-api-format)<br>[Cambio de la configuración del agente de OMS para detectar la etiqueta oms.api.eset y analizar los datos estructurados](#change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data)<br>[Deshabilitación de la configuración automática y reinicio del agente](#disable-automatic-configuration-and-restart-agent)|
| **Tabla de Log Analytics** | eset_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación del servidor de Syslog de ESET](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) |
| **Compatible con** | [ESET](https://support.eset.com/en) |
| | |

### <a name="configure-the-eset-smc-logs-to-be-collected"></a>Configuración de los registros de ESET SMC que se van a recopilar

Configure rsyslog para que acepte registros de la dirección IP de Eset SMC.

```bash
    sudo -i
    # Set ESET SMC source IP address
    export ESETIP={Enter your IP address}

    # Create rsyslog configuration file
    cat > /etc/rsyslog.d/80-remote.conf << EOF
    \$ModLoad imudp
    \$UDPServerRun 514
    \$ModLoad imtcp
    \$InputTCPServerRun 514
    \$AllowedSender TCP, 127.0.0.1, $ESETIP
    \$AllowedSender UDP, 127.0.0.1, $ESETIP user.=alert;user.=crit;user.=debug;user.=emerg;user.=err;user.=info;user.=notice;user.=warning  @127.0.0.1:25224
    EOF

    # Restart rsyslog
    systemctl restart rsyslog
```

### <a name="configure-oms-agent-to-pass-eset-smc-data-in-api-format"></a>Configuración del agente de OMS para pasar los datos de Eset SMC en formato de API

Para reconocer fácilmente los datos de Eset, debe insertarlos en una tabla independiente y analizarlos en el agente, a fin de simplificar y acelerar la consulta de Azure Sentinel. 

En el archivo **/etc/opt/microsoft/omsagent/{REEMPLACE_id_del_área_de_trabajo}/conf/omsagent.conf**, modifique la sección `match oms.**` para enviar los datos como objetos de API, y cambie el tipo a `out_oms_api`.
    
A continuación se muestra un ejemplo de la sección `match oms.**` completa:

```bash
    <match oms.** docker.**>
      type out_oms_api
      log_level info
      num_threads 5
      run_in_background false

      omsadmin_conf_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsadmin.conf
      cert_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.crt
      key_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.key

      buffer_chunk_limit 15m
      buffer_type file
      buffer_path /var/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/state/out_oms_common*.buffer

      buffer_queue_limit 10
      buffer_queue_full_action drop_oldest_chunk
      flush_interval 20s
      retry_limit 10
      retry_wait 30s
      max_retry_wait 9m
    </match>
```

### <a name="change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data"></a>Cambio de la configuración del agente de OMS para detectar la etiqueta oms.api.eset y analizar los datos estructurados

Modifique el archivo **/etc/opt/microsoft/omsagent/{REEMPLACE_id_del_área_de_trabajo}/conf/omsagent.d/syslog.conf**. 

Por ejemplo:

```bash
    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.api.eset
    </source>

    <filter oms.api.**>
      @type parser
      key_name message
      format /(?<message>.*?{.*})/
    </filter>

    <filter oms.api.**>
      @type parser
      key_name message
      format json
    </filter>
```
### <a name="disable-automatic-configuration-and-restart-agent"></a>Deshabilitación de la configuración automática y reinicio del agente

Por ejemplo: 

```bash
    # Disable changes to configuration files from Portal
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'

    # Restart agent
    sudo /opt/microsoft/omsagent/bin/service_control restart

    # Check agent logs
    tail -f /var/opt/microsoft/omsagent/log/omsagent.log
```

### <a name="configure-eset-smc-to-send-logs-to-connector"></a>Configuración de Eset SMC para enviar registros al conector

Configure los registros de Eset con el estilo BSD y el formato JSON.

- Vaya a la configuración del servidor de Syslog para configurar el host (el conector), con el formato BSD y el transporte TCP
- Vaya a la sección Logging (Registro) y habilite JSON.

Para obtener más información, vea la documentación de Eset.

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | ExabeamEvent |
| **URL de función de Kusto:** | https://aka.ms/sentinel-Exabeam-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de notificaciones de actividad del sistema de Análisis avanzado](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08) |
| **Compatible con** | Microsoft |
| | |

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Conector SIEM de ExtraHop Detection](https://aka.ms/asi-syslog-extrahop-forwarding) |
| **Compatible con** | [ExtraHop](https://www.extrahop.com/support/) |
| | |

## <a name="f5-big-ip"></a>F5 BIG-IP

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | F5Telemetry_LTM_CL<br>F5Telemetry_system_CL<br>F5Telemetry_ASM_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Integración de F5 BIG-IP con Azure Sentinel](https://aka.ms/F5BigIp-Integrate) |
| **Compatible con** | [Redes F5](https://support.f5.com/csp/home) |
| | |
## <a name="f5-networks-asm"></a>F5 Networks (ASM)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración del registro de eventos de seguridad de aplicaciones](https://aka.ms/asi-syslog-f5-forwarding) |
| **Compatible con** | [Redes F5](https://support.f5.com/csp/home) |
| | |

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Forcepoint CASB y Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) |
| **Compatible con** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Forcepoint Cloud Security Gateway y Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) |
| **Compatible con** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | ForcepointDLPEvents_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Forcepoint Data Loss Prevention y Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) |
| **Compatible con** | [Forcepoint](https://support.forcepoint.com/) |
| | |
## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Firewall de última generación de Forcepoint y Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) |
| **Compatible con** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) for CEF (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Instale esto primero. ForgeRock Common Audit (CAUD) para Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler) |
| **Compatible con** | [ForgeRock](https://www.forgerock.com/support) |
| | |

## <a name="fortinet"></a>Fortinet

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** <br><br>[Envío de registros de Fortinet al reenviador de registros](#send-fortinet-logs-to-the-log-forwarder) |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Biblioteca de documentos de Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)<br>Seleccione la versión y use los PDF *Handbook* (Manual) y *Log Message Reference* (Referencia de mensajes de registro). |
| **Compatible con** | [Fortinet](https://support.fortinet.com/) |
| | |
### <a name="send-fortinet-logs-to-the-log-forwarder"></a>Envío de registros de Fortinet al reenviador de registros

Abra la CLI en su dispositivo Fortinet y ejecute los siguientes comandos:

```Console
config log syslogd setting
set status enable
set format cef
set port 514
set server <ip_address_of_Forwarder>
end
```

- Reemplace la **dirección IP** del servidor por la dirección IP del reenviador de registros.
- Establezca el **puerto de syslog** en **514** o en el que haya establecido en el demonio de Syslog en el reenviador.
- Para habilitar el formato CEF en las primeras versiones de FortiOS, es posible que deba ejecutar el conjunto de comandos **csv disable**.


## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md)<br><br>[Configuración adicional para la API de informes de Google](#extra-configuration-for-the-google-reports-api) |
| **Tabla de Log Analytics** | GWorkspace_ReportsAPI_admin_CL<br>GWorkspace_ReportsAPI_calendar_CL<br>GWorkspace_ReportsAPI_drive_CL<br>GWorkspace_ReportsAPI_login_CL<br>GWorkspace_ReportsAPI_mobile_CL<br>GWorkspace_ReportsAPI_token_CL<br>GWorkspace_ReportsAPI_user_accounts_CL<br> |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-GWorkspaceReportsAPI-functionapp |
| **Credenciales de API** | <li>GooglePickleString |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de la API](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>Obtenga las credenciales en [Perform Google Workspace Domain-Wide Delegation of Authority](https://developers.google.com/admin-sdk/reports/v1/guides/delegation) (Delegación de autoridad de todo el dominio de Google Workspace).<li>[Conversión del archivo token.pickle en una cadena pickle](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | GWorkspaceActivityReports |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-GWorkspaceReportsAPI-parser |
| **Configuración de la aplicación** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

### <a name="extra-configuration-for-the-google-reports-api"></a>Configuración adicional para la API de informes de Google

Agregue http://localhost:8081/ en **Authorized redirect URIs** (URI de redireccionamiento autorizados) al crear las [credenciales de aplicación web](https://developers.google.com/workspace/guides/create-credentials#web).

1. [Siga las instrucciones](https://developers.google.com/admin-sdk/reports/v1/quickstart/python) para obtener el archivo credentials.json.
1. Para obtener la cadena pickle de Google, ejecute [este script de Python](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) (en la misma ruta que credentials.json).
1. Copie la salida de la cadena pickle entre comillas simples y guárdela. La necesitará para implementar la aplicación de funciones.

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de administración de Illusive Networks](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version) |
| **Compatible con** | [Illusive Networks](https://www.illusivenetworks.com/technical-support/) |
| | |

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Pasos para habilitar el registro de alertas de Imperva WAF Gateway en Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert) |
| **Compatible con** | [Imperva](https://www.imperva.com/support/technical-support/) |
| | |


## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | InfobloxNIOS |
| **URL de función de Kusto:** | https://aka.ms/sentinelgithubparsersinfoblox |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de implementación de SNMP y Syslog de NIOS](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf) |
| **Compatible con** | Microsoft |
| | |

## <a name="juniper-srx-preview"></a>Juniper SRX (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | JuniperSRX |
| **URL de función de Kusto:** | https://aka.ms/sentinel-junipersrx-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración del registro de tráfico (registros de directivas de seguridad) para dispositivos de rama SRX](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)<br>[Configuración del registro del sistema](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502) |
| **Compatible con** | [Juniper Networks](https://support.juniper.net/support/) |
| | |


## <a name="microsoft-365-defender"></a>Microsoft 365 Defender

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexión de datos de Microsoft 365 Defender a Azure Sentinel](connect-microsoft-365-defender.md)** (Artículo de conector principal) |
| **Requisitos previos de licencia/<br>Información de costos** | [Licencia válida para Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites)
| **Tabla de Log Analytics** | SecurityAlert<br>SecurityIncident<br>DeviceEvents<br>DeviceFileEvents<br>DeviceImageLoadEvents<br>DeviceInfo<br>DeviceLogonEvents<br>DeviceNetworkEvents<br>DeviceNetworkInfo<br>DeviceProcessEvents<br>DeviceRegistryEvents<br>DeviceFileCertificateInfo |
| **Compatible con** | Microsoft |
| | |

## <a name="microsoft-cloud-app-security-mcas"></a>Microsoft Cloud App Security (MCAS)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Para transmitir registros de Cloud Discovery, [habilite Azure Sentinel como SIEM en Microsoft Cloud App Security](/cloud-app-security/siem-sentinel). |
| **Tabla de Log Analytics** | SecurityAlert: para alertas<br>McasShadowItReporting: para registros de Cloud Discovery |
| **Compatible con** | Microsoft |
| | |

## <a name="microsoft-defender-for-endpoint"></a>Microsoft Defender para punto de conexión

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Requisitos previos de licencia/<br>Información de costos** | [Licencia válida para Microsoft Defender para punto de conexión](/microsoft-365/security/defender-endpoint/production-deployment)
| **Tabla de Log Analytics** | SecurityAlert |
| **Compatible con** | Microsoft |
| | |

## <a name="microsoft-defender-for-identity"></a>Microsoft Defender for Identity

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Tabla de Log Analytics** | SecurityAlert |
| **Compatible con** | Microsoft |
| | |

## <a name="microsoft-defender-for-office-365"></a>Microsoft Defender para Office 365

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Requisitos previos de licencia/<br>Información de costos** | Debe tener una licencia válida para [Defender para Office 365 Plan 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2)
| **Tabla de Log Analytics** | SecurityAlert |
| **Compatible con** | Microsoft |
| | |

## <a name="microsoft-office-365"></a>Microsoft Office 365

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en API](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Requisitos previos de licencia/<br>Información de costos** | La implementación de Office 365 debe estar en el mismo inquilino que el área de trabajo de Azure Sentinel.<br>Es posible que se apliquen cargos adicionales |
| **Tabla de Log Analytics** | OfficeActivity |
| **Compatible con** | Microsoft |
| | |

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | Morphisec |
| **URL de función de Kusto** | https://aka.ms/sentinel-Morphiescutpp-parser |
| **Compatible con** | [Morphisec](https://support.morphisec.com/support/home) |
| | |


## <a name="netskope-preview"></a>Netskope (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | Netskope_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-netskope-functioncode |
| **Credenciales de API** | <li>Token de API de Netskope |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Plataforma Netskope Cloud Security](https://www.netskope.com/platform)<li>[Documentación de API de Netskope](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[Obtención de un token de API](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Alias de función de Kusto** | Netskope |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-netskope-parser |
| **Configuración de la aplicación** | <li>apikey<li>workspaceID<li>workspaceKey<li>uri (depende de la región, sigue el esquema: `https://<Tenant Name>.goskope.com`) <li>timeInterval (se establece en 5)<li>logTypes<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) |
| **Tabla de Log Analytics** | NGINX_CL |
| **Alias de función de Kusto:** | NGINXHTTPServer |
| **URL de función de Kusto** | https://aka.ms/sentinel-NGINXHTTP-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Módulo ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) |
| **Archivo de ejemplo de registro personalizado:** | access.log o error.log |
| **Compatible con** | Microsoft |
| | |

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) en macOS (Preview)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | BSMmacOS_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía del usuario de Azure Sentinel de NXLog](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Compatible con** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-dns-logs-preview"></a>Registros DNS de NXLog (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | DNS_Logs_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía del usuario de Azure Sentinel de NXLog](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Compatible con** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | LinuxAudit_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** |  [Guía del usuario de Azure Sentinel de NXLog](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **Compatible con** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="okta-single-sign-on-preview"></a>Inicio de sesión único de Okta (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | Okta_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **Credenciales de API** | <li>Token de API |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de la API de registro del sistema de Okta](https://developer.okta.com/docs/reference/api/system-log/)<li>[Creación de un token de API](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[Conexión de Okta SSO a Azure Sentinel](#okta-single-sign-on-preview) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Configuración de la aplicación** | <li>apiToken<li>workspaceID<li>workspaceKey<li>uri (sigue el esquema `https://<OktaDomain>/api/v1/logs?since=`. [Identifique el espacio de nombres del dominio](https://developer.okta.com/docs/reference/api-overview/#url-namespace)). <li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |


## <a name="onapsis-platform-preview"></a>Onapsis Platform (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con una función de enriquecimiento y búsqueda de Kusto<br><br>[Configuración de Onapsis para enviar registros CEF al reenviador de registros](#configure-onapsis-to-send-cef-logs-to-the-log-forwarder) |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | incident_lookup |
| **URL de función de Kusto** | https://aka.ms/sentinel-Onapsis-parser |
| **Compatible con** | [Onapsis](https://onapsis.force.com/s/login/) |
| | |

### <a name="configure-onapsis-to-send-cef-logs-to-the-log-forwarder"></a>Configuración de Onapsis para enviar registros CEF al reenviador de registros

Consulte la ayuda de Onapsis en el producto para configurar el reenvío de registros al agente de Log Analytics.

1. Vaya a **Setup > Third-party integrations > Defend Alarms** (Configuración > Integraciones de terceros > Alarmas de defensa) y siga las instrucciones para Azure Sentinel.
1. Asegúrese de que la consola de Onapsis puede acceder a la máquina del reenviador de registros donde está instalado el agente. Los registros se deben enviar al puerto 514 mediante TCP.

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de administración de One Identity Safeguard for Privileged Sessions](https://aka.ms/sentinel-cef-oneidentity-forwarding) |
| **Compatible con** | [One Identity](https://support.oneidentity.com/) |
| | |


## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) |
| **Tabla de Log Analytics** | OracleWebLogicServer_CL |
| **Alias de función de Kusto:** | OracleWebLogicServerEvent |
| **URL de función de Kusto:** | https://aka.ms/sentinel-OracleWebLogicServer-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación de Oracle WebLogic Server](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html) |
| **Archivo de ejemplo de registro personalizado:** | server.log |
| **Compatible con** | Microsoft |
| | |

## <a name="orca-security-preview"></a>Orca Security (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | OrcaAlerts_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Integración de Azure Sentinel](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration) |
| **Compatible con** | [Orca Security](http://support.orca.security/) |
| | |


## <a name="ossec-preview"></a>OSSEC (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | OSSECEvent |
| **URL de función de Kusto:** | https://aka.ms/sentinel-OSSEC-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación de OSSEC](https://www.ossec.net/docs/)<br>[Envío de alertas mediante syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html) |
| **Compatible con** | Microsoft |
| | |


## <a name="palo-alto-networks"></a>Palo Alto Networks

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guías de configuración de formato de evento común (CEF)](https://aka.ms/asi-syslog-paloalto-forwarding)<br>[Configuración de la supervisión de Syslog](https://aka.ms/asi-syslog-paloalto-configure) |
| **Compatible con** | [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support) |
| | |


## <a name="perimeter-81-activity-logs-preview"></a>Registros de actividad de Perimeter 81 (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | Perimeter81_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación de Perimeter 81](https://support.perimeter81.com/docs/360012680780) |
| **Compatible con** | [Perimeter 81](https://support.perimeter81.com/) |
| | |


## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | ProofpointPOD_message_CL<br>ProofpointPOD_maillog_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-proofpointpod-functionapp |
| **Credenciales de API** | <li>ProofpointClusterID<li>ProofpointToken |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Inicio de sesión en Proofpoint Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Instrucciones y documentación de Proofpoint API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | ProofpointPOD |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-proofpointpod-parser |
| **Configuración de la aplicación** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | ProofPointTAPClicksPermitted_CL<br>ProofPointTAPClicksBlocked_CL<br>ProofPointTAPMessagesDelivered_CL<br>ProofPointTAPMessagesBlocked_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **Credenciales de API** | <li>Nombre de usuario de API<li>Contraseña de API |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de API SIEM de Proofpoint](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Configuración de la aplicación** | <li>apiUsername<li>apiUsername<li>uri (se establece en `https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300`)<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | PulseConnectSecure |
| **URL de función de Kusto:** | https://aka.ms/sentinelgithubparserspulsesecurevpn |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) |
| **Compatible con** | Microsoft |
| | |


## <a name="qualys-vm-knowledgebase-kb-preview"></a>Qualys VM KnowledgeBase (KB) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md)<br><br>[Configuración adicional para la KB de máquina virtual de Qualys](#extra-configuration-for-the-qualys-vm-kb) |
| **Tabla de Log Analytics** | QualysKB_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-qualyskb-functioncode |
| **Credenciales de API** | <li>Nombre de usuario de API<li>Contraseña de API |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Guía del usuario de QualysVM API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Alias de función de Kusto** | QualysKB |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-qualyskb-parser |
| **Configuración de la aplicación** | <li>apiUsername<li>apiUsername<li>uri (por región; vea [Lista de servidores de API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Sigue el esquema `https://<API Server>/api/2.0`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (se agrega al final del URI, delimitado por `&`. Sin espacios).<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm-kb"></a>Configuración adicional para la KB de máquina virtual de Qualys

1. Inicie sesión en la consola de administración de vulnerabilidades de Qualys con una cuenta de administrador, seleccione la pestaña **Users** (Usuarios) y la subpestaña **Users** (Usuarios).
1. Seleccione el menú desplegable **New** (Nuevo) y seleccione **Users** (Usuarios).
1. Cree un nombre de usuario y una contraseña para la cuenta de API.
1. En la pestaña **User Roles** (Roles de usuario), asegúrese de que el rol de la cuenta está establecido en **Manager** (Administrador) y de que se permite el acceso a **GUI** y **API**.
1. Cierre la sesión de la cuenta de administrador, inicie sesión en la consola con las nuevas credenciales de API para la validación y, después, cierre la sesión de la cuenta de API.
1. Vuelva a iniciar sesión en la consola con una cuenta de administrador y modifique las cuentas de API, los roles de usuario y quite el acceso a la **GUI**.
1. Guarde todos los cambios.

## <a name="qualys-vulnerability-management-vm-preview"></a>Qualys Vulnerability Management (VM) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md)<br><br>[Configuración adicional para la máquina virtual de Qualys](#extra-configuration-for-the-qualys-vm) <br>[Implementación manual: después de configurar la aplicación de funciones](#manual-deployment---after-configuring-the-function-app)|
| **Tabla de Log Analytics** | QualysHostDetection_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **Credenciales de API** | <li>Nombre de usuario de API<li>Contraseña de API |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Guía del usuario de QualysVM API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Configuración de la aplicación** | <li>apiUsername<li>apiUsername<li>uri (por región; vea [Lista de servidores de API](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Sigue el esquema `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (se agrega al final del URI, delimitado por `&`. Sin espacios).<li>timeInterval (se establece en 5. Si lo cambia, modifique el desencadenador del temporizador de la aplicación de funciones según sea necesario).<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm"></a>Configuración adicional para la máquina virtual de Qualys

1. Inicie sesión en la consola de administración de vulnerabilidades de Qualys con una cuenta de administrador, seleccione la pestaña **Users** (Usuarios) y la subpestaña **Users** (Usuarios).
1. Seleccione el menú desplegable **New** (Nuevo) y seleccione **Users** (Usuarios).
1. Cree un nombre de usuario y una contraseña para la cuenta de API.
1. En la pestaña **User Roles** (Roles de usuario), asegúrese de que el rol de la cuenta está establecido en **Manager** (Administrador) y de que se permite el acceso a **GUI** y **API**.
1. Cierre la sesión de la cuenta de administrador, inicie sesión en la consola con las nuevas credenciales de API para la validación y, después, cierre la sesión de la cuenta de API.
1. Vuelva a iniciar sesión en la consola con una cuenta de administrador y modifique las cuentas de API, los roles de usuario y quite el acceso a la **GUI**.
1. Guarde todos los cambios.

### <a name="manual-deployment---after-configuring-the-function-app"></a>Implementación manual: después de configurar la aplicación de funciones

**Configuración del archivo host.json**

Debido a la cantidad potencialmente grande de datos de detección de host de Qualys que se ingieren, puede hacer que el tiempo de ejecución supere el tiempo de espera predeterminado de la aplicación de funciones de cinco (5) minutos. Aumente la duración del tiempo de espera predeterminado hasta el máximo de diez (10) minutos, en el Plan de consumo, para permitir más tiempo para que se ejecute la aplicación de funciones.

1. En la aplicación de funciones, seleccione el nombre y después el panel **Editor de App Service**.
1. Seleccione **Ir** para abrir el editor y después el archivo **host.json** en el directorio **wwwroot**.
1. Agregue la línea `"functionTimeout": "00:10:00",` por encima de la línea `managedDependancy`.
1. Asegúrese de que aparece **GUARDADO** en la esquina superior derecha del editor y, después, salga del editor.

Si se necesita una tiempo de espera más largo, considere la posibilidad de actualizar a un [plan de App Service](../azure-functions/functions-scale.md).


## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | SalesforceServiceCloud_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-SalesforceServiceCloud-functionapp |
| **Credenciales de API** | <li>Nombre de usuario de la API de Salesforce<li>Contraseña de la API de Salesforce<li>Token de seguridad de Salesforce<li>Clave de consumidor de Salesforce<li>Secreto de consumidor de Salesforce |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía para desarrolladores de API REST de Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)<br>En **Configurar autorización**, use el método **Id. de sesión** en lugar de OAuth. |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | SalesforceServiceCloud |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-SalesforceServiceCloud-parser |
| **Configuración de la aplicación** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |


## <a name="security-events-windows"></a>Eventos de seguridad (Windows)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexión a servidores de Windows para recopilar eventos de seguridad](connect-windows-security-events.md)** (Artículo de conector superior) |
| **Tabla de Log Analytics** | SecurityEvents |
| **Compatible con** | Microsoft |
| | |

Para obtener más información, vea [Configuración de libros de protocolos poco seguros](./get-visibility.md#use-built-in-workbooks).

## <a name="sentinelone-preview"></a>SentinelOne (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) <br><br>[Configuración adicional para SentinelOne](#extra-configuration-for-sentinelone)|
| **Tabla de Log Analytics** | SentinelOne_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-SentinelOneAPI-functionapp |
| **Credenciales de API** | <li>SentinelOneAPIToken<li>SentinelOneUrl (`https://<SOneInstanceDomain>.sentinelone.net`) |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>Vea las instrucciones siguientes |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | SentinelOne |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-SentinelOneAPI-parser |
| **Configuración de la aplicación** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

### <a name="extra-configuration-for-sentinelone"></a>Configuración adicional para SentinelOne

Siga las instrucciones para obtener las credenciales.

1. Inicie sesión en la consola de administración de SentinelOne con credenciales de usuario administrador.
1. En la consola de administración, seleccione **Settings** (Configuración).
1. En la vista **SETTINGS** (CONFIGURACIÓN), seleccione **Users** (USUARIOS).
1. Seleccione **New User** (Nuevo usuario).
1. Escriba la información del nuevo usuario de la consola.
1. En Role (Rol), seleccione **Admin** (Administrador).
1. Seleccione **SAVE** (Guardar).
1. Guarde las credenciales del nuevo usuario para utilizarlo en el conector de datos.

## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Log > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)<br>Seleccione la instalación local4 y ArcSight como formato de Syslog.  |
| **Compatible con** | [SonicWall](https://www.sonicwall.com/support/) |
| | | 


## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | SophosCloudOptix_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Realice la integración con Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html), y omita el primer paso.<br>[Ejemplos de consultas de Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html) |
| **Compatible con** | [Sophos](https://secure2.sophos.com/en-us/support.aspx) |
| | |


## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | SophosXGFirewall |
| **URL de función de Kusto:** | https://aka.ms/sentinelgithubparserssophosfirewallxg |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Adición de un servidor de Syslog](https://docs.sophos.com/nsg/sophos-firewall/18.5/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html) |
| **Compatible con** | Microsoft |
| | |

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | secRMM_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía del administrador de Azure Sentinel de secRMM](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) |
| **Compatible con** | [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx) |
| | |


## <a name="squid-proxy-preview"></a>Squid Proxy (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md) |
| **Tabla de Log Analytics** | SquidProxy_CL |
| **Alias de función de Kusto:** | SquidProxy |
| **URL de función de Kusto** | https://aka.ms/sentinel-squidproxy-parser |
| **Archivo de ejemplo de registro personalizado:** | access.log o cache.log |
| **Compatible con** | Microsoft |
| | |

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md) |
| **Tabla de Log Analytics** | SymantecICDx_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de reenviadores de Microsoft Azure Sentinel (Log Analytics)](https://techdocs.broadcom.com/us/en/symantec-security-software/integrated-cyber-defense/integrated-cyber-defense-exchange/1-4-3/Forwarders/configuring-forwarders-v131944722-d2707e17438.html) |
| **Compatible con** | [Broadcom Symantec](https://support.broadcom.com/security) |
| | |


## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | SymantecProxySG |
| **URL de función de Kusto:** | https://aka.ms/sentinelgithubparserssymantecproxysg |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Envío de registros de acceso a un servidor de Syslog](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html) |
| **Compatible con** | Microsoft |
| | |


## <a name="symantec-vip-preview"></a>Symantec VIP (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | SymantecVIP |
| **URL de función de Kusto:** | https://aka.ms/sentinelgithubparserssymantecvip |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Configuración de Syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US) |
| **Compatible con** | Microsoft |
| | |


## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Registro seguro de Syslog/CEF](https://thy.center/ss/link/syslog) |
| **Compatible con** | [Thycotic](https://thycotic.force.com/support/s/) |
| | |

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | TrendMicroDeepSecurity |
| **URL de función de Kusto** | https://aka.ms/TrendMicroDeepSecurityFunction |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Reenvío de eventos de seguridad profunda a un servidor de Syslog o SIEM](https://aka.ms/Sentinel-trendMicro-connectorInstructions) |
| **Compatible con** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog**, con un analizador de funciones de Kusto |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Alias de función de Kusto:** | TrendMicroTippingPoint |
| **URL de función de Kusto** | https://aka.ms/sentinel-trendmicrotippingpoint-function |
| **Documentación del proveedor/<br>instrucciones de instalación** | Envíe mensajes de Syslog en formato ArcSight CEF v4.2. |
| **Compatible con** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-vision-one-xdr-preview"></a>Trend Micro Vision One (XDR) (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | TrendMicro_XDR_CL |
| **Credenciales de API** | <li>Token de API |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Trend Micro Vision One API](https://automation.trendmicro.com/xdr/home)<li>[Obtención de claves de API para el acceso de terceros](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **Instrucciones de implementación del conector** | [Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM) |
| **Compatible con** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | CarbonBlackEvents_CL<br>CarbonBlackAuditLogs_CL<br>CarbonBlackNotifications_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **Credenciales de API** | **Nivel de acceso de API** (para registros de *auditoría* y *eventos*):<li>Id. de API<li>Clave de API<br><br>**Nivel de acceso SIEM** (para eventos de *notificación*):<li>Id. de API de SIEM<li>Clave de API de SIEM |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Documentación de Carbon Black API](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[Creación de una clave de API](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPS) |
| **Configuración de la aplicación** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>uri (por región; [vea la lista de opciones](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346). Sigue el esquema `https://<API URL>.conferdeploy.net`).<li>timeInterval (se establece en 5)<li>SIEMapiId (si se ingieren eventos de *notificación*)<li>SIEMapiKey (si se ingieren eventos de *notificación*)<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="vmware-esxi-preview"></a>VMware ESXi (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | VMwareESXi |
| **URL de función de Kusto:** | https://aka.ms/sentinel-vmwareesxi-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Habilitación de syslog en ESXi 3.5 y 4.x](https://kb.vmware.com/s/article/1016621)<br>[Configuración de Syslog en hosts de ESXi](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html) |
| **Compatible con** | Microsoft |
| | |

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Syslog**](connect-syslog.md) |
| **Tabla de Log Analytics** | syslog |
| **Alias de función de Kusto:** | WatchGuardFirebox |
| **URL de función de Kusto:** | https://aka.ms/sentinel-watchguardfirebox-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de integración de Microsoft Azure Sentinel](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html) |
| **Compatible con** | [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview) |
| | |

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | Póngase en contacto con el [soporte técnico de WireX](https://wirexsystems.com/contact-us/) a fin de configurar la solución NFP para enviar mensajes de Syslog en formato CEF. |
| **Compatible con** | [WireX Systems](mailto:support@wirexsystems.com) |
| | |


## <a name="windows-firewall"></a>Firewall de Windows

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexiones basadas en el agente de Log Analytics](connect-azure-windows-microsoft-services.md#log-analytics-agent-based-connections)** |
| **Tabla de Log Analytics** | WindowsFirewall |
| **Compatible con** | Microsoft |
| | |

## <a name="windows-security-events"></a>Eventos de seguridad de Windows

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **Integración entre servicios de Azure: <br>[Conexión a servidores de Windows para recopilar eventos de seguridad](connect-windows-security-events.md)** (Artículo de conector superior) |
| **Tabla de Log Analytics** | SecurityEvents |
| **Compatible con** | Microsoft |
| | |

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md)<br><br>[Configuración de webhooks](#configure-webhooks) <br>[Adición de la dirección URL de devolución de llamada a la configuración de webhook](#add-callback-url-to-webhook-configuration)|
| **Tabla de Log Analytics** | Workplace_Facebook_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-WorkplaceFacebook-functionapp |
| **Credenciales de API** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Configuración de webhooks](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[Configuración de permisos](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | Workplace_Facebook |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-WorkplaceFacebook-parser |
| **Configuración de la aplicación** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

### <a name="configure-webhooks"></a>Configuración de webhooks

1. Inicie sesión en Workplace con credenciales de usuario administrador.
1. En el panel Administración, seleccione **Integraciones**.
1. En la vista **All integrations** (Todas las integraciones), seleccione **Create custom integration** (Crear integración personalizada).
1. Escriba el nombre y la descripción, y seleccione **Create** (Crear).
1. En el panel **Integration details** (Detalles de integración), muestre el **App secret** (Secreto de la aplicación) y cópielo.
1. En el panel **Integration permissions** (Permisos de integración), establezca todos los permisos de lectura. Consulte la [página de permisos](https://developers.facebook.com/docs/workplace/reference/permissions) para obtener más información.

### <a name="add-callback-url-to-webhook-configuration"></a>Adición de la dirección URL de devolución de llamada a la configuración de webhook

1. Abra la página de la aplicación de funciones, vaya a la lista **Funciones**, seleccione **Obtener la dirección URL de la función** y cópiela.
1. Vuelva a **Workplace from Facebook**. En el panel **Configure webhooks** (Configurar webhooks), en cada pestaña, establezca **Callback URL** (Dirección URL de devolución de llamada) en la dirección URL de la función que ha copiado en el último paso y **Verify token** (Comprobar token) en el mismo valor que ha recibido durante la implementación automática o que ha escrito durante la implementación manual.
1. Seleccione **Guardar**.

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (versión preliminar)

El conector de datos de Zimperium Mobile Threat Defense conecta el registro de amenazas de Zimperium a Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. Este conector le ofrece más conclusiones sobre el panorama de amenazas móviles de la organización y mejora las capacidades de las operaciones de seguridad. Para obtener más instrucciones, vea.

Para obtener más información sobre cómo conectarse a Azure Sentinel, vea [Conexión de Zimperium a Azure Sentinel](#zimperium-mobile-thread-defense-preview).

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Data Collector API de Azure Sentinel**](connect-rest-api-template.md)<br><br>[Configuración y conexión de Zimperium MTD](#configure-and-connect-zimperium-mtd) |
| **Tabla de Log Analytics** | ZimperiumThreatLog_CL<br>ZimperiumMitigationLog_CL |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Portal del servicio de atención al cliente de Zimperium](https://support.zimperium.com/) (es necesario iniciar sesión) |
| **Compatible con** | [Zimperium](https://www.zimperium.com/support) |
| | |

### <a name="configure-and-connect-zimperium-mtd"></a>Configuración y conexión de Zimperium MTD

1. En zConsole, seleccione **Manage** (Administrar) en la barra de navegación.
1. Seleccione la pestaña **Integrations** (Integraciones).
1. Seleccione el botón **Threat Reporting** (Informes de amenazas) y después el botón **Add Integrations** (Agregar integraciones).
1. Cree la integración:
    1. En las integraciones disponibles, seleccione **Microsoft Azure Sentinel**.
    1. Escriba el *identificador de área de trabajo* y la *clave principal*, y seleccione **Next** (Siguiente).
    1. Rellene un nombre para la integración de Azure Sentinel.
    1. Seleccione un **Filter Level** (Nivel de filtro) para los datos de amenaza que quiera enviar a Azure Sentinel.
    1. Seleccione **Finalizar**.

## <a name="zoom-reports-preview"></a>Zoom Reports (versión preliminar)

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Azure Functions y la API REST**](connect-azure-functions-template.md) |
| **Tabla de Log Analytics** | Zoom_CL |
| **Código de la aplicación de funciones de Azure** | https://aka.ms/sentinel-ZoomAPI-functionapp |
| **Credenciales de API** | <li>ZoomApiKey<li>ZoomApiSecret |
| **Documentación del proveedor/<br>instrucciones de instalación** | <li>[Obtención de credenciales mediante JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **Instrucciones de implementación del conector** | <li>[Implementación de un solo clic](connect-azure-functions-template.md?tabs=ARM) mediante una plantilla de Azure Resource Manager (ARM)<li>[Implementación manual](connect-azure-functions-template.md?tabs=MPY) |
| **Alias de función de Kusto** | Zoom |
| **URL de la función Kusto/<br>Instrucciones de configuración del analizador** | https://aka.ms/sentinel-ZoomAPI-parser |
| **Configuración de la aplicación** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (opcional) |
| **Compatible con** | Microsoft |
| | |

## <a name="zscaler"></a>Zscaler

| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | **[Formato de evento común (CEF)](connect-common-event-format.md) sobre Syslog** |
| **Tabla de Log Analytics** | CommonSecurityLog |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Guía de implementación de Zscaler y Microsoft Azure Sentinel](https://aka.ms/ZscalerCEFInstructions) |
| **Compatible con** | [Zscaler](https://help.zscaler.com/submit-ticket-links) |
| | |


## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (versión preliminar)


| Atributo del conector | Descripción |
| --- | --- |
| **Método de ingesta de datos** | [**Agente de Log Analytics: registros personalizados**](connect-custom-logs.md)<br><br>[Configuración adicional para Zscaler Private Access](#extra-configuration-for-zscaler-private-access) |
| **Tabla de Log Analytics** | ZPA_CL |
| **Alias de función de Kusto:** | ZPAEvent |
| **URL de función de Kusto** | https://aka.ms/sentinel-zscalerprivateaccess-parser |
| **Documentación del proveedor/<br>instrucciones de instalación** | [Documentación de Zscaler Private Access](https://help.zscaler.com/zpa)<br>Además, vea lo siguiente |
| **Compatible con** | Microsoft |
| | |

### <a name="extra-configuration-for-zscaler-private-access"></a>Configuración adicional para Zscaler Private Access

Siga los pasos de configuración siguientes para obtener los registros de Zscaler Private Access en Azure Sentinel. Para obtener más detalles sobre estos pasos, consulte la [documentación de Azure Monitor](../azure-monitor/agents/data-sources-json.md). Los registros de Zscaler Private Access se entregan por medio de Log Streaming Service (LSS). Para obtener más información, consulte la [documentación de LSS](https://help.zscaler.com/zpa/about-log-streaming-service).

1. Configure [receptores de registro](https://help.zscaler.com/zpa/configuring-log-receiver). Al configurar un receptor de registro, elija **JSON** como **Log Template** (Plantilla de registro).
1. Descargue el archivo de configuración [zpa.conf](https://aka.ms/sentinel-zscalerprivateaccess-conf).

    ```bash
    wget -v https://aka.ms/sentinel-zscalerprivateaccess-conf -O zpa.conf
    ```

1. Inicie sesión en el servidor donde ha instalado el agente de Azure Log Analytics.
1. Copie zpa.conf en la carpeta /etc/opt/microsoft/omsagent/`workspace_id`/conf/omsagent.d/.
1. Edite zpa.conf como se muestra a continuación:
    1. Especifique el puerto que ha establecido para que los receptores de registro de Zscaler reenvíen los registros (línea 4)
    1. Reemplace `workspace_id` por el valor real del identificador del área de trabajo (líneas 14, 15, 16 y 19)
1. Guarde los cambios y reinicie el servicio del agente de Log Analytics para Linux con el siguiente comando:

    ```bash
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```
Puede encontrar el valor del identificador del área de trabajo en la página del conector ZScaler Private Access o en la página de administración de agentes del área de trabajo de Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Catálogo de soluciones de Azure Sentinel](sentinel-solutions-catalog.md)
- [Integración de inteligencia sobre amenazas en Azure Sentinel](threat-intelligence-integration.md)