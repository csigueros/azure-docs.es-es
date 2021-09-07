---
title: Actualización de un SKU
titleSuffix: Azure Bastion
description: Aprenda a cambiar los niveles de la SKU básica a la estándar.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: 5ed0dd6ad86b5f7758f0e1972867876eedc3b51a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225936"
---
# <a name="upgrade-a-sku-preview"></a>Actualización de una SKU (versión preliminar)

Este artículo le ayuda a actualizar del nivel básico al estándar (SKU). Una vez que actualice, no puede volver a la SKU básica sin eliminar y volver a configurar Bastion. En la versión preliminar, esta opción solo se puede configurar en Azure Portal. Para más información sobre el escalado de host, consulte [Valores de configuración: SKU](configuration-settings.md#skus). 

## <a name="configuration-steps"></a>Pasos de configuración

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
1. En Azure Portal, vaya al host bastión.
1. En la página **Configuración**, en **Nivel**, seleccione **Estándar** en el menú desplegable.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="Captura de pantalla de la lista desplegable de selección de niveles con Estándar seleccionado." lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. Haga clic en **Aplicar** para aplicar los cambios.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Opciones de configuración](configuration-settings.md) para información de configuración adicional.
* Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
