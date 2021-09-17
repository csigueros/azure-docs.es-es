---
title: Conexión de datos de Check Point con Azure Sentinel | Microsoft Docs
description: Configure el dispositivo Check Point para reenviar mensajes de Syslog en formato CEF a su área de trabajo de Azure Sentinel a través del agente de Syslog.
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
ms.openlocfilehash: 72aa2efe611a500a8a7c4a56725bb5bd02b247da
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694086"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Conexión de Check Point a Azure Sentinel



En este artículo se explica cómo conectar el dispositivo Check Point a Azure Sentinel. El conector de datos de Check Point permite conectar fácilmente los registros de Check Point con Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. El uso de Check Point en Azure Sentinel le proporcionará más información sobre el uso de Internet de su organización y mejorará sus capacidades de operación de seguridad. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Reenvío de los registros de Check Point al agente de Syslog

Configure el dispositivo Check Point para reenviar mensajes de Syslog en formato CEF a su área de trabajo de Azure a través del agente de Syslog.

1. Vaya a [Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding) (Exportación del registro de Check Point).
1. Desplácese hacia abajo hasta **Implementación básica** y siga las instrucciones para configurar la conexión según lo que se indica a continuación:
   - Establezca el **puerto de Syslog** en **514** o en el puerto que establezca en el agente.
     - Reemplace el **nombre** y la **dirección IP del servidor de destino** en la CLI con la dirección IP y el nombre del agente de Syslog.
     - Establezca el formato en **CEF**.
1. Si usa la versión R77.30 o R80.10, desplácese hacia arriba hasta **Instalaciones** y siga las instrucciones para instalar un exportador de registros para su versión.
1. Vaya a [Validación de la conectividad de CEF](troubleshooting-cef-syslog.md#validate-cef-connectivity).
 

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar dispositivos de Check Point a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- [Validación de conectividad](troubleshooting-cef-syslog.md#validate-cef-connectivity).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.