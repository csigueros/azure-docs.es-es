---
title: 'Administración de redes virtuales mediante Azure Portal en Azure Database for MySQL: Servidor flexible'
description: 'Creación y administración de redes virtuales para Azure Database for MySQL: Servidor flexible mediante Azure Portal'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b317dc91f8d4768e767a93d618e9c768d4039659
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780385"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Creación y administración de redes virtuales para Azure Database for MySQL: Servidor flexible mediante Azure Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Actualmente, Azure Database for MySQL: Servidor flexible se encuentra en versión preliminar pública.

El Servidor flexible de Azure Database for MySQL admite dos métodos de conectividad de red mutuamente excluyentes para conectarse al servidor flexible. Las dos opciones son las siguientes:

- Acceso público (direcciones IP permitidas)
- Acceso privado (integración con red virtual)

En este artículo nos vamos a centrar en la creación de un servidor de MySQL con **acceso privado (Integración con red virtual)** mediante Azure Portal. Con Acceso privado (Integración con red virtual), puede implementar el servidor flexible en la propia instancia de [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Las redes de Azure Virtual Network proporcionan una comunicación de red privada y segura. Con el acceso privado, las conexiones al servidor MySQL están restringidas a la red virtual. Para obtener más información al respecto, consulte [Acceso privado (Integración con red virtual)](./concepts-networking-vnet.md#private-access-vnet-integration).

>[!Note]
>Puede implementar el servidor flexible en una red virtual y una subred durante la creación del servidor. Una vez que se haya implementado el servidor flexible, no se puede trasladar a otra red virtual, subred o a *Acceso público (direcciones IP permitidas)* .

## <a name="prerequisites"></a>Requisitos previos

Para crear un servidor flexible en una red virtual, necesitará lo siguiente:

- Una [red virtual](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > La red virtual y la subred deben estar en la misma región y suscripción que el servidor flexible.

- [Delegar una subred](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) a **Microsoft.DBforMySQL/flexibleServers**. Esta delegación significa que solo los servidores flexibles de Azure Database for MySQL pueden utilizar esa subred. No puede haber otros tipos de recursos de Azure en la subred delegada.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Creación del Servidor flexible de Azure Database for MySQL en una red virtual existente

1. En la esquina superior izquierda del portal, seleccione **Crear un recurso** (+).
2. Seleccione **Bases de datos** > **Azure Database for MySQL**. También puede escribir **MySQL** en el cuadro de búsqueda para encontrar el servicio.
3. Seleccione **Servidor flexible** como opción de implementación.
4. Rellene el formulario **Datos básicos**.
5. Vaya a la pestaña **Redes**.
6. En **Método de conectividad**, seleccione **Acceso privado (integración con red virtual)** . Vaya a la sección **Red virtual**, puede seleccionar una *red virtual* ya existente y una *Subred* que esté delegada en *Microsoft.DBforMySQL/flexibleServers* o crear una nueva haciendo clic en el vínculo *Crear red virtual*.
    > [!Note]
    > Solo las redes virtuales y subredes de la misma región y suscripción se mostrarán en la lista desplegable. </br>
    > La subred elegida se delegará a *Microsoft.DBforMySQL/flexibleServers*. Esto significa que solo los servidores flexibles de Azure Database for MySQL pueden usar esa subred.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Integración con red virtual":::

7. Seleccione una **zona DNS privada** existente o cree una nueva.
    > [!NOTE]
    > Los nombres de las zonas DNS privadas deben terminar con `mysql.database.azure.com`. </br>
    > Si no ve la opción de crear una zona DNS privada nueva, escriba el nombre del servidor en la pestaña **Aspectos básicos**.</br>
    > Una vez que se haya implementado el servidor flexible en una red virtual y una subred, no se puede trasladar a Acceso público (direcciones IP permitidas).</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dnsconfiguration":::
8. Seleccione **Revisar y crear** para revisar la configuración del servidor flexible.
9. Seleccione **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento puede tardar unos minutos.

## <a name="next-steps"></a>Pasos siguientes

- [Creación y administración de redes virtuales del Servidor flexible de Azure Database for MySQL mediante la CLI de Azure](./how-to-manage-virtual-network-cli.md).
- Obtenga más información sobre [redes en el Servidor flexible de Azure Database for MySQL](./concepts-networking.md).
- Más información sobre [Redes virtuales del Servidor flexible de Azure Database for MySQL](./concepts-networking-vnet.md#private-access-vnet-integration).
