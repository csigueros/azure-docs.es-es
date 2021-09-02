---
title: Restauración de un servidor flexible de Azure Database for MySQL con Azure Portal
description: En este artículo se explica cómo realizar operaciones de restauración en el servidor flexible de Azure Database for MySQL mediante Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 687d9386d330e5b09366e22ace8f7fd8666ee9d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781064"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Restauración a un momento dado de Azure Database for MySQL: servidor flexible (versión preliminar) con Azure Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL: servidor flexible está actualmente en versión preliminar pública.

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

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>Uso de la restauración para mover un servidor del acceso público al acceso privado

Siga estos pasos para restaurar un servidor flexible mediante la copia de seguridad más antigua.

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible cuya copia de seguridad quiera restaurar.

2. En la página Información general, haga clic en **Restaurar**.

3. Se mostrará la página de restauración con una opción para elegir entre el primer punto de restauración y uno personalizado.

4. Elija el **punto de restauración más antiguo** o un **punto de restauración personalizado**.

5. Indique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**.

6. Indique un nombre de servidor nuevo en el campo **Restaurar en el servidor nuevo**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="ver información general":::

7. Vaya a la pestaña **Redes** para configurar las opciones de red.

8. En **Método de conectividad**, seleccione **Acceso privado (integración con red virtual)** . Vaya a la sección **Red virtual**, puede seleccionar una *red virtual* ya existente y una *Subred* que esté delegada en *Microsoft.DBforMySQL/flexibleServers* o crear una nueva haciendo clic en el vínculo *Crear red virtual*.
    > [!Note]
    > Solo las redes virtuales y subredes de la misma región y suscripción se mostrarán en la lista desplegable. </br>
    > La subred elegida se delegará a *Microsoft.DBforMySQL/flexibleServers*. Esto significa que solo los servidores flexibles de Azure Database for MySQL pueden usar esa subred.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Configuración de Vnet":::

9. Seleccione una **zona DNS privada** existente o cree una nueva.
    > [!NOTE]
    > Los nombres de las zonas DNS privadas deben terminar con `mysql.database.azure.com`. </br>
    > Si no ve la opción de crear una nueva zona DNS privada, escriba el nombre del servidor en la pestaña **Aspectos básicos**.</br>
    > Una vez que se haya implementado el servidor flexible en una red virtual y una subred, no se puede trasladar a Acceso público (direcciones IP permitidas).</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="Configuración de DNS":::
10. Seleccione **Revisar y crear** para revisar la configuración del servidor flexible.
11. Seleccione **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento puede tardar unos minutos.

12. Se mostrará una notificación en la que se indica que se ha iniciado la operación de restauración.

## <a name="perform-post-restore-tasks"></a>Tareas posteriores a la restauración

Una vez finalizada la restauración, deberá realizar las siguientes tareas para que los usuarios y las aplicaciones vuelvan a conectarse:

- Si el nuevo servidor está destinado a reemplazar al original, redirija a los clientes y las aplicaciones cliente al nuevo servidor.
- Asegúrese de que haya vigentes reglas de red virtual adecuadas para que los usuarios se conecten. Estas reglas no se copian desde el servidor original.
- No se olvide de emplear los permisos de nivel de base de datos y los inicios de sesión apropiados.
- Configure las alertas según corresponda para el servidor recién restaurado.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [continuidad del negocio](concepts-business-continuity.md).
