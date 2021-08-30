---
title: 'Migración de MySQL local a Azure Database for MySQL: continuidad empresarial y recuperación ante desastres (BCDR)'
description: Igual que sucede con cualquier sistema crítico, tener una copia de seguridad y restauración, y una estrategia de recuperación ante desastres (BCDR) es una parte importante del diseño general del sistema.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 35ab4f952b2e8082f4923926f11698fef352c8f8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296222"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-business-continuity-and-disaster-recovery-bcdr"></a>Migración de MySQL local a Azure Database for MySQL: continuidad empresarial y recuperación ante desastres (BCDR)

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Prerrequisitos

[Optimización](11-optimization.md)

## <a name="back-up-and-restore"></a>Copia de seguridad y restauración

Igual que sucede con cualquier sistema crítico, tener una copia de seguridad y restauración, y una estrategia de recuperación ante desastres (BCDR) es una parte importante del diseño general del sistema. Si se produce un evento imprevisto, debe tener la capacidad de restaurar los datos a un momento dado (objetivo de punto de recuperación) en un período de tiempo razonable (objetivo de tiempo de recuperación).

### <a name="backup"></a>Copia de seguridad

Azure Database for MySQL admite copias de seguridad automáticas durante 7 días de forma predeterminada. Puede que resulte adecuado modificarlo al máximo actual de 35 días. Es importante tener en cuenta que, si el valor se cambia a 35 días, habrá cargos por cualquier almacenamiento de copia de seguridad adicional por encima del 1x del almacenamiento asignado.

Existen varias limitaciones actuales en la característica de copia de seguridad de base de datos, tal como se describe en el artículo que cuenta con los documentos [Copia de seguridad y restauración en Azure Database for MySQL](../../concepts-backup.md). Es importante comprender estas limitaciones a la hora de decidir qué estrategias adicionales se deben implementar.

Algunos elementos que se deben tener en cuenta son:

- No hay acceso directo a las copias de seguridad.

- Los niveles que permiten hasta 4 TB realizan una copia de seguridad completa una vez por semana, una diferencial dos veces al día y registros cada cinco minutos.

- Los niveles que permiten hasta 16 TB tienen copias de seguridad basadas en instantáneas.

    > [!NOTE]
    > [Algunas regiones](../../concepts-pricing-tiers.md#storage) aún no admiten el almacenamiento de hasta 16 TB.

### <a name="restore"></a>Restauración

La redundancia (local o geográfica) debe configurarse durante la creación del servidor. Sin embargo, se puede realizar una restauración geográfica, ya que permite la modificación de estas opciones durante el proceso de restauración. Al realizar una operación de restauración, puede que se detenga temporalmente la conectividad y todas las aplicaciones estén sin funcionar durante el proceso de restauración.

Durante una restauración de base de datos, también se tienen que restaurar los elementos compatibles externos de la base de datos. Revise el proceso de migración. Consulte [Realizar tareas posteriores a la restauración](../../concepts-backup.md#perform-post-restore-tasks) para más información.

## <a name="read-replicas"></a>Réplicas de lectura

Las [réplicas de lectura](../../concepts-read-replicas.md) se pueden usar para aumentar el rendimiento de lectura de MySQL, mejorar el rendimiento de los usuarios regionales e implementar la recuperación ante desastres. Al crear una o varias réplicas de lectura, tenga en cuenta que se aplicarán cargos adicionales para el mismo proceso y almacenamiento que en el servidor principal.

## <a name="deleted-servers"></a>Servidores eliminados

Si un administrador o un actor malintencionado elimina el servidor en Azure Portal o a través de métodos automatizados, también se eliminarán todas las copias de seguridad y réplicas de lectura. Es importante que se creen [bloqueos de recursos](../../../azure-resource-manager/management/lock-resources.md) en el grupo de recursos de Azure Database for MySQL para agregar en las instancias una capa adicional de prevención ante eliminaciones.

## <a name="regional-failure"></a>Error regional

Aunque es poco frecuente, si se produce un error regional, se pueden usar las copias de seguridad con redundancia geográfica o una réplica de lectura para volver a ejecutar las cargas de trabajo de datos. Es mejor tener la replicación geográfica y una réplica de lectura disponibles para conseguir la mejor protección frente a errores regionales inesperados.

> [!NOTE]
> Tenga en cuenta que el cambio de la región del servidor de base de datos también significa que el punto de conexión cambiará y que las configuraciones de la aplicación tendrán que actualizarse en consecuencia.

### <a name="load-balancers"></a>Equilibradores de carga

Si la aplicación tiene muchas instancias diferentes de todo el mundo, puede que no sea factible actualizar todos los clientes. Use una instancia de [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) o [Application Gateway](../../../application-gateway/overview.md) para implementar una funcionalidad de conmutación por error sin problemas. Aunque estas opciones son útiles y ahorran tiempo, estas herramientas no son necesarias para la funcionalidad de conmutación por error regional.

## <a name="wwi-scenario"></a>Escenario de WWI

WWI quiere probar las funcionalidades de conmutación por error de las réplicas de lectura para realizar los pasos descritos a continuación.

### <a name="creating-a-read-replica"></a>Creación de una réplica de lectura

- Abra Azure Portal.

- Vaya a la instancia de Azure Database for MySQL.

- En **Configuración**, seleccione **Replicación**.

- Seleccione **Agregar réplica**.

- Escriba un nombre de servidor.

- Seleccione la región.

- Seleccione **Aceptar** y espere a que se implemente la instancia. En función del tamaño de la instancia principal, es posible que tarde algún tiempo en replicarse.

    > [!NOTE]
    > Cada réplica incurre en cargos adicionales iguales a los de la instancia principal.

### <a name="fail-over-to-read-replica"></a>Conmutación por error a una réplica de lectura

Una vez creada una réplica de lectura y completado el proceso de replicación, se puede usar para la conmutación por error. La replicación se detiene durante una conmutación por error y hará que la réplica de lectura sea su propia instancia principal.

Pasos de la conmutación por error:

- Abra Azure Portal.

- Vaya a la instancia de Azure Database for MySQL.

- En **Configuración**, seleccione **Replicación**.

- Seleccione una de las réplicas de lectura.

- Selección de **Detener replicación**. Esto interrumpe la réplica de lectura.

- Modifique todas las cadenas de conexión de aplicaciones para que apunten a la nueva instancia principal.

## <a name="bcdr-checklist"></a>Lista de comprobación de BCDR

- Modifique la frecuencia de copia de seguridad para que cumpla con los requisitos.

- Configure réplicas de lectura para cargas de trabajo de lectura intensiva y conmutaciones por error regionales.

- Cree bloqueos de recursos en grupos de recursos.

- Implemente una estrategia de equilibrio de carga para las aplicaciones y así obtener una conmutación por error rápida.  


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Seguridad](./13-security.md)