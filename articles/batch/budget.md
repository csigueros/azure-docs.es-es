---
title: Obtención de análisis de costos y creación de presupuestos para Azure Batch
description: Obtenga información sobre cómo obtener un análisis de costos, establecer un presupuesto y reducir los costos para los recursos de proceso subyacentes y las licencias de software que se usan para ejecutar las cargas de trabajo de Batch.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 0e4c0b6235537d1e5c36e7adf997516d0f966d76
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532528"
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

## <a name="view-cost-analysis-and-create-budgets"></a>Visualización del análisis de costos y creación de presupuestos

[Azure Cost Management](/cost-management-billing/cost-management-billing-overview.md) le permite planificar, analizar y reducir sus gastos para maximizar la inversión en la nube. Los costos de uso de todos los servicios de Azure están disponibles, incluidos los de Azure Batch. Puede ver y filtrar los costos de Batch que se verán y filtrarán, pronosticar costos futuros y establecer límites de gasto con alertas cuando se alcancen esos límites.

En Azure Portal, puede crear presupuestos y alertas de gastos para los grupos o cuentas de Batch. Los presupuestos y las alertas son útiles para notificar a las partes interesadas de cualquier riesgo de exceso de gasto, aunque es posible que haya un retraso en las alertas de gasto y que se supere ligeramente el presupuesto.

En la captura de pantalla siguiente se muestra un ejemplo de la vista **Análisis de costos** de una suscripción; se ha filtrado para que solo muestre los costos acumulados asociados a todas las cuentas de Batch. En los gráficos inferiores se muestra cómo el costo total del período seleccionado se puede clasificar por servicio consumido, ubicación y medidor. Aunque este es un ejemplo y no está pensado para reflejar los costos que puede ver en las suscripciones, es habitual en que el mayor costo sea para las máquinas virtuales que se asignan a los nodos del grupo de Batch.

:::image type="content" source="media/batch-budget/subscription-cost-analysis.png" alt-text="Captura de pantalla que muestra el análisis de costos en Azure Portal para todas las cuentas de Batch de una suscripción.":::

Se puede obtener un nivel adicional de detalle del análisis de costos mediante la especificación de un filtro de **recursos**. Para las cuentas de Batch, estos valores son el nombre de la cuenta de Batch más el nombre del grupo. Esto le permite ver los costos de un grupo específico, varios grupos o una o varias cuentas.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Visualización del análisis de costos de un grupo de Batch

#### <a name="batch-service-pool-allocation-mode"></a>Modo de asignación de grupo de servicio Batch

Para las cuentas de Batch creadas con el modo de asignación del grupo de servicios de Batch:

1. En Azure Portal, escriba o seleccione **Cost Management + Billing**.
1. Seleccione su suscripción en la sección **Ámbitos de facturación**.
1. En **Cost Management**, seleccione **Análisis de costos**.
1. Seleccione **Agregar filtro**. En la primera lista desplegable, seleccione **Recurso**.
1. En la segunda lista desplegable, seleccione el grupo de Batch. Cuando se selecciona el grupo, verá el análisis de costos para ese grupo. La siguiente captura de pantalla muestra un ejemplo de los datos.
   :::image type="content" source="media/batch-budget/pool-cost-analysis.png" alt-text="Captura de pantalla que muestra el análisis de costos de un grupo de Batch en Azure Portal.":::

El análisis de costos resultante muestra el costo del grupo, así como los recursos que contribuyen a este costo. En este ejemplo, las máquinas virtuales que se usan en el grupo son el recurso más costoso.

> [!NOTE]
> En este ejemplo, el grupo usa la **configuración de máquina virtual**, que se [recomienda para la mayoría de los grupos](batch-pool-cloud-service-to-virtual-machine-configuration.md) y tiene cargos basados en la estructura de precios de Virtual Machines. Los grupos que usan la **Configuración de Cloud Services** se cobran en función de la estructura de precios de Cloud Services.

Las [etiquetas](../azure-resource-manager/management/tag-resources.md) se pueden asociar a cuentas de Batch, lo que le permite usar esas etiquetas para filtrar más costos. Por ejemplo, se pueden usar etiquetas para asociar información del proyecto, usuario o grupo a una cuenta de Batch. Asimismo, las etiquetas no se pueden asociar actualmente a los grupos de Batch.

#### <a name="user-subscription-pool-allocation-mode"></a>Modo de asignación del grupo de suscripción de Batch

Cuentas de Batch creadas con el modo de asignación de grupos de suscripción de usuario:

