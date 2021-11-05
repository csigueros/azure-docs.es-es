---
title: Detección avanzada de ataques de varias fases en Azure Sentinel
description: Use la tecnología de fusión de Azure Sentinel para reducir el exceso de alertas y crear incidentes útiles basados en la detección avanzada de ataques de varias fases.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2a8a6167208e95a253a2826b47a871278e0f15dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083904"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Detección avanzada de ataques de varias fases en Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Algunas detecciones de Fusion (consulte las que se indican a continuación) se encuentran actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel usa Fusion, un motor de correlación basado en algoritmos de aprendizaje automático escalable, para detectar automáticamente ataques de varias fases (también conocidos como amenazas persistentes avanzadas o APT) al identificar combinaciones de comportamientos anómalos y de actividades sospechosas que se observan en diversas fases de la cadena de eliminación. A partir de estas detecciones, Azure Sentinel genera incidentes que, de otro modo, serían muy difíciles de detectar. Estos incidentes incluyen dos o más alertas o actividades. Por diseño, estos incidentes tienen poco volumen, alta fidelidad y alta gravedad.

Cuando se personaliza para adaptarla a su entorno, esta tecnología de detección no solo reduce las tasas de [falsos positivos](false-positives.md), sino que también puede detectar ataques con información limitada o que falta.

Dado que Fusion correlaciona varias señales de diferentes productos para detectar ataques avanzados en varias fases, las detecciones de Fusion correctas se presentan como **incidentes de Fusion** en la página **Incidentes** de Azure Sentinel y no como **alertas**, y se almacenan en la tabla *Incidentes* de **Registros** y no en la tabla *SecurityAlerts*.

### <a name="configure-fusion"></a>Configuración de Fusion

