---
title: Conexión de datos de Zscaler a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar datos de Zscaler a Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a08004abc62bfe0e01c42ea7d3e5d6eeecb89170
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695033"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Conectar Zscaler Internet Access a Azure Sentinel

En este artículo se explica cómo conectar el dispositivo Zscaler Internet Access a Azure Sentinel. El conector de datos de Zscaler permite conectar fácilmente los registros de Zscaler Internet Access (ZIA) con Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. El uso de Zscaler en Azure Sentinel le proporcionará más información sobre el uso de Internet de su organización y mejorará sus capacidades de operación de seguridad. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configurar Zscaler para enviar mensajes de CEF

1. Debe establecer estos valores en el dispositivo Zscaler para que envíe los registros necesarios en el formato necesario al agente de Syslog de Azure Sentinel, según el agente de Log Analytics. Puede modificar estos parámetros en su dispositivo, siempre y cuando modifique también el demonio de Syslog en el agente de Azure Sentinel.
    - Protocolo = TCP
    - Puerto = 514
    - Formato = CEF
    - Dirección IP: Asegúrese de enviar los mensajes CEF a la dirección IP de la máquina virtual que ha dedicado para tales efectos.
 Para más información, consulte la [Guía de implementación de Zscaler y Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Esta solución admite RFC 3164 o RFC 5424 de Syslog.


1. Para usar el esquema correspondiente en Log Analytics para los eventos CEF, busque `CommonSecurityLog`.
1. Vaya a [Validación de la conectividad de CEF](troubleshooting-cef-syslog.md#validate-cef-connectivity).


## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar Zscaler Internet Access a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.