---
title: Copia de seguridad de Grupos de disponibilidad Always On de SQL Server
description: En este artículo, obtendrá información sobre cómo realizar una copia de seguridad de grupos de disponibilidad Always On de SQL Server.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 84826d7d6d8f0611c73dcfda25ca3492d9370c97
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113832"
---
# <a name="backup-sql-server-always-on-availability-groups"></a>Copia de seguridad de Grupos de disponibilidad Always On de SQL Server

Azure Backup ofrece compatibilidad de un extremo a otro para la copia de seguridad de Grupos de disponibilidad Always On de SQL Server si todos los nodos están en la misma región y suscripción que el almacén de Recovery Services. Sin embargo, si los nodos de grupos de disponibilidad están repartidos entre regiones, suscripciones o entornos locales y Azure, hay algunas consideraciones que debe tener en cuenta.

>[!Note]
>Azure Backup no admite la copia de seguridad de bases de datos de grupos de disponibilidad básica.

La preferencia de copia de seguridad del grupo de disponibilidad de SQL que usa Azure Backup admite copias de seguridad completas y diferenciales solo desde la réplica principal. Por lo tanto, estos trabajos de copia de seguridad siempre se ejecutan en el nodo principal independientemente de la preferencia de copia de seguridad. En el caso de las copias de seguridad de registros de transacciones y completas de solo copia, se tiene en cuenta la preferencia de copia de seguridad del grupo de disponibilidad al decidir el nodo donde se ejecutará la copia de seguridad.

| Preferencia de copia de seguridad del grupo de disponibilidad | Las copias de seguridad completas y diferenciales se ejecutan en | Las copias de seguridad de registros y de solo copia se toman de |
| -------------------- | ---------------------------- | ---------------------------------------- |
| Principal | Réplica principal | Réplica principal |
| Solo secundaria | Réplica principal | Cualquiera de las réplicas secundarias |
| Preferir secundaria | Réplica principal | Se prefieren réplicas secundarias, pero las copias de seguridad también se pueden ejecutar en la réplica principal. |
| Ninguna/cualquiera | Réplica principal | Cualquier réplica |

La extensión de copia de seguridad de la carga de trabajo se instala en el nodo cuando se registra con el servicio Azure Backup. Cuando se configura una base de datos de grupo de disponibilidad para la copia de seguridad, las programaciones de copia de seguridad se insertan en todos los nodos registrados del grupo de disponibilidad. Las programaciones se activan en todos los nodos del grupo de disponibilidad y las extensiones de copia de seguridad de la carga de trabajo de estos nodos se sincronizan entre sí para decidir qué nodo realizará la copia de seguridad. La selección del nodo depende del tipo de copia de seguridad y de la preferencia de copia de seguridad, como se explica en la sección 1. 

El nodo seleccionado continúa con el trabajo de copia de seguridad, mientras que el trabajo desencadenado en los otros nodos se retira, es decir, omite el trabajo.

>[!Note]
>Azure Backup no tiene en cuenta las prioridades o réplicas de copia de seguridad al decidir entre las réplicas secundarias.

## <a name="register-ag-nodes-to-the-recovery-services-vault"></a>Registro de nodos de grupo de disponibilidad en el almacén de Recovery Services

Un almacén de Recovery Services admite la copia de seguridad de bases de datos solo de VM de la misma región y suscripción que la del almacén.

- Debe registrar el nodo principal en el almacén (de lo contrario, no se pueden realizar copias de seguridad completas).
- Si la preferencia de copia de seguridad es _solo secundaria_, debe registrar al menos un nodo secundario en el almacén (de lo contrario, no se pueden realizar copias de seguridad de registros o completas de solo copia).

Si no se cumplen las condiciones anteriores, al configurar las copias de seguridad de las bases de datos de grupos de disponibilidad, se producirá un error con el código _FabricSvcBackupPreferenceCheckFailedUserError_.

Veamos la siguiente implementación de grupo de disponibilidad como referencia.

:::image type="content" source="./media/backup-sql-server-on-availability-groups/ag-deployment.png" alt-text="Diagrama de implementación de grupo de disponibilidad como referencia.":::

