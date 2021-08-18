---
title: Conexión de Zimperium Mobile Threat Defense a Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo conectar Zimperium Mobile Threat Defense a Azure Sentinel.
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
ms.openlocfilehash: 840d87fe1bf14e60cdfdd2438189763bcc548c3a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252468"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Conexión de Zimperium Mobile Threat Defense a Azure Sentinel


> [!IMPORTANT]
> El conector de datos Zimperium Mobile Threat Defense de Azure Sentinel está actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


El conector Zimperium Mobile Threat Defense ofrece la posibilidad de conectar el registro de amenazas de Zimperium con Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. Esto le ofrece más información sobre el panorama de amenazas móviles de su organización y mejora las capacidades de las operaciones de seguridad.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configuración y conexión de Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense puede integrar y exportar los registros directamente a **Azure Sentinel**.

1. En el portal de Azure Sentinel, haga clic en Data connectors (Conectores de datos) y seleccione **Zimperium Mobile Threat Defense**.
2. Seleccione **Open connector page** (Abrir página del conector).
3. Siga las instrucciones de la página del conector **Zimperium Mobile Threat Defense**, que se resumen de la manera siguiente.
 1. En zConsole, haga clic en **Administrar** en la barra de navegación.
 2. Haga clic en la pestaña **Integraciones** .
 3. Haga clic en el botón **Threat Reporting** (Informes de amenazas) y, a continuación, en el botón **Add Integrations** (Agregar integraciones).
 4. Para crear la integración, seleccione **Microsoft Azure Sentinel** en las integraciones disponibles y escriba el identificador del área de trabajo y la clave principal para conectarse a Azure Sentinel.
 5. También está disponible la opción para seleccionar un nivel de filtro para los datos de la amenaza que se van a enviar a Azure Sentinel. 

4. Para obtener más información, consulte el [portal del servicio de atención al cliente de Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en Log Analytics en los registros de CustomLogs ZimperiumThreatLog_CL y ZimperiumMitigationLog_CL.

Para usar el esquema pertinente de Log Analytics para Zimperium Mobile Threat Defense, busque ZimperiumThreatLog_CL y ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha obtenido información sobre cómo conectar Zimperium Mobile Threat Defense a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).

- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).

- [Use libros](monitor-your-data.md) para supervisar los datos.

Para obtener más información sobre Zimperium, vea la documentación siguiente:

- [Zimperium](https://zimperium.com)

- [Blog sobre seguridad móvil de Zimperium](https://blog.zimperium.com)

- [Portal del servicio de atención al cliente de Zimperium](https://support.zimperium.com)