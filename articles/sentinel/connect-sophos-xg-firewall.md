---
title: Conexión datos de Sophos XG Firewall a Azure Sentinel| Microsoft Docs
description: Aprenda a conectar datos de Sophos XG Firewals a Azure Sentinel.
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
ms.openlocfilehash: 05b3461d06e831e72b3363d558c1bc29d13b59a9
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253119"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Conexión de Sophos XG Firewal a Azure Sentinel

> [!IMPORTANT]
> El conector de datos de Sophos XG Firewal en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de [Sophos XG Firewal](https://www.sophos.com/products/next-gen-firewall.aspx) con Azure Sentinel. El conector de datos de Sophos XG Firewal permite conectar fácilmente los registros de Sophos XG Firewal con Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. La integración entre Sophos XG Firewal y Azure Sentinel usa Syslog.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Reenvío de registros de Sophos XG Firewal al agente de Syslog  

Configure Sophos XG Firewal para reenviar mensajes de Syslog a su área de trabajo de Azure a través del agente de Syslog.

1. En el portal de Azure Sentinel, haga clic en **Conectores de datos** y seleccione el conector **Sophos XG Firewal**.

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones que aparecen en la página de **Sophos XG Firewal**.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en Log Analytics bajo Syslog.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Sophos XG Firewal a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.