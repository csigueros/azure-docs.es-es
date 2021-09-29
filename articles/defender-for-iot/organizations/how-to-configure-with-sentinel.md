---
title: Configuración de Azure Sentinel con Defender para IoT para organizaciones
description: Explica cómo configurar Azure Sentinel para recibir datos de la solución de Defender para IoT.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: 1c3c03c99c733ea41a4f7b2a4eddd9ae750c92c6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771504"
---
# <a name="connect-your-data-from-defender-for-iot-for-organizations-to-azure-sentinel-public-preview"></a>Conexión de los datos de Defender para IoT para organizaciones a Azure Sentinel (versión preliminar pública)

Use el conector de Defender para IoT a fin de transmitir todos los eventos de esta solución a Azure Sentinel. 

Esta integración permite a las organizaciones detectar rápidamente ataques de varias fases que, a menudo, cruzan los límites de TI y OT. Además, la integración de Defender para IoT con las funcionalidades de orquestación de seguridad, automatización y respuesta (SOAR) de Azure Sentinel permite la prevención y respuesta automatizadas mediante el uso de cuadernos de estrategias integrados optimizados para OT. 

## <a name="prerequisites"></a>Prerequisites

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

1. Use el siguiente filtro kql a fin de ver solo las alertas generadas por Defender para IoT:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Notas del servicio

Tras conectar una **Suscripción**, los datos del centro de conectividad están disponibles en Azure Sentinel aproximadamente 15 minutos más tarde.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha obtenido información sobre cómo conectar Defender para IoT a Azure Sentinel. Para obtener más información sobre la detección de amenazas y el acceso a datos de seguridad, consulte los siguientes artículos:

- Aprenda a usar Azure Sentinel en el [Inicio rápido: Introducción a Azure Sentinel](../../sentinel/get-visibility.md).