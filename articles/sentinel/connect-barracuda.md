---
title: Conexión de datos de Barracuda a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector del firewall de aplicaciones web (WAF) de Barracuda para conectar registros de Barracuda con Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 187df87b841fdab877181ed6c381bd0d018660b4
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252676"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Conexión de un dispositivo WAF de Barracuda 

El conector del firewall de aplicaciones web (WAF) de Barracuda permite conectar fácilmente los registros de Barracuda con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación, de modo que dispondrá de más información sobre la red de la organización y de mejores capacidades de seguridad. Azure Sentinel aprovecha las ventajas de la integración nativa entre **Barracuda** y el agente de Log Analytics para proporcionar una integración sin problemas. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-barracuda-waf"></a>Configuración y conexión del WAF de Barracuda

El firewall de aplicaciones web de Barracuda puede integrar y exportar registros directamente a Azure Sentinel a través del agente de Log Analytics.

1. Vaya al [flujo de configuración del WAF de Barracuda](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/) y siga las instrucciones para configurar la conexión con estos parámetros:

    - **Id. del área de trabajo**: copie el valor del identificador del área de trabajo que se indica en la página del conector de Barracuda de Azure Sentinel.

    - **Clave principal**: copie el valor de la clave principal que se indica en la página del conector de Barracuda de Azure Sentinel.

1. Para usar el esquema pertinente en Log Analytics para los eventos de Barracuda, busque **CommonSecurityLog** y **barracuda_CL**.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 



## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar dispositivos de Barracuda a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.