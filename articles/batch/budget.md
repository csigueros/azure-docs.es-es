---
title: Obtención de análisis de costos y creación de presupuestos para Azure Batch
description: Obtenga información sobre cómo obtener un análisis de costos, establecer un presupuesto y reducir los costos para los recursos de proceso subyacentes y las licencias de software que se usan para ejecutar las cargas de trabajo de Batch.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: a590d8687c51b1693494c11d95de720f7f2c7eb0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538856"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Obtención de análisis de costos y creación de presupuestos para Azure Batch

Este tema le ayudará a entender los costos que pueden estar asociados con Azure Batch, cómo crear un presupuesto para un grupo o una cuenta de Batch y cómo reducir los costos de las cargas de trabajo de Batch.

## <a name="understand-costs-associated-with-batch-resources"></a>Descripción de los costos asociados con los recursos de Batch

No se aplica ningún costo por usar Azure Batch, aunque puede haber cargos por los recursos de proceso subyacentes y las licencias de software que se usan para ejecutar las cargas de trabajo de Batch. Se puede incurrir en costos desde las máquinas virtuales (VM) en un grupo, la transferencia de datos desde la VM o cualquier dato de entrada o salida almacenado en la nube.

### <a name="virtual-machines"></a>Máquinas virtuales

Las máquinas virtuales son el recurso más importante que se usa para el procesamiento por lotes. El costo de usar máquinas virtuales para Batch se calcula en función del tipo, la cantidad y la duración del uso. Las opciones de facturación de las máquinas virtuales incluyen [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) o [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pago por adelantado). Ambas opciones de pago tienen distintas ventajas en función de la carga de trabajo del proceso y afectarán a la factura de manera distinta.

Cada máquina virtual de un grupo que se crea con [Configuración de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) tiene un disco de sistema operativo asociado que usa discos administrados de Azure. Los discos administrados de Azure tienen un costo adicional y otros niveles de rendimiento de discos tienen también distintos costos.

### <a name="storage"></a>Storage

Cuando se implementan aplicaciones en los nodos de Batch (máquinas virtuales) mediante [paquetes de aplicación](batch-application-packages.md), se le cobrarán los recursos de Azure Storage que los paquetes de aplicación consumen. También se le cobrará el almacenamiento de cualquier archivo de entrada o salida, como los archivos de recursos u otros datos de registro.

En general, el costo de los datos de almacenamiento asociados con Batch es mucho menor que el costo de los recursos de proceso.

### <a name="networking-resources"></a>Recursos de redes

Los grupos de Batch usan recursos de red, y algunos tienen costos asociados. En concreto, para los grupos de configuración de máquina virtual, se usan equilibradores de carga estándar, que requieren direcciones IP estáticas. Los equilibradores de carga utilizados por Batch son visibles para [cuentas](accounts.md#batch-accounts) configuradas en el modo de suscripción de usuario, pero no en el modo de servicio de Batch.

Los equilibradores de carga estándar incurren en gastos por todos los datos transmitidos desde y hacia las máquinas virtuales de los grupos de Batch. Seleccione las API de Batch que recuperan datos desde los nodos de grupo (como Get Task/Node File), los paquetes de aplicación de tareas, los archivos de recursos o de salida y las imágenes de contenedor también incurrirán en cargos.

### <a name="additional-services"></a>Servicios adicionales

En función de los servicios que usa con la solución de Batch, es posible que incurra en tarifas adicionales. Consulte la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) para determinar el costo de cada servicio adicional. Entre los servicios que se suelen usar con Batch que pueden tener costos asociados se incluyen los siguientes:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Máquinas virtuales con aplicaciones de gráficos

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Visualización del análisis de costos y creación de un presupuesto para un grupo

En Azure Portal, puede crear presupuestos y alertas de gastos para los grupos o cuentas de Batch. Los presupuestos y las alertas son útiles para notificar a las partes interesadas de cualquier riesgo de exceso de gasto, aunque es posible que haya un retraso en las alertas de gasto y que se supere ligeramente el presupuesto.

