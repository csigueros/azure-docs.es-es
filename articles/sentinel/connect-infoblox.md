---
title: Conexión de datos de Infoblox Network Identity Operating System (NIOS) a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar datos de Infoblox Network Identity Operating System (NIOS) a Azure Sentinel.
services: sentinel
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 937ea003dea2358f8fd434cd32393d41b038892d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252471"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Conexión de Infoblox NIOS a Azure Sentinel

> [!IMPORTANT]
> El conector de datos de Infoblox NIOS en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar su [dispositivo de Infoblox Network Identity Operating System (NIOS)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) a Azure Sentinel. El conector de datos de Infoblox NIOS permite conectar fácilmente los registros de Infoblox con Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. La integración entre Infoblox NIOS y Azure Sentinel usa Syslog.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Reenvío de los registros de Infoblox al agente de Syslog  

Configure Infoblox para reenviar mensajes de Syslog a su área de trabajo de Azure Sentinel a través del agente de Syslog.

1. En el portal de Azure Sentinel, haga clic en **Data connectors** (Conectores de datos) y seleccione el conector **Infoblox NIOS**.

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones de la página **Infoblox NIOS**.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en Log Analytics bajo Syslog.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Infoblox NIOS a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.