---
title: Adición de unidades de escalado para el escalado de host
titleSuffix: Azure Bastion
description: Aprenda a agregar instancias adicionales (unidades de escalado) a Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 9b9315705896dcec4e7a3baa2ad962ca04036a48
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732097"
---
# <a name="configure-host-scaling-preview"></a>Configuración del escalado de host (versión preliminar)

Este artículo le ayuda a agregar unidades de escalado (instancias) adicionales a Azure Bastion para dar cabida a conexiones de cliente simultáneas adicionales. En la versión preliminar, esta opción solo se puede configurar en Azure Portal. Para más información sobre el escalado de host, consulte [Valores de configuración](configuration-settings.md#instance). 

## <a name="configuration-steps"></a>Pasos de configuración

[!INCLUDE [Azure Bastion preview portal](../../includes/bastion-preview-portal-note.md)]

1. En Azure Portal, vaya al host bastión.
1. El recuento de instancias del escalado de host requiere el nivel Estándar. En la página **Configuración**, en **Nivel**, compruebe que el nivel es **Estándar**. Si es Básico, seleccione **Estándar** en la lista desplegable. 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="Captura de pantalla de Seleccionar nivel." lightbox="./media/configure-host-scaling/select-sku.png":::
1. Para configurar el escalado, ajuste el recuento de instancias. Cada instancia es una unidad de escalado.

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="Captura de pantalla del control deslizante Recuento de instancias." lightbox="./media/configure-host-scaling/instance-count.png":::
1. Haga clic en **Aplicar** para aplicar los cambios.

## <a name="next-steps"></a>Pasos siguientes

* Para más información, lea las [P+F sobre Bastion](bastion-faq.md).
