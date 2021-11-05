---
title: Hoja de ruta de preparación de Defender for Cloud | Microsoft Docs
description: En este documento se proporciona un mapa de ruta de preparación para el desarrollo de Defender for Cloud.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 345b1373371cc8643cb6d9c82e8a98e2eee42def
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457152"
---
# <a name="defender-for-cloud-readiness-roadmap"></a>Hoja de ruta de preparación de Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Este documento le proporciona un mapa de ruta de preparación que le ayudará a empezar a trabajar con Defender for Cloud.

## <a name="understanding-defender-for-cloud"></a>Descripción de Defender for Cloud
Defender for Cloud ofrece una administración de seguridad unificada y una protección contra amenazas avanzada para las cargas de trabajo que se ejecutan en Azure, de forma local y en otras nubes. 

Use los siguientes recursos para empezar a trabajar con Defender for Cloud.

Artículos
- [Introducción a Defender for Cloud](defender-for-cloud-introduction.md)
- [Guía de inicio rápido de Defender for Cloud](get-started.md)

Vídeos
- [Vídeo de presentación rápida](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Introducción a las funcionalidades de prevención, detección y respuesta de Defender for Cloud](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planeamiento y operaciones

Para sacar el máximo partido a Defender for Cloud, es importante comprender cómo distintas personas o equipos de su organización usarán el servicio para satisfacer las necesidades relativas a las operaciones seguras, la supervisión, la gobernanza y la respuesta a incidentes.

Use los siguientes recursos como ayuda durante los procesos de planeamiento y de operaciones.

- [Guía de planeamiento y operaciones de Defender for Cloud](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-defender-for-cloud"></a>Incorporación de equipos a Defender for Cloud
Defender for Cloud detecta automáticamente las suscripciones o áreas de trabajo de Azure que no están protegidas por Azure Defender. Aquí se incluyen las suscripciones de Azure que usan Defender for Cloud gratis y las áreas de trabajo que no tiene la solución de seguridad habilitada.

Use los siguientes recursos como ayuda durante los procesos de incorporación.

- [Incorporar equipos que no son de Azure](quickstart-onboard-machines.md)
- [Defender for Cloud híbrido: información general](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-defender-for-cloud"></a>Mitigación de problemas de seguridad mediante Defender for Cloud
Defender for Cloud recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos.

Utilice los siguientes recursos como ayuda para administrar las alertas de seguridad y proteger los recursos.

Artículos    
- [Protección de la red en Defender for Cloud](./protect-network-resources.md)
- [Protección del servicio de Azure SQL y los datos en Defender for Cloud](./implement-security-recommendations.md)


Vídeo    
- [Mitigación de problemas de seguridad mediante Defender for Cloud](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="defender-for-cloud-for-incident-response"></a>Defender for Cloud para la respuesta a incidentes
Para reducir los costos y los daños, es importante tener un plan de respuesta a incidentes implantado antes de que se produzca un ataque. Puede usar Defender for Cloud en distintas fases de una respuesta a incidentes.

Use los siguientes recursos para comprender cómo se puede incorporar Defender for Cloud al proceso de respuesta a incidentes.

Vídeos    
* [Defender for Cloud en la respuesta a incidentes](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Respuesta rápida ante amenazas mediante investigación y operaciones de última generación)

Artículos    
* [Uso de Defender for Cloud para una respuesta a incidentes](./tutorial-security-incident.md)
* [Uso de la automatización para responder a los desencadenadores de Defender for Cloud](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Protección en la nube avanzada

Las máquinas virtuales de Azure pueden aprovechar las funcionalidades de protección en la nube avanzada de Defender for Cloud. Estas funcionalidades incluyen el acceso a máquinas virtuales "Just-In-Time" y controles de aplicación adaptables.

Use los siguientes recursos para aprender a utilizar estas funcionalidades de Defender for Cloud.

Vídeos    
* [Defender for Cloud: acceso a máquinas virtuales JIT](https://youtu.be/UOQb2FcdQnU)
* [Defender for Cloud: controles de aplicaciones adaptables](https://youtu.be/wWWekI1Y9ck)

Artículos    
* [Administración del acceso a máquina virtual mediante Just-In-Time](./just-in-time-access-usage.md)
* [Controles de aplicaciones adaptables en Defender for Cloud](./adaptive-application-controls.md)

## <a name="hands-on-activities"></a>Actividades prácticas

* [Laboratorio de prácticas de Defender for Cloud](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Guión de procedimientos con recomendaciones de firewall de aplicaciones web (WAF) en Defender for Cloud](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Cuaderno de Defender for Cloud: alertas de seguridad](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Recursos adicionales
* [Página de documentación de Defender for Cloud](./index.yml)
* [Página de documentación de la API de REST de Defender for Cloud](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Preguntas más frecuentes de Defender for Cloud (P+F)](./faq-general.yml)
* [Página de precios](https://azure.microsoft.com/pricing/details/security-center/)
* [Identificación de procedimientos recomendados de seguridad](../security/fundamentals/identity-management-best-practices.md)
* [Procedimientos recomendados de seguridad de red](../security/fundamentals/network-best-practices.md)
* [Recomendaciones sobre PaaS](../security/fundamentals/paas-deployments.md)
* [Cumplimiento normativo](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Los clientes de Log Analytics pueden usar ahora Defender for Cloud para proteger sus cargas de trabajo en la nube híbrida](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)