> [!NOTE]
> En este ejemplo, el grupo usa la **configuración de máquina virtual**, que se recomienda para la mayoría de los grupos y tiene cargos basados en la estructura de precios de Virtual Machines. Los grupos que usan la **Configuración de Cloud Services** se cobran en función de la estructura de precios de Cloud Services.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Visualización del análisis de costos de un grupo de Batch

1. En Azure Portal, escriba o seleccione **Cost Management + Billing**.
1. Seleccione su suscripción en la sección **Ámbitos de facturación**.
1. En **Cost Management**, seleccione **Análisis de costos**.
1. Seleccione **Agregar filtro**. En la primera lista desplegable, seleccione **Recurso**.
1. En la segunda lista desplegable, seleccione el grupo de Batch. Una vez seleccionado el grupo, verá el análisis de costos del grupo, similar al ejemplo que se muestra aquí.
    ![Captura de pantalla que muestra el análisis de costos de un grupo en Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

El análisis de costos resultante muestra el costo del grupo, así como los recursos que contribuyen a este costo. En este ejemplo, las máquinas virtuales que se usan en el grupo son el recurso más costoso.

### <a name="create-a-budget-for-a-batch-pool"></a>Creación de un presupuesto para un grupo de Batch

1. En la página **Análisis de costos**, seleccione **Presupuesto: ninguno**.
1. Seleccione **Crear presupuesto >** .
1. Use la ventana para configurar un presupuesto específicamente para el grupo. Para más información, consulte el [Tutorial: Creación y administración de presupuestos en Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimización de los costos asociados con Azure Batch

En función de su escenario, puede que desee reducir los costos lo máximo posible. Considere la posibilidad de usar una o varias de estas estrategias para maximizar la eficacia de las cargas de trabajo y reducir los costos potenciales.

### <a name="use-low-priority-virtual-machines"></a>Uso de máquinas virtuales de prioridad baja

Las [VM de prioridad baja](batch-low-pri-vms.md) disminuyen el costo de las cargas de trabajo de Batch al usar la capacidad de proceso sobrante en Azure. Cuando se especifican máquinas virtuales de prioridad baja en los grupos, Batch usa este excedente para ejecutar la carga de trabajo. Puede lograr grandes ahorros de costos al usar VM de prioridad baja en lugar de VM dedicadas.

### <a name="use-ephemeral-os-disks"></a>Uso de discos de SO efímeros

Los grupos de configuración de máquina virtual pueden usar [discos de SO efímeros](create-pool-ephemeral-os-disk.md), que crean el disco de SO en la caché de la máquina virtual o SSD temporal, para evitar costos adicionales asociados a los discos administrados.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Compra de reservas de instancias de máquina virtual

Si piensa usar Batch durante largo tiempo, puede reducir el costo de las VM si usa [Reservas de Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para las cargas de trabajo. Una tarifa de reserva es considerablemente menor que una tarifa de pago por uso. Las instancias de máquina virtual que se usan sin una reserva se cobran según la tarifa de pago por uso. Al comprar una reserva, se aplica el descuento de la reserva.

### <a name="use-automatic-scaling"></a>Uso del escalado automático

El [escalado automático](batch-automatic-scaling.md) escala de manera dinámica el número de máquinas virtuales en el grupo de Batch en función de las demandas del trabajo actual. Al escalar el grupo según la duración de un trabajo, el escalado automático garantiza que las VM se escalen verticalmente y se usen solo cuando haya que realizar un trabajo. Cuando el trabajo se complete o no haya trabajos, las VM se reducen verticalmente de manera automáticamente para ahorrar recursos de proceso. El escalamiento permite disminuir los costos generales de la solución de Batch mediante el uso solo de los recursos que necesita.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md)
- Más información sobre las [VM de prioridad baja con Batch](batch-low-pri-vms.md).
