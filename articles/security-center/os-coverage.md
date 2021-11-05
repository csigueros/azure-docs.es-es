---
title: Plataformas compatibles con Microsoft Defender for Cloud | Microsoft Docs
description: En este documento se proporciona una lista de plataformas compatibles con Microsoft Defender for Cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f3c8538ca327c031bed7ca0a17098af07fc5bfd9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093698"
---
# <a name="supported-platforms"></a>Plataformas compatibles 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En esta página se muestran las plataformas y los entornos compatibles con Microsoft Defender for Cloud.

## <a name="combinations-of-environments"></a>Combinaciones de entornos <a name="vm-server"></a>

Microsoft Defender for Cloud admite máquinas virtuales y servidores en diferentes tipos de entornos híbridos:

* Solo Azure
* Azure y entorno local
* Azure y otras nubes
* Azure, otras nubes y entorno local

En el caso de un entorno de Azure activado en una suscripción de Azure, Microsoft Defender for Cloud detectará automáticamente los recursos de IaaS que se implementan en la suscripción.

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Defender for Cloud depende del [agente de Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Asegúrese de que las máquinas ejecutan uno de los sistemas operativos compatibles con este agente, como se describe en las siguientes páginas:

* [Sistemas operativos Windows compatibles con el agente de Log Analytics](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Sistemas operativos Linux compatibles con el agente de Log Analytics](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Asegúrese también de que el agente de Log Analytics esté [configurado correctamente para enviar datos a Defender for Cloud](enable-data-collection.md#manual-agent).

Para obtener más información acerca de las características de Defender for Cloud específicas que están disponibles en Windows y Linux, consulte [Cobertura de características para máquinas](supported-machines-endpoint-solutions-clouds.md).

> [!NOTE]
> Aunque **Microsoft Defender para servidores** está diseñado para proteger servidores, la mayoría de las funcionalidades son compatibles con máquinas con Windows 10. Una característica que no se admite actualmente es la [solución integrada EDR de Defender for Cloud: Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md).

## <a name="managed-virtual-machine-services"></a>Servicios de máquinas virtuales administradas <a name="virtual-machine"></a>

También se crean máquinas virtuales en una suscripción de cliente como parte de algunos servicios administrados de Azure, como Azure Kubernetes (AKS), Azure Databricks, etc. Defender for Cloud detecta también estas máquinas virtuales y el agente de Log Analytics se puede instalar y configurar si hay disponible un sistema operativo compatible.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

También se admiten máquinas virtuales que se ejecuten en un servicio en la nube. Se supervisan los roles de trabajo y web de servicios en la nube que se ejecutan en espacios de producción. Para más información sobre Cloud Services, consulte la [información general sobre Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

También se admite la protección para las máquinas virtuales que residan en Azure Stack Hub. Para obtener más información acerca de la integración de Defender for Cloud con Azure Stack Hub, consulte el artículo sobre la [incorporación de máquinas virtuales de Azure Stack Hub a Defender for Cloud](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Defender for Cloud recopila datos mediante el agente de Log Analytics](enable-data-collection.md).
- Obtenga información sobre cómo [Defender for Cloud administra y protege los datos](data-security.md).
- Obtenga información sobre cómo [planear y entender las consideraciones de diseño para adoptar Microsoft Defender for Cloud](security-center-planning-and-operations-guide.md).
