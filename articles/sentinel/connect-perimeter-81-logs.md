---
title: Conexión de datos de Perimeter 81 a Azure Sentinel| Microsoft Docs
description: Obtenga información sobre cómo conectar datos de Perimeter 81 a Azure Sentinel.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 07c850cf98ffddf01cb4479affdb62d30a5bb522
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253885"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Conexión de los registros de actividad de Perimeter 81 a Azure Sentinel

> [!IMPORTANT]
> El conector de datos Perimeter 81 en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de [registros de actividad de Perimeter 81](https://www.perimeter81.com/) a Azure Sentinel. El conector de registros de actividad de Perimeter 81 permite incorporar fácilmente los datos de Perimeter 81 a Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Configuración y conexión de registros de actividad de Perimeter 81

Los registros de actividad de Perimeter 81 pueden integrar y exportar los registros directamente a Azure Sentinel.

1. En el portal de Azure Sentinel, haga clic en la opción **Conectores de datos** situada en el menú de navegación.

1. Seleccione **Perimeter 81 Activity Logs** (Registros de actividad de Perimeter 81) en la galería y luego haga clic en el botón **Open connector page** (Abrir página de conector).

1. En la página del conector de registros de actividad de Perimeter 81, copie el **Id. del área de trabajo** y la **Clave principal** y péguelos en Perimeter 81, [como se indica aquí](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Después de completar las instrucciones, verá los tipos de datos conectados en la página de conectores de Azure Sentinel.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros** en **CustomLogs** - **Perimeter81_CL**.

Pueden transcurrir hasta 20 minutos hasta que los registros empiecen a aparecer.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar los registros de actividad de Perimeter 81 a Azure Sentinel. Para sacar el máximo partido de las capacidades integradas en este conector de datos, haga clic en la pestaña **Pasos siguientes** de la página del conector de datos. Allí encontrará un libro preparado y algunas consultas de ejemplo para que pueda empezar a buscar información útil.

Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.