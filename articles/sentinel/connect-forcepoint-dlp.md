---
title: Conexión de Forcepoint DLP a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar Forcepoint DLP a Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 56ed14cf3010b967965eddf0605d36bafebdbb57
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253541"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Conecte Forcepoint DLP a Azure Sentinel

> [!IMPORTANT]
> El conector de datos Forcepoint Data Loss Prevention (DLP) en Azure Sentinel se encuentra actualmente en versión preliminar pública. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


El conector Forcepoint DLP permite exportar automáticamente datos de incidentes de DLP a Azure Sentinel. Se puede usar para obtener visibilidad de las actividades de los usuarios y de los incidentes que acarrean pérdida de datos. También permite correlaciones de los datos de las cargas de trabajo de Azure y otras fuentes, y mejora la funcionalidad de supervisión con los libros dentro de Azure Sentinel.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-forcepoint-dlp"></a>Configuración y conexión de Forcepoint DLP

Configure Forcepoint DLP para que reenvíe los datos de los incidentes en formato JSON a su área de trabajo de Azure mediante API REST, tal como se explica en la [guía de integración de Forcepoint DLP](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez que el conector de Forcepoint DLP esté configurado, los datos aparecen en Log Analytics en CustomLogs **ForcepointDLPEvents_CL**.


Para usar el esquema pertinente en Log Analytics para Forcepoint DLP, busque **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Forcepoint DLP a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.