Si tomamos la implementación de grupo de disponibilidad de ejemplo anterior, se deben tener en cuentas las consideraciones siguientes:

- Dado que el nodo principal está en la región 1 y la suscripción 1, el almacén de Recovery Services (almacén 1) debe estar en la región 1 y en la suscripción 1 para proteger este grupo de disponibilidad.
- La VM3 no se puede registrar en el almacén 1, ya que se encuentra en una suscripción diferente.
- La VM4 no se puede registrar en el almacén 1, ya que se encuentra en una región diferente.
- Si la preferencia de copia de seguridad es _solo secundaria_, la VM1 (principal) y la VM2 (secundaria) deben registrarse en el almacén 1 (ya que las copias de seguridad completas requieren el nodo principal y los registros requieren un nodo secundario). Para otras preferencias de copia de seguridad, la VM1 (principal) debe registrarse en el almacén 1, y la VM2 es opcional (porque todas las copias de seguridad se pueden ejecutar en el nodo principal).
- La VM3 podría registrarse en el almacén 2 de la suscripción 2 y las bases de datos del grupo de disponibilidad se mostrarían entonces para la protección en el almacén 2, pero debido a la ausencia del nodo principal en el almacén 2, se produciría un error al configurar las copias de seguridad.
- Del mismo modo, la VM4 podría registrarse en el almacén 4 de la región 2, pero la configuración de las copias de seguridad produciría un error, ya que el nodo principal no está registrado en el almacén 4.

## <a name="handle-failover"></a>Control de la conmutación por error

Después de que el grupo de disponibilidad realice la conmutación por error en uno de los nodos secundarios:

- Las copias de seguridad completas y diferenciales continuarán desde el nuevo nodo principal si está registrado en el almacén.
- Las copias de seguridad de registros y completas de solo copia continuarán desde el nodo principal o secundario en función de la preferencia de copia de seguridad.

>[!Note]
>Las interrupciones de la cadena de registros no se producen en la conmutación por error si esta no coincide con una copia de seguridad.

Si tomamos la implementación de grupo de disponibilidad de ejemplo anterior, las distintas posibilidades de conmutación son las siguientes:

- Conmutación por error a la VM2
  - Las copias de seguridad completas y diferenciales se realizarán desde la VM2.
  - Las copias de seguridad de registros o completas de solo copia se realizarán desde la VM1 o la VM2 en función de la preferencia de copia de seguridad.
