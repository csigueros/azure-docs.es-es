---
title: Conecte BETTER Mobile Threat Defense (MTD) a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos de BETTER Mobile Threat Defense (MTD) para extraer los registros de MTD en Azure Sentinel. Vea los datos de MTD en los libros, cree alertas y mejore la investigación.
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: f3f5c2c2a22dd46d008a002db64c045af8132c8d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252474"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Conecte su instancia de BETTER Mobile Threat Defense (MTD) a Azure Sentinel

> [!IMPORTANT]
> El conector de BETTER Mobile Threat Defense (MTD) se encuentra actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector de BETTER Mobile Threat Defense (MTD) permite conectar fácilmente todos los registros de la solución de seguridad de BETTER MTD con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación. La integración entre BETTER Mobile Threat Defense y Azure Sentinel usa la API REST.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Configuración y conexión de BETTER Mobile Threat Defense

BETTER MTD puede integrar y exportar los registros directamente a Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione **BETTER Mobile Threat Defense (MTD) (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga los pasos que se indican en la página del conector y en [esta página de la documentación de BETTER MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) para finalizar la integración en la consola de BETTER MTD.

    Cuando se le solicite que escriba los valores de **Id. de área de trabajo** y **Clave principal**, cópielos de la página del conector de Azure Sentinel y péguelos en la configuración de BETTER MTD.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Identificador de área de trabajo y clave principal}":::

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **CustomLogs**, en una o varias de las tablas siguientes:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Para consultar los registros de BETTER MTD en reglas de análisis, consultas de búsqueda o en cualquier otro lugar de Azure Sentinel, escriba uno de los nombres de tabla anteriores en la parte superior de la ventana de consulta.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha obtenido información sobre cómo conectar BETTER Mobile Threat Defense (MTD) a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.