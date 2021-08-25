---
title: Conexión de datos de Citrix WAF con Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo usar el conector de datos de Citrix WAF para extraer sus registros en Azure Sentinel. Vea los datos de Citrix WAF en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 309d2cfb47c61d516be19d110cb7ade1f3c63e18
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695177"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Conexión de Citrix WAF a Azure Sentinel

> [!IMPORTANT]
> El conector de datos de Citrix Web Application Firewall (WAF) de Azure Sentinel se encuentra actualmente en versión preliminar pública. Esta característica se proporciona sin un acuerdo de nivel de servicio. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo Citrix Web Application Firewall (WAF) a Azure Sentinel. El conector de datos de Citrix WAF permite conectar fácilmente los registros de Citrix WAF a Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. Al conectar los registros CEF de Citrix WAF a Azure Sentinel, puede aprovechar las ventajas de la búsqueda, la correlación, las alertas y el enriquecimiento de la inteligencia sobre amenazas de cada registro.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Reenvío de los registros de Citrix WAF al agente de Syslog  

Citrix WAF envía mensajes de Syslog en formato CEF a un servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng) con el agente de Log Analytics instalado, que reenvía los registros al área de trabajo de Azure Sentinel.

1. Si no tiene ningún servidor de reenvío de registros de este tipo, consulte [estas instrucciones](connect-cef-agent.md) para instalar uno y ponerlo en funcionamiento.

1. Siga las instrucciones proporcionadas por Citrix para [configurar WAF](https://support.citrix.com/article/CTX234174), [configurar el registro de CEF](https://support.citrix.com/article/CTX136146) y [configurar el envío de registros al reenviador de registros](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Asegúrese de enviar los registros al puerto TCP 514 en la dirección IP de la máquina reenviadora de registros.

1. Valide la conexión y compruebe la ingesta de datos con [estas instrucciones](troubleshooting-cef-syslog.md#validate-cef-connectivity). Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Para consultar los registros de Citrix WAF en Log Analytics, escriba `CommonSecurityLog` en la parte superior de la ventana de consulta.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Citrix WAF a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.