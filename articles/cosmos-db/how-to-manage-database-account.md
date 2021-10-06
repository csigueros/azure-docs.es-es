---
title: Aprenda a administrar cuentas de base de datos en Azure Cosmos DB
description: Aprenda a administrar recursos de Azure Cosmos DB mediante Azure Portal, PowerShell, la CLI y plantillas de Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: d6cf0b9ba4fe856a153abf004a81c250c59b2aa1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618616"
---
# <a name="manage-an-azure-cosmos-account-using-the-azure-portal"></a>Administración de una cuenta de Azure Cosmos mediante Azure Portal

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

En este artículo se describe cómo administrar varias tareas en una cuenta de Azure Cosmos mediante Azure Portal. 

> [!TIP]
> Azure Cosmos DB también se puede administrar con otros clientes de administración de Azure, incluidos [Azure PowerShell](manage-with-powershell.md), [la CLI de Azure](sql/manage-with-cli.md), [plantillas de Azure Resource Manager](./manage-with-templates.md) y [Bicep](sql/manage-with-bicep.md).

## <a name="create-an-account"></a>Crear una cuenta

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

## <a name="addremove-regions-from-your-database-account"></a>Incorporación o eliminación de regiones de una cuenta de base de datos

> [!TIP]
> Cuando se agrega una nueva región, todos los datos deben replicarse por completo y estar confirmados en la nueva región antes de que la región se marque como disponible. La cantidad de tiempo que tarde esta operación dependerá de la cantidad de datos almacenados en la cuenta.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

1. Vaya a la cuenta de Azure Cosmos y abra el menú **Replicar datos globalmente**.

1. Para agregar regiones, seleccione los hexágonos del mapa con la etiqueta **+** correspondiente a la región (o regiones) deseada. Otra forma de agregar una región es seleccionar la opción **+ Agregar región** y elegir una región en el menú desplegable.

1. Para quitar regiones, desactive una o varias regiones del mapa; para ello, seleccione los hexágonos azules con marcas de verificación. O seleccione el icono de "Papelera" (🗑) junto a la región en el lado derecho.

1. Seleccione **Aceptar** para guardar los cambios.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Menú Agregar o eliminar regiones":::

En el modo de escritura de región individual, no puede quitar la región de escritura. Debe realizar la conmutación por error a otra región antes de poder eliminar la región de escritura actual.

En el modo de escritura de varias regiones, puede agregar o quitar cualquier región, siempre que tenga al menos una región.

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Configuración de varias regiones de escritura

Abra la pestaña **Replicar datos globalmente** y seleccione **Habilitar** para habilitar las escrituras en varias regiones. Después de habilitar las escrituras en varias regiones, todas las regiones de lectura que actualmente tiene en la cuenta se convertirán en regiones de lectura y escritura.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Captura de pantalla de la cuenta de Azure Cosmos que configura las escrituras en varias regiones":::

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Habilitación de la conmutación por error automática en una cuenta de Azure Cosmos

La opción de conmutación automática por error permite que Azure Cosmos DB conmute por error en la región con la prioridad de conmutación por error más alta sin acción del usuario si una región deja de estar disponible. Cuando la conmutación automática por error está habilitada, es posible modificar la prioridad de región. La cuenta debe tener dos o más regiones para habilitar la conmutación automática por error.

1. En la cuenta de Azure Cosmos, abra el panel **Replicar datos globalmente**.

2. En la parte superior del panel, seleccione **Conmutación automática por error**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menú Replicar datos globalmente":::

3. En el panel **Conmutación automática por error**, asegúrese de que **Habilitar la conmutación automática por error** está establecido en **Activado**. 

4. Seleccione **Guardar**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menú Conmutación por error automática del portal":::

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Establecimiento de las prioridades de conmutación por error para la cuenta de Azure Cosmos

Una vez que una cuenta de Cosmos se configura para la conmutación automática por error, es posible modificar la prioridad de la conmutación por error para las regiones.

> [!IMPORTANT]
> No se puede modificar la región de escritura (prioridad de conmutación por error de cero) cuando la cuenta está configurada para la conmutación automática por error. Para cambiar la región de escritura, debe deshabilitar la conmutación automática por error y realizar una conmutación por error manual.

1. En la cuenta de Azure Cosmos, abra el panel **Replicar datos globalmente**.

2. En la parte superior del panel, seleccione **Conmutación automática por error**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menú Replicar datos globalmente":::

3. En el panel **Conmutación automática por error**, asegúrese de que **Habilitar la conmutación automática por error** está establecido en **Activado**.

4. Para modificar la prioridad de la conmutación por error, arrastre las regiones de lectura mediante los tres puntos del lado izquierdo de la fila que aparecen al mantener el puntero sobre ellos.

5. Seleccione **Guardar**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menú Conmutación por error automática del portal":::

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Realización de una conmutación por error manual en una cuenta de Azure Cosmos

> [!IMPORTANT]
> La cuenta de Azure Cosmos debe estar configurada para la conmutación por error manual para que esta operación se realice correctamente.

1. Vaya a la cuenta de Azure Cosmos y abra el menú **Replicar datos globalmente**.

2. En la parte superior del menú, seleccione **Conmutación por error manual**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menú Replicar datos globalmente":::

3. En el menú **Conmutación por error manual**, seleccione la nueva región de escritura. Active la casilla de verificación para indicar que comprende que esta opción cambia la región de escritura.

4. Para desencadenar la conmutación por error, seleccione **Aceptar**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Menú Conmutación por error del portal":::

## <a name="next-steps"></a>Pasos siguientes

Para más información y ejemplos sobre cómo administrar la cuenta de Azure Cosmos así como la base de datos y los contenedores, lea estos artículos:

* [Manage Azure Cosmos DB using Azure PowerShell](manage-with-powershell.md) (Administración de Azure Cosmos DB con Azure PowerShell)
* [Manage Azure Cosmos DB using Azure CLI](sql/manage-with-cli.md) (Administración de Azure Cosmos DB con la CLI de Azure)
* [Administración de recursos de Core (SQL) API de Azure Cosmos DB con plantillas de Azure Resource Manager](./manage-with-templates.md)
* [Administración de recursos de Core (SQL) API de Azure Cosmos DB con Bicep](sql/manage-with-bicep.md)
