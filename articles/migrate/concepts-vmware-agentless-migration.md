---
title: Replicación sin agente de conceptos de máquinas virtuales de VMware
description: Describe los conceptos relacionados con la migración sin agente de máquinas virtuales de VMware en Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 05/31/2021
ms.openlocfilehash: ed0560d85d267de90ff23d8aa66c1f628c90e3c6
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967443"
---
# <a name="azure-migrate-agentless-migration-of-vmware-virtual-machines"></a>La migración sin agentes de Azure Migrate de máquinas virtuales de VMware

En este artículo se describen los conceptos de replicación al migrar máquinas virtuales de VMware mediante Azure Migrate: método de migración sin agente de Server Migration.

## <a name="replication-process"></a>Proceso de replicación

La opción de replicación sin agente funciona mediante el uso de instantáneas de VMware y la tecnología de seguimiento de bloques con cambios (CBT) de VMware para replicar datos de discos de máquina virtual. En el siguiente diagrama de bloques se muestran los distintos pasos necesarios para migrar las máquinas virtuales mediante la herramienta de Azure Migrate: Server Migration.

 ![Pasos de migración.](./media/concepts-vmware-agentless-migration/migration-phases.png)

Cuando la replicación está configurada para una máquina virtual, primero pasa por una fase de replicación inicial. Durante la replicación inicial, se realiza una instantánea de la máquina virtual y se replica una copia completa de los datos de los discos de instantánea en los discos administrados en su suscripción de destino.

Una vez completada la replicación inicial de la máquina virtual, el proceso de replicación cambia a una fase de replicación incremental (replicación diferencial). En la fase de replicación incremental, los cambios en los datos que se han producido desde el principio del último ciclo de replicación completado se replican y se escriben en los discos administrados de la réplica, lo que mantiene la replicación sincronizada con los cambios que se producen en la máquina virtual.

La tecnología de seguimiento de bloques con cambios de VMware se usa para realizar un seguimiento de los cambios entre los ciclos de replicación. Al principio del ciclo de replicación, se toma una instantánea de la máquina virtual y se usa el seguimiento de bloques con cambios para obtener los cambios entre la instantánea actual y la última instantánea que se replicó correctamente. Solo se replican los datos que han cambiado desde el ciclo de replicación completado anterior para mantener sincronizada la replicación de la máquina virtual. Al final de cada ciclo de replicación, se publica la instantánea y se realiza la consolidación de instantáneas para la máquina virtual.

Al realizar la operación de migración en una máquina virtual de replicación, hay un ciclo de replicación diferencial a petición que replica los cambios restantes desde el último ciclo de replicación. Después de que se complete el ciclo a petición, se usan los discos administrados de la réplica correspondientes a la máquina virtual para crear la máquina virtual en Azure. Justo antes de desencadenar la migración o conmutación por error, debe apagar la máquina virtual local. El apagado de la máquina virtual garantiza que no se pierdan datos durante la migración.


Una vez que la migración se realiza correctamente y la máquina virtual se inicia en Azure, asegúrese de detener la replicación de la máquina virtual. Al detener la replicación, se eliminarán los discos intermedios (discos de inicialización) que se crearon durante la replicación de datos y también evitará incurrir en cargos adicionales asociados a las transacciones de almacenamiento en estos discos.

## <a name="replication-cycles"></a>Ciclos de replicación

Los ciclos de replicación hacen referencia al proceso periódico de transferencia de datos desde el entorno local a discos administrados de Azure. Un ciclo de replicación completo consta de los pasos siguientes:

1. Creación de una instantánea de VMware para cada disco asociado a la máquina virtual
2. Carga de datos en una cuenta de almacenamiento de registros en Azure
3. Publicación de instantáneas
4. Consolidación de discos de VMware

Se dice que un ciclo se completa una vez que se consolidan los discos.

## <a name="components-involved-in-replication"></a>Componentes implicados en la replicación

**Componentes locales:** el dispositivo de Azure Migrate tiene los siguientes componentes que se encargan de la replicación

- Agente de recuperación de datos (DRA)
- Agente de puerta de enlace

**Componentes de Azure:** en la tabla siguiente se resumen los Azure Artifacts que se crean al usar el método sin agente de migración de máquinas virtuales de VMware.

