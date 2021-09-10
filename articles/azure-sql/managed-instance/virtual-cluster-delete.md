---
title: Eliminación de una subred después de eliminar una instancia de SQL Managed Instance
description: Aprenda a eliminar una red virtual de Azure después de eliminar una instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: d61a3f5a3dac8cfb6215bdfd6ff1c457c4e14150
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214402"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Eliminación de una subred después de eliminar una instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

En este artículo se proporcionan instrucciones sobre cómo eliminar manualmente una subred después de eliminar la última instancia administrada de Azure SQL que reside en ella.

Las instancias de SQL Managed Instance se implementan en [clústeres virtuales](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Cada clúster virtual está asociado a una subred, y se implementa durante la creación de la primera instancia. Del mismo modo, se quita automáticamente durante la eliminación de la última instancia, lo que deja a la subred vacía y lista para su eliminación. No es necesario realizar ninguna acción manual en el clúster virtual para liberar la subred. Una vez eliminado el último clúster virtual, se puede ir a eliminar la subred.

Hay circunstancias poco frecuentes en que la operación de creación puede producir un error y generar un clúster virtual vacío implementado. Además, como la creación de instancias [se puede cancelar](management-operations-cancel.md), es posible implementar un clúster virtual con instancias que residen en su interior, en un estado de error. La eliminación del clúster virtual se inicia automáticamente en estas situaciones, y se quita en segundo plano.

> [!NOTE]
> No hay cargos por mantener un clúster virtual vacío o instancias que no se han podido crear.

> [!IMPORTANT]
> - Para que la eliminación se realice correctamente, el clúster virtual no debe contener ninguna instancia de SQL Managed Instance. Esto no incluye las instancias que no se han podido crear. 
> - La eliminación de un clúster virtual es una operación de ejecución prolongada que requiere aproximadamente 1,5 horas (consulte [Operaciones de administración de SQL Managed Instance](management-operations-overview.md) para actualizar el tiempo de eliminación del clúster virtual). El clúster virtual seguirá visible en el portal hasta que se complete este proceso.
> - Solo se puede ejecutar una operación de eliminación en el clúster virtual. Todas las solicitudes de eliminación posteriores iniciadas por el cliente producen un error, ya que la operación de eliminación ya está en curso.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Eliminación de un clúster virtual desde Azure Portal

> [!IMPORTANT]
> A partir del 1 de septiembre de 2021. Todos los clústeres virtuales se quitan automáticamente cuando se ha eliminado la última instancia del clúster. Ya no es necesaria la eliminación manual del clúster virtual.

Para eliminar un clúster virtual mediante Azure Portal, busque los recursos del clúster virtual.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de Azure Portal con el cuadro de búsqueda resaltado](./media/virtual-cluster-delete/virtual-clusters-search.png)

Una vez que localice el clúster virtual que desea eliminar, seleccione este recurso y seleccione **Eliminar**. Se le pedirá que confirme la eliminación del clúster virtual.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla del panel de clústeres virtuales de Azure Portal con la opción Eliminar resaltada](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Las notificaciones de Azure Portal le mostrarán una confirmación de que la solicitud para eliminar el clúster virtual se ha enviado correctamente. La operación de eliminación en sí durará aproximadamente 1,5 horas, durante las cuales el clúster virtual seguirá visible en el portal. Una vez completado el proceso, el clúster virtual dejará de estar visible y se liberará la subred asociada a él para su reutilización.

> [!TIP]
> Si no se muestran instancias de SQL Managed Instance en el clúster virtual y no puede eliminar el clúster virtual, asegúrese de que no tiene una implementación de instancia en curso. Esto incluye las implementaciones iniciadas y canceladas que siguen en curso. Esto se debe a que estas operaciones aún utilizan el clúster virtual, lo que bloquea su eliminación. Revise la pestaña **Implementaciones** del grupo de recursos en el que se ha implementado la instancia para ver si hay implementaciones en curso. En este caso, espere a que la implementación finalice y luego elimine la instancia de SQL Managed Instance. El clúster virtual se elimina de forma simultánea como parte de la eliminación de la instancia.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Eliminación de un clúster virtual mediante la API

Para eliminar un clúster virtual mediante la API, utilice los parámetros de URI que se especifican en el [método de eliminación de clústeres virtuales](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general, consulte [¿Qué es Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Obtenga información sobre la [arquitectura de conectividad en Instancia administrada de SQL](connectivity-architecture-overview.md).
- Aprenda a [modificar una red virtual existente para Instancia administrada de SQL](vnet-existing-add-subnet.md).
- Para obtener un tutorial que muestre cómo crear una red virtual, crear una instancia de Azure SQL Managed Instance y restaurar una base de datos desde una copia de seguridad de base de datos, consulte [Creación de una instancia de Azure SQL Managed Instance (portal)](instance-create-quickstart.md).
- Para problemas de DNS, consulte [Configuración de un DNS personalizado](custom-dns-configure.md).
