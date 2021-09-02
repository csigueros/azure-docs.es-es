---
title: Adición de unidades de escalado para el escalado de host
titleSuffix: Azure Bastion
description: Aprenda a agregar instancias adicionales (unidades de escalado) a Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: d5087994a72b52ce091dac98987b8e0bc60e287e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224731"
---
# <a name="configure-host-scaling-preview"></a>Configuración del escalado de host (versión preliminar)

Este artículo le ayuda a agregar unidades de escalado (instancias) adicionales a Azure Bastion para dar cabida a conexiones de cliente simultáneas adicionales. En la versión preliminar, esta opción solo se puede configurar en Azure Portal. Para más información sobre el escalado de host, consulte [Valores de configuración](configuration-settings.md#instance). 

## <a name="configuration-steps"></a>Pasos de configuración

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
1. En Azure Portal, vaya al host bastión.
1. El recuento de instancias del escalado de host requiere el nivel Estándar. En la página **Configuración**, en **Nivel**, compruebe que el nivel es **Estándar**. Si es Básico, seleccione **Estándar** en la lista desplegable. 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="Captura de pantalla de Seleccionar nivel." lightbox="./media/configure-host-scaling/select-sku.png":::
1. Para configurar el escalado, ajuste el recuento de instancias. Cada instancia es una unidad de escalado.

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="Captura de pantalla del control deslizante Recuento de instancias." lightbox="./media/configure-host-scaling/instance-count.png":::
1. Haga clic en **Aplicar** para aplicar los cambios.

## <a name="next-steps"></a>Pasos siguientes

* Para más información, lea las [P+F sobre Bastion](bastion-faq.md).
