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
ms.custom: ignite-fall-2021
ms.openlocfilehash: f88ab798618608b8dbb0e27fb772a82a5199a889
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080866"
---
# <a name="upgrade-a-sku"></a>Actualización de un SKU

Este artículo le ayuda a actualizar del nivel básico al estándar (SKU). Una vez que actualice, no puede volver a la SKU básica sin eliminar y volver a configurar Bastion. Actualmente, esta opción solo se puede configurar en Azure Portal. Para más información sobre el escalado de host, consulte [Valores de configuración: SKU](configuration-settings.md#skus). 

## <a name="configuration-steps"></a>Pasos de configuración

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
1. En Azure Portal, vaya al host bastión.
1. En la página **Configuración**, en **Nivel**, seleccione **Estándar** en el menú desplegable.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="Captura de pantalla de la lista desplegable de selección de niveles con Estándar seleccionado." lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. Haga clic en **Aplicar** para aplicar los cambios.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Opciones de configuración](configuration-settings.md) para información de configuración adicional.
* Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
