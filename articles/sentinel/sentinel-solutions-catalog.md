---
title: Catálogo del centro de contenido de Azure Sentinel | Microsoft Docs
description: En este artículo se muestran los paquetes del centro de contenido de Azure Sentinel disponibles actualmente y se explican todos sus detalles.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/12/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: b7f0cc91f8705ac865cac4820402051dce1e9eff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064114"
---
# <a name="azure-sentinel-content-hub-catalog"></a>Catálogo del centro de contenido de Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> La experiencia del centro de contenido de Azure Sentinel está actualmente en **VERSIÓN PRELIMINAR**, al igual que todos los paquetes de soluciones individuales. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Las [soluciones de Azure Sentinel](sentinel-solutions.md) proporcionan una forma consolidada de adquirir contenido de Azure Sentinel (como conectores de datos, libros, análisis y automatización) en el área de trabajo con un solo paso de implementación.

En este artículo se muestran las soluciones y conectores de datos de Azure Sentinel integrados y disponibles a petición que se pueden implementar en el área de trabajo. Cuando se implementa una solución, todo el contenido de seguridad incluido (como conectores de datos, cuadernos de estrategias, libros o reglas) se integra en el área pertinente de Azure Sentinel. 

Para obtener más información, consulte [Detección e implementación central de soluciones y contenido integrados de Azure Sentinel](sentinel-solutions-deploy.md).

