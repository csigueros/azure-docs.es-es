---
title: Conexión de Azure Defender para IoT con Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo conectar los datos de Azure Defender (anteriormente Azure Security Center) para IoT a Azure Sentinel.
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
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 367e0cbd6d771baad1234f99080200a60a20a196
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252587"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel-public-preview"></a>Conexión de los datos de Azure Defender (anteriormente Azure Security Center) para IoT con Azure Sentinel (versión preliminar pública)

Use el conector de Defender para IoT a fin de transmitir todos los eventos de esta solución a Azure Sentinel. 

Esta integración permite a las organizaciones detectar rápidamente ataques de varias fases que, a menudo, cruzan los límites de TI y OT. Además, la integración de Defender para IoT con las funcionalidades de orquestación de seguridad, automatización y respuesta (SOAR) de Azure Sentinel permite la prevención y respuesta automatizadas mediante el uso de cuadernos de estrategias integrados optimizados para OT.

> [!IMPORTANT]
> El conector de Azure Defender para IoT se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- Permisos de **lectura** y **escritura** en el área de trabajo donde está implementado Azure Sentinel.
- **Defender para IoT** debe estar **habilitado** en las instancias pertinentes de IoT Hub.
- Debe tener permisos de **Colaborador** en la **Suscripción** a la que desea conectarse.

## <a name="connect-to-defender-for-iot"></a>Conexión a Defender para IoT

1. En Azure Sentinel, seleccione **Conectores de datos** y, después, seleccione **Defender para IoT** (es posible que todavía se llame Azure Security Center para IoT) desde la galería.

1. Desde la parte inferior del panel derecho, haga clic en **Abrir la página del conector**. 

1. Haga clic en **Conectar** junto a cada suscripción de IoT Hub cuyas alertas y alertas de dispositivo quiera transmitir a Azure Sentinel. 
    - Recibirá un mensaje de error si Defender para IoT no está habilitado al menos en una instancia de IoT Hub dentro de una suscripción. Habilite Defender para IoT en la instancia de IoT Hub para eliminar el error.

1. Puede decidir si quiere que las alertas de Defender para IoT generen incidentes automáticamente en Azure Sentinel. En **Crear incidentes**, seleccione **Habilitar** para que la regla de análisis predeterminada pueda crear incidentes de forma automática a partir de las alertas generadas. Esta regla puede cambiarse o editarse en **Análisis** > **Reglas activas**.

> [!NOTE]
> Puede tardar 10 segundos o más en actualizar la lista de **Suscripción** tras realizar cambios en la conexión. 

## <a name="log-analytics-alert-view"></a>Vista de alerta de Log Analytics

Para usar el esquema correspondiente en Log Analytics a fin de mostrar las alertas de Defender para IoT, haga lo siguiente:

1. Abra **Registros** > **SecurityInsights** > **SecurityAlert** o busque **SecurityAlert**. 

2. Use el siguiente filtro kql a fin de ver solo las alertas generadas por Defender para IoT:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas del servicio

Tras conectar una **Suscripción**, los datos del centro de conectividad están disponibles en Azure Sentinel aproximadamente 15 minutos más tarde.


## <a name="next-steps"></a>Pasos siguientes

En este documento, ha obtenido información sobre cómo conectar Defender para IoT a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.