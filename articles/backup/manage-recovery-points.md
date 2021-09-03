---
title: Administración de puntos de recuperación
description: Obtenga información sobre cómo el servicio Azure Backup administra los puntos de recuperación de las máquinas virtuales
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 2dd33b430d5aa117f35f74ff92bb3655a9002803
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300676"
---
# <a name="manage-recovery-points"></a>Administración de puntos de recuperación

En este artículo se describe cómo funciona la retención en las máquinas virtuales. Cada vez que se realizan copias de seguridad, se crean puntos de recuperación a partir de los cuales se pueden llevar a cabo operaciones de restauración.

En el caso de las máquinas virtuales, la copia de seguridad inicial es una copia de seguridad completa, y las posteriores, copias de seguridad incrementales.

## <a name="recovery-points-and-retention"></a>Puntos de recuperación y retención

### <a name="scheduled-initial-and-incremental-backup"></a>Copias de seguridad inicial e incremental programadas

Veamos un ejemplo simplificado de la máquina virtual *V1* con un disco de datos compuesto por cuatro bloques: bloque 1, bloque 2, bloque 3 y bloque 4. Cada bloque tiene un tamaño de 16 kB.

![Máquina virtual con cuatro bloques](./media/manage-recovery-points/four-blocks.png)

**Paso 1. Copia de seguridad inicial:** la copia de seguridad inicial es una copia de seguridad completa. Actúa como base de referencia en la que se aplican las copias de seguridad incrementales posteriores. Supongamos que hay datos escritos en el bloque 1 y en el bloque 2 de la máquina virtual de origen. Los mismos datos se replicarán como D1 y D2 en el almacén de Recovery Services.

![La copia de seguridad inicial se replica](./media/manage-recovery-points/initial-backup.png)

**Paso 2. Copia de seguridad incremental 1:** imagínese que hay nuevos datos agregados al bloque 3 de la máquina virtual. Los mismos datos se replicarán en la siguiente copia de seguridad incremental y solo el bloque que se cambie se almacenará como D3.  Durante cada paso, incluso si se cambia 1 kB del bloque, se carga todo el bloque de 16 kB en el punto de recuperación.

![Primera copia de seguridad incremental](./media/manage-recovery-points/first-incremental-backup.png)

**Paso 3. Copia de seguridad incremental 2:**  ahora, imagínese que se producen cambios en los datos del bloque 3 y del bloque 2 de la máquina virtual de origen. Estos cambios se replicarán en la siguiente copia de seguridad incremental como D3 y D2.

