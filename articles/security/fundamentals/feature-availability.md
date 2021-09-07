---
title: Disponibilidad de características en la nube de servicios de Azure para clientes de la Administración Pública de Estados Unidos
description: Incluya una lista de la disponibilidad de características para los servicios de seguridad de Azure, como Azure Sentinel para los clientes de la Administración Pública de Estados Unidos
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 08/15/2021
ms.openlocfilehash: 46e97cf7bb2fd3f54988f1643d047cc19e3fa82e
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195203"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Disponibilidad de las características en la nube para clientes de la Administración Pública de Estados Unidos

En este artículo se describe la disponibilidad de características en las nubes de Microsoft Azure y Azure Government para los siguientes servicios de seguridad:

- [Azure Security Center](#azure-security-center)
- [Azure Sentinel](#azure-sentinel)
- [Azure Defender para IoT](#azure-defender-for-iot)

> [!NOTE]
> Pronto se agregarán servicios de seguridad adicionales a este artículo.
> 

## <a name="azure-government"></a>Azure Government

Azure Government usa las mismas tecnologías subyacentes que Azure (a veces denominadas Azure Commercial o Azure Public), que incluye los componentes principales de la infraestructura como servicio (IaaS), la plataforma como servicio (PaaS) y el software como servicio (SaaS). Tanto Azure como Azure Government cuentan con controles de seguridad integrales y el compromiso de Microsoft para la protección de los datos de los clientes.

Azure Government es un entorno en la nube aislado físicamente que está dedicado a los gobiernos federales, estatales, locales y tribales de Estados Unidos, así como a sus asociados. Mientras que ambos entornos en la nube se evalúan y autorizan en el nivel de impacto FedRAMP High, Azure Government proporciona un nivel adicional de protección a los clientes a través de compromisos contractuales relacionados con el almacenamiento de los datos de clientes en Estados Unidos y la limitación del posible acceso a los sistemas que procesen los datos del cliente a personas seleccionadas de Estados Unidos. Estos compromisos pueden ser de interés para los clientes que usan la nube a fin de almacenar o procesar datos sujetos a las regulaciones de control de exportación de Estados Unidos, como EAR, ITAR y 10 CFR parte 810 del Departamento de Energía de Estados Unidos.

Para obtener más información acerca de Azure Government, consulte [¿Qué es Azure Government?](../../azure-government/documentation-government-welcome.md)

## <a name="microsoft-365-integration"></a>Integración de Microsoft 365

Las integraciones entre productos se basan en la interoperabilidad entre las plataformas de Azure y Office. Las ofertas hospedadas en el entorno de Azure son accesibles desde las plataformas Microsoft 365 Enterprise y Microsoft 365 Government. Office 365 y Office 365 GCC trabajan con Azure Active Directory (Azure AD) en Azure. Office 365 GCC High y Office 365 DoD se trabajan con Azure AD en Azure Government.

En el diagrama siguiente se muestra la jerarquía de nubes de Microsoft y cómo se relacionan entre sí.

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Integración de nube de Microsoft 365.":::

El entorno de Office 365 GCC ayuda a los clientes a cumplir los requisitos de la Administración Pública de Estados Unidos, incluidos FedRAMP High, CJIS y la publicación 1075 de IRS. Los entornos de Office 365 GCC High y DoD admiten a los clientes que necesitan cumplir con DoD IL4/5, DFARS 7012, NIST 800-171 e ITAR.

Para obtener más información acerca de los entornos de Office 365 para la Administración Pública de Estados Unidos, consulte:

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High y DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)


En las siguientes secciones se identifica cuándo un servicio tiene una integración con Microsoft 365 y la disponibilidad de características para Office 365 GCC, Office 365 GCC High y Office 365 DoD.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center es un sistema unificado de administración de seguridad de la infraestructura que fortalece la posición de seguridad de los centros de datos y proporciona una protección contra amenazas avanzada de todas las cargas de trabajo híbridas que se encuentran en la nube, ya sea que estén en Azure o no, así como también en el entorno local.

Para más información, consulte la [documentación del producto de Azure Security Center](../../security-center/security-center-introduction.md).

En la tabla siguiente se muestra la disponibilidad actual de características de Security Center en Azure y Azure Government.


| Característica o servicio                                                                                                                                                               | Azure          | Azure Government               |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **Características gratuitas de Security Center**                                                                                                                                             |                |                                |
| - [Exportación continua](../../security-center/continuous-export.md)                                                                                                             | GA             | GA                             |
| - [Automatización de flujos de trabajo](../../security-center/continuous-export.md)                                                                                                           | GA             | GA                             |
| - [Reglas de exención de recomendaciones](../../security-center/exempt-resource.md)                                                                                                  | Vista previa pública | No disponible                  | 
| - [Reglas de eliminación de alertas](../../security-center/alerts-suppression-rules.md)                                                                                                | GA             | GA                             | 
| - [Notificaciones de correo electrónico para alertas de seguridad](../../security-center/security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | 
| - [Aprovisionamiento automático de agentes y extensiones](../../security-center/security-center-enable-data-collection.md)                                                              | GA             | GA                             | 
| - [Inventario de recursos](../../security-center/asset-inventory.md)                                                                                                                 | GA             | GA                             | 
| - [Informes de libros de Azure Monitor en la galería de libros de Azure Security Center](../../security-center/custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | 
| **Planes y extensiones de Azure Defender**                                                                                                                                       |                |                                | 
| - [Azure Defender para servidores](../../security-center/defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | 
| - [Azure Defender para App Service](../../security-center/defender-for-app-service-introduction.md)                                                                            | GA             | No disponible                  | 
| - [Azure Defender para DNS](../../security-center/defender-for-dns-introduction.md)                                                                                            | GA             | No disponible                  | 
| - [Azure Defender para registros de contenedor](../../security-center/defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | Disponibilidad general             | GA  <sup>[2](#footnote2)</sup> | 
| - [Azure Defender para registros de contenedor que examinan imágenes en flujos de trabajo de CI/CD](../../security-center/defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | Vista previa pública | No disponible                  | 
| - [Azure Defender para Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | 
| - [Extensión de Azure Defender para clústeres de Kubernetes habilitados para Azure Arc](../../security-center/defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Vista previa pública | No disponible                  | 
| - [Azure Defender para servidores de Azure SQL Database](../../security-center/defender-for-sql-introduction.md)                                                                     | GA             | GA                             | 
| - [Azure Defender para servidores SQL en máquinas](../../security-center/defender-for-sql-introduction.md)                                                                        | GA             | GA                             |
| - [Azure Defender para bases de datos relacionales de código abierto](../../security-center/defender-for-databases-introduction.md)                                                         | GA             | No disponible                  |
| - [Azure Defender para Key Vault](../../security-center/defender-for-key-vault-introduction.md)                                                                                | GA             | No disponible                  |
| - [Azure Defender para Resource Manager](../../security-center/defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             |
| - [Azure Defender para Storage](../../security-center/defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             |
| - [Protección contra amenazas para Cosmos DB](../../security-center/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Vista previa pública | No disponible                  |
| - [Protección de cargas de trabajo de Kubernetes](../../security-center/kubernetes-workload-protections.md)                                                                                  | GA             | GA                             |
| - [Sincronización de alertas bidireccional con Sentinel](../../sentinel/connect-azure-security-center.md)                                                                          | Vista previa pública | No disponible                  | 
| **Azure Defender para características de servidores** <sup>[7](#footnote7)</sup>                                                                                                            |                |                                |
| - [Acceso de máquina virtual Just-In-Time](../../security-center/security-center-just-in-time.md)                                                                                             | GA             | GA                             |
| - [Supervisión de la integridad de los archivos](../../security-center/security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             |
| - [Controles de aplicaciones adaptables](../../security-center/security-center-adaptive-application.md)                                                                              | GA             | GA                             |
| - [Protección de red adaptable](../../security-center/security-center-adaptive-network-hardening.md)                                                                           | GA             | No disponible                  |
| - [Protección de hosts de Docker](../../security-center/harden-docker-hosts.md)                                                                                                       | GA             | GA                             |
| - [Evaluación integrada de vulnerabilidades para las máquinas](../../security-center/deploy-vulnerability-assessment-vm.md)                                                             | GA             | No disponible                  |
| - [Panel e informes de cumplimiento normativo](../../security-center/security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             |
| - [Implementación de Microsoft Defender para punto de conexión y licencia integrada](../../security-center/security-center-wdatp.md)                                                         | GA             | GA                             |
| - [Conexión de cuentas de AWS](../../security-center/quickstart-onboard-aws.md)                                                                                                      | GA             | No disponible                  |
| - [Conexión de cuentas de GCP](../../security-center/quickstart-onboard-gcp.md)                                                                                                      | GA             | No disponible                  |
|                                                                                                                                                                               |                |                                |

<sup> <a name="footnote1" /> </a> 1</sup> Parcialmente en disponibilidad general: la capacidad de deshabilitar los resultados específicos de los exámenes de vulnerabilidades está en versión preliminar pública.

<sup><a name="footnote2" /></a>2</sup> Los exámenes de vulnerabilidades de los registros de contenedor en Azure Gov solo se pueden realizar con la característica de examen en inserción.

<sup><a name="footnote3" /></a>3</sup> Requiere Azure Defender para registros de contenedor.

<sup><a name="footnote4" /></a>4</sup> Parcialmente en disponibilidad general: la compatibilidad con clústeres habilitados para Arc está en versión preliminar pública y no está disponible en Azure Government.

<sup><a name="footnote5" /></a>5</sup> Requiere Azure Defender para Kubernetes.

<sup><a name="footnote6" /></a> 6</sup> Parcialmente en disponibilidad general: algunas de las alertas de protección contra amenazas de Azure Defender para Storage están en versión preliminar pública.

<sup><a name="footnote7" /></a>7</sup> Todas estas características requieren [Azure Defender para servidores](../../security-center/defender-for-servers-introduction.md).

<sup><a name="footnote8" /></a>8</sup> Puede haber diferencias en los estándares ofrecidos por cada tipo de nube.

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel es una solución de administración de eventos de información de seguridad (SIEM) y respuesta automatizada de orquestación de seguridad (SOAR) que es escalable y nativa de la nube. Azure Sentinel ofrece análisis de seguridad inteligente e inteligencia frente a amenazas en toda la empresa, de forma que proporciona una única solución para la detección de alertas, la visibilidad de amenazas, la búsqueda proactiva y la respuesta a amenazas.

Para más información, consulte la [documentación del producto de Azure Sentinel](../../sentinel/overview.md).

En las siguientes tablas se muestra la disponibilidad de características de Azure Sentinel actuales en Azure y Azure Government.


| Característica | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Regalas de automatización](../../sentinel/automate-incident-handling-with-automation-rules.md) | Vista previa pública | No disponible |
|- [Traiga su propio aprendizaje automático (BYO-ML)](../../sentinel/bring-your-own-ml.md) | Vista previa pública | Vista previa pública |
| - [Vista de incidentes entre inquilinos o áreas de trabajo](../../sentinel/multiple-workspace-view.md) |Vista previa pública | Vista previa pública |
| - [Conclusiones sobre la entidad](../../sentinel/enable-entity-behavior-analytics.md) | Disponibilidad general | Vista previa pública |
| - [Fusion](../../sentinel/fusion.md)<br>Detección avanzada de ataques de varias fases <sup>[1](#footnote1)</sup> | GA | GA |
| - [Búsqueda](../../sentinel/hunting.md) | GA | GA |
|- [Cuadernos](../../sentinel/notebooks.md) | GA | GA |
|- [Métricas de auditoría de incidentes de centro de operaciones de seguridad](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [Listas de reproducción](../../sentinel/watchlists.md) | GA | GA |
| **Compatibilidad con inteligencia sobre amenazas** | | |
| - [Inteligencia sobre amenazas: conector de datos TAXII](../../sentinel/understand-threat-intelligence.md)  | Vista previa pública | No disponible |
| - [Conector de datos de la plataforma de inteligencia sobre amenazas](../../sentinel/understand-threat-intelligence.md)  | Vista previa pública | No disponible |
| - [Hoja de investigación de la inteligencia sobre amenazas](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | Vista previa pública | No disponible |
| - [Detonación de direcciones URL](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | Vista previa pública | No disponible |
| - [Libro de inteligencia sobre amenazas](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | No disponible |
|**Compatibilidad con la detección** | | |
| - [Detección de acceso anómalo al recurso compartido de archivos de Windows](../../sentinel/fusion.md)  | Vista previa pública | No disponible |
| - [Detección de inicios de sesión anómalos mediante RDP](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>Detección de ML integrada | Vista previa pública | No disponible |
| - [Detección de inicios de sesión anómalos mediante SSH](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Detección de ML integrada | Vista previa pública | No disponible |
| **Conectores de servicio de Azure** |  |  |
| - [Registros de actividad de Azure](../../sentinel/connect-azure-activity.md) | GA | GA |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | GA | GA |
| - [Azure ADIP](../../sentinel/connect-azure-ad-identity-protection.md) | GA | GA |
| - [Azure DDoS Protection](../../sentinel/connect-azure-ddos-protection.md) | GA | GA |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md) | GA | GA |
| - [Azure Defender para IoT](../../sentinel/connect-asc-iot.md) | Vista previa pública | No disponible |
| - [Azure Firewall ](../../sentinel/connect-azure-firewall.md) | GA | GA |
| - [Azure Information Protection](../../sentinel/connect-azure-information-protection.md) | Vista previa pública | No disponible |
| - [Azure Key Vault ](../../sentinel/connect-azure-key-vault.md) | Vista previa pública | No disponible |
| - [Azure Kubernetes Services (AKS)](../../sentinel/connect-azure-kubernetes-service.md) | Vista previa pública | No disponible |
| - [Instancias de Azure SQL Database](../../sentinel/connect-azure-sql-logs.md) | GA | GA |
| - [Firewall de aplicaciones web de Azure](../../sentinel/connect-azure-waf.md) | GA | GA |
| **Conectores de Windows** |  |  |
| - [Firewall de Windows](../../sentinel/connect-windows-firewall.md) | GA | GA |
| - [Eventos de seguridad de Windows](../../sentinel/connect-windows-security-events.md) | GA | GA |
| **Conectores externos** |  |  |
| - [Agari Phishing Defense y Agari Brand Protection](../../sentinel/connect-agari-phishing-defense.md) | Vista previa pública | Vista previa pública |
| - [Analista de IA de Darktrace](../../sentinel/connect-data-sources.md) | Vista previa pública | Vista previa pública |
| - [Detección de IA Vectra](../../sentinel/connect-ai-vectra-detect.md) | Vista previa pública | Vista previa pública |
| - [Akamai Security Events](../../sentinel/connect-akamai-security-events.md) | Vista previa pública | Vista previa pública |
| - [Alcide kAudit](../../sentinel/connect-alcide-kaudit.md) | Vista previa pública | No disponible |
| - [Alsid para Active Directory](../../sentinel/connect-alsid-active-directory.md) | Vista previa pública | No disponible |
| - [Servidor HTTP de Apache](../../sentinel/connect-apache-http-server.md) | Vista previa pública | No disponible |
| - [Aruba ClearPass](../../sentinel/connect-aruba-clearpass.md) | Vista previa pública | Vista previa pública |
| - [AWS](../../sentinel/connect-data-sources.md) | GA | GA |
| - [Firewall de Barracuda CloudGen](../../sentinel/connect-barracuda-cloudgen-firewall.md) | GA | GA |
| - [Firewall de aplicación web de Barracuda](../../sentinel/connect-barracuda.md) | GA | GA |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/connect-better-mtd.md) | Vista previa pública | No disponible |
| - [Beyond Security beSECURE](../../sentinel/connect-besecure.md) | Vista previa pública | No disponible |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | Vista previa pública | Vista previa pública |
| - [DLP de Broadcom Symantec](../../sentinel/connect-broadcom-symantec-dlp.md) | Vista previa pública | Vista previa pública |
| - [Check Point](../../sentinel/connect-checkpoint.md) | GA | GA |
| - [Cisco ASA](../../sentinel/connect-cisco.md) | GA | GA |
| - [Cisco Meraki](../../sentinel/connect-cisco-meraki.md) | Vista previa pública | Vista previa pública |
| - [Cisco Umbrella](../../sentinel/connect-cisco-umbrella.md) | Vista previa pública | Vista previa pública |
| - [Cisco UCS](../../sentinel/connect-cisco-ucs.md) | Vista previa pública | Vista previa pública |
| - [Cisco Firepower eStreamer](../../sentinel/connect-data-sources.md) | Vista previa pública | Vista previa pública |
| - [Citrix Analytics WAF](../../sentinel/connect-citrix-waf.md) | GA | GA |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md) | GA | GA |
| - [Eventos de CyberArk Enterprise Password Vault (EPV)](../../sentinel/connect-cyberark.md) | Vista previa pública | Vista previa pública |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | Vista previa pública | No disponible      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | Vista previa pública | No disponible      |
| - [ExtraHop Reveal(x)](../../sentinel/connect-extrahop.md)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/connect-f5-big-ip.md)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/connect-f5.md)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | Vista previa pública | Vista previa pública |
| - [Forcepoint CASB](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | Vista previa pública | Vista previa pública |
| - [Forcepoint DLP](../../sentinel/connect-forcepoint-dlp.md)                                   | Vista previa pública | No disponible      |
| - [Common Audit de ForgeRock para CEF](../../sentinel/connect-data-sources.md)                  | Vista previa pública | Vista previa pública |
| - [Fortinet](../../sentinel/connect-fortinet.md)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](../../sentinel/connect-google-workspace.md)                      | Vista previa pública | No disponible      |
| - [Illusive Attack Management System](../../sentinel/connect-illusive-attack-management-system.md)                | Vista previa pública | Vista previa pública |
| - [Imperva WAF Gateway](../../sentinel/connect-imperva-waf-gateway.md)                             | Vista previa pública | Vista previa pública |
| - [Infoblox NIOS](../../sentinel/connect-infoblox.md)                                    | Vista previa pública | Vista previa pública |
| - [Juniper SRX](../../sentinel/connect-juniper-srx.md)                                      | Vista previa pública | Vista previa pública |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | Vista previa pública | Vista previa pública |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | Vista previa pública | Vista previa pública |
| - [NXLog DNS para Windows](../../sentinel/connect-nxlog-dns.md)                                             | Vista previa pública | No disponible      |
| - [NXLog LinuxAudit](../../sentinel/connect-nxlog-linuxaudit.md)                                 | Vista previa pública | No disponible      |
| - [Inicio de sesión único de Okta](../../sentinel/connect-okta-single-sign-on.md)                              | Vista previa pública | Vista previa pública |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | Vista previa pública | Vista previa pública |
| - [One Identity Safeguard](../../sentinel/connect-one-identity.md)                          | GA             | GA             |
| - [Alertas de Orca Security](../../sentinel/connect-orca-security-alerts.md)                            | Vista previa pública | No disponible      |
| - [Palo Alto Networks](../../sentinel/connect-paloalto.md)                               | GA             | GA             |
| - [Registros de actividad de Perimeter 81](../../sentinel/connect-perimeter-81-logs.md)                      | GA             | No disponible      |
| - [Proofpoint On Demand Email Security](../../sentinel/connect-proofpoint-pod.md)             | Vista previa pública | No disponible      |
| - [Proofpoint TAP](../../sentinel/connect-proofpoint-tap.md)                                   | Vista previa pública | Vista previa pública |
| - [Pulse Connect Secure](../../sentinel/connect-proofpoint-tap.md)                             | Vista previa pública | Vista previa pública |
| - [Qualys Vulnerability Management](../../sentinel/connect-qualys-vm.md)                  | Vista previa pública | Vista previa pública |
| - [Salesforce Service Cloud](../../sentinel/connect-salesforce-service-cloud.md)                         | Vista previa pública | No disponible      |
| - [SonicWall Firewall ](../../sentinel/connect-sophos-cloud-optix.md)                              | Vista previa pública | Vista previa pública |
| - [Sophos Cloud Optix](../../sentinel/connect-sophos-cloud-optix.md)                               | Vista previa pública | No disponible      |
| - [Sophos XG Firewall](../../sentinel/connect-sophos-xg-firewall.md)                               | Vista previa pública | Vista previa pública |
| - [secRMM de Squadra Technologies](../../sentinel/connect-squadra-secrmm.md)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/connect-squid-proxy.md)                                      | Vista previa pública | No disponible      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/connect-symantec.md)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/connect-symantec-proxy-sg.md)                                | Vista previa pública | Vista previa pública |
| - [Symantec VIP](../../sentinel/connect-symantec-vip.md)                                     | Vista previa pública | Vista previa pública |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [Plataformas de inteligencia sobre amenazas](../../sentinel/connect-threat-intelligence-tip.md)                   | Vista previa pública | No disponible      |
| - [Inteligencia sobre amenazas de TAXII](../../sentinel/connect-threat-intelligence-tip.md)                       | Vista previa pública | No disponible      |
| - [Thycotic Secret Server](../../sentinel/connect-thycotic-secret-server.md)                          | Vista previa pública | Vista previa pública |
| - [Deep Security de Trend Micro](../../sentinel/connect-trend-micro.md)                       | GA             | GA             |
| - [TippingPoint de Trend Micro](../../sentinel/connect-trend-micro-tippingpoint.md)                         | Vista previa pública | Vista previa pública |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | Vista previa pública | No disponible      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/connect-vmware-carbon-black.md)           | Vista previa pública | Vista previa pública |
| - [VMware ESXi](../../sentinel/connect-vmware-esxi.md)                                      | Vista previa pública | Vista previa pública |
| - [WireX Network Forensics Platform](../../sentinel/connect-wirex-systems.md)                | Vista previa pública | Vista previa pública |
| - [Zimperium Mobile Threat Defense](../../sentinel/connect-zimperium-mtd.md)                  | Vista previa pública | No disponible      |
| - [Zscaler](../../sentinel/connect-zscaler.md)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> Las detecciones de SSH y RDP no son compatibles con las nubes soberanas porque la plataforma de aprendizaje automático de Databricks no está disponible.

### <a name="microsoft-365-data-connectors"></a>Conectores de datos de Microsoft 365

Office 365 GCC trabaja con Azure Active Directory (Azure AD) en Azure. Office 365 GCC High y Office 365 DoD se trabajan con Azure AD en Azure Government.

> [!TIP]
> Asegúrese de prestar atención al entorno de Azure para comprender dónde [es posible la interoperabilidad](#microsoft-365-integration). En la tabla siguiente, la interoperabilidad que *no* es posible está marcada con un guion (-) a fin de indicar que la compatibilidad no es pertinente.
>

| Conector | Azure | Azure Government |
|--|--|--|
| **[Dynamics 365](../../sentinel/connect-dynamics-365.md)** |  |  |
| - Office 365 GCC | Vista previa pública | - |
| - Office 365 GCC High | - | No disponible |
| - Office 365 DoD | - | No disponible |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | Vista previa pública | - |
| - Office 365 GCC High | - | No disponible |
| - Office 365 DoD | - | No disponible |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)** <br>Registros de Shadow IT |  |  |
| - Office 365 GCC | Vista previa pública | - |
| - Office 365 GCC High | - | Vista previa pública |
| - Office 365 DoD | - | Vista previa pública |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                  <br>Alertas |  |  |
| - Office 365 GCC | Vista previa pública | - |
| - Office 365 GCC High | - | Vista previa pública |
| - Office 365 DoD | - | Vista previa pública |
| **[Microsoft Defender para punto de conexión](../../sentinel/connect-microsoft-defender-advanced-threat-protection.md)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender for Identity](../../sentinel/connect-azure-atp.md)** |  |  |
| - Office 365 GCC | Vista previa pública | - |
| - Office 365 GCC High | - | No disponible |
| - Office 365 DoD | - | No disponible |
| **[Microsoft Defender para Office 365](../../sentinel/connect-office-365-advanced-threat-protection.md)** |  |  |
| - Office 365 GCC | Vista previa pública | - |
| - Office 365 GCC High | - | No disponible |
| - Office 365 DoD | - | No disponible |
| **[Office 365](../../sentinel/connect-office-365.md)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|  |  |

## <a name="azure-defender-for-iot"></a>Azure Defender para IoT

Azure Defender para IoT le permite acelerar la innovación de IoT/OT con seguridad integral en todos los dispositivos de IoT/OT.Para las organizaciones de usuarios finales, Azure Defender para IoT ofrece una seguridad de capa de red sin agentes que se implementa rápidamente, funciona con diversos equipos industriales, e interopera con Azure Sentinel y otras herramientas de SOC. Se puede implementar de forma local o en entornos conectados a Azure.Para los fabricantes de dispositivos de IoT, los agentes de seguridad de Azure Defender para IoT permiten compilar la seguridad directamente en los nuevos dispositivos IoT y en proyectos de Azure IoT. El micro-agente tiene opciones de implementación flexibles, incluida la capacidad de implementarse como un paquete binario o de modificar código fuente. Y está disponible para los sistemas operativos de IoT estándar como Linux y Azure RTOS. Para más información, consulte la [documentación del producto de Azure Defender para IoT](../../defender-for-iot/index.yml).

En la tabla siguiente se muestra la disponibilidad actual de características de Azure Defender para IoT en Azure y Azure Government.

### <a name="for-organizations"></a>Para organizaciones

| Característica | Azure | Azure Government |
|--|--|--|
| [Inventario y detección de dispositivos locales](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | GA | GA |
| [Administración de vulnerabilidades](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | GA | GA |
| [Detección de amenazas con IoT, y análisis de comportamiento de OT](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | GA | GA |
| [Actualizaciones automáticas de inteligencia sobre amenazas](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | GA | GA |
| **Unificación de la seguridad de TI y OT con SIEM, SOAR y XDR** |  |  |
| - [Reenvío de la información de las alertas](../../defender-for-iot/how-to-forward-alert-information-to-partners.md) | GA | GA |
| - [Configuración de Sentinel con Azure Defender para IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | GA | No disponible |
| - [Sistemas SOC](../../defender-for-iot/integration-splunk.md) | GA | GA |
| - [Sistema de vales y CMDB (ServiceNow)](../../defender-for-iot/integration-servicenow.md) | GA | GA |
| - [Aprovisionamiento de sensores](../../defender-for-iot/how-to-manage-sensors-on-the-cloud.md) | GA | GA |

### <a name="for-device-builders"></a>Para fabricantes de dispositivos

| Característica | Azure | Azure Government |
|--|--|--|
| [Microagente para Azure RTOS](../../defender-for-iot/iot-security-azure-rtos.md) | GA | GA |
| - [Configuración de Sentinel con Azure Defender para IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | GA | No disponible |
| **Microagente independiente para Linux** |  |  |
| - [Introducción a los microagentes independientes](../../defender-for-iot/concept-standalone-micro-agent-overview.md) | Vista previa pública | Vista previa pública |
| - [Instalación binaria de agentes independientes](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | Vista previa pública | Vista previa pública |

## <a name="next-steps"></a>Pasos siguientes

- Información sobre el [modelo de responsabilidad compartida](shared-responsibility.md), las tareas de seguridad que administra el proveedor de nube y las tareas que administra el usuario.
- Información sobre las capacidades de la [nube de Azure Government](../../azure-government/documentation-government-welcome.md) y de la seguridad y el diseño de confianza que se usa para apoyar el cumplimiento aplicable a organizaciones locales, estatales, federales y sus asociados.
- Información sobre el [plan de Office 365 Administración Pública](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Información sobre el [cumplimiento en Azure](../../compliance/index.yml) de los estándares legales y normativos.
