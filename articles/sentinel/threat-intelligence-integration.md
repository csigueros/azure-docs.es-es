---
title: Integración de inteligencia sobre amenazas en Azure Sentinel | Microsoft Docs
description: Obtenga información sobre las distintas formas en que las fuentes de inteligencia sobre amenazas se integran y utilizan en Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2021
ms.author: yelevin
ms.openlocfilehash: 8c9206404294557f3f4a50d03ae550e407b92ed3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751791"
---
# <a name="threat-intelligence-integration-in-azure-sentinel"></a>Integración de inteligencia sobre amenazas en Azure Sentinel

Azure Sentinel proporciona varias maneras diferentes de [usar fuentes de inteligencia sobre amenazas](work-with-threat-indicators.md) para mejorar la capacidad de los analistas de seguridad de detectar y priorizar las amenazas conocidas. 

Ahora puede usar uno de los muchos productos de la [plataforma de inteligencia sobre amenazas integrada (TIP)](connect-threat-intelligence-tip.md) disponibles, [conectarse a servidores de TAXII](connect-threat-intelligence-taxii.md) para aprovechar cualquier origen de inteligencia sobre amenazas compatible con STIX y usar cualquier solución personalizada que pueda comunicarse directamente con la [API Security tiIndicators de Microsoft Graph](/graph/api/resources/tiindicator). 

También puede conectarse a orígenes de inteligencia sobre amenazas a partir de cuadernos de estrategias, con el fin de enriquecer los incidentes con información de TI que puede ayudar a dirigir acciones de investigación y respuesta.

> [!TIP]
> Si tiene varias áreas de trabajo en el mismo inquilino, como para los [proveedores de servicios administrados (MSSP)](mssp-protect-intellectual-property.md), puede ser más rentable conectar indicadores de amenazas solo al área de trabajo centralizada.
>
> Si tiene el mismo conjunto de indicadores de amenazas importados en cada área de trabajo independiente, puede ejecutar consultas entre áreas de trabajo para agregar indicadores de amenazas en las áreas de trabajo. Correlaciónelos en la experiencia de detección, investigación y búsqueda de incidentes de MSSP.
>

## <a name="taxii-threat-intelligence-feeds"></a>Fuentes de inteligencia sobre amenazas TAXII

Para conectarse a las fuentes de inteligencia sobre amenazas de TAXII, siga las instrucciones para [conectar Azure Sentinel a fuentes de inteligencia sobre amenazas de TAXII o STIX](connect-threat-intelligence-taxii.md), junto con los datos que proporciona cada proveedor vinculado a continuación. Es posible que tenga que ponerse en contacto directamente con el proveedor para obtener los datos necesarios para usarlos con el conector.

### <a name="anomali-limo"></a>Anomali Limo

