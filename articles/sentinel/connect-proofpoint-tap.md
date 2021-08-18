---
title: Conexión de los datos de Proofpoint Targeted Attack Protection (TAP) a Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo conectar los datos de Proofpoint Targeted Attack Protection (TAP) a Azure Sentinel.
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
ms.openlocfilehash: 674ed9818b91636432609c8b79d3932a95c065f5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254111"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Conexión de Proofpoint TAP a Azure Sentinel con Azure Functions

> [!IMPORTANT]
> El conector de datos de Proofpoint TAP en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector de Proofpoint Targeted Attack Protection (TAP) permite conectar fácilmente todos los registros de la solución de seguridad de [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación. La integración entre Proofpoint TAP y Azure Sentinel usa Azure Functions para extraer datos de registro mediante la API REST.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-proofpoint-tap"></a>Configuración y conexión de Proofpoint TAP

Azure Functions puede integrar y extraer eventos y registros directamente de Proofpoint TAP y reenviarlos a Azure Sentinel.

1. En el portal de Azure Sentinel, haga clic en **Conectores de datos** y seleccione el conector **Proofpoint TAP**.

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones de la página **Proofpoint TAP**.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida correctamente una conexión, los datos aparecen en Log Analytics en las tablas **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** y **ProofpointTAPClicksBlocked_CL**.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Proofpoint TAP a Azure Sentinel con Azure Function Apps. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.