1. En Azure Portal, escriba o seleccione **Cost Management + Billing**.
1. Seleccione su suscripción en la sección **Ámbitos de facturación**.
1. En **Cost Management**, seleccione **Análisis de costos**.
1. Seleccione **Agregar filtro**. En la primera lista desplegable, seleccione **Etiqueta**.
1. En la segunda lista desplegable, seleccione el **nombre de grupo**.
1. En la tercera lista desplegable, seleccione el grupo de Batch. Cuando se selecciona el grupo, verá el análisis de costos para ese grupo. La siguiente captura de pantalla muestra un ejemplo de los datos.
   :::image type="content" source="media/batch-budget/user-subscription-pool.png" alt-text="Captura de pantalla que muestra el análisis de costos de un grupo de Batch de suscripción de usuario en Azure Portal.":::

Tenga en cuenta que si está interesado en ver los datos de costos de todos los grupos de una cuenta de Batch de suscripción de usuario, puede seleccionar **batchaccountname** en la segunda lista desplegable y el nombre de la cuenta de Batch en la tercera lista desplegable. 

> [!NOTE]
> Los grupos que creen las cuentas de Batch de la suscripción de usuario no aparecerán en el filtro **Recurso**, aunque su uso seguirá apareciendo al filtrar el elemento "máquinas virtuales" en el nombre del servicio.

### <a name="create-a-budget-for-a-batch-pool"></a>Creación de un presupuesto para un grupo de Batch

Se pueden crear presupuestos y emitir alertas de costos cuando se alcanzan varios porcentajes de un presupuesto, como el 60 %, el 80 % y el 100 %. Los presupuestos pueden especificar uno o varios filtros, para que así pueda supervisar y alertar sobre los costos de la cuenta de Batch con varios niveles de detalle.

