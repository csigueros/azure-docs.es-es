---
title: Configuración de la solución de seguridad para conectar datos de CEF a Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo configurar la solución de seguridad para conectar datos de CEF a Azure Sentinel.
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
ms.openlocfilehash: 49dab18a0f3b58d82b7de9c393f63428ac0d115b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695195"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASO 2: Configurar la solución de seguridad para enviar mensajes CEF

En este paso, realizará los cambios de configuración necesarios en la solución de seguridad para enviar registros al agente de CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configuración de una solución con un conector

Si la solución de seguridad ya tiene un conector existente, use las instrucciones específicas del conector como se indica a continuación:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [Reveal(x) de ExtraHop](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Productos de Forcepoint](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [AMS de Illusive Networks](connect-illusive-attack-management-system.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [Plataforma de análisis forense de redes de WireX](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Configurar cualquier otra solución

Si no existe un conector para su solución de seguridad específica, use las siguientes instrucciones genéricas para reenviar los registros al agente de CEF.

1. Vaya al artículo de configuración específico para conocer los pasos para configurar la solución para enviar mensajes CEF. Si su solución no aparece, debe establecer estos valores en el dispositivo para que envíe los registros necesarios en el formato necesario al agente de Syslog de Azure Sentinel, según el agente de Log Analytics. Puede modificar estos parámetros en su dispositivo, siempre y cuando modifique también el demonio de Syslog en el agente de Azure Sentinel.
    - Protocolo = TCP
    - Puerto = 514
    - Formato = CEF
    - Dirección IP: Asegúrese de enviar los mensajes CEF a la dirección IP de la máquina virtual que ha dedicado para tales efectos.

   Esta solución admite RFC 3164 o RFC 5424 de Syslog.

1. Para buscar eventos CEF en Log Analytics, escriba `CommonSecurityLog` en la ventana de consulta.

1. Diríjase al PASO 3: [validación de conectividad](troubleshooting-cef-syslog.md#validate-cef-connectivity).

> [!NOTE]
> **Cambio del origen del campo TimeGenerated**
>
> - De forma predeterminada, el agente de Log Analytics rellena el campo *TimeGenerated* en el esquema con la hora en la que el agente recibió el evento del demonio de Syslog. Como resultado, la hora a la que se generó el evento en el sistema de origen no se registra en Azure Sentinel.
>
> - Sin embargo, puede ejecutar el siguiente comando que descargará y ejecutará el script `TimeGenerated.py`. Este script configura el agente de Log Analytics para rellenar el campo *TimeGenerated* con la hora original del evento en su sistema de origen, en lugar de usar la hora a la que lo recibió el agente.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar dispositivos CEF a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Obtenga información sobre la [asignación de campos de CEF y CommonSecurityLog](cef-name-mapping.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).