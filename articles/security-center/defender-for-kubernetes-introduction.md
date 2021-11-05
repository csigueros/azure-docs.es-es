---
title: 'Microsoft Defender para Kubernetes: ventajas y características'
description: Obtenga información sobre las ventajas y características de Microsoft Defender para Kubernetes.
author: memildin
ms.author: memildin
ms.date: 07/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8de5687d645dce485514957d7be490bbd502b85a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055889"
---
# <a name="introduction-to-microsoft-defender-for-kubernetes"></a>Introducción a Microsoft Defender para Kubernetes

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender para Kubernetes es el plan de seguridad mejorada que proporciona protecciones para los clústeres de Kubernetes dondequiera que se ejecuten. 

Defender for Cloud puede proteger clústeres en:

- **Azure Kubernetes Service (AKS)** : servicio administrado por Microsoft para desarrollar, implementar y administrar aplicaciones en contenedores.

- **Entornos locales y de varias nubes:** mediante una [extensión para Kubernetes habilitado para Azure Arc](defender-for-kubernetes-azure-arc.md)

Microsoft Defender for Cloud y AKS forman una oferta de seguridad de Kubernetes nativa de nube con protección del entorno, protección de la carga de trabajo y protección en tiempo de ejecución, tal y como se describe en [Seguridad de contenedores en Defender for Cloud](container-security.md).

La detección de amenazas de nivel de host en los nodos de AKS de Linux está disponible si habilita [Microsoft Defender para servidores](defender-for-servers-introduction.md) y su agente de Log Analytics. Sin embargo, si el clúster se implementa en un conjunto de escalado de máquinas virtuales de Azure Kubernetes Service, el agente de Log Analytics no se admite actualmente.



## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|**Microsoft Defender para Kubernetes** se factura como se muestra en la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).|
|Roles y permisos necesarios:|**Administración de seguridad** puede descartar las alertas.<br>**El lector de seguridad** puede ver los resultados.|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nacionales o soberanas (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-kubernetes"></a>¿Cuáles son las ventajas de Microsoft Defender para Kubernetes?

Microsoft Defender para Kubernetes proporciona **protección frente a amenazas de nivel de clúster** mediante la supervisión de los registros del clúster.

Entre los ejemplos de eventos de seguridad que supervisa Microsoft Defender para Kubernetes, se incluyen los paneles de Kubernetes expuestos y la creación de roles con privilegios elevados y de montajes confidenciales. Para la lista completa de las alertas de nivel de clúster, consulte la [tabla de referencia de alertas](alerts-reference.md#alerts-k8scluster).

> [!TIP]
> Puede simular las alertas de contenedor al seguir las instrucciones de [esta entrada de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Además, nuestro equipo global de investigadores de seguridad supervisa constantemente el panorama de las amenazas. Agregan alertas específicas del contenedor y vulnerabilidades a medida que se detectan.

>[!NOTE]
> Defender for Cloud genera alertas de seguridad para las acciones e implementaciones que se producen después de habilitar el plan de Defender para Kubernetes en la suscripción.




## <a name="faq---microsoft-defender-for-kubernetes"></a>Preguntas frecuentes: Microsoft Defender para Kubernetes

- [¿Puedo obtener protecciones del clúster aún sin el agente de Log Analytics?](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [¿Me permite AKS instalar extensiones de máquina virtual personalizadas en mis nodos de AKS?](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [Si el clúster ya está ejecutando un agente de Azure Monitor para contenedores, ¿necesito también el agente de Log Analytics?](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [¿Microsoft Defender para Kubernetes admite AKS con nodos del conjunto de escalado de máquinas virtuales?](#does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>¿Puedo obtener protecciones del clúster aún sin el agente de Log Analytics?

El plan de **Microsoft Defender para Kubernetes** proporciona protecciones de nivel de clúster. Si implementa también el agente de Log Analytics de **Microsoft Defender para servidores**, obtendrá la protección contra amenazas para los nodos que se proporciona con ese plan. Más información en [Introducción a Microsoft Defender para servidores](defender-for-servers-introduction.md).

Se recomienda implementar ambos para obtener la protección más completa posible.

Si decide no instalar el agente en los hosts, solo recibirá un subconjunto de las ventajas de la protección contra amenazas y las alertas de seguridad. Aún así, seguirá recibiendo alertas relacionadas con el análisis de redes y las comunicaciones con servidores malintencionados.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>¿Me permite AKS instalar extensiones de máquina virtual personalizadas en mis nodos de AKS?
Para que Defender for Cloud supervise los nodos de AKS, estos deben ejecutar el agente de Log Analytics.

AKS es un servicio administrado y, como el agente de Log Analytics es una extensión administrada por Microsoft, también es compatible con los clústeres de AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Si el clúster ya está ejecutando un agente de Azure Monitor para contenedores, ¿necesito también el agente de Log Analytics?
Para que Defender for Cloud supervise los nodos, estos deben ejecutar el agente de Log Analytics.

Si los clústeres ya ejecutan el agente de Azure Monitor para contenedores, puede instalar el agente de Log Analytics y los dos agentes pueden trabajar junto con otros sin problemas.

[Más información sobre el agente de Azure Monitor para contenedores](../azure-monitor/containers/container-insights-manage-agent.md).


### <a name="does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes"></a>¿Microsoft Defender para Kubernetes admite AKS con nodos del conjunto de escalado de máquinas virtuales?
Si el clúster está implementado en un conjunto de escalado de máquinas virtuales de Azure Kubernetes Service, el agente de Log Analytics no se admite actualmente.



## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre la protección para Kubernetes de Defender for Cloud, incluido Microsoft Defender para Kubernetes. 

> [!div class="nextstepaction"]
> [Habilitación de las protecciones mejoradas](enable-enhanced-security.md)

Para obtener material relacionado, consulte los siguientes artículos: 

- [Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR](export-to-siem.md)
- [Tabla de referencia de alertas](alerts-reference.md)