| Componente | Region | Subscription | Descripción |
| --- | --- | --- | --- |
| Almacén de Recovery Services | Región de los proyectos de Azure Migrate | Suscripción de los proyectos de Azure Migrate | Se usa para orquestar la replicación de datos |
| Service Bus | Región de destino | Suscripción de los proyectos de Azure Migrate | Se usa para la comunicación entre el servicio en la nube y el dispositivo de Azure Migrate |
| Cuenta de almacenamiento de registros | Región de destino | Suscripción de los proyectos de Azure Migrate | Se usa para almacenar datos de replicación, que lee el servicio y se aplica en el disco administrado del cliente |
| Cuenta de almacenamiento de puerta de enlace | Región de destino | Suscripción de los proyectos de Azure Migrate | Se usa para almacenar los estados de la máquina durante la replicación |
| Almacén de claves | Región de destino | Suscripción de los proyectos de Azure Migrate | Administra las claves de la cuenta de almacenamiento de registros y almacena las cadenas de conexión de Service Bus |
| Máquina virtual de Azure | Región de destino | Suscripción de destino | Máquina virtual que se crea en Azure al migrar |
| Azure Managed Disks | Región de destino | Suscripción de destino | Discos administrados conectados a máquinas virtuales de Azure |
| Tarjetas adaptadoras de red | Región de destino | Suscripción de destino | Las NIC asociadas a las máquinas virtuales que se crean en Azure |

## <a name="permissions-required"></a>Permisos necesarios

Al iniciar la replicación por primera vez, al usuario que inició sesión se le deben asignar los siguientes roles:

- Propietario o colaborador y administrador de acceso de usuario en el grupo de recursos del proyecto de Azure Migrate y el grupo de recursos de destino

Para las replicaciones posteriores, al usuario que inició sesión se le deben asignar los siguientes roles:

- Propietario o colaborador en el grupo de recursos del proyecto de Azure Migrate y el grupo de recursos de destino

Además de los roles descritos anteriormente, el usuario que inició sesión necesitará el siguiente permiso en un nivel de suscripción: Microsoft.Resources/subscriptions/resourceGroups/read


## <a name="data-integrity"></a>Integridad de datos

Hay dos fases en cada ciclo de replicación que garantizan la integridad de los datos entre el disco local (disco de origen) y el disco de réplica en Azure (disco de destino).

1. En primer lugar, validamos si todos los sectores que han cambiado en el disco de origen se replican en el disco de destino. La validación se realiza mediante mapas de bits.
El disco de origen se divide en sectores de 512 bytes. Cada sector del disco de origen se asigna a un bit del mapa de bits. Cuando se inicia la replicación de datos, se crea el mapa de bits para todos los bloques de cambio (en el ciclo diferencial) en el disco de origen que se debe replicar. De forma similar, cuando los datos se transfieren al disco de Azure de destino, se crea un mapa de bits. Una vez que la transferencia de datos se completa correctamente, el servicio en la nube compara los dos mapas de bits para asegurarse de que no se pierde ningún bloque de cambio. En caso de que haya algún error de coincidencia entre los mapas de bits, el ciclo no se considera satisfactorio. Como cada ciclo se resincroniza, el error de coincidencia se solucionará en el siguiente ciclo.

1. A continuación, nos aseguramos de que los datos que se transfieren a los discos de Azure son los mismos que los datos que se replicaron desde los discos de origen. Todos los bloques con cambios que se cargan se comprimen y cifran antes de que se escriban como blobs en la cuenta de almacenamiento de registros. Calculamos la suma de comprobación de este bloque antes de la compresión. Esta suma de comprobación se almacena como metadatos junto con los datos comprimidos. Tras la descompresión, la suma de comprobación de los datos se calcula y se compara con la suma de comprobación calculada en el entorno de origen. Si hay un error de coincidencia, los datos no se escriben en los discos de Azure y el ciclo no se considera satisfactorio. Como cada ciclo se resincroniza, el error de coincidencia se solucionará en el siguiente ciclo.

## <a name="security"></a>Seguridad

El dispositivo Azure Migrate comprime los datos y los cifra antes de cargarlos. Los datos se transmiten a través de un canal de comunicación seguro a través de HTTPS y usa TLS 1.2 o posterior. Además, Azure Storage cifra automáticamente los datos al guardarlos en la nube (cifrado en reposo).



## <a name="migration-phase"></a>Fase de migración

Cuando una máquina virtual se somete a replicación, hay pocos estados posibles:

