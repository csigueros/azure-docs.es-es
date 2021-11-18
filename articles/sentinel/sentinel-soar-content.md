---
title: Catálogo de contenido SOAR de Azure Sentinel | Microsoft Docs
description: En este artículo se muestra y se detalla el contenido proporcionado por Azure Sentinel para la orquestación, automatización y respuesta de seguridad (SOAR), incluidos los cuadernos de estrategias y los conectores Logic Apps.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2021
ms.author: yelevin
ms.openlocfilehash: 28b5e0de931df0f84c2761babc9b0a85a7bbe9c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711724"
---
# <a name="azure-sentinel-soar-content-catalog"></a>Catálogo de contenido SOAR de Azure Sentinel

Azure Sentinel proporciona una amplia variedad de cuadernos de estrategias y conectores para la orquestación, automatización y respuesta de seguridad (SOAR), para que pueda integrar fácilmente Azure Sentinel con cualquier producto o servicio de su entorno.

Las integraciones enumeradas a continuación pueden incluir algunos o todos los componentes siguientes:

| Tipo de componente | Propósito | Caso de uso e instrucciones relacionadas |
| -------------- | ------- | -------------------------------- |
| **Plantillas de cuadernos de estrategias** | Flujo de trabajo automatizado | Use plantillas de cuadernos de estrategias para implementar cuadernos de estrategias listos para responder a las amenazas automáticamente.<br><br>[Automatización de la respuesta a amenazas con cuadernos de estrategias en Azure Sentinel](automate-responses-with-playbooks.md) |
| **Conectores administrados de Azure Logic Apps** | Bloques de creación para crear cuadernos de estrategias | Los cuadernos de estrategias usan conectores administrados para comunicarse con cientos de servicios, tanto de Microsoft como de otros fabricantes.<br><br>[Lista de todos los conectores de Logic Apps y su documentación](/connectors/connector-reference/) |
| **Creación de un conector personalizado para Azure Logic Apps** | Bloques de creación para crear cuadernos de estrategias | Es posible que desee comunicarse con servicios que no están disponibles como conectores previamente integrados. Para resolver esta necesidad, los conectores personalizados permiten crear (e incluso compartir) un conector y definir sus propios desencadenadores y acciones.<br><ul><li>[Introducción a los conectores personalizados](/connectors/custom-connectors/)<li>[Creación de un conector personalizado en Azure Logic Apps](/connectors/custom-connectors/create-logic-apps-connector) |
|

Puede encontrar integraciones de SOAR y sus componentes en los siguientes lugares:

- Soluciones de Azure Sentinel
- Hoja Azure Sentinel Automation, pestaña Plantillas de cuadernos de estrategias
- Diseñador de Logic Apps (para conectores Logic Apps administrados)
- Repositorio de GitHub de Azure Sentinel

> [!TIP]
> - Muchas integraciones SOAR se pueden implementar como parte de una [solución de Azure Sentinel](sentinel-solutions.md), junto con los conectores de datos, reglas de análisis y cuadernos de estrategias relacionados. Para obtener más información, vea [Catálogo de soluciones de Azure Sentinel](sentinel-solutions-catalog.md).
> - La comunidad de Azure Sentinel proporciona más integraciones y se pueden encontrar en el repositorio de GitHub.
> - Si tiene un producto o servicio que no aparezca en la lista o que no sea compatible en la actualidad, envíe una Solicitud de característica.  
> También puede crear las suyas propias, utilizando las siguientes herramientas:
>    - Conector personalizado de Logic Apps
>    - Azure Functions
>    - Llamadas HTTP de Logic Apps


## <a name="atlassian"></a>Atlassian

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Jira** | [Conector administrado de Logic Apps](/connectors/jira/)<br><br>Playbooks | Microsoft<br><br>comunidad | Sincronización de incidentes |
|

