---
title: Actualización de un SKU
titleSuffix: Azure Bastion
description: Aprenda a cambiar los niveles de la SKU básica a la estándar.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 8a2c35f2ef4dafe9024deab1c26116c0ca20e01e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733311"
---
# <a name="upgrade-a-sku-preview"></a>Actualización de una SKU (versión preliminar)

Este artículo le ayuda a actualizar del nivel básico al estándar (SKU). Una vez que actualice, no puede volver a la SKU básica sin eliminar y volver a configurar Bastion. En la versión preliminar, esta opción solo se puede configurar en Azure Portal. Para más información sobre el escalado de host, consulte [Valores de configuración: SKU](configuration-settings.md#skus). 

## <a name="configuration-steps"></a>Pasos de configuración

[!INCLUDE [Azure Bastion preview portal](../../includes/bastion-preview-portal-note.md)]

1. En Azure Portal, vaya al host bastión.
1. En la página **Configuración**, en **Nivel**, seleccione **Estándar** en el menú desplegable.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="Captura de pantalla de la lista desplegable de selección de niveles con Estándar seleccionado." lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. Haga clic en **Aplicar** para aplicar los cambios.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Opciones de configuración](configuration-settings.md) para información de configuración adicional.
* Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
