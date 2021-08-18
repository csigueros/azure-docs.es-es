---
title: Conectar el Firewall de Barracuda CloudGen a Azure Sentinel | Microsoft Docs
description: Más información sobre cómo conectar el Firewall de Barracuda CloudGen a Azure Sentinel.
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
ms.openlocfilehash: 1ff3d5481b02e587d993bb4a5d7abf6aecd22ee8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254055"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Conexión del Firewall de Barracuda CloudGen

El conector del Firewall de Barracuda CloudGen (CGFW) le permite conectar fácilmente los registros de Barracuda CGFW con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación. Esto le ofrece más información sobre la red de su organización y mejora las capacidades de las operaciones de seguridad.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- Permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- El Firewall de Barracuda CloudGen debe estar configurado para exportar registros a través de Syslog.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Conexión de Azure Sentinel con el Firewall de Barracuda CloudGen

1. En el Azure Portal, vaya a **Azure Sentinel** > **Conectores de datos** y, después, seleccione el conector de **Firewall de Barracuda C4**.

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones que aparecen en la página del **Firewall de Barracuda CloudGen**.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar dispositivos del Firewall de Barracuda a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.