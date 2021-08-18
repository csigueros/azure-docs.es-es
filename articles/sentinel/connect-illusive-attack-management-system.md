---
title: Conexión de datos de Illusive Attack Management System a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar los datos de Connect Illusive Attack Management System a Azure Sentinel.
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
ms.openlocfilehash: a2c4f48f77442d888be006c878a7e55d9a2b4fd2
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254049"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Conexión de Illusive Attack Management System a Azure Sentinel

> [!IMPORTANT]
> El conector de datos de Illusive Attack Management System en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar una instancia de [llusive Attack Management System](https://www.illusivenetworks.com/technology/platform/attack-detection-system) a Azure Sentinel. El conector de datos de llusive Attack Management System le permite compartir registros de incidentes y datos de análisis de la superficie expuesta a ataques que son de Illusive con Azure Sentinel, así como ver esta información en paneles dedicados que ofrecen conclusiones sobre el riesgo de la superficie expuesta a ataques de su organización (panel de ASM). Asimismo, permite realizar un seguimiento del desplazamiento lateral no autorizado en la red de su organización (panel de ADS).

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Reenvío de los registros de Illusive Attack Management System al agente de Syslog  

Configure Attack Management System para reenviar mensajes de Syslog en formato CEF al área de trabajo de Azure mediante el agente de Syslog.

1. Inicie sesión en la consola de Illusive y vaya a Settings->Reporting (Configuración-> Informes).

1. Busque los servidores de Syslog.

1. Proporcione la siguiente información:
   - Nombre de host: Dirección IP o nombre de host FQDN del agente Syslog de Linux
   - Puerto: 514
   - Protocolo: TCP
   - Mensajes de auditoría: Enviar mensajes de auditoría al servidor

1. Para agregar el servidor de Syslog, haga clic en Agregar.

1. Para usar el esquema apropiado en **Registros** para Illusive Attack Management System, busque **CommonSecurityLog**.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Illusive Attack Management System a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.