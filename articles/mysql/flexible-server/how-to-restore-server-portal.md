---
title: Restauración de un servidor flexible de Azure Database for MySQL con Azure Portal
description: En este artículo se explica cómo realizar operaciones de restauración en el servidor flexible de Azure Database for MySQL mediante Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: cce31cc3d83be03da462c9345c1b8d3d86ef6228
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467946"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Restauración a un momento dado del servidor flexible de Azure Database for MySQL con Azure Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este artículo se proporciona un procedimiento detallado para llevar a cabo recuperaciones a un momento dado en servidores flexibles mediante copias de seguridad.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía, necesita:

- Debe tener Azure Database for MySQL: servidor flexible.

## <a name="restore-to-the-latest-restore-point"></a>Recuperación al punto de restauración más reciente

Siga estos pasos para restaurar un servidor flexible utilizando su copia de seguridad más antigua.

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar.

2. En el panel izquierdo, haga clic en **Información general**.

3. En la página Información general, haga clic en **Restaurar**.

4. Se mostrará la página de restauración con una opción para elegir entre el **punto de restauración más reciente** y uno personalizado.

5. Seleccione el **punto de restauración más reciente**.

6. Indique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="Punto de restauración más antiguo":::

7. Haga clic en **Aceptar**.

8. Se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.


## <a name="restore-to-a-fastest-restore-point"></a>Restauración a un punto de restauración más rápido

Siga estos pasos para restaurar el servidor flexible mediante una copia de seguridad completa existente como punto de restauración más rápido. 

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar. 

2. En el panel izquierdo, haga clic en **Información general**. 

3. En la página Información general, haga clic en **Restaurar**. 

4. Se mostrará la página de restauración con una opción para elegir entre el punto de restauración más reciente, el punto de restauración personalizado y el punto de restauración más rápido. 

5. Seleccione la opción **Seleccionar el punto de restauración más rápido (restaurar con copia de seguridad completa)** . 

6. Seleccione la copia de seguridad completa deseada en la lista desplegable **Punto de restauración más rápido (UTC)** . 
 
    :::image type="content" source="./media/how-to-restore-server-portal/fastest-restore-point.png" alt-text="Punto de restauración más rápido":::

7. Indique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**.

8. Haga clic en **Revisar + crear**. 

9. Después de hacer clic en **Crear**, se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.  

## <a name="restore-from-a-full-backup-through-the-backup-and-restore-blade"></a>Restauración desde una copia de seguridad completa a través de la hoja Copia de seguridad y restauración

Siga estos pasos para restaurar un servidor flexible utilizando una copia de seguridad completa existente. 

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar. 

2. Haga clic **Copia de seguridad y restauración** en el panel izquierdo. 

3. Aparecerá la página para ver las copias de seguridad disponibles, con la opción de restaurar a partir de todas las copias de seguridad automatizadas completas realizadas para el servidor en la duración de retención.  

4. Seleccione la copia de seguridad completa deseada de la lista haciendo clic en la acción correspondiente **Restaurar**. 
 
    :::image type="content" source="./media/how-to-restore-server-portal/view-available-backups.png" alt-text="Ver copias de seguridad disponibles":::

5. La página de restauración se mostrará con la opción de Punto de restauración más rápido seleccionada de forma predeterminada y la marca de tiempo de la copia de seguridad completa deseada seleccionada en la página Ver copias de seguridad disponibles. 

6. Indique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**.

7. Haga clic en **Revisar + crear**. 

8. Después de hacer clic en **Crear**, se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.  


## <a name="geo-restore-to-latest-restore-point"></a>Restauración geográfica al punto de restauración más reciente

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar.

2. En el panel izquierdo, haga clic en **Información general**.

3. En la página Información general, haga clic en **Restaurar**.

