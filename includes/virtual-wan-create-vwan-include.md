---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 6dea9948a85278c236c704562d194f18c962683b
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778528"
---
1. En el portal, en la barra **Buscar recursos**, escriba **Virtual WAN** en el cuadro de búsqueda y seleccione **Entrar**.

1. Seleccione **Redes WAN virtuales** de los resultados. En la página Redes WAN virtuales, seleccione **Crear** para abrir la página **Crear una red WAN**.

1. En la página **Crear una red WAN**, en la pestaña **Aspectos básicos**, rellene los campos. Modifique los valores de ejemplo que se aplicarán a su entorno.

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Captura de pantalla que muestra el panel Crear una red WAN con la pestaña Aspectos básicos seleccionada.":::

   * **Suscripción**: seleccione la suscripción que quiere usar.
   * **Grupo de recursos**: créelo o utilice uno existente.
   * **Ubicación del grupo de recursos**: elija una ubicación para los recursos de la lista desplegable. Una red WAN es un recurso global y no reside en una región determinada. No obstante, tiene que seleccionar una región con el fin de administrar y ubicar el recurso de WAN que cree.
   * **Nombre**: escriba el nombre que quiera asignar a su la red WAN virtual.
   * **Tipo**: Básico o Estándar. Seleccione **Estándar**. Si selecciona Básico, entienda que las redes WAN virtuales básicas solo pueden contener centros de conectividad básicos. Los centros de conectividad básicos solo se pueden usar para conexiones de sitio a sitio.

1. Una vez rellenos los campos, en la parte inferior de la página, seleccione **Revisar y crear**.

1. Una vez que se haya superado la validación, seleccione **Crear** para crear la WAN virtual.