- **Replicación inicial (en cola):** la máquina virtual se pone en cola para replicación (o migración) cuando hay otras máquinas virtuales que consumen los recursos locales durante la replicación (o migración). Una vez que los recursos están disponibles, se procesará esta máquina virtual.
- **Replicación inicial:** la máquina virtual está experimentando la replicación inicial. Cuando la máquina virtual está experimentando la replicación inicial, no puede realizar la migración ni la migración de prueba. Solo puede detener la replicación en esta fase.
- **Migración de prueba pendiente:** la máquina virtual está en fase de replicación diferencial y ahora puede realizar una migración de prueba (o migración).
- **Migración en curso (en cola):** la máquina virtual se pone en cola para migración cuando hay otras máquinas virtuales que consumen los recursos locales durante la replicación (o migración). Una vez que los recursos están disponibles, se procesará la máquina virtual para la migración.
- **Migración en curso:** la máquina virtual está migrando. Puede seleccionar el vínculo para comprobar el trabajo de migración en curso. Este trabajo consta de cinco fases: comprobación de requisitos previos para la migración, apagado de la máquina virtual (paso opcional), preparación para la migración, creación de máquina virtual de Azure e inicio de la máquina virtual de Azure.
- **No aplicable:** cuando la máquina virtual ha migrado correctamente o cuando se ha detenido la replicación, el estado cambia a "no aplicable". Una vez que detenga la replicación y la operación finalice correctamente, la máquina virtual desaparecerá de la lista de máquinas de replicación. Puede encontrar la máquina virtual en la pestaña "Máquinas virtuales" del Asistente para replicación.

> [!Note]
> Algunas máquinas virtuales se ponen en estado "en cola" para garantizar un impacto mínimo en el entorno de origen debido al consumo de IOPS de almacenamiento. Estas máquinas virtuales se procesan en función de la lógica de programación, como se describe en la sección siguiente.

## <a name="scheduling-logic"></a>Lógica de programación

La replicación inicial se programa cuando se configura la replicación para una máquina virtual. Le siguen las replicaciones incrementales (replicaciones diferenciales).

Los ciclos de replicación diferencial se programan de la siguiente manera:

- El primer ciclo de replicación diferencial se programa inmediatamente después de que se complete el ciclo de replicación inicial
- Los siguientes ciclos de replicación diferencial se programan según la lógica siguiente: max [(tiempo del ciclo de replicación diferencial previo/2), 1 hora]

Es decir, la siguiente replicación diferencial se programará, como mínimo, después de una hora. Por ejemplo, si una máquina virtual tarda cuatro horas en un ciclo de replicación diferencial, su siguiente ciclo de replicación diferencial se programa para dos horas después y no para una hora después.

> [!Note]
> La lógica de programación es diferente una vez completada la replicación inicial. El primer ciclo diferencial se programa inmediatamente después de que se complete la replicación inicial y los ciclos posteriores siguen la lógica de programación descrita anteriormente.


- Al desencadenar la migración, se realiza un ciclo de replicación diferencial a petición (ciclo de replicación diferencial previa a la conmutación por error) para la máquina virtual antes de la migración.

**Priorización de máquinas virtuales para varias fases de replicación**

- Las replicaciones de máquinas virtuales en curso tienen prioridad sobre las replicaciones programadas (nuevas replicaciones)
- El ciclo previo a la migración (replicación diferencial a petición) tiene la mayor prioridad seguida del ciclo de replicación inicial. El ciclo de replicación diferencial tiene la menor prioridad.

Es decir, cada vez que se desencadena una operación de migración, se programa el ciclo de replicación a petición de la máquina virtual y otras replicaciones en curso se mantienen en segundo plano si compiten por los recursos.

**Restricciones:**

Usamos las siguientes restricciones para garantizar que no superemos los límites de IOPS en las SAN.

- Cada dispositivo de Azure Migrate admite la replicación de 52 discos en paralelo
- Cada host ESXi admite ocho discos. Cada host ESXi tiene un búfer NFC de 32 MB. Por lo tanto, podemos programar ocho discos en el host (cada disco ocupa 4 MB de búfer para IR, DR).
- Cada almacén de datos puede tener un máximo de 15 instantáneas de disco. La única excepción es cuando una máquina virtual tiene más de 15 discos conectados.

## <a name="scale-out-replication"></a>Replicación de escalabilidad horizontal

Azure Migrate admite la replicación simultánea de 500 máquinas virtuales. Cuando tenga la intención de replicar más de 300 máquinas virtuales, debe implementar un dispositivo de escalabilidad horizontal. El dispositivo de escalabilidad horizontal es similar a un dispositivo principal de Azure Migrate, pero consta solo de un agente de puerta de enlace para facilitar la transferencia de datos a Azure. En el diagrama siguiente se muestra la manera recomendada de usar el dispositivo de escalabilidad horizontal.

![Configuración de escalado horizontal.](./media/concepts-vmware-agentless-migration/scale-out-configuration.png)


Puede implementar el dispositivo de escalabilidad horizontal en cualquier momento después de configurar el dispositivo principal hasta que haya 300 máquinas virtuales replicando simultáneamente. Cuando hay 300 máquinas virtuales replicando simultáneamente, debe implementar el dispositivo de escalabilidad horizontal para continuar.