4. Se mostrará la página de restauración con la opción de elegir **Restauración con redundancia geográfica**. Si ha configurado el servidor para copias de seguridad con redundancia geográfica, el servidor se puede restaurar en la región emparejada de Azure correspondiente y se puede habilitar la opción de restauración con redundancia geográfica. La opción de restauración con redundancia geográfica restaura el servidor a la marca de tiempo de UTC más reciente ahora y, por tanto, después de seleccionar la restauración con redundancia geográfica, las opciones de restauración a un momento dado no se pueden seleccionar simultáneamente.

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-flex.png" alt-text="Opción de restauración geográfica":::

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-enabled-flex.png" alt-text="Habilitación de la restauración geográfica":::

5. Proporcione un nuevo nombre de servidor en el campo **Nombre** de la sección Detalles del servidor.

6. Seleccione **Revisar y crear** para revisar las selecciones. 

7. Se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración. Esta operación puede tardar algunos minutos. 

El nuevo servidor creado mediante restauración geográfica tiene el mismo nombre de inicio de sesión y contraseña de administrador del servidor que el servidor existente tenía cuando se inició la restauración. La contraseña se puede cambiar en la página Información general del nuevo servidor. Además, durante una restauración geográfica, la configuración de la **red**, como los parámetros de la red virtual y las reglas de firewall, se pueden establecer como se describe en la sección siguiente.

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>Uso de la restauración para mover un servidor del acceso público al acceso privado

Siga estos pasos para restaurar un servidor flexible mediante la copia de seguridad más antigua.

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar.

2. En la página Información general, haga clic en **Restaurar**.

3. Aparecerá la página Restaurar con una opción para elegir entre las opciones Restauración geográfica o Restauración a un momento dado.

4. Elija **Restauración geográfica** o **Restauración a un momento dado**.

5. Indique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="ver información general":::

6. Vaya a la pestaña **Redes** para configurar las opciones de red.

7. En **Método de conectividad**, seleccione **Acceso privado (integración con red virtual)** . Vaya a la sección **Red virtual**, puede seleccionar una *red virtual* ya existente y una *Subred* que esté delegada en *Microsoft.DBforMySQL/flexibleServers* o crear una nueva haciendo clic en el vínculo *Crear red virtual*.
    > [!Note]
    > Solo las redes virtuales y subredes de la misma región y suscripción se mostrarán en la lista desplegable. </br>
    > La subred elegida se delegará a *Microsoft.DBforMySQL/flexibleServers*. Esto significa que solo los servidores flexibles de Azure Database for MySQL pueden usar esa subred.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Configuración de Vnet":::

8. Seleccione una **zona DNS privada** existente o cree una nueva.
    > [!NOTE]
    > Los nombres de las zonas DNS privadas deben terminar con `mysql.database.azure.com`. </br>
    > Si no ve la opción de crear una zona DNS privada nueva, escriba el nombre del servidor en la pestaña **Aspectos básicos**.</br>
    > Una vez que se haya implementado el servidor flexible en una red virtual y una subred, no se puede trasladar a Acceso público (direcciones IP permitidas).</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="Configuración de DNS":::
9. Seleccione **Revisar y crear** para revisar la configuración del servidor flexible.
10. Seleccione **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento puede tardar unos minutos.

11. Se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.


## <a name="perform-post-restore-tasks"></a>Tareas posteriores a la restauración

Una vez finalizada la restauración, deberá realizar las siguientes tareas para que los usuarios y las aplicaciones vuelvan a conectarse:

- Si el nuevo servidor está destinado a reemplazar al original, redirija a los clientes y las aplicaciones cliente al nuevo servidor.
- Asegúrese de que haya vigentes reglas de red virtual adecuadas para que los usuarios se conecten. Estas reglas no se copian desde el servidor original.
- No se olvide de emplear los permisos de nivel de base de datos y los inicios de sesión apropiados.
- Configure las alertas según corresponda para el servidor recién restaurado.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [continuidad del negocio](concepts-business-continuity.md).