- [Vea lo que necesita para conectarse a la fuente Anomali Limo](https://www.anomali.com/resources/limo).

### <a name="cybersixgill-darkfeed"></a>Cybersixgill Darkfeed

- [Más información sobre la integración de Cybersixgill con Azure Sentinel @Cybersixgill](https://www.cybersixgill.com/partners/azure-sentinel/)
- Para conectar Azure Sentinel a Cybersixgill TAXII Server y obtener acceso a Darkfeed, [póngase en contacto con Cybersixgill](mailto://azuresentinel@cybersixgill.com) para obtener la raíz de API, el identificador de colección, el nombre de usuario y la contraseña.

### <a name="financial-services-information-sharing-and-analysis-center-fs-isac"></a>Centro de Análisis e Intercambio de Información de Servicios Financieros (FS-ISAC)

- Únase a [FS-ISAC](https://www.fsisac.com/membership?utm_campaign=ThirdParty&utm_source=MSFT&utm_medium=ThreatFeed-Join) para obtener las credenciales para acceder a esta fuente.

### <a name="health-intelligence-sharing-community-h-isac"></a>Comunidad de intercambio de inteligencia sanitaria (H-ISAC)

- [Únase a H-ISAC](https://h-isac.org/soltra/) para obtener las credenciales para acceder a esta fuente.

### <a name="ibm-x-force"></a>IBM X-Force

- [Más información sobre la integración de IBM X-Force](https://www.ibm.com/security/xforce)

### <a name="intsights"></a>IntSights

- [Obtenga más información sobre la integración de IntSights con Azure Sentinel @IntSights](https://intsights.com/resources/intsights-microsoft-azure-sentinel)
- Para conectar Azure Sentinel al servidor TAXII de IntSights, obtenga la raíz de la API, el identificador de colección, el nombre de usuario y la contraseña del portal de IntSights después de configurar una directiva de los datos que desea enviar a Azure Sentinel.

### <a name="threatconnect"></a>ThreatConnect

- [Más información sobre STIX y TAXII @ThreatConnect](https://threatconnect.com/stix-taxii/)
- [Documentación de servicios de TAXII @ThreatConnect](https://docs.threatconnect.com/en/latest/rest_api/taxii/taxii.html)

## <a name="integrated-threat-intelligence-platform-products"></a>Productos de la plataforma de inteligencia sobre amenazas integrada

Para conectarse a fuentes de la plataforma de inteligencia sobre amenazas (TIP), siga las instrucciones para [conectar las plataformas de inteligencia sobre amenazas a Azure Sentinel](connect-threat-intelligence-tip.md). En la segunda parte de estas instrucciones se le pedirá que escriba información en la solución TIP. Para más información, consulte los vínculos que se muestran a continuación.

### <a name="agari-phishing-defense-and-brand-protection"></a>Agari Phishing Defense y Agari Brand Protection

- Para conectar [Agari Phishing Defense y Brand Protection](https://agari.com/products/phishing-defense/), use el [conector de datos de Agari](connect-agari-phishing-defense.md) integrado en Azure Sentinel.

### <a name="anomali-threatstream"></a>Anomali ThreatStream

- Para descargar [ThreatStream Integrator y sus extensiones](https://www.anomali.com/products/threatstream), así como las instrucciones para conectar ThreatStream Intelligence a la API de Microsoft Graph Security, consulte la página de [descargas de ThreatStream](https://ui.threatstream.com/downloads).

### <a name="alienvault-open-threat-exchange-otx-from-att-cybersecurity"></a>AlienVault Open Threat Exchange (OTX) de AT&T Cybersecurity

- [AlienVault OTX](https://otx.alienvault.com/) usa Azure Logic Apps (cuadernos de estrategias) para conectarse a Azure Sentinel. Consulte las [instrucciones especializadas](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) necesarias para aprovechar al máximo la oferta completa.

### <a name="eclecticiq-platform"></a>Plataforma EclecticIQ

- La plataforma EclecticIQ se integra con Azure Sentinel para mejorar la detección y la búsqueda de amenazas, así como la respuesta ante ellas. Obtenga más información sobre [las ventajas y los casos de uso](https://www.eclecticiq.com/resources/azure-sentinel-and-eclecticiq-intelligence-center) de esta integración bidireccional.

### <a name="groupib-threat-intelligence-and-attribution"></a>GroupIB Threat Intelligence & Attribution

- Para conectar [GroupIB Threat Intelligence & Attribution](https://www.group-ib.com/intelligence-attribution.html) a Azure Sentinel, GroupIB usa Azure Logic Apps. Consulte las [instrucciones especializadas](https://techcommunity.microsoft.com/t5/azure-sentinel/group-ib-threat-intelligence-and-attribution-connector-azure/ba-p/2252904) necesarias para aprovechar al máximo la oferta completa.

### <a name="misp-open-source-threat-intelligence-platform"></a>Plataforma de inteligencia sobre amenazas de código abierto de MISP

- Para ver un script de ejemplo que proporciona a los clientes instancias de MISP para migrar indicadores de amenazas a Microsoft Graph Security API, consulte el artículo [MISP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP) (MISP para un script de Microsoft Graph Security).
- [Obtenga más información sobre el proyecto MISP](https://www.misp-project.org/).

### <a name="palo-alto-networks-minemeld"></a>Palo Alto Networks MineMeld

- Para configurar [Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld) con la información de conexión a Azure Sentinel, consulte el artículo sobre el [envío de indicadores de riesgo a la API de seguridad de Microsoft Graph mediante MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540) y vaya directamente al encabezado de **configuración de MineMeId**.

### <a name="recorded-future-security-intelligence-platform"></a>Plataforma de inteligencia de seguridad Recorded Future

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) usa Azure Logic Apps (cuadernos de estrategias) para conectarse a Azure Sentinel. Consulte las [instrucciones especializadas](https://go.recordedfuture.com/hubfs/partners/microsoft-azure-installation-guide.pdf) necesarias para aprovechar al máximo la oferta completa.

### <a name="threatconnect-platform"></a>Plataforma ThreatConnect

- Consulte la guía [Microsoft Graph Security Threat Indicators Integration Configuration Guide](https://training.threatconnect.com/learn/article/microsoft-graph-security-threat-indicators-integration-configuration-guide-kb-article) para obtener instrucciones para conectar [ThreatConnect](https://threatconnect.com/solution/) a Azure Sentinel.

### <a name="threatquotient-threat-intelligence-platform"></a>Plataforma de inteligencia sobre amenazas ThreatQ

- Consulte el artículo sobre la [integración de Microsoft Sentinel Connector para ThreatQ ](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=DetailsAndSupport) para obtener información de soporte técnico e instrucciones para conectar [ThreatQuotient TIP](https://www.threatq.com/) a Azure Sentinel.

## <a name="incident-enrichment-sources"></a>Orígenes de enriquecimiento de incidentes

Además de usarse para importar indicadores de amenazas, las fuentes de inteligencia sobre amenazas también pueden servir como origen para enriquecer la información de los incidentes y proporcionar más contexto para las investigaciones. Las fuentes siguientes sirven para este propósito y proporcionan cuadernos de estrategias de aplicación lógica para usarlos en la [respuesta a incidentes automatizada](automate-responses-with-playbooks.md).

### <a name="hyas-insight"></a>HYAS Insight

- Busque y habilite cuadernos de estrategias de enriquecimiento de incidentes de [HYAS Insight](https://www.hyas.com/hyas-insight) en el [repositorio de GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) de Azure Sentinel. Busque subcarpetas que comiencen por "Enrich-Sentinel-Incident-HYAS-Insight-".
- Consulte la [documentación del conector](/connectors/hyasinsight/) de aplicaciones lógicas de HYAS Insight.

### <a name="recorded-future-security-intelligence-platform"></a>Plataforma de inteligencia de seguridad Recorded Future

- Busque y habilite cuadernos de estrategias de enriquecimiento de incidentes de [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) en el [repositorio de GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) de Azure Sentinel. Busque subcarpetas que comiencen por "RecordedFuture_".
- Consulte la [documentación del conector](/connectors/recordedfuture/) de aplicaciones lógicas de Recorded Future.

### <a name="reversinglabs-titaniumcloud"></a>ReversingLabs TitaniumCloud

- Busque y habilite cuadernos de estrategias de enriquecimiento de incidentes de [ReversingLabs](https://www.reversinglabs.com/products/file-reputation-service) en el [repositorio de GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/ReversingLabs/Playbooks/Enrich-SentinelIncident-ReversingLabs-File-Information) de Azure Sentinel.
- Consulte la [documentación del conector](/connectors/reversinglabsintelligence/) de aplicaciones lógicas de ReversingLabs Intelligence.

### <a name="riskiq-passive-total"></a>RiskIQ PassiveTotal

- Busque y habilite cuadernos de estrategias de enriquecimiento de incidentes de [RiskIQ PassiveTotal](https://www.riskiq.com/products/passivetotal/) en el [repositorio de GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) de Azure Sentinel. Busque subcarpetas que comiencen por "Enrich-SentinelIncident-RiskIQ-".
- Consulte [más información](https://techcommunity.microsoft.com/t5/azure-sentinel/enrich-azure-sentinel-security-incidents-with-the-riskiq/ba-p/1534412) sobre cómo trabajar con cuadernos de estrategias de RiskIQ.
- Consulte la [documentación del conector](/connectors/riskiqpassivetotal/) de aplicaciones lógicas de RiskIQ PassiveTotal.

### <a name="virus-total"></a>VirusTotal

- Busque y habilite cuadernos de estrategias de enriquecimiento de incidentes de [VirusTotal](https://developers.virustotal.com/v3.0/reference) en el [repositorio de GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) de Azure Sentinel. Busque subcarpetas que comiencen por "Get-VirusTotal" y "Get-VTURL".
- Consulte la [documentación del conector](/connectors/virustotal/) de aplicaciones lógicas de VirusTotal.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar su proveedor de inteligencia sobre amenazas a Azure Sentinel. Para obtener más información sobre Azure Sentinel, consulte los siguientes artículos.

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