## <a name="domain-solutions"></a>Soluciones de dominio

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Administración de riesgos internos de Microsoft** |[Conector de datos](data-connectors-reference.md#microsoft-365-insider-risk-management-irm-preview), libro, reglas de análisis, consultas de búsqueda |Seguridad: amenaza de Insider | Microsoft|
|**Solución Microsoft MITRE ATT&CK para la nube**| Libros, reglas de análisis, consultas de búsqueda|Seguridad: protección contra amenazas, seguridad: otros |Microsoft |
|**Confianza cero** (TIC3.0) |Workbooks |Identidad, seguridad: otros |Microsoft |
| | | | |


## <a name="arista-networks"></a>Arista Networks

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Arista Networks** (Awake Security) |Conector de datos, libros, reglas de análisis | Seguridad: red |[Arista: Awake Security](https://awakesecurity.com/) |
| | | | |


## <a name="armorblox"></a>Armorblox

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Armorblox: Sentinel** |Conector de datos | Seguridad: protección contra amenazas |[Armorblox](https://www.armorblox.com/contact/) |
| | | | |




## <a name="azure"></a>Azure

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Azure Firewall para Sentinel**| [Conector de datos](data-connectors-reference.md#azure-firewall), libro, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, conector personalizado de aplicaciones lógicas |Seguridad: seguridad de red, redes | Comunidad|
|**Azure Sentinel para PaaS de SQL**     |  [Conector de datos](data-connectors-reference.md#azure-sql-databases), libro, reglas de análisis, cuadernos de estrategias, consultas de búsqueda     | Application        |      Comunidad   |
|**Azure Sentinel Training Lab** |Libro, reglas de análisis, cuadernos de estrategias, consultas de búsqueda | Entrenamiento y tutoriales |Microsoft |
|**SQL de Azure** | [Conector de datos](data-connectors-reference.md#azure-sql-databases), libro, análisis, cuadernos de estrategias, consultas de búsqueda  | Application |Microsoft  |
| | | | |


## <a name="box"></a>Box

|Nombre   |Includes  |Categorías |Compatible con  |
|------------------|---------|---------|---------|
|**Solución Box**| Conector de datos, libro, reglas de análisis, consultas de búsqueda, analizador |  Almacenamiento, aplicación  | Microsoft|
| | | | |


## <a name="check-point"></a>Punto de comprobación

|Nombre   |Includes  |Categorías |Compatible con  |
|------------------|---------|---------|---------|
|**Soluciones de Azure Sentinel para Check Point**   |[Conector de datos](data-connectors-reference.md#check-point), cuadernos de estrategias, conector personalizado de aplicaciones lógicas  | Seguridad: Automatización (SOAR) | [Punto de control](https://www.checkpoint.com/support-services/contact-support/)|
| | | | |


## <a name="cisco"></a>Cisco

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Cisco ACI** |Conector de datos, analizador |Seguridad: red |Microsoft |
|**Cisco ASA** |[Conector de datos](data-connectors-reference.md#cisco-asa), cuadernos de estrategias, conector personalizado de aplicaciones lógicas |Seguridad: Automatización (SOAR) |Microsoft |
|**Cisco Duo Security** |Conector de datos, analizador | Identidad|Microsoft |
|**Cisco ISE**  |Conector de datos, libros, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizador, conector personalizado de aplicaciones lógicas |Redes, seguridad: otros | Microsoft |
|**Cisco Meraki** |[Conector de datos](data-connectors-reference.md#cisco-meraki-preview), cuadernos de estrategias, conector personalizado de aplicaciones lógicas |Seguridad: red |Microsoft |
|**Cisco Secure Email Gateway/ESA** |Conector de datos, analizador |Seguridad: protección contra amenazas |Microsoft |
|**Cisco StealthWatch** |Conector de datos, analizador |Seguridad: red | Microsoft|
|**Cisco Umbrella** |[Conector de datos](data-connectors-reference.md#cisco-umbrella-preview), libros, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizador, conector personalizado de aplicaciones lógicas |Seguridad: seguridad en la nube |Microsoft |
|**Cisco Web Security Appliance (WSA)** | Conector de datos, analizador|Seguridad: red |Microsoft |
| | | | |


## <a name="cloudflare"></a>Cloudflare


|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Cloudflare**|Conector de datos, libros, reglas de análisis, consultas de búsqueda, analizador| Seguridad: red, redes |Microsoft |
| | | | |


## <a name="contrast-security"></a>Seguridad de contraste



|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Contrast Protect Azure Sentinel**|Conector de datos, libros, reglas de análisis |Seguridad: protección contra amenazas |Microsoft  |
| | | | |

## <a name="crowdstrike"></a>Crowdstrike


|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución CrowdStrike Falcon Endpoint Protection**| Conector de datos, libros, reglas de análisis, cuadernos de estrategias, analizador| Seguridad: protección contra amenazas| Microsoft|
| | | | |

## <a name="digital-guardian"></a>Digital Guardian


|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Digital Guardian** |Conector de datos, analizador |Seguridad: Information Protection |Microsoft |
| | | |

## <a name="falconforce"></a>FalconForce

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Contenido FalconFriday: Falcon Friday** |Reglas de análisis |Comportamiento del usuario (UEBA), seguridad: amenaza de Insider | [FalconForce](https://www.falconforce.nl/en/)|
| | | |

## <a name="fireeye-nx-network-security"></a>FireEye NX (seguridad de red)

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**FireEye NX (seguridad de red)** |Conector de datos, analizador |Seguridad: red| Microsoft|
| | | |

## <a name="flare-systems-firework"></a>Flare Systems Firework

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Flare Systems Firework** |Conector de datos |Seguridad: protección contra amenazas |Microsoft|
| | | |

## <a name="forescout"></a>Forescout

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Forescout** |Conector de datos, analizador |Seguridad: red | Microsoft|
| | | |

## <a name="fortinet-fortigate"></a>Fortinet Fortigate

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Fortinet FortiGate** |[Conector de datos](data-connectors-reference.md#fortinet), cuadernos de estrategias, conector personalizado de aplicaciones lógicas|Seguridad: Automatización (SOAR) | Microsoft|
| | | |


## <a name="google"></a>Google

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución DNS de Google Cloud Platform** |Conector de datos, analizador |Proveedor de nube, redes |Microsoft |
|**Solución de supervisión en la nube de Google Cloud Platform**|Conector de datos, analizador |Proveedor de la nube | Microsoft|
|**Solución de administración de identidades y acceso de Google Cloud Platform**|Conector de datos, libro, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizador, conector personalizado de aplicaciones lógicas|Proveedor de nube, identidad |Microsoft |
| | | | |


## <a name="hyas"></a>HYAS

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**HYAS Insight para la Galería de soluciones de Azure Sentinel**| Playbooks| Seguridad: inteligencia sobre amenazas, seguridad: automatización (SOAR) |Microsoft |
| | | | |

## <a name="imperva"></a>Imperva

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Imperva Cloud WAF** (formalmente Imperva Incapsula)| [Conector de datos](data-connectors-reference.md#imperva-waf-gateway-preview), analizador| Seguridad: red | Microsoft|
| | | | |

## <a name="infoblox"></a>InfoBlox

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**InfoBlox Threat Defense/InfoBlox Cloud Data Connector**| [Conector de datos](data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview), libro, reglas de análisis| Seguridad: protección contra amenazas | Microsoft|
| | | | |


## <a name="ironnet"></a>IronNet

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**IronNet CyberSecurity Iron Defense: Azure Sentinel** | |Seguridad: red |Microsoft |
| | | |



## <a name="juniper"></a>Juniper

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Juniper IDP** |Conector de datos, analizador|Seguridad: red |Microsoft |
| | | | |


## <a name="kaspersky"></a>Kaspersky

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Kaspersky Anti-Virus** |Conector de datos, analizador   | Seguridad: protección contra amenazas|Microsoft |
| | | | |


## <a name="lookout"></a>Lookout

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Lookout Mobile Threat Defense para Azure Sentinel**| [Conector de datos](data-connectors-reference.md#lookout-mobile-threat-defense-preview)|Seguridad: red |[Lookout](https://www.lookout.com/support) |
| | | |

## <a name="mcafee"></a>McAfee

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución McAfee ePolicy Orchestrator**| Conector de datos, libro, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizador, conector personalizado de aplicaciones lógicas| Seguridad: protección contra amenazas| Microsoft |
|**McAfee Network Security Platform Solution** (Intrushield) + información de antivirus (T1 menos aplicaciones lógicas) |Conector de datos, libros, reglas de análisis, consultas de búsqueda, analizador |Seguridad: protección contra amenazas | Microsoft|
| | | | |

## <a name="microsoft"></a>Microsoft

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Azure Sentinel para Microsoft Dynamics 365**     |   [Conector de datos](data-connectors-reference.md#dynamics-365), libros, reglas de análisis, consultas de búsqueda |      Application   |Microsoft         |
|**Azure Sentinel para Teams**     | Conector de datos, reglas de análisis, cuadernos de estrategias, consultas de búsqueda      |   Application      |    Comunidad     |
| | | | |


## <a name="oracle"></a>Oracle


|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Oracle Cloud Infrastructure** |Conector de datos, analizador | Proveedor de la nube | Microsoft|
|**Solución Oracle Database Audit** | Conector de datos, libro, reglas de análisis, consultas de búsqueda, analizador| Application|Microsoft |
| | | | |

## <a name="palo-alto"></a>Palo Alto

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Palo Alto PAN-OS**|[Conector de datos](#palo-alto), cuadernos de estrategias, conector personalizado de aplicaciones lógicas |Seguridad: automatización (SOAR), seguridad: red |Microsoft |
|**Solución Palo Alto Prisma**|[Conector de datos](#palo-alto), libros, reglas de análisis, consultas de búsqueda, analizador |Seguridad: seguridad en la nube |Microsoft |
| | | | |

## <a name="ping-identity"></a>Identidad de ping

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución PingFederate** |Conector de datos, libros, reglas de análisis, consultas de búsqueda, analizador| Identidad|Microsoft |
| | | | |

## <a name="proofpoint"></a>Proofpoint

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Proofpoint POD** |[Conector de datos](data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview), libro, reglas de análisis, consultas de búsqueda, analizador| Seguridad: protección contra amenazas|Microsoft |
|**Solución Proofpoint TAP** | Libros, reglas de análisis, cuadernos de estrategias, conector personalizado de aplicaciones lógicas|Seguridad: automatización (SOAR), seguridad: protección contra amenazas |Microsoft |
| | | |

## <a name="qualys"></a>Qualys

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Qualys VM** |Libros, reglas de análisis |Seguridad: administración de vulnerabilidades |Microsoft |
| | | | |

## <a name="rapid7"></a>Rapid7

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución CloudAPI de Rapid7 InsightVM** |Conector de datos, analizador|Seguridad: administración de vulnerabilidades |Microsoft |
| | | | |

## <a name="reversinglabs"></a>ReversingLabs

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución TitaniumCloud File Enrichment de ReversingLabs**|Playbooks |Seguridad: inteligencia sobre amenazas |[ReversingLabs](https://support.reversinglabs.com/hc/en-us) |
| | | | |

## <a name="riskiq"></a>RiskIQ

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Cuadernos de estrategias de RiskIQ Security Intelligence**|Playbooks |Seguridad: inteligencia sobre amenazas, seguridad: automatización (SOAR) |[RiskIQ](https://www.riskiq.com/integrations/microsoft/) |
| | | | |

## <a name="rsa"></a>RSA

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**RSA SecurID** |Conector de datos, analizador |Seguridad: otros, identidad |Microsoft |
| | | |



## <a name="sap"></a>SAP

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Continuous Threat Monitoring para SAP**|[Conector de datos](sap-deploy-solution.md), [libros, reglas de análisis, listas de reproducción](sap-solution-security-content.md) | Application  |Comunidad |
| | | | |

## <a name="semperis"></a>Semperis

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Semperis**|Conector de datos, libros, reglas de análisis, analizador | Seguridad: protección contra amenazas, identidad  |[Semperis](https://www.semperis.com/contact-us/) |
| | | | |

## <a name="senserva-pro"></a>Senserva Pro

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Oferta de Senserva para Azure Sentinel** |Conector de datos, libros, reglas de análisis, consultas de búsqueda |Cumplimiento normativo |[Senserva](https://www.senserva.com/contact/) |
| | | | |


## <a name="sonrai-security"></a>Sonrai Security

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Sonrai Security: Azure Sentinel** |Conector de datos, libros, reglas de análisis   | Cumplimiento normativo|Sonrai Security |
| | | | |

## <a name="slack"></a>Slack

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Slack Audit**|Conector de datos, libros, reglas de análisis, consultas de búsqueda, analizador |Application| Microsoft|
| | | | |


## <a name="sophos"></a>Sophos

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Sophos Endpoint Protection** |Conector de datos, analizador| Seguridad: protección contra amenazas |Microsoft |
|**Solución Sophos XG Firewall**| Libros, reglas de análisis, analizador |Seguridad: red |Microsoft |
| | | | |


## <a name="symantec"></a>Symantec

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Symantec Endpoint**|Conector de datos, libro, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizador| Seguridad: protección contra amenazas|Microsoft |
|**Solución Symantec ProxySG**|Libros, reglas de análisis |Seguridad: red |Symantec |
| | | | |

## <a name="tenable"></a>Tenable

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Nessus Scanner de Tenable/Informes de máquina virtual de E/S para la nube**  | Conector de datos, analizador| Seguridad: administración de vulnerabilidades| Microsoft |
| | | | |


## <a name="trend-micro"></a>Trend Micro

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Trend Micro Apex One**  | Conector de datos, consultas de búsqueda, analizador| Seguridad: protección contra amenazas|Microsoft |
| | | | |




## <a name="ubiquiti"></a>Ubiquiti

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Ubiquiti UniFi**|Conector de datos, libros, reglas de análisis, consultas de búsqueda, analizador |Seguridad: red |Microsoft |
| | | | |


## <a name="varmour"></a>vArmour

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución vArmour Application Controller y Azure Sentinel**|Conector de datos, libro, reglas de análisis |Operaciones de TI |[vArmour](https://www.varmour.com/contact-us/) |
| | | | |

## <a name="vectra"></a>Vectra

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución Vectra Stream** |Conector de datos, consultas de búsqueda, analizador |Seguridad: red |Microsoft |
| | | |


## <a name="vmware"></a>VMware

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Solución VMware Carbon Black**|Libros, reglas de análisis| Seguridad: protección contra amenazas| Microsoft|
| | | | |

## <a name="zeek-network"></a>Red Zeek

|Nombre    |Includes  |Categorías |Compatible con  |
|---------|---------|---------|---------|
|**Corelight para Azure Sentinel**|Conector de datos, libros, reglas de análisis, consultas de búsqueda, analizador | Operaciones de TI, seguridad: red | [Red Zeek](https://support.corelight.com/)|
| | | | |


## <a name="next-steps"></a>Pasos siguientes

En este documento, no solo ha obtenido información sobre las soluciones de Azure Sentinel, sino que también ha aprendido a buscarlas e implementarlas.

- Más información sobre las [soluciones de Azure Sentinel](sentinel-solutions.md).
- [Búsqueda e implementación de soluciones de Azure Sentinel](sentinel-solutions-deploy.md).
