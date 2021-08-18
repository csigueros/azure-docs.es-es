---
title: Migración del grupo de SQL dedicado (anteriormente SQL DW) a Gen2
description: Instrucciones para la migración de un grupo de SQL dedicado (anteriormente SQL DW) existente a Gen2 y la programación de la migración por región.
services: synapse-analytics
author: rothja
ms.author: jroth
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 59ebd89701f23979a8a359fecfd68a4796bf6d9c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472117"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Actualización del grupo de SQL dedicado (anteriormente SQL DW) a Gen2

Microsoft está ayudando a reducir el costo inicial de ejecutar un grupo de SQL dedicado (anteriormente SQL DW).  Ahora, hay disponibles niveles inferiores de proceso capaces de manejar consultas exigentes, para el grupo de SQL dedicado. Lea el anuncio completo sobre la [compatibilidad de niveles inferiores de proceso para Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nueva oferta está disponible en las regiones que se indican en la tabla siguiente. Para las regiones admitidas, el grupo de SQL dedicado de Gen1 existentes se pueden actualizar a Gen2 a través de uno de los siguientes métodos:

- **El proceso de actualización automática:** las actualizaciones automáticas no se inician en cuanto el servicio está disponible en una región.  Cuando las actualizaciones automáticas se inician en una región específica, las actualizaciones individuales de almacenamiento de datos se llevarán a cabo durante la programación de mantenimiento seleccionada.
- [**Actualización manual a Gen2:**](#self-upgrade-to-gen2) puede controlar cuándo se debe actualizar mediante una actualización manual a Gen2. Si aún no se admite su región, puede realizar una restauración desde un punto de restauración directamente a una instancia de Gen2 en una región admitida.

## <a name="automated-schedule-and-region-availability-table"></a>Programación automatizada y tabla de disponibilidad de regiones

En la tabla siguiente se resume por región cuándo estará disponible el nivel de proceso inferior Gen2 y en qué momento iniciar las actualizaciones automáticas. Las fechas están sujetos a cambios. Consúltela en otro momento para ver cuándo estará disponible su región.

\* indica que una programación específica para la región no está disponible actualmente.

| **Región** | **Proceso inferior Gen2 disponible** | **Fecha de inicio de la actualización automática** |
|:--- |:--- |:--- |
| Este de China |\* |\* |
| Norte de China |\* |\* |

## <a name="automatic-upgrade-process"></a>Proceso de actualización automática

En función del gráfico de disponibilidad anterior, programaremos actualizaciones automatizadas para las instancias de Gen1. Con el fin de evitar interrupciones inesperadas de la disponibilidad del grupo de SQL dedicado (anteriormente SQL DW), se programarán las actualizaciones automatizadas durante la programación de mantenimiento. La capacidad de crear una instancia de Gen1 se deshabilitará en las regiones en las que se está llevando a cabo la actualización automática a Gen2. Gen1 dejará de utilizarse una vez que se hayan completado las actualizaciones automáticas. Para obtener más información sobre las programaciones, consulte [Vista de una programación de mantenimiento](maintenance-scheduling.md#view-a-maintenance-schedule).

El proceso de actualización implicará una breve desconexión de aproximadamente 5 minutos, ya que tenemos que reiniciar el grupo de SQL dedicado (anteriormente SQL DW).  Una vez reiniciado el grupo de SQL dedicado (anteriormente SQL DW), estará totalmente disponible para usarse. Aun así, puede experimentar una degradación del rendimiento mientras continúa el proceso de actualización de los archivos de datos en segundo plano. El tiempo total de esta degradación del rendimiento variará en función del tamaño de los archivos de datos.

También puede acelerar el proceso de actualización de archivos de datos ejecutando [Alter Index rebuild](sql-data-warehouse-tables-index.md) en todas las tablas de almacén de columnas principales usando una clase de recurso y un SLO de mayor tamaño después del reinicio.

> [!NOTE]
> Alter Index rebuild es una operación sin conexión y las tablas no estarán disponibles hasta que se complete la recompilación.

## <a name="self-upgrade-to-gen2"></a>Actualización manual a Gen2

Puede optar por realizar la actualización manual. Para ello, siga estos pasos en un grupo de SQL dedicado (anteriormente SQL DW) existente de Gen1. Si elige esta opción, debe completar la actualización manual antes de que comience el proceso de actualización automática en su región. Esto asegura que se evite cualquier riesgo de que las actualizaciones automáticas causen un conflicto.

Hay dos opciones a la hora de realizar una actualización manual.  Puede actualizar el grupo de SQL dedicado (anteriormente SQL DW) actual en contexto, o bien puede restaurar un grupo de SQL dedicado (anteriormente SQL DW) de Gen1 en una instancia de Gen2.

- [Actualización en contexto](upgrade-to-latest-generation.md): esta opción actualizará el grupo de SQL dedicado (anteriormente SQL DW) existente de Gen1 a Gen2. El proceso de actualización implicará una breve desconexión de aproximadamente 5 minutos, ya que tenemos que reiniciar el grupo de SQL dedicado (anteriormente SQL DW).  Una vez reiniciado, estará totalmente disponible para usarse. Si experimenta problemas durante la actualización, cree una [solicitud de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md) e indique que la posible causa es la "actualización a Gen2".
- [Actualización desde un punto de restauración](sql-data-warehouse-restore-points.md): cree un punto de restauración definido por el usuario en el grupo de SQL dedicado (anteriormente SQL DW) actual de Gen1 y, a continuación, restaure directamente a una instancia de Gen2. El grupo de SQL dedicado (anteriormente SQL DW) de Gen1 existente permanecerá en su lugar. Una vez completada la restauración, el grupo de SQL dedicado (anteriormente SQL DW) de Gen2 estará totalmente disponible para usarse.  Una vez que haya ejecutado todos los procesos de prueba y validación en la instancia de Gen2 restaurada, se podrá eliminar la instancia original de Gen1.

  - Paso 1: En Azure Portal, [cree un punto de restauración definido por el usuario](sql-data-warehouse-restore-active-paused-dw.md).
  - Paso 2: Al restaurar desde el punto de restauración definido por el usuario, establezca el nivel de rendimiento en el nivel de Gen2 que prefiera.

Puede experimentar un período de degradación del rendimiento mientras continúa el proceso de actualización de los archivos de datos en segundo plano. El tiempo total de esta degradación del rendimiento variará en función del tamaño de los archivos de datos.

Para acelerar el proceso en segundo plano de migración de los datos, puede forzar inmediatamente el movimiento de datos ejecutando [Alter Index rebuild](sql-data-warehouse-tables-index.md) en todas las tablas de almacén de columnas principales que consulte con una clase de recurso y un SLO de mayor tamaño.

> [!NOTE]
> Alter Index rebuild es una operación sin conexión y las tablas no estarán disponibles hasta que se complete la recompilación.

Si encuentra problemas con el grupo de SQL dedicado (anteriormente SQL DW), cree una [solicitud de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md) e indique que la posible causa es la "actualización a Gen2".

Para obtener más información, vea [Actualización a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Preguntas frecuentes sobre migración

**P: ¿Gen2 cuesta lo mismo que Gen1?**

- A. Sí.

**P: ¿Cómo afectarán las actualizaciones a mis scripts de automatización?**

- A. Los scripts de automatización que hacen referencia a un objetivo de nivel de servicio deben cambiarse para que se correspondan con el equivalente de Gen2.  Consulte los detalles [aquí](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**P: ¿Cuánto suele tardar una actualización manual?**

- A. Puede actualizar en contexto o desde un punto de restauración.

  - La actualización en contexto provocará que, momentáneamente se pause el grupo de SQL dedicado (anteriormente SQL DW) y que luego se reinicie.  Un proceso en segundo plano seguirá ejecutándose mientras el grupo de SQL dedicado (anteriormente SQL DW) está en línea.  
  - La actualización a través de un punto de restauración tarda más porque se realiza el proceso de restauración completa.

**P: ¿Cuánto tiempo tarda la actualización automática?**

- A. El tiempo de inactividad real de la actualización es solo el necesario para pausar y reanudar el servicio; es decir, entre 5 y 10 minutos. Tras este tiempo de inactividad breve, un proceso en segundo plano ejecutará una migración de almacenamiento. El período de tiempo del proceso en segundo plano depende del tamaño del grupo de SQL dedicado (anteriormente SQL DW).

**P: ¿Cuándo se llevará a cabo la actualización automática?**

- A. Durante una programación de mantenimiento. Aprovechar la programación de mantenimiento elegida minimizará la interrupción de su actividad empresarial.

**P: ¿Qué debo hacer si mi proceso de actualización en segundo plano parece estar bloqueado?**

- A. Inicie una reindexación de las tablas de almacén de columnas. Tenga en cuenta que la reindexación de las tablas se realiza sin conexión durante esta operación.

**P: ¿Qué ocurre si Gen2 no tiene el objetivo de nivel de servicio necesario en Gen1?**

- A. Si usa DW600 o DW1200 en Gen1, se recomienda usar DW500c o DW1000c respectivamente, ya que Gen2 proporciona más cantidad de memoria, recursos y rendimiento que Gen1.

**P: ¿Puedo deshabilitar la copia de seguridad con redundancia geográfica?**

- A. No. La copia de seguridad con replicación geográfica es una característica empresarial que mantiene la disponibilidad del grupo de SQL dedicado (anteriormente SQL DW) en caso de que una región deje de estar disponible. Abra una [solicitud de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md) si tiene más problemas.

**P: ¿Hay alguna diferencia de sintaxis de T-SQL entre Gen1 y Gen2?**

- A. No hay ningún cambio en la sintaxis del lenguaje T-SQL de Gen1 a Gen2.

**P: ¿Gen2 admite ventanas de mantenimiento?**

- A. Sí.

**P: ¿Podré crear una nueva instancia de Gen1 después de actualizar mi región?**

- A. No. Después de haber actualizado una región, se deshabilitará la creación de instancias de Gen1.

## <a name="next-steps"></a>Pasos siguientes

- [Pasos de actualización](upgrade-to-latest-generation.md)
- [Ventana de mantenimiento](maintenance-scheduling.md)
- [Supervisión del estado de los recursos](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Revisión de antes de comenzar la migración](upgrade-to-latest-generation.md#before-you-begin)
- [Actualización en contexto y desde un punto de restauración](upgrade-to-latest-generation.md)
- [Creación de un punto de restauración definido por el usuario](sql-data-warehouse-restore-points.md)
- [Información sobre cómo restaurar a Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Apertura de una solicitud de soporte técnico de Azure Synapse Analytics](./sql-data-warehouse-get-started-create-support-ticket.md)
