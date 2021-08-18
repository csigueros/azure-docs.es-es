---
title: Conexión de datos de F5 ASM a Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo usar el conector de datos de F5 ASM para extraer los registros de F5 ASM en Azure Sentinel. Vea los datos de F5 ASM en los libros, cree alertas y mejore la investigación.
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
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 632b55b699df409049f77abecfca4c3cf75d3e73
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252673"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Conexión de F5 ASM a Azure Sentinel

En este artículo se explica cómo usar el conector de datos de F5 ASM para extraer fácilmente los registros de F5 ASM en Azure Sentinel. De este modo, puede ver los datos de F5 ASM en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. Tener datos de F5 ASM en Azure Sentinel le proporcionará información detallada sobre la seguridad de las aplicaciones web de la organización y mejorará las funcionalidades de las operaciones de seguridad. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Configuración de F5 ASM para enviar mensajes de CEF

1. Siga las instrucciones de [configuración del registro de eventos de seguridad de la aplicación de F5](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) y siga las instrucciones para configurar el registro remoto mediante estas directrices:
   - Establezca **Remote storage type** (Tipo de almacenamiento remoto) en **CEF**.
   - Configure el **protocolo** como **TCP**.
   - Establezca la **IP address** (Dirección IP) en la dirección IP del servidor de Syslog.
   - Establezca el valor de **port number** (número de puerto) en **514** o en el puerto que estableció para que lo usara el agente.
   - Puede establecer el valor de **Maximum Query String Size** (Tamaño máximo de la cadena de consulta) en el tamaño que estableció en el agente.

1. Para usar el esquema pertinente en Log Analytics para los eventos de CEF, busque `CommonSecurityLog`.

1. Diríjase al [PASO 3: Validación de conectividad](connect-cef-verify.md).


## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar F5 ASM a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.