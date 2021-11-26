---
title: Restauración de un servidor flexible de Azure Database for PostgreSQL en Azure Portal
description: En este artículo se explica cómo realizar operaciones de restauración en Azure Database for PostgreSQL mediante Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/18/2021
ms.openlocfilehash: a9d09169b81e274202e9bf3df5a91844cf34807b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721961"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Restauración de un servidor flexible a un momento dado

> [!IMPORTANT]
> La opción de implementación Servidor flexible de Azure Database for PostgreSQL está en versión preliminar

En este artículo se proporciona un procedimiento detallado para llevar a cabo recuperaciones a un momento dado en servidores flexibles mediante copias de seguridad. Puede realizarlas al punto de restauración más reciente o a un punto de restauración personalizado dentro del período de retención.

## <a name="pre-requisites"></a>Requisitos previos

Para completar esta guía, necesita:

-   Un servidor flexible de Azure Database for PostgreSQL. Este mismo procedimiento también se puede aplicar a un servidor flexible configurado con redundancia de zona.

## <a name="restoring-to-the-latest-restore-point"></a>Restauración al punto de restauración más reciente

Siga estos pasos para restaurar un servidor flexible utilizando una copia de seguridad existente.

1.  En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar.

2.  Haga clic en **Información general** en el panel izquierdo y, después, haga clic en **Restaurar**.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Introducción a la restauración":::

3.  Se mostrará la página de restauración con una opción para elegir entre el punto de restauración más reciente y uno personalizado.

4.  Seleccione **Punto de restauración más reciente** y especifique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**. Opcionalmente, puede elegir la zona de disponibilidad en la que se va a realizar la restauración.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-latest.png" alt-text="Momento de restauración más reciente":::

5.  Haga clic en **Aceptar**.

6.  Se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.

## <a name="restoring-to-a-custom-restore-point"></a>Restauración a un punto de restauración personalizado

Siga estos pasos para restaurar un servidor flexible utilizando una copia de seguridad existente.

1.  En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar.

2.  En la página Información general, haga clic en **Restaurar**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Introducción a la restauración":::
    
3.  Se mostrará la página de restauración con una opción para elegir entre el punto de restauración más reciente y uno personalizado.

4.  Elija **Punto de restauración personalizado**.

5.  Seleccione la fecha y hora, y especifique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**. Proporcione un nuevo nombre de servidor y, opcionalmente, puede elegir la **zona de disponibilidad** en la que se va a restaurar.
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom-2.png" alt-text="Hora de restauración personalizada":::
 
6.  Haga clic en **Aceptar**.

7.  Se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.

## <a name="performing-geo-restore-preview"></a>Realización de restauración geográfica (versión preliminar)

Si el servidor de origen está configurado con copia de seguridad con redundancia geográfica, puede restaurar los servidores a una región emparejada. Tenga en cuenta que, para la primera restauración, es preciso esperar al menos una hora después de crear el servidor de origen.

1.  En [Azure Portal](https://portal.azure.com/), elija el servidor flexible cuya copia de seguridad quiera restaurar geográficamente.

2.  En la página Información general, haga clic en **Restaurar**.
 :::image type="content" source="./media/how-to-restore-server-portal/geo-restore-click.png" alt-text="Clic de restauración":::

3. En la página de restauración, elija Restauración con redundancia geográfica para restaurar en una región emparejada. 
 :::image type="content" source="./media/how-to-restore-server-portal/geo-restore-choose-checkbox.png" alt-text="Selección de restauración geográfica":::
 
4. La región y las versiones de la base de datos están seleccionadas previamente. Se restaurará a los últimos datos disponibles en la región emparejada. Puede elegir la **zona de disponibilidad** en la región en que se va a realizar la restauración.

5. De forma predeterminada, las copias de seguridad del servidor restaurado se configuran con copia de seguridad con redundancia geográfica. Si no desea realizar una copia de seguridad con redundancia geográfica, puede hacer clic en **Configurar servidor** y desactivar la copia de seguridad con redundancia geográfica.

6. Si el servidor de origen está configurado con **acceso privado**, solo puede realizar la restauración a otra red virtual de la región remota. Puede elegir una red virtual existente o crear una red virtual y restaurar el servidor en ella.  

## <a name="next-steps"></a>Pasos siguientes

-   Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
-   Obtenga información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md)
-   Más información sobre [copia de seguridad y recuperación](./concepts-backup-restore.md)
