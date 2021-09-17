---
author: cherylmc
ms.author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 563de3b6c57f38d63f2759c62fccc5a17d17dae9
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778525"
---
1. Vaya a **Red WAN virtual**.

1. Seleccione **Conexiones de la red virtual**.

1. En la página de conexión de red virtual, seleccione **+Agregar conexión**.

   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/add.png" alt-text="Captura de pantalla que muestra la adición.":::

1. En la página **Agregar conexión**, configure la configuración necesaria. Para obtener más información acerca de la configuración de enrutamiento, consulte [Acerca del enrutamiento](../articles/virtual-wan/about-virtual-hub-routing.md).
 
   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/connection.png" alt-text="Captura de pantalla que muestra la página de conexión de la red virtual.":::

   * **Nombre de la conexión**: asigne un nombre a la conexión.
   * **Centros de conectividad**: seleccione el centro de conectividad que desea asociar a esta conexión.
   * **Suscripción**: compruebe la suscripción.
   * **Grupo de recursos**: grupo de recursos que contiene la red virtual.
   * **Red virtual**: seleccione la red virtual que quiere conectar con este centro de conectividad. La red virtual que seleccione no puede tener una puerta de enlace de red virtual ya existente.
   * **Propagate to none** (Propagar a ninguno): se establece en **No** de manera predeterminada. Si cambia el conmutador a **Sí**, las opciones de configuración para la opción **Propagate to Route Tables** (Propagar a tablas de enrutamiento) y  **Propagate to labels** (Propagar a etiquetas) no estarán disponibles para la configuración.
   * **Associate Route Table**  (Asociar tabla de rutas): puede seleccionar la tabla de rutas que quiere asociar.
   * **Rutas estáticas**: puede usar esta opción para especificar el próximo salto.

1. Una vez establezca la configuración que quiera, seleccione **Crear** para crear la conexión.