1. En la página **Análisis de costos**, seleccione **Presupuesto: ninguno**.
1. Seleccione **Crear presupuesto >** .
1. Use la ventana para configurar un presupuesto específicamente para el grupo. Para más información, consulte el [Tutorial: Creación y administración de presupuestos en Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimización de los costos asociados con Azure Batch

En función de su escenario, puede que desee reducir los costos lo máximo posible. Considere la posibilidad de usar una o varias de estas estrategias para maximizar la eficacia de las cargas de trabajo y reducir los costos potenciales.

### <a name="reduce-pool-node-use"></a>Reducción del uso del nodo de grupo

Los costos más grandes asociados con el uso de Batch suelen provenir de las máquinas virtuales asignadas a los nodos de grupo. En el caso de los grupos de configuración de máquinas virtuales, los discos administrados asociados que se usan para los discos del sistema operativo de la VM también pueden contribuir significativamente a los costos.

Evalúe la aplicación de Batch para determinar si las tareas de trabajo están utilizando correctamente los nodos del grupo o si los nodos del grupo están inactivos durante más tiempo del esperado. Es posible reducir el número de nodos de grupo asignados, reducir la tasa de escalado vertical de los nodos de grupo o aumentar la velocidad del escalado vertical para aumentar el uso.

Además de la supervisión personalizada, las [métricas de Batch](batch-diagnostics.md#view-batch-metrics) pueden ayudarle a identificar los nodos que están asignados pero en un estado inactivo. Puede seleccionar una métrica para la mayoría de los estados de nodo de grupo que quiera ver, mediante las métricas de supervisión de Batch en Azure Portal. Las opciones "Recuento de nodos inactivos" y "Recuento de nodos en ejecución" se pueden ver para obtener información de cómo se usan los nodos del grupo, por ejemplo.

### <a name="ensure-pool-nodes-are-able-to-run-tasks"></a>Asegúrese de que los nodos del grupo pueden ejecutar tareas

Los nodos asignados que se enumeran para un grupo normalmente incurren en costos, pero es posible que los nodos de grupo estén en un estado en el que no se puedan ejecutar tareas, como "unusable" o "starttaskfailed". Las API o métricas de Batch se pueden usar para supervisar y detectar esta categoría de VM. A continuación, se puede determinar el motivo de estos estados y tomar medidas correctivas para reducir o eliminar los nodos incorrectos.

### <a name="use-the-right-pool-node-vm-size"></a>Uso del tamaño de VM del nodo de grupo correcto

Asegúrese de que se usa el tamaño de VM adecuado, de modo que las VM se utilicen correctamente al ejecutar tareas, a la vez que se proporciona el rendimiento necesario para completar las tareas de trabajo en el tiempo necesario. Las VM del nodo de grupo se pueden infrautilizar en algunas situaciones, como el bajo uso de CPU. Puede ahorrar costos si elige un tamaño de VM con un precio más bajo.

Para determinar el uso de la VM, puede iniciar sesión en un nodo al ejecutar tareas para ver datos de rendimiento o usar [funcionalidades de supervisión](monitoring-overview.md), como Application Insights, para obtener datos de rendimiento de los nodos del grupo.

### <a name="use-pool-slots-to-reduce-node-requirements"></a>Uso de las ranuras de grupo para reducir los requisitos de nodo

Se pueden especificar varias ranuras de tareas para un grupo, de modo que el número correspondiente de tareas se pueda ejecutar en paralelo en cada nodo. Las ranuras de tareas de grupo se pueden usar para reducir el número de nodos que se usan en un grupo eligiendo tamaños de VM más grandes y ejecutando varias tareas en paralelo en el nodo para asegurarse de que este se usa bien. Si los nodos están infrautilizados, se pueden usar ranuras para aumentar el uso. Por ejemplo, para una aplicación de tarea de un solo subproceso, se podría configurar una ranura por núcleo. También es posible tener más ranuras que núcleos. Esto sería viable si la aplicación se bloquea significativamente a la espera de que se devuelvan llamadas a servicios externos, por poner un ejemplo.

Establecer [`taskSchedulingPolicy`](/rest/api/batchservice/pool/add#taskschedulingpolicy) en `pack` le permitirá garantizar que las VM se usen tanto como sea posible, y el escalado podrá quitar más fácilmente los nodos que no ejecutan ninguna tarea.

### <a name="use-low-priority-virtual-machines"></a>Uso de máquinas virtuales de prioridad baja

Las [VM de prioridad baja](batch-low-pri-vms.md) disminuyen el costo de las cargas de trabajo de Batch al usar la capacidad de proceso sobrante en Azure. Cuando se especifican máquinas virtuales de prioridad baja en los grupos, Batch usa este excedente para ejecutar la carga de trabajo. Puede lograr grandes ahorros de costos al usar VM de prioridad baja en lugar de VM dedicadas. Tenga en cuenta que las VM de prioridad baja no son adecuadas para todas las cargas de trabajo, ya que es posible que no haya capacidad disponible para asignarlas o se pueden adelantar.

### <a name="use-ephemeral-os-disks"></a>Uso de discos de SO efímeros

De forma predeterminada, los nodos de grupo usan discos administrados, lo que conlleva costos. Los grupos de configuración de máquina virtual pueden usar [discos de SO efímeros](create-pool-ephemeral-os-disk.md), que crean el disco de SO en la caché de la VM o SSD temporal, para evitar costos adicionales asociados a los discos administrados.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Compra de reservas de instancias de máquina virtual

Si piensa usar Batch durante largo tiempo, puede reducir el costo de las VM si usa [Reservas de Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para las cargas de trabajo. Una tarifa de reserva es considerablemente menor que una tarifa de pago por uso. Las instancias de máquina virtual que se usan sin una reserva se cobran según la tarifa de pago por uso. Al comprar una reserva, se aplica el descuento de la reserva. Al confirmar planes de uno o tres años para [instancias de VM](../virtual-machines/prepay-reserved-vm-instances.md), se aplican descuentos significativos al uso de VM, incluidas las [VM consumidas a través de grupos de Batch](../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).

Es importante tener en cuenta que el descuento por reserva es de tipo "use-it-or-lose-it".  Si no se usan los recursos correspondientes durante una hora, perderá la cantidad de reservas de esa hora. Las horas reservadas que no se usen no se pueden llevar a cabo y, por tanto, se pierden si no se usan. Las cargas de trabajo de Batch a menudo escalan el número de VM asignadas según la carga y tienen una carga variable, incluidos los períodos en los que no hay ninguna carga. Por lo tanto, es necesario tener cuidado al determinar el importe de la reserva, dado que las horas reservadas se perderán si las VM de Batch se escalan verticalmente por debajo de la cantidad de reserva.

### <a name="use-automatic-scaling"></a>Uso del escalado automático

El [escalado automático](batch-automatic-scaling.md) escala de manera dinámica el número de máquinas virtuales en el grupo de Batch en función de las demandas del trabajo actual. Al escalar el grupo según la duración de un trabajo, el escalado automático garantiza que las VM se escalen verticalmente y se usen solo cuando haya que realizar un trabajo. Cuando el trabajo se complete o no haya trabajos, las VM se reducen verticalmente de manera automáticamente para ahorrar recursos de proceso. El escalamiento permite disminuir los costos generales de la solución de Batch mediante el uso solo de los recursos que necesita.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md).
- Más información sobre las [VM de prioridad baja con Batch](batch-low-pri-vms.md).