- Conmutación por error a la VM3 (otra suscripción)
  - Dado que las copias de seguridad no están configuradas en el almacén 2, no se realizará ninguna copia de seguridad.
  - Si la preferencia de copia de seguridad no es solo secundaria, las copias de seguridad ahora se pueden configurar en el almacén 2, ya que el nodo principal está registrado en este almacén. No obstante, esto puede provocar conflictos o errores de copia de seguridad. Puede obtener más información al respecto en [Configuración de copias de seguridad para un grupo de disponibilidad de varias regiones](#configure-backups-for-a-multi-region-ag).
- Conmutación por error a la VM4 (otra región)
  - Dado que las copias de seguridad no están configuradas en el almacén 4, no se realizará ninguna copia de seguridad.
  - Si la preferencia de copia de seguridad no es solo secundaria, las copias de seguridad ahora se pueden configurar en el almacén 4, ya que el nodo principal está registrado en este almacén. No obstante, esto puede provocar conflictos o errores de copia de seguridad. Puede obtener más información al respecto en [Configuración de copias de seguridad para un grupo de disponibilidad de varias regiones](#configure-backups-for-a-multi-region-ag).

## <a name="configure-backups-for-a-multi-region-ag"></a>Configuración de copias de seguridad para un grupo de disponibilidad de varias regiones

El almacén de Recovery Services no admite copias de seguridad entre suscripciones ni entre regiones. En esta sección se resume cómo habilitar las copias de seguridad de grupos de administración que abarcan suscripciones o regiones de Azure y se explican las consideraciones asociadas.

- Evalúe si realmente necesita habilitar las copias de seguridad de todos los nodos. Si una región o suscripción tiene la mayoría de los nodos del grupo de disponibilidad y la conmutación por error a otros nodos se produce muy rara vez, la configuración de la copia de seguridad en esa primera región puede ser suficiente. Si las conmutaciones por error a otra región o suscripción se producen con frecuencia y durante un período prolongado, es posible que también desee configurar las copias de seguridad de forma proactiva en la otra región.

- Cada almacén donde se habilite la copia de seguridad tendrá su propio conjunto de cadenas de puntos de recuperación. Las restauraciones desde estos puntos de recuperación solo se pueden realizar a las VM registradas en ese almacén.

- Las copias de seguridad completas o diferenciales solo se realizarán correctamente en el almacén que tiene el nodo principal. Estas copias de seguridad seguirán generando errores en otros almacenes.

- Las copias de seguridad de registros seguirán funcionando en el almacén anterior hasta que se ejecute una copia de seguridad de registros en el nuevo almacén (es decir, en el almacén donde está presente el nuevo nodo principal) y se _interrumpe_ la cadena de registros del almacén antiguo.
  >[!Note]
  >Hay un límite máximo de 15 días más allá del cual las copias de seguridad de registros comenzarán a generar errores.

- Las copias de seguridad completas de solo copia funcionarán en todos los almacenes.

- La protección de cada almacén se trata como un origen de datos distinto y se factura por separado.

Para evitar conflictos de copia de seguridad de registros entre los dos almacenes, se recomienda establecer la preferencia de copia de seguridad en Principal. Así, el almacén que tiene el nodo principal también se encargará de las copias de seguridad de registros.

Tomando la implementación del grupo de disponibilidad de ejemplo anterior, estos son los pasos para habilitar la copia de seguridad de todos los nodos. La suposición es que la preferencia de copia de seguridad se cumple en todos los pasos.

### <a name="step-1-enable-backups-in-region-1-subscription-1-vault-1"></a>Paso 1: Habilitación de copias de seguridad en la región 1, suscripción 1 (almacén 1)

Dado que el nodo principal está en la región y la suscripción, los pasos habituales para habilitar las copias de seguridad funcionarán.

### <a name="step-2-enable-backups-in-region-1-subscription-2-vault-2"></a>Paso 2: Habilitación de copias de seguridad en la región 1, suscripción 2 (almacén 2)

1. Realice una conmutación por error del grupo de disponibilidad a la VM3 para que el nodo principal esté presente en el almacén 2.
1. Configure las copias de seguridad de las bases de datos de grupo de disponibilidad en el almacén 2.
1. En este punto:
   1. Las copias de seguridad completas o diferenciales producirán un error en el almacén 1, ya que ninguno de los nodos registrados puede encargarse de esta copia de seguridad.
   1. Las copias de seguridad de registros se realizarán correctamente en el almacén 1 hasta que se ejecute una copia de seguridad de registros en el almacén 2 y se _interrumpa_ la cadena de registros del almacén 1.
1. Conmutación por recuperación del grupo de disponibilidad a la VM1.

### <a name="step-3-enable-backups-in-region-2-subscription-1-vault-4"></a>Paso 3: Habilitación de copias de seguridad en la región 2, suscripción 1 (almacén 4)

Igual que en el paso 2.

## <a name="backup-an-ag-that-spans-azure-and-on-premises"></a>Copia de seguridad de un grupo de disponibilidad que abarca Azure y el entorno local

Azure Backup para SQL Server no se puede ejecutar en el entorno local. Si el nodo principal está en Azure y los nodos de Azure satisfacen la preferencia de copia de seguridad, puede seguir las instrucciones anteriores del grupo de disponibilidad de varias regiones para habilitar las copias de seguridad de las réplicas en Azure. Si se produce una conmutación por error al nodo local, se comenzarán a producir errores en las copias de seguridad completas y diferenciales en Azure. Las copias de seguridad de registros pueden continuar hasta que se produzca una interrupción en la cadena de registros o pasen 15 días.

## <a name="throttling-for-backup-jobs-in-an-ag-database"></a>Limitación de trabajos de copia de seguridad en una base de datos de grupo de disponibilidad

Actualmente, los límites de copia de seguridad se aplican en el nivel de máquina individual. El límite predeterminado es 20: si se desencadenan más de 20 copias de seguridad simultáneamente, se ejecutarán las primeras 20 y las demás se pondrán en cola. Una vez completados los trabajos en ejecución, los trabajos en cola comenzarán a ejecutarse.

Puede cambiar este valor por uno menor si las copias de seguridad simultáneas provocan sobrecarga de memoria, E/S o CPU en el nodo.
**Dado que la limitación tiene lugar en el nivel de nodo, tener nodos de grupo de disponibilidad no equilibrados puede provocar problemas de sincronización de copias de seguridad**. Para entenderlo, considere la posibilidad de usar un grupo de disponibilidad de 2 nodos, por ejemplo.

Por ejemplo, el primer nodo tiene 50 bases de datos independientes protegidas y ambos nodos tienen 5 bases de datos de grupo de disponibilidad protegidas. De hecho, el nodo 1 tiene programados 55 trabajos de copia de seguridad de base de datos, mientras que el nodo 2 solo tiene 5. Además, todas estas copias de seguridad están configuradas para ejecutarse al mismo tiempo, cada hora. En un momento dado, las 55 copias de seguridad se desencadenarán en el nodo 1 y 35 de estas se pondrán en cola. Algunas serán las copias de seguridad de la base de datos del grupo de disponibilidad. No obstante, en el nodo 2, las copias de seguridad de base de datos del grupo de disponibilidad continuarán sin ponerse en cola.

Dado que los trabajos de base de datos del grupo de disponibilidad se ponen en cola en un nodo y se ejecutan en otro, la sincronización de copias de seguridad (mencionada en la sección 6) no funcionará correctamente. El nodo 2 podría suponer que el nodo 1 está fuera de servicio y, por tanto, que los trabajos de este último no se están incluyen en la sincronización. Esto puede provocar interrupciones en la cadena de registros o copias de seguridad adicionales, ya que ambos nodos pueden realizar copias de seguridad de forma independiente.

Puede producirse un problema similar si el número de bases de datos de grupo de disponibilidad protegidas supera las limitaciones. En tal caso, la copia de seguridad de, por ejemplo, DB1 se puede poner en cola en el nodo 1, mientras se ejecuta en el nodo 2. 

Se recomienda usar las siguientes preferencias de copia de seguridad para evitar estos problemas de sincronización:

- Para un grupo de disponibilidad de 2 nodos, establezca Preferencia de copia de seguridad en Principal o Solo secundaria; de este modo, solo un nodo podrá realizar copias de seguridad y el otro siempre se retirará. 
- En el caso de un grupo de disponibilidad con más de 2 nodos, establezca Preferencia de copia de seguridad en Principal; de este modo, solo el nodo principal puede realizar las copias de seguridad y los demás se retirarán.

## <a name="billing-for-ag-backups"></a>Facturación de copias de seguridad de grupo de disponibilidad

Del mismo modo que con una instancia de SQL independiente, una instancia de grupo de disponibilidad de la que se ha realizado una copia de seguridad se considera una instancia protegida. Se cobra el tamaño total de front-end de todas las bases de datos protegidas de una instancia. Tenga en cuenta la siguiente implementación:

:::image type="content" source="./media/backup-sql-server-on-availability-groups/protected-instances-calculation.png" alt-text="Diagrama que muestra el cálculo de instancias protegidas de bases de datos.":::

Las instancias protegidas se calculan de la siguiente manera:

| Instancia protegida o instancia de facturación | Bases de datos que se consideran para calcular el tamaño de front-end |
| ------------------------------------ | -------------------------------------------------- |
| AG1 | DB1, DB2 |
| AG2 | DB4 |
| VM2 | DB3 |
| VM3 | DB6 |
| VM4 | DB5 |

## <a name="moving-a-protected-database-in-or-out-of-an-ag"></a>Inclusión o exclusión de una base de datos protegida en un grupo de disponibilidad

Azure Backup considera **el nombre de la instancia de SQL o el grupo de disponibilidad o el nombre de la base de datos** como el nombre único de la base de datos. Cuando la base de datos independiente estaba protegida, su nombre único era _StandAloneInstanceName\DBName_. Cuando se mueve bajo un grupo de disponibilidad, el nombre único cambia a _AGName\DBName_. Las copias de seguridad de la base de datos independiente comenzarán a generar errores con el código: _UserErrorBackupFailedStandaloneDatabaseMovedInToAG_.

La base de datos debe configurarse para la protección desde el grupo de disponibilidad. Se tratará como un nuevo origen de datos con una cadena de puntos de recuperación independiente. La protección anterior de la base de datos independiente se puede detener con la conservación de datos para evitar que se desencadenen futuras copias de seguridad y generen errores en esta. Del mismo modo, cuando una base de datos de grupo de disponibilidad protegida se excluye del grupo de disponibilidad y se convierte en una base de datos independiente, sus copias de seguridad comienzan a generar errores con el código _: UserErrorBackupFailedDatabaseMovedOutOfAG_.

La base de datos debe configurarse para la protección desde la instancia independiente. Se tratará como un nuevo origen de datos con una cadena de puntos de recuperación independiente. La protección anterior de la base de datos de grupo de disponibilidad se puede detener con la conservación de datos para evitar que se desencadenen futuras copias de seguridad y generen errores en esta.

## <a name="additionremoval-of-a-node-to-an-ag"></a>Adición o eliminación de un nodo en un grupo de disponibilidad

Cuando se agrega un nuevo nodo a un grupo de disponibilidad configurado para las copias de seguridad, las extensiones de copia de seguridad de la carga de trabajo que se ejecutan en los nodos de grupo de disponibilidad ya registrados detectan el cambio de topología del grupo de disponibilidad e informan al servicio Azure Backup durante el siguiente trabajo de detección de base de datos programado. Cuando este nuevo nodo se registra para las copias de seguridad en el mismo almacén de Recovery Services que los demás nodos existentes, el servicio Azure Backup desencadena un flujo de trabajo que configura este nuevo nodo con los metadatos necesarios para realizar copias de seguridad del grupo de disponibilidad.

Después de esto, el nuevo nodo sincroniza la información de programación de copia de seguridad del grupo de disponibilidad del servicio Azure Backup y comienza a participar en el proceso de copia de seguridad sincronizado. Si el nuevo nodo no puede sincronizar las programaciones de copia de seguridad y participar en las copias de seguridad, al desencadenar un nuevo registro en el nodo también se fuerza la reconfiguración del nodo para las copias de seguridad del grupo de disponibilidad. De forma similar a la adición de nodos, las extensiones de la carga de trabajo detectan el cambio de topología del grupo de disponibilidad en este caso e informan al servicio Azure Backup. El servicio inicia un flujo de trabajo de _desconfiguración_ de nodos en el nodo quitado para borrar las programaciones de copia de seguridad de las bases de datos del grupo de disponibilidad y eliminar los metadatos relacionados con el grupo de disponibilidad.

## <a name="un-register-an-ag-node-from-azure-backup"></a>Anulación del registro de un nodo de grupo de disponibilidad de Azure Backup

Si un nodo forma parte de un grupo de disponibilidad que tiene una o varias bases de datos configuradas para la copia de seguridad, Azure Backup no permite anular el registro de ese nodo. Esto es para evitar futuros errores de copia de seguridad en caso de que no poder satisfacer la preferencia de copia de seguridad sin este nodo. Para anular el registro del nodo, primero debe quitarlo del grupo de disponibilidad. Cuando se complete el flujo de trabajo de _desconfiguración_ del nodo, puede anular el registro del nodo al limpiarlo.

Al restaurar una base de datos de Azure Backup en una instancia de SQL de grupo de disponibilidad, Grupos de disponibilidad no admiten la restauración directa de una base de datos en un grupo de disponibilidad. La base de datos debe restaurarse en una instancia de SQL independiente y, a continuación, unirse a un grupo de disponibilidad.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo:

* [Restauración de bases de datos de SQL Server con copia de seguridad](restore-sql-database-azure-vm.md)
* [Administración de bases de datos de SQL Server con copia de seguridad](manage-monitor-sql-database-backup.md)
