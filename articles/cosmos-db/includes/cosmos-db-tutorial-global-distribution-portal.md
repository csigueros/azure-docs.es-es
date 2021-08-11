---
title: Distribución global de Azure Cosmos DB
description: Aprenda a replicar datos globalmente con Azure Cosmos DB en Azure Portal
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 70c0400a5b1cdf4d0de6c657bd907061b1aa31d0
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233874"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Incorporación de regiones a la base de datos global mediante Azure Portal
Azure Cosmos DB está disponible en todas las [regiones de Azure][azureregions] globales. Tras seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos, puede asociar una o varias regiones (según la elección del nivel de coherencia y las necesidades de distribución global).

1. En la barra izquierda de [Azure Portal](https://portal.azure.com/), haga clic en **Azure Cosmos DB**.
2. En la página **Azure Cosmos DB**, seleccione la cuenta de base de datos que quiere modificar.
3. En la página de la cuenta, haga clic en **Replicar datos globalmente** en el menú.
4. En la página **Replicar datos globalmente**, seleccione las regiones que quiere agregar o quitar haciendo clic en ellas en el mapa y, después, haga clic en **Guardar**. Agregar regiones conlleva un costo; consulte la [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/) o el artículo [Distribución de datos global con Azure Cosmos DB](../distribute-data-globally.md) para más información.
   
    ![Haga clic en las regiones en el mapa para agregarlas o quitarlas][1]
    
Cuando agrega una segunda región, se habilita la opción **Conmutación por error manual** en la página **Replicar datos globalmente** del portal. Puede usar esta opción para probar el proceso de conmutación por error o cambiar la región de escritura principal. Cuando agrega una tercera región, la opción **Prioridades de conmutación por error** se habilita en la misma página para que pueda cambiar el orden de la conmutación por error en las lecturas.  

### <a name="selecting-global-database-regions"></a>Selección de regiones de la base de datos global
Hay dos escenarios comunes para configurar dos o más regiones:

1. Proporcionar acceso de baja latencia a los datos a los usuarios finales, independientemente de la región del mundo en la que se encuentren.
2. Agregar resistencia regional para la continuidad empresarial y la recuperación ante desastres (BCDR).

Para proporcionar latencia baja a los usuarios finales, se recomienda implementar la aplicación y Azure Cosmos DB en las regiones correspondientes a la ubicación de los usuarios de la aplicación.

En el caso de BCDR, se recomienda agregar las regiones en función de los pares de regiones descritos en el artículo [Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure](../../best-practices-availability-paired-regions.md).

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[consistency]: ../consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/