Fusion está habilitado de forma predeterminada en Azure Sentinel como una [regla de análisis](detect-threats-built-in.md#view-built-in-detections) denominada **Detección avanzada de ataques de varias fases.** Puede ver y cambiar el estado de la regla, configurar las señales de origen para que se incluyan en el modelo de ML de Fusion o excluir patrones de detección específicos que podrían no ser aplicables a su entorno desde la detección de Fusion. Aprenda a [configurar la regla de Fusion](configure-fusion-rules.md).

> [!NOTE]
> Actualmente, Azure Sentinel usa 30 días de datos del historial para entrenar los algoritmos de aprendizaje automático de Fusion. Estos datos siempre se cifran mediante las claves de Microsoft cuando pasan por la canalización de aprendizaje automático. Pero los datos de entrenamiento no se cifran mediante [claves administradas por el cliente (CMK)](customer-managed-keys.md) si CMK se ha habilitado en el área de trabajo de Azure Sentinel. Para deshabilitar Fusion, vaya a **Azure Sentinel** \> **Configuración** \> **Análisis \> Reglas activas**, haga clic con el botón derecho en la regla **Detección avanzada de ataques de varias fases** y seleccione **Deshabilitar**.

## <a name="fusion-for-emerging-threats"></a>Fusion para amenazas emergentes

> [!IMPORTANT]
>
> - La detección basada en Fusion para amenazas emergentes se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

El volumen de eventos de seguridad sigue creciendo y el ámbito y la sofisticación de los ataques no paran de crecer. Podemos definir los escenarios de ataque conocidos, pero ¿qué pasa con las amenazas emergentes y desconocidas del entorno?  

El motor de Fusion con tecnología de ML de Azure Sentinel puede ayudarle a encontrar las **amenazas emergentes y desconocidas** del entorno mediante la aplicación de **análisis extendido de ML** y la correlación de **un ámbito más amplio de señales anómalas** al mismo tiempo que reduce la fatiga por alertas. 

Los algoritmos de ML del motor de Fusion aprenden constantemente de los ataques existentes y aplican análisis en función de cómo piensan los analistas de seguridad. Por lo tanto, puede detectar amenazas no detectadas previamente de millones de comportamientos anómalos en la cadena de eliminación en todo el entorno, lo que le ayuda a estar un paso por delante de los atacantes.

**Fusion para amenazas emergentes** admite la recopilación y el análisis de datos de los orígenes siguientes:

- [Detección rápida de anomalías](soc-ml-anomalies.md)
- Alertas de productos de Microsoft:
    - Azure Active Directory Identity Protection
    - Azure Defender
    - Azure Defender para IoT
    - Microsoft 365 Defender
    - Microsoft Cloud App Security
    - Microsoft Defender para punto de conexión
    - Microsoft Defender for Identity
    - Microsoft Defender para Office 365
- [**Alertas de reglas de análisis programadas**](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections), tanto [integradas](detect-threats-built-in.md#scheduled) como [creadas por los analistas de seguridad](detect-threats-custom.md). Las reglas de análisis deben contener información sobre la cadena de terminación (táctica) y la asignación de entidades para que Fusion las pueda usar.

No es necesario que haya conectado *todos* los orígenes de datos enumerados anteriormente para que Fusion funcione para las amenazas emergentes. Sin embargo, cuantos más orígenes de datos haya conectado, mayor será la cobertura y más amenazas encontrará Fusion.

Cuando las correlaciones del motor de Fusion generan la detección de una amenaza emergente, se genera un incidente de gravedad alta denominado **Possible multistage attack activities detected by Fusion** (Posibles actividades de ataque de varias fases detectadas por Fusion) en la tabla de *incidentes* del área de trabajo de Azure Sentinel.

## <a name="fusion-for-ransomware"></a>Fusion para ransomware

El motor Fusion de Azure Sentinel genera un incidente cuando detecta varias alertas de tipos diferentes de los siguientes orígenes de datos y determina que pueden estar relacionadas con la actividad de ransomware:

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender para punto de conexión](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
- [Reglas de análisis programadas de Azure Sentinel](detect-threats-built-in.md#scheduled). Fusion solo tiene en cuenta las reglas de análisis programadas con información táctica y entidades asignadas.

Esos incidentes de Fusion se denominan **Se han detectado varias alertas posiblemente relacionadas con la actividad de ransomware** y se generan cuando se detectan alertas relevantes durante un período de tiempo específico. Además, están asociadas a las fases **Ejecución** y **Evasión defensiva** de un ataque.

Por ejemplo, Azure Sentinel generaría un incidente para posibles actividades de ransomware si se desencadenan las siguientes alertas en el mismo host en un período de tiempo específico:

| Alerta | Source | Gravedad |
| ----- | ------ | -------- |
| **Error de Windows y eventos de advertencia** | Reglas de análisis programadas de Azure Sentinel | Informational (Informativa) |
| **Se evitó el ransomware “GandCrab”** . | Azure Defender | medio |
| **Se detectó el malware “Emotet”** . | Microsoft Defender para punto de conexión | Informational (Informativa) |
| **Se detectó la puerta trasera "Tofsee"** . | Azure Defender | low |
| **Se detectó el malware “Parite”** . | Microsoft Defender para punto de conexión | Informational (Informativa) 

## <a name="scenario-based-fusion-detections"></a>Detecciones de Fusion basadas en escenarios

En la sección siguiente se enumeran los tipos de [ataques de varias fases basados en escenario](fusion-scenario-reference.md), agrupados por clasificación de amenazas, que Azure Sentinel detecta mediante el motor de correlación de Fusion.

Para habilitar estos escenarios de detección de ataques con tecnología de Fusion, sus orígenes de datos asociados deben ingerirse en el área de trabajo de Log Analytics. Seleccione los vínculos de la tabla siguiente para obtener información sobre cada escenario y sus orígenes de datos asociados.

> [!NOTE]
> Algunos de estos escenarios se encuentran en **versión preliminar**. Aparecerá indicado.

| Clasificación de amenazas  | Escenarios  |
| -------- | -------- |
| **Uso abusivo de recursos de proceso**      | <ul><li>(VERSIÓN PRELIMINAR) [Varias actividades de creación de VM *después* de un inicio de sesión sospechoso en Azure Active Directory](fusion-scenario-reference.md#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in) |
| **Acceso de credencial**           | <ul><li>(VERSIÓN PRELIMINAR) [Restablecimiento de varias contraseñas por parte del usuario *después* de un inicio de sesión sospechoso](fusion-scenario-reference.md#multiple-passwords-reset-by-user-following-suspicious-sign-in) <li>(VERSIÓN PRELIMINAR) [Inicio de sesión sospechoso que *coincide* con un inicio de sesión correcto en una VPN de Palo Alto <br>por IP con varios inicios de sesión en Azure AD con errores](fusion-scenario-reference.md#suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) |
| **Recolección de credenciales**       | <ul><li>[Ejecución de la herramienta de robo de credenciales malintencionada *después* de un inicio de sesión sospechoso](fusion-scenario-reference.md#malicious-credential-theft-tool-execution-following-suspicious-sign-in)    <li>[Sospecha de actividad de robo de credenciales *después* de un inicio de sesión sospechoso](fusion-scenario-reference.md#suspected-credential-theft-activity-following-suspicious-sign-in)      |
| **Minería de cifrado**               | <ul><li>[Actividad de minería de cifrado *después* del inicio de sesión sospechoso](fusion-scenario-reference.md#crypto-mining-activity-following-suspicious-sign-in)          |
| **Destrucción de datos**            | <ul><li>[Eliminación masiva de archivos *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#mass-file-deletion-following-suspicious-azure-ad-sign-in)     <li>(VERSIÓN PRELIMINAR) [Eliminación masiva de archivos *después* de un inicio de sesión correcto en Azure AD desde una <br>IP bloqueada por un dispositivo de firewall de Cisco](fusion-scenario-reference.md#mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)        <li>(VERSIÓN PRELIMINAR) [Eliminación masiva de archivos *después* de iniciar sesión correctamente en la VPN de Palo Alto <br>por IP con varios inicios de sesión en Azure AD con errores](fusion-scenario-reference.md#mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins)        <li>(VERSIÓN PRELIMINAR) [Actividad de eliminación de correo electrónico sospechosa *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in) |
| **Filtración de datos**           | <ul><li>(VERSIÓN PRELIMINAR) [Actividades de reenvío de correo *después* de nueva actividad en la cuenta de administrador que no se ha visto recientemente](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently)      <li>[Descarga masiva de archivos *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#mass-file-download-following-suspicious-azure-ad-sign-in)       <li>(VERSIÓN PRELIMINAR) [Descarga masiva de archivos *después* de un inicio de sesión correcto en Azure AD desde una <br>IP bloqueada por un dispositivo de firewall de Cisco](fusion-scenario-reference.md#mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)       <li>(VERSIÓN PRELIMINAR) [Descarga masiva de archivos que *coincide con* la operación de archivos de SharePoint desde una dirección IP no vista previamente](fusion-scenario-reference.md#mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip)        <li>[Uso compartido de archivos masivo *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#mass-file-sharing-following-suspicious-azure-ad-sign-in)         <li>(VERSIÓN PRELIMINAR) [Varias actividades de uso compartido de informes de Power BI *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in)         <li>[Filtración de buzones de Office 365 *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in)        <li>(VERSIÓN PRELIMINAR) [Operación de archivo de SharePoint desde una dirección IP que no se ha visto previamente *después* de la detección de malware](fusion-scenario-reference.md#sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection)         <li>(VERSIÓN PRELIMINAR) [Establecimiento de reglas de manipulación sospechosa de la bandeja de entrada *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in)        <li>(VERSIÓN PRELIMINAR) [Uso compartido sospechoso de informes de Power BI *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in)  |
| **Denegación de servicio**           | <ul><li>(VERSIÓN PRELIMINAR) [Varias actividades de eliminación de VM *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in)          |
| **Desplazamiento lateral**            | <ul><li>[Suplantación de Office 365 *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#office-365-impersonation-following-suspicious-azure-ad-sign-in)      <li>(VERSIÓN PRELIMINAR) [Establecimiento de reglas de manipulación sospechosa de la bandeja de entrada *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in-1)        |
| **Actividad administrativa malintencionada**     | <ul><li>[Actividad de administración de aplicaciones en la nube sospechosa *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in)    <li>(VERSIÓN PRELIMINAR) [Actividades de reenvío de correo *después* de nueva actividad en la cuenta de administrador que no se ha visto recientemente](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently-1)          |
| **Ejecución malintencionada <br>con un proceso legítimo**    | <ul><li>(VERSIÓN PRELIMINAR) [PowerShell estableció una conexión de red sospechosa, *seguida por* <br>tráfico anómalo marcado por el firewall de Palo Alto Networks](fusion-scenario-reference.md#powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>(VERSIÓN PRELIMINAR) [Ejecución de WMI remota sospechosa *seguida por* <br>tráfico anómalo marcado por el firewall de Palo Alto Networks](fusion-scenario-reference.md#suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>[Línea de comandos de PowerShell sospechosa *después* del inicio de sesión sospechoso](fusion-scenario-reference.md#suspicious-powershell-command-line-following-suspicious-sign-in)          |
| **Comando y control (C2) o descarga de malware**      | <ul><li>(VERSIÓN PRELIMINAR) [Patrón de señal detectado por Fortinet después de varios inicios de sesión de usuario con error en un servicio](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service)     <li>(VERSIÓN PRELIMINAR) [Patrón de señal detectado por Fortinet *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in)       <li>(VERSIÓN PRELIMINAR) [Solicitud de red a un servicio de anonimización de TOR, *seguida por* <br>un tráfico anómalo marcado por el firewall de Palo Alto Networks](fusion-scenario-reference.md#network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)       <li>(VERSIÓN PRELIMINAR) [Conexión saliente a IP con un historial de intentos de acceso no autorizados, *seguida por* <br>tráfico anómalo marcado por el firewall de Palo Alto Networks](fusion-scenario-reference.md#outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)    |
| **Persistencia**                 | <ul><li>(VERSIÓN PRELIMINAR) [Consentimiento poco frecuente de la aplicación *después de* un inicio de sesión sospechoso](fusion-scenario-reference.md#rare-application-consent-following-suspicious-sign-in)         |
| **Ransomware**                  | <ul><li>[Ejecución de ransomware *después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#ransomware-execution-following-suspicious-azure-ad-sign-in)          |
| **Explotación remota**         | <ul><li>(VERSIÓN PRELIMINAR) [Sospecha de uso de un marco de ataque *seguido por* <br>tráfico anómalo marcado por el firewall de Palo Alto Networks](fusion-scenario-reference.md#suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)          |
| **Secuestro de recursos**          | <ul><li>(VERSIÓN PRELIMINAR) [Implementación sospechosa de un recurso o un grupo de recursos por parte de un autor de una llamada que nunca se había visto <br>*después* de un inicio de sesión sospechoso en Azure AD](fusion-scenario-reference.md#suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in)          |
|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la detección avanzada de ataques de varias fases de Fusion:
- Obtenga más información sobre las [detecciones de ataques basadas en escenarios de Fusion](fusion-scenario-reference.md).
- Aprenda a [configurar las reglas de Fusion](configure-fusion-rules.md).

Ahora que conoce más detalles sobre la detección avanzada de ataques de varias fases, puede que le interese el siguiente inicio rápido para aprender a obtener visibilidad sobre sus datos y a detectar posibles amenazas: [Introducción a Azure Sentinel](get-visibility.md).

Si está preparado para investigar los incidentes que se han creado, consulte el siguiente tutorial: [Investigación de incidentes con Azure Sentinel](investigate-cases.md).
