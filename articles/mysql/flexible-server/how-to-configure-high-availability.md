---
title: Administración de la alta disponibilidad con redundancia de zona en un servidor flexible de Azure Database for MySQL mediante Azure Portal
description: En este artículo se describe cómo habilitar o deshabilitar la alta disponibilidad con redundancia de zona en un servidor flexible de Azure Database for MySQL mediante Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 9cd237a6441c8da748c9c5b999a608cfcecb0d92
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653601"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Administración de la alta disponibilidad con redundancia de zona en un servidor flexible de Azure Database for MySQL (versión preliminar)

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este artículo se describe cómo habilitar o deshabilitar la configuración de la alta disponibilidad con redundancia de zona en un servidor flexible.

La característica de alta disponibilidad proporciona una réplica principal y una réplica en espera separadas físicamente en distintas zonas. Para más información, consulte la [documentación sobre los conceptos de alta disponibilidad](./concepts/../concepts-high-availability.md).

> [!IMPORTANT]
> Solo puede habilitar la alta disponibilidad con redundancia de zona durante la creación de un servidor flexible.

En esta página se proporcionan instrucciones sobre cómo habilitar o deshabilitar la alta disponibilidad. Esta operación no cambia las demás opciones, como la configuración de red virtual, la configuración de firewall y la retención de copias de seguridad. Del mismo modo, la deshabilitación de la alta disponibilidad es una operación en línea y no afecta a la conectividad ni a las operaciones de las aplicaciones.

> [!IMPORTANT]
> La alta disponibilidad con redundancia de zona está disponible en un conjunto limitado de regiones: Sudeste Asiático, Oeste de EE. UU. 2, Oeste de Europa y Este de EE. UU.  

## <a name="enable-high-availability-during-server-creation"></a>Habilitación de la alta disponibilidad durante la creación del servidor

En esta sección se proporcionan detalles específicos de los campos relacionados con la alta disponibilidad. Puede seguir estos pasos para implementar la alta disponibilidad al crear el servidor flexible.

1. En [Azure Portal](https://portal.azure.com/), elija Servidor flexible y seleccione **Crear**.  Para más información sobre cómo rellenar los datos de **Suscripción**, **Grupo de recursos**, **Nombre del servidor**, **Región** y otros campos, consulte la documentación de procedimientos de la creación del servidor.

2. Haga clic en la casilla **Zone redundant high availability** (Alta disponibilidad con redundancia de zona) en la opción Disponibilidad.

3. Si quiere cambiar las opciones de proceso y almacenamiento predeterminadas, seleccione **Configurar servidor**.

4. Si la opción de alta disponibilidad está activada, el nivel por ráfagas no estará disponible para elegirlo. Puede elegir los niveles de proceso **De uso general** u **Optimizado para memoria**.

    > [!IMPORTANT]
    > Solo se admite la alta disponibilidad con redundancia de zona en los planes de tarifa ***De uso general** _ y _ *_Optimizado para memoria_**.

5. Seleccione **Tamaño de proceso** para elegirlo en la lista desplegable.

6. Seleccione el **tamaño de almacenamiento** en GiB con la barra deslizante y seleccione el **período de retención de copia de seguridad** entre 7 y 35 días.   

## <a name="disable-high-availability"></a>Deshabilitación de la alta disponibilidad

Siga estos pasos para deshabilitar la alta disponibilidad en el servidor flexible que ya está configurado con redundancia de zona.

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible de Azure Database for MySQL existente.

2. En la página del servidor flexible, haga clic en **Alta disponibilidad** en el panel frontal para abrir la página de alta disponibilidad.

3. Active la casilla **Zone redundant high availability** (Alta disponibilidad con redundancia de zona) para desactivar la opción y seleccione **Guardar** para guardar el cambio.

4. Aparece un cuadro de diálogo de confirmación en el que puede confirmar la deshabilitación de la alta disponibilidad.

5. Haga clic en el botón **Disable HA** (Deshabilitar la alta disponibilidad) para deshabilitar esta opción.

6. Aparece una notificación que muestra que la retirada de la implementación de la alta disponibilidad está en curso.

## <a name="forced-failover"></a>conmutación por error forzada

Siga estos pasos para forzar la conmutación por error desde el servidor flexible principal al servidor flexible en espera.

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor flexible de Azure Database for MySQL existente con la característica de alta disponibilidad habilitada.

2. En la página del servidor flexible, haga clic en **Alta disponibilidad** en el panel frontal para abrir la página de alta disponibilidad.

3. Compruebe la **zona de disponibilidad principal** y la **zona de disponibilidad en espera**

4. Seleccione **Conmutación por error forzada** para iniciar el procedimiento de conmutación por error manual. Un elemento emergente le informará sobre el tiempo esperado de la conmutación por error en función de la carga de trabajo actual en el dispositivo principal y la antigüedad del último punto de comprobación. Tras leer el mensaje, seleccione Aceptar.

5. Aparecerá una notificación donde se menciona que la conmutación por error está en curso.

6. Una vez que la conmutación por error al servidor en espera se realice correctamente, se abrirá una notificación.

7. Compruebe la nueva **zona de disponibilidad principal** y la **zona de disponibilidad en espera**.

![Cómo llevar a cabo la conmutación por error forzada](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
- Obtenga información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md)