## <a name="check-point"></a>Punto de comprobación

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Check Point NGFW**<br>([Disponible como solución](sentinel-solutions-catalog.md#check-point)) | Conector personalizado Logic Apps<br><br>Playbooks | CheckPoint |  |
|

## <a name="cisco"></a>Cisco

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Cisco ASA,<br>Cisco Meraki** | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Bloquear direcciones IP |
| **Cisco FirePower** | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Bloquear direcciones IP y direcciones URL |
| **Cisco ISE**<br>([Disponible como solución](sentinel-solutions-catalog.md#cisco)) | Conector personalizado Logic Apps<br><br>Playbooks | Microsoft |  |
| **Cisco Umbrella**<br>([Disponible como solución](sentinel-solutions-catalog.md#cisco)) | Conector personalizado Logic Apps<br><br>Playbooks | Microsoft | Dominios de bloque, <br>administración de directivas, <br>administración de listas de destino, <br>enriquecimiento e investigación |
|

## <a name="crowdstrike"></a>Crowdstrike

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Protección de puntos de conexión Falcon**<br>([Disponible como solución](sentinel-solutions-catalog.md#crowdstrike)) | Playbooks | Microsoft | Enriquecimiento de puntos de conexión,<br>aislamiento de puntos de conexión |
|

## <a name="f5"></a>F5

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Big-IP** | Playbooks | comunidad | Bloquear direcciones IP y direcciones URL |
|

## <a name="forcepoint"></a>Forcepoint

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Forcepoint NGFW** | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Bloquear direcciones IP y direcciones URL |
|

## <a name="fortinet"></a>Fortinet

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **FortiGate**<br>([Disponible como solución](sentinel-solutions-catalog.md#fortinet-fortigate)) | Conector personalizado Logic Apps<br><br>Función de Azure<br><br>Playbooks | Microsoft | Bloquear direcciones IP y direcciones URL |
|

## <a name="freshdesk"></a>Freshdesk

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Freshdesk** | [Conector administrado de Logic Apps](/connectors/freshdesk/) |  | Sincronización de incidentes |
|


## <a name="have-i-been-pwned"></a>Have I Been Pwned

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Have I Been Pwned** | Conector personalizado Logic Apps<br><br>Playbooks | comunidad |  |
|

## <a name="hyas"></a>HYAS

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **HYAS Insight**<br>([Disponible como solución](sentinel-solutions-catalog.md#hyas)) | [Conector administrado de Logic Apps](/connectors/hyasinsight/)<br><br>Playbooks | HYAS |  |
|

## <a name="ibm"></a>IBM

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Resistente** | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Sincronización de incidentes |
|

## <a name="microsoft"></a>Microsoft

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Azure DevOps** | Conector administrado de Logic Apps<br><br>Playbooks | Microsoft<br><br>comunidad | Sincronización de incidentes |
| **Azure Firewall**<br>([Disponible como solución](sentinel-solutions-catalog.md#azure)) | Conector personalizado Logic Apps<br><br>Playbooks | Microsoft | Bloquear direcciones IP |
| **Azure AD Identity Protection** | [Conector administrado de Logic Apps](/connectors/azureadip/)<br><br>Playbooks | Microsoft<br><br>comunidad | Enriquecimiento de usuarios, <br>Corrección de usuarios |
| **Azure AD** | [Conector administrado de Logic Apps](/connectors/azuread/)<br><br>Playbooks | Microsoft<br><br>comunidad | Enriquecimiento de usuarios, <br>Corrección de usuarios |
| **Azure Data Explorer** | [Conector administrado de Logic Apps](/connectors/kusto/) | Microsoft | Consulta e investigación |
| **Azure Log Analytics Data Collector** | [Conector administrado de Logic Apps](/connectors/azureloganalyticsdatacollector/) | Microsoft<br><br>comunidad | Consulta e investigación |
| **Microsoft Defender para punto de conexión** | [Conector administrado de Logic Apps](/connectors/wdatp/)<br><br>Playbooks | Microsoft<br><br>comunidad | Enriquecimiento de puntos de conexión, <br>aislamiento de puntos de conexión |
| **Microsoft Teams** | [Conector administrado de Logic Apps](/connectors/teams/)<br><br>Playbooks | Microsoft<br><br>comunidad | Notificaciones, <br>Colaboración, <br>crear respuestas con implicación de usuarios |
|

## <a name="okta"></a>Okta

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Okta** | Conector administrado de Logic Apps<br><br>Playbooks | comunidad | Enriquecimiento de usuarios, <br>Corrección de usuarios |
|

## <a name="palo-alto"></a>Palo Alto

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Palo Alto PAN-OS**<br>([Disponible como solución](sentinel-solutions-catalog.md#palo-alto)) | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Bloquear direcciones IP y direcciones URL |
| **Wildfire** | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Enriquecimiento y respuesta de Filehash |
|

## <a name="proofpoint"></a>Proofpoint

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Proofpoint TAP**<br>([Disponible como solución](sentinel-solutions-catalog.md#proofpoint)) | Conector personalizado Logic Apps<br><br>Playbooks | Microsoft | Enriquecimiento de cuentas |
|

## <a name="recorded-future"></a>Futuro registrado

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Inteligencia futura registrada** | [Conector administrado de Logic Apps](/connectors/recordedfuture/)<br><br>Playbooks | Futuro registrado | Enriquecimiento de entidades |
|

## <a name="reversinglabs"></a>ReversingLabs

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Enriquecimiento de archivos TitaniumCloud**<br>([Disponible como solución](sentinel-solutions-catalog.md#reversinglabs)) | [Conector administrado de Logic Apps](/connectors/reversinglabsintelligence/)<br><br>Playbooks | ReversingLabs | Enriquecimiento de FileHash |
|

## <a name="riskiq"></a>RiskIQ

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Huella digital de RiskIQ**<br>([Disponible como solución](sentinel-solutions-catalog.md#riskiq)) | [Conector administrado de Logic Apps](/connectors/riskiqdigitalfootprint/)<br><br>Playbooks | RiskIQ | Enriquecimiento de entidades |
| **RiskIQ PassiveTotal** | [Conector administrado de Logic Apps](/connectors/riskiqpassivetotal/)<br><br>Playbooks | RiskIQ | Enriquecimiento de entidades |
| **Inteligencia de seguridad RiskIQ**<br>([Disponible como solución](sentinel-solutions-catalog.md#riskiq)) | [Conector administrado de Logic Apps](/connectors/riskiqintelligence/)<br><br>Playbooks | RiskIQ | Enriquecimiento de entidades |
|

## <a name="servicenow"></a>ServiceNow

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **ServiceNow** | [Conector administrado de Logic Apps](/connectors/service-now/)<br><br>Playbooks | Microsoft<br><br>comunidad | Sincronización de incidentes |
|

## <a name="slack"></a>Slack

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Slack** | [Conector administrado de Logic Apps](/connectors/slack/)<br><br>Playbooks | Microsoft<br><br>comunidad | Notificación, <br>Colaboración |
|

## <a name="virus-total"></a>VirusTotal

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **VirusTotal** | [Conector administrado de Logic Apps](/connectors/virustotal/)<br><br>Playbooks | Microsoft<br><br>comunidad | Enriquecimiento de entidades |
|

## <a name="vmware"></a>VMware

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Carbon Black Cloud**<br>([Disponible como solución](sentinel-solutions-catalog.md#vmware)) | Conector personalizado Logic Apps<br><br>Playbooks | comunidad | Enriquecimiento de puntos de conexión, <br>aislamiento de puntos de conexión |
|

## <a name="zendesk"></a>Zendesk

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Zendesk** | [Conector administrado de Logic Apps](/connectors/zendesk/)<br><br>Playbooks | Microsoft<br><br>comunidad | Sincronización de incidentes |
|

## <a name="zscaler"></a>Zscaler

| Producto | Componentes de integración | Compatible con | Escenarios |
| --- | --- | --- | --- |
| **Zscaler** | Playbooks | Microsoft | Corrección de direcciones URL, <br>Enriquecimiento de incidentes |
|

## <a name="next-steps"></a>Pasos siguientes

En este documento, no solo ha obtenido información sobre las soluciones de Azure Sentinel, sino que también ha aprendido a buscarlas e implementarlas.

- Más información sobre las [soluciones de Azure Sentinel](sentinel-solutions.md).
- [Búsqueda e implementación de soluciones de Azure Sentinel](sentinel-solutions-deploy.md).