## <a name="stop-replication"></a>Detención replicación

Al detener la replicación, se eliminarán los discos administrados intermedios (discos de inicialización) que se crearon durante la replicación. La máquina virtual cuya replicación se detiene se puede replicar y migrar de nuevo siguiendo los pasos habituales.

Puede detener la replicación en dos fases:

-  Cuando la replicación está en curso
-  Cuando se ha completado la migración de una máquina virtual

Como procedimiento recomendado, siempre debe detener la replicación después de que la máquina virtual haya migrado correctamente a Azure para asegurarse de que no incurre en cargos adicionales por las transacciones de almacenamiento en los discos administrados intermedios (discos de inicialización).

En algunos casos, observará que detener la replicación lleva tiempo. Esto se debe a que cada vez que detiene la replicación, se completa el ciclo de replicación en curso (solo cuando la máquina virtual está en sincronización diferencial) antes de eliminar los artefactos de compilación.

## <a name="impact-of-churn"></a>Impacto del abandono

Intentamos minimizar la cantidad de transferencia de datos en cada ciclo de replicación permitiendo que los datos se plieguen lo máximo posible antes de programar el siguiente ciclo. Dado que la replicación sin agente incorpora datos, el _patrón de modificación_ es más importante que la _tasa de modificación_. Cuando un archivo se escribe una y otra vez, la tasa no tiene un gran efecto. Sin embargo, un patrón en el que se escriben todos los demás sectores produce una gran renovación en el ciclo siguiente.

## <a name="management-of-replication"></a>Administración de la replicación

### <a name="throttling"></a>Limitaciones

Puede aumentar o disminuir el ancho de banda de replicación mediante _NetQosPolicy._ El elemento _AppNamePrefix_ que se va a usar en _NetQosPolicy_ es "GatewayWindowsService.exe". 

Se puede crear una directiva en el dispositivo de Azure Migrate para limitar el tráfico de replicación desde el dispositivo mediante la creación de una directiva como la siguiente:

```New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB```

> [!NOTE]
> Esto es aplicable a todas las máquinas virtuales de replicación del dispositivo de Azure Migrate simultáneamente.

También puede aumentar y reducir el ancho de banda de replicación en función de una programación mediante el [script de muestra](./common-questions-server-migration.md).

### <a name="blackout-window"></a>Ventana sin disponibilidad

Azure Migrate proporciona un mecanismo basado en la configuración a través del cual los clientes pueden especificar el intervalo de tiempo durante el cual no desean que se realicen replicaciones. Este intervalo de tiempo se denomina ventana sin disponibilidad. La necesidad de una ventana sin disponibilidad puede surgir en varios escenarios, como cuando el entorno de origen tiene recursos limitados, cuando los clientes desean que la replicación tenga lugar solo en horas no comerciales, etc.

> [!NOTE]
> Los ciclos de replicación existentes al principio de la ventana sin disponibilidad se completarán antes de que se detenga la replicación.

Se puede especificar una ventana sin disponibilidad para el dispositivo mediante la creación o actualización del archivo GatewayDataWorker.json en C:\ProgramData\Microsoft Azure\Config. Estos archivos suelen tener el siguiente aspecto:

```
{
    
    "BlackoutWindows": "List of blackout windows"
    
}
```

    
La lista de ventanas sin disponibilidad es una cadena delimitada "|" con el formato "DayOfWeek; StartTime;Duration". La duración se puede especificar en días, horas y minutos. Las ventanas sin disponibilidad se pueden especificar de la manera siguiente:

```
{
    
    "BlackoutWindows": "Monday;7:00;7h | Tuesday;8:00;1d7h | Wednesday;16:00;1d | Thursday;18:00;5h | Friday;13:00;8m"
    
}
```
    
El primer valor del ejemplo anterior indica una ventana sin disponibilidad que comienza todos los lunes a las 7:00 a. m. hora local (hora en el dispositivo) y que dura 7 horas.

Una vez que GatewayDataWorker.jsen se crea o actualiza con este contenido, es necesario reiniciar el servicio de puerta de enlace en el dispositivo para que estos cambios tengan efecto.

En el escenario de escalado horizontal, el dispositivo principal y el dispositivo de escalabilidad horizontal respetan las ventanas sin disponibilidad de forma independiente. Como procedimiento recomendado, se recomienda mantener las ventanas coherentes entre dispositivos.

## <a name="next-steps"></a>Pasos siguientes
[Migración de VM de VMware](tutorial-migrate-vmware.md) con migración sin agentes.