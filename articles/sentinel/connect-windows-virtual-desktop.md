---
title: Conexión de Windows Virtual Desktop a Microsoft Sentinel | Microsoft Docs
description: Obtenga información sobre cómo conectar los datos de Windows Virtual Desktop a Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3a1aa86db8b7a969b0c526ff4eaf9ff0d3351865
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524500"
---
# <a name="connect-windows-virtual-desktop-data-to-microsoft-sentinel"></a>Conexión de los datos de Windows Virtual Desktop a Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se describe cómo puede supervisar los entornos de Windows Virtual Desktop (WVD) mediante Microsoft Sentinel.

Por ejemplo, la supervisión de los entornos de Windows Virtual Desktop puede permitirle proporcionar un trabajo más remoto mediante escritorios virtualizados, a la vez que mantiene la posición de seguridad de la organización.

## <a name="windows-virtual-desktop-data-in-microsoft-sentinel"></a>Datos de Windows Virtual Desktop en Microsoft Sentinel

Los datos de Windows Virtual Desktop en Microsoft Sentinel incluyen los siguientes tipos:


|data  |Descripción  |
|---------|---------|
|**Registros de eventos de Windows**     |  Los registros de eventos de Windows del entorno de WVD se transmiten a un área de trabajo de Log Analytics compatible con Microsoft Sentinel, de la misma manera que se transmiten desde otras máquinas de Windows fuera del entorno de WVD. <br><br>Instale el agente de Log Analytics en la máquina de Windows y configure los registros de eventos de Windows que se enviarán al área de trabajo de Log Analytics.<br><br>Para más información, consulte:<br>- [Instalación del agente de Log Analytics en equipos Windows](../azure-monitor/agents/agent-windows.md)<br>- [Recopilación de orígenes de datos del registro de eventos de Windows con el agente de Log Analytics](../azure-monitor/agents/data-sources-windows-events.md)<br>- [Conexión de eventos de seguridad de Windows](connect-windows-security-events.md)       |
|**Alertas de Microsoft Defender para punto de conexión**     |  Para configurar Microsoft Defender para punto de conexión para Windows Virtual Desktop, use el mismo procedimiento para cualquier otro punto de conexión de Windows. <br><br>Para más información, consulte: <br>- [Configuración de la implementación de Microsoft Defender para punto de conexión](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Conexión de los datos de Microsoft 365 Defender a Microsoft Sentinel](connect-microsoft-365-defender.md)       |
|**Diagnostico de Windows Virtual Desktop**     | Diagnóstico de Windows Virtual Desktop es una característica del servicio PaaS de Windows Virtual Desktop, que registra información cada vez que alguien asignado al rol de Windows Virtual Desktop usa el servicio. <br><br>Cada registro contiene información sobre el rol de Windows Virtual Desktop implicado en la actividad, los mensajes de error que se muestran durante la sesión, la información del inquilino y la información del usuario. <br><br>La característica de diagnóstico crea registros de actividad para las acciones de usuario y administrativas. <br><br>Para obtener más información, consulte [Uso de Log Analytics para la característica de diagnóstico en Windows Virtual Desktop](../virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Conexión con los datos de Windows Virtual Desktop

Para empezar a ingerir datos de Windows Virtual Desktop en Microsoft Sentinel, siga las instrucciones de la documentación del servicio.

Para obtener más información, consulte [Inserción de los datos de Windows Virtual Desktop en el área de trabajo de Log Analytics.](../virtual-desktop/diagnostics-log-analytics.md)

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida correctamente una conexión, ejecute las consultas en Microsoft Sentinel con relación a los datos de Log Analytics.

Por ejemplo, puede acceder a las consultas de ejemplo que se encuentran en la [documentación de Windows Virtual Desktop](../virtual-desktop/diagnostics-log-analytics.md).


Microsoft Sentinel también proporciona consultas integradas en el área **General** > **Registros** > **WINDOWS VIRTUAL DESKTOP**:

[![Consultas integradas de Windows Virtual Desktop en Microsoft Sentinel](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png) ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes


Para obtener más información, consulte el [glosario de Azure Monitor para Windows Virtual Desktop](../virtual-desktop/azure-monitor-glossary.md).
