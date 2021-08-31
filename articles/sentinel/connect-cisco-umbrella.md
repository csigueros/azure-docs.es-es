---
title: Conexión de Cisco Umbrella a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos Cisco Umbrella para extraer datos de Umbrella en Azure Sentinel. Vea los datos de Umbrella en libros, cree alertas y mejore la investigación.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a6a32f33eb88ac82793445badef25227f93fd809
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253273"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Conexión de Cisco Umbrella a Azure Sentinel

> [!IMPORTANT]
> El conector Cisco Umbrella está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector de Cisco Umbrella permite conectarse a buckets de Amazon Web Services S3, a través de la API S3, para ingerir registros de soluciones de seguridad de Cisco Umbrella en Azure Sentinel.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-cisco-umbrella"></a>Configuración y conexión de Cisco Umbrella

Cisco Umbrella puede integrar y exportar registros en Azure Sentinel a través de la API de Amazon S3.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Data connectors** (Conectores de datos), seleccione **Cisco Umbrella (Preview)** (Cisco Umbrella [versión preliminar]) y, luego, **Open connector page** (Abrir página del conector).

1. Siga los pasos que se describen en la sección **Configuración** de la página de conectores.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **CustomLogs**, en una o varias de las tablas siguientes:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar Cisco Umbrella con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.