![Segunda copia de seguridad incremental](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Copia de seguridad a petición

Puede optar por ejecutar una copia de seguridad a petición de una máquina virtual en cualquier momento después de configurar la protección en ella.

- La copia de seguridad a petición será una copia de seguridad completa si se desencadena antes de la primera copia de seguridad inicial programada.
- Si la copia de seguridad inicial se ha completado y se desencadena una copia de seguridad a petición, se trata de una copia de seguridad incremental.
- El tiempo de retención de los puntos de recuperación creados para una copia de seguridad a petición es el valor de retención que especifique al desencadenar la copia de seguridad.

### <a name="storage-cost"></a>Coste del almacenamiento

El **punto de recuperación** creado para la copia de seguridad inicial contiene todos los bloques que tienen los datos. Los puntos de recuperación incrementales posteriores constan solo de los bloques donde se han cambiado datos. Los costos de almacenamiento se corresponden con la suma de todos los bloques de todos los puntos de recuperación.

Vamos a usar el ejemplo anterior para comprender el costo de almacenamiento después de cada paso:

|Paso  |Tipo de copia de seguridad  |Bloques modificados  |Tipo de almacenamiento |
|------|---------|---------|---------|
|1     |     Copia de seguridad inicial    | Bloque 1 y bloque 2        |    Se corresponde con el punto de recuperación 1 (D1 + D2).     |
|2     |  Copia de seguridad incremental 1       |  Bloque 3       |   Se corresponde con la suma del punto de recuperación 1 (D1 + D2) y el punto de recuperación 2 (D3).      |
|3     |    Copia de seguridad incremental 2     |    Bloque 2 y bloque 3     |   Se corresponde con la suma del punto de recuperación 1 (D1 + D2), el punto de recuperación 2 (D3) y el punto de recuperación 3 (D2 + D3).      |

### <a name="recovery-point-expiration"></a>Expiración de los puntos de recuperación

Cada punto de recuperación tiene una duración de retención, tal como se especifica en la directiva de copia de seguridad. La limpieza se produce a intervalos regulares y se limpian todos los puntos de recuperación que han expirado.

Cuando el punto de recuperación expira, se elimina o se combina.

### <a name="case-1-initial-recovery-point-expires"></a>Caso 1: El punto de recuperación inicial expira

Cuando el punto de recuperación inicial expira, se combina con el siguiente punto de recuperación incremental. Todos los bloques de datos que se sobrescriben en el punto de recuperación incremental se eliminan, y el resto se combinan. La copia de seguridad incremental se convierte entonces en la copia de seguridad completa inicial. Veamos un ejemplo:

- *Punto de recuperación 1* se crea durante la copia de seguridad inicial y tiene la copia de seguridad completa de la máquina virtual.
- Cuando expira *Punto de recuperación 1*, *Punto de recuperación 2* se convierte en la siguiente copia de seguridad completa.
- El bloque D1 se combina con *Punto de recuperación 2* y se elimina D2, ya que los datos del bloque 2 se sobrescriben en *Punto de recuperación 2*. Este cambio se captura como el bloque D2.
- El bloque D1 se conserva en los puntos de recuperación consecutivos tal cual, hasta que se produzca algún cambio en él antes de la siguiente copia de seguridad.

![Primer caso](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Caso 2: El punto de recuperación incremental intermedio expira

- Si *Punto de recuperación 2* expira antes que *Punto de recuperación 1*, los datos de *Punto de recuperación 2* se combinan con el siguiente punto de recuperación disponible: *Punto de recuperación 3*. Por lo tanto, el bloque D3 se combina con *Punto de recuperación 3*.
- *Punto de recuperación 1* sigue siendo la copia de seguridad completa con el bloque D1 y D2.

![Segundo caso](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Caso 3: El punto de recuperación a petición expira

En este ejemplo, se programa la ejecución de una directiva de programación (copia de seguridad diaria) con un período de retención de *n* días.  Si se desencadena una copia de seguridad a petición el cuarto día antes de la siguiente copia de seguridad programada y el período de retención se especifica en 10 días, seguirá siendo una copia de seguridad incremental. Se creará un punto de recuperación (*PR1 a petición*) después de *Punto de recuperación 3* y antes que *Punto de recuperación 4*.  Al final del día 14, el punto de recuperación a petición (*PR1 a petición*) expira y se combinará con el siguiente punto de recuperación disponible. Los bloques de datos que siguen presentes en el servidor se combinan, mientras que los bloques de datos que han cambiado (sobrescritos o eliminados) se eliminan del punto de recuperación expirado.

![Tercer caso](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Impacto de los cambios de directiva en los puntos de recuperación

Cuando se modifica una directiva, se aplica tanto a los puntos de recuperación nuevos como a los que ya existan. Para obtener más información, consulte [Impacto de los cambios de directiva en los puntos de recuperación](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Impacto de la detención de la protección en los puntos de recuperación

Hay dos formas de detener la protección de una máquina virtual:

- **Detener la protección y eliminar los datos de copia de seguridad.** Esta opción hará que todos los trabajos de copia de seguridad futuros dejen de proteger la máquina virtual y eliminará todos los puntos de recuperación. Si se habilita la [eliminación temporal](backup-azure-security-feature-cloud.md), los datos eliminados se conservarán durante 14 días. No se incurre en cargos por los elementos que tengan el estado de eliminación temporal. Los datos se pueden recuperar dentro del período de 14 días. Si no está habilitada la eliminación temporal, los datos se limpiarán inmediatamente y no podrá restaurar la máquina virtual o usar la opción **Reanudar copia de seguridad**.
- **Detener la protección y conservar los datos de copia de seguridad**. Esta opción detendrá la protección para todos los trabajos de copia de seguridad futuros de la máquina virtual. Sin embargo, el servicio Azure Backup conservará de forma permanente los puntos de recuperación de los que se ha realizado una copia de seguridad. Deberá pagar para mantener los puntos de recuperación en el almacén (consulte [Precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/) para obtener detalles). Podrá restaurar la máquina virtual si es necesario. Si decide reanudar la protección de la máquina virtual, podrá usar la opción **Reanudar copia de seguridad**. Después de reanudar la copia de seguridad, las reglas de retención se aplicarán a los puntos de expiración. También puede eliminar los datos de los que se ha realizado una copia de seguridad mediante la opción **Eliminar datos de copia de seguridad**.

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Impacto de la eliminación de una máquina virtual sin detener la protección

La eliminación de una máquina virtual sin detener la protección afecta a los puntos de recuperación, así que hay que evitar este escenario. Idealmente, las copias de seguridad deben detenerse antes de eliminar la máquina virtual. Como el recurso no existe, se producirá un error en las copias de seguridad programadas; concretamente, el [error VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Los puntos de recuperación se limpiarán periódicamente de acuerdo con la directiva de retención, pero la última copia de la máquina virtual seguirá siendo permanente y se le facturará en consecuencia. En función de cuál sea el escenario, tiene las dos opciones siguientes:

- **Opción 1:** restaure la máquina virtual con cualquiera de los puntos de recuperación. Si desea recuperar la máquina virtual eliminada, restáurela con el mismo nombre y en el mismo grupo de recursos. Si protege la máquina virtual restaurada en el mismo almacén, los puntos de recuperación que existan se asociarán automáticamente.
- **Opción 2:** vaya al almacén de Recovery Services y detenga la protección con Eliminar datos.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Impacto de los puntos de recuperación expirados en los elementos que tienen el estado de eliminación temporal

Si la [eliminación temporal](backup-azure-security-feature-cloud.md) está habilitada en el almacén de Recovery Services, el punto de recuperación expirado permanecerá en estado de eliminación temporal y, por tanto, no se limpiará. No se aplica ningún cargo cuando un punto de recuperación se encuentra en estado de eliminación temporal.

### <a name="impact-of-churn-on-backup-performance"></a>Impacto de la renovación en el rendimiento de las copias de seguridad

Supongamos que el almacenamiento total de una máquina virtual es de 8 TB y la renovación es del 5 %. Por tanto, el almacenamiento de copia de seguridad incremental correspondiente será el 5 % de 8 TB; es decir, 0,4 TB. Cuanto mayor es la renovación, más almacenamiento de reserva tendrán disponibles las copias de seguridad incrementales posteriores. La renovación afecta al rendimiento de las copias de seguridad. Sin embargo, cuanto mayor es la renovación, más lento será el proceso de copia de seguridad y más recursos consumirá el almacenamiento de reserva.

Para entender cómo afecta la renovación al rendimiento de las copias de seguridad, consulte esta tabla:

|Máquinas virtuales  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Número de discos de datos    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Tamaño de cada disco   |      4 TB   | 4 TB        |  4 TB       |
|Renovación de datos de copias de seguridad    |   A1- 4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4-1 TB  |   C1-2 TB; C4-2 TB      |

El rendimiento de las copias de seguridad seguirá el orden VM2>VM3>VM1. Esto se debe a que los datos renovados se reparten entre los distintos discos. Como la copia de seguridad de discos se produce en paralelo, VM2 mostrará el mejor rendimiento.

## <a name="frequently-asked-question"></a>Preguntas más frecuentes

### <a name="how-can-i-find-the-retention-period-of-an-on-demand-backup"></a>¿Cómo puedo encontrar el período de retención de una copia de seguridad a petición?

En el campo **Hora de expiración del punto de recuperación en UTC** de los trabajos de copia de seguridad a petición se muestra el período de retención del punto de recuperación. Para obtener más información, vea [Ejecución de una copia de seguridad a petición](backup-azure-manage-vms.md#run-an-on-demand-backup). 

## <a name="next-steps"></a>Pasos siguientes

- [Arquitectura y componentes de Azure Backup](backup-architecture.md)
