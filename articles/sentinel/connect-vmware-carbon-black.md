---
title: Conexión de datos de VMware Carbon Black Cloud Endpoint Standard a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar datos de VMware Carbon Black Cloud Endpoint Standard a Azure Sentinel.
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
ms.openlocfilehash: 362245b6aa3fa4ae943925242b8f306e43efd246
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253093"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Conexión de datos de VMware Carbon Black Cloud Endpoint Standard a Azure Sentinel a Azure Functions

> [!IMPORTANT]
> El conector de datos de VMware Carbon Black Cloud Endpoint Standard de Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector VMware Carbon Black Cloud Endpoint Standard permite conectar fácilmente todos los registros de la solución de seguridad [VMware Carbon Black Endpoint Standard](https://www.carbonblack.com/products/endpoint-standard/) a Azure Sentinel, lo que permite ver paneles, crear alertas personalizadas y mejorar la investigación. La integración entre VMware Carbon Black y Azure Sentinel usa Azure Functions para extraer datos de registro mediante la API REST.


> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Configuración y conexión de VMware Carbon Black Cloud Endpoint Standard

Azure Functions puede integrar y extraer eventos y registros directamente de VMware Carbon Black Cloud Endpoint Standard y reenviarlos a Azure Sentinel.

1. En el portal de Azure Sentinel, haga clic en **Data connectors** (Conectores de datos) y seleccione el conector **VMware Carbon Black**.
2. Seleccione **Open connector page** (Abrir página del conector).
3. Siga las instrucciones que aparecen en la página **VMware Carbon Black**.


## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en Log Analytics en las tablas **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** y ****CarbonBlackEvents_CL****.

## <a name="validate-connectivity"></a>Validar conectividad
Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 


## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar VMware Carbon Black Cloud Endpoint Standard a Azure Sentinel con Azure Function Apps. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.