---
title: Panel Traslado entre regiones
description: Supervise los recursos que se trasladan entre regiones mediante el panel Traslado entre regiones.
author: Aarthi-Vijayaraghavan
manager: sutalasi
ms.service: resource-move
ms.topic: how-to
ms.date: 08/30/2021
ms.author: AarthiV
ms.openlocfilehash: 9339b61135d4b8b21f88b35a842a080dac9f8e69
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620937"
---
# <a name="move-across-region-dashboard"></a>Panel Traslado entre regiones
En este artículo se describe cómo supervisar los recursos que se trasladan entre regiones mediante el panel Traslado entre regiones de Azure Resource Mover. 
## <a name="monitor-via-the-dashboard"></a>Supervisión a través del panel
1. En **Azure Resource Mover**, seleccione **Información general** en el panel de navegación izquierdo. Puede alternar entre dos páginas: **Introducción** y **Panel Traslado entre regiones**. La página de **introducción** proporciona opciones para trasladar los recursos entre suscripciones, entre grupos de recursos y regiones.
La página del **panel Traslado entre regiones** combina toda la información de supervisión del traslado entre regiones en un único lugar.
    [![Pestaña del panel Traslado entre regiones](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)
2. En el panel se enumeran todas las combinaciones de movimiento creadas por usted. Las dos secciones siguientes se usan para capturar el estado del traslado entre regiones.
    En **Recursos por estado de traslado**, supervise el porcentaje y el número de recursos en cada estado.
    En **Resumen de errores**, supervise los errores activos que deben resolverse para poder realizar el traslado correctamente a la región de destino.
    [![Sección Estado y problemas](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)
> [!NOTE]
> Solo se mostrarán en el panel las combinaciones de origen y destino que ya se han creado en la suscripción elegida.

3. Use los filtros para elegir la **suscripción**, **región de origen** y **región de destino** preferidas.
    [![Filtros](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)
4. Para ir a la página de detalles, seleccione la opción **Ver todos los recursos** situada junto al origen y destino.
    [![Detalles](media\move-across-region-dashboard\move-across-region-dashboard-details.png)](media\move-across-region-dashboard\move-across-region-dashboard-details.png)
## <a name="next-steps"></a>Pasos siguientes
[Más información](about-move-process.md) acerca del proceso de traslado.
