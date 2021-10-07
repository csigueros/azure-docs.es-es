---
title: Instantáneas coherentes con la base de datos mediante un marco de scripts previos y posteriores mejorado
description: Conozca cómo Azure Backup permite tomar instantáneas coherentes con la base de datos gracias al aprovechamiento de la copia de seguridad de máquinas virtuales de Azure y el empleo de scripts previos y posteriores empaquetados
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa9f4ba3dc344e07a3383c6f8081c9304e3ebbeb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092212"
---
# <a name="enhanced-pre-post-scripts-for-database-consistent-snapshot"></a>Scripts previos y posteriores mejorados para unas instantáneas coherentes con la base de datos

El servicio Azure Backup ya proporciona un [marco de scripts _previos y posteriores_](./backup-azure-linux-app-consistent.md) para lograr la coherencia de la aplicación en máquinas virtuales Linux mediante Azure Backup. Esto implica invocar a un script previo (para detener las aplicaciones) antes de tomar una instantánea de los discos y llamar a un script posterior (comandos para liberar las aplicaciones) una vez completada la instantánea para devolver las aplicaciones al modo normal.

La creación, depuración y mantenimiento de los scripts previos y posteriores pueden plantear dificultades. Para quitar esta complejidad, Azure Backup proporciona una experiencia simplificada de scripts previos y posteriores para marcar las bases de datos a fin de obtener una instantánea coherente con la aplicación con la menor sobrecarga.

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/linux-application-consistent-snapshot.png" alt-text="Diagrama que muestra una instantánea coherente con la aplicación de Linux mediante Azure Backup.":::

El nuevo marco de scripts previos y posteriores _mejorado_ tiene las principales ventajas que se señalan a continuación:

- Estos scripts previos y posteriores se instalan directamente en las máquinas virtuales de Azure junto con la extensión de copias de seguridad. Esto ayuda a eliminar la creación y a descargarlos desde una ubicación externa.
- Puede ver la definición y el contenido de los scripts previos en [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), e incluso enviar sugerencias y cambios. También puede enviar sugerencias y cambios a través de GitHub, que se evaluarán y agregarán para beneficiar a una comunidad más amplia.
- Además, puede agregar nuevos scripts previos y posteriores para otras bases de datos a través de [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), que se evaluarán y se tratarán para beneficiar a una comunidad _más amplia_.
- El marco sólido es eficaz para abordar escenarios como bloqueos o errores de ejecución de scripts previos. En cualquier caso, se ejecuta el script posterior automáticamente para revertir todos los cambios realizados en el script previo.
- El marco también proporciona un canal de _mensajería_ para que las herramientas externas capturen actualizaciones y preparen su propio plan de acción sobre cualquier mensaje o evento.

## <a name="solution-flow-preview"></a>Flujo de solución (versión preliminar)

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/solution-flow.png" alt-text="Diagrama que muestra el flujo de solución.":::

## <a name="support-matrix"></a>Matrices compatibles

El marco mejorado cubre la siguiente lista de bases de datos:

- [Oracle (disponible con carácter general)](../virtual-machines/workloads/oracle/oracle-database-backup-azure-backup.md) - [Vínculo a la matriz de compatibilidad](backup-support-matrix-iaas.md#support-matrix-for-managed-pre-post-scripts-for-linux-databases)
- MySQL (versión preliminar)

## <a name="prerequisites"></a>Prerrequisitos

Solo tiene que modificar un archivo de configuración, _workload.conf_ en `/etc/azure`, para proporcionar los detalles de conexión. Esto permite a Azure Backup conectarse a la aplicación pertinente y ejecutar scripts previos y posteriores. El archivo de configuración tiene los siguientes parámetros.

```json
[workload]
# valid values are mysql, oracle
workload_name =
command_path = 
linux_user =
credString = 
ipc_folder = 
timeout =
```

En la siguiente tabla se describen los parámetros:

|Parámetro  |Mandatory  |Explicación  |
|---------|---------|---------|
|workload_name     |   Yes      |     Contendrá el nombre de la base de datos cuya copia de seguridad coherente con la aplicación necesita. Los valores actuales admitidos son `oracle` o `mysql`.    |
|command_path/configuration_path     |         |   Contendrá la ruta de acceso al binario de la carga de trabajo. No es un campo obligatorio si el binario de la carga de trabajo se establece como variable de ruta de acceso.      |
|linux_user     |    Yes     |   Contendrá el nombre del usuario de Linux con acceso al inicio de sesión de usuario de la base de datos. Si no se establece este valor, root se considera el usuario predeterminado.      |
|credString     |         |     Esto representa a la cadena de credencial para conectarse a la base de datos. Contendrá toda la cadena de inicio de sesión.    |
|ipc_folder     |         |   La carga de trabajo solo puede escribir en determinadas rutas de acceso del sistema de archivos. Debe proporcionar aquí esta ruta de acceso de carpeta para que el script previo pueda escribir los estados en ella.      |
|timeout     |    Yes     |     El límite de tiempo máximo hasta el que la base de datos está en estado inactivo. El valor predeterminado es de 90 segundos. No se recomienda establecer un valor inferior a 60 segundos.    |

> [!NOTE]
> La definición JSON es una plantilla que el servicio Azure Backup puede modificar para adaptarse a una base de datos determinada. Para entender el archivo de configuración de cada base de datos, vea el [manual de cada base de datos](#support-matrix).

La experiencia general para usar el marco de scripts previos y posteriores mejorado es la siguiente:

- Preparación del entorno de la base de datos
- Editar el archivo de configuración
- Desencadenamiento de la copia de seguridad de máquina virtual
- Restauración de máquinas virtuales, discos o archivos desde el punto de recuperación coherente con la aplicación, según sea necesario.

## <a name="build-a-database-backup-strategy"></a>Creación de una estrategia de copia de seguridad de base de datos

### <a name="using-snapshots-instead-of-streaming"></a>Uso de instantáneas en lugar de streaming

Normalmente, los administradores de bases de datos usan copias de seguridad de streaming (por ejemplo, completas, diferenciales o incrementales) y registros en su estrategia de copia de seguridad. A continuación se indican algunos de los aspectos clave del diseño.

- **Rendimiento y costo**: una copia de seguridad completa diaria más registros sería lo más rápido durante la restauración, pero implica un costo considerable. Incluir el tipo de copia de seguridad de secuencias diferencial o incremental reduce el costo, pero puede afectar al rendimiento de la restauración. No obstante, las instantáneas proporcionan la mejor combinación de rendimiento y costo.  Como las instantáneas son intrínsecamente incrementales, tienen un impacto mínimo en el rendimiento durante la copia de seguridad, se restauran rápidamente y también ahorran costos.
- **Impacto sobre la base de datos o la infraestructura**: el rendimiento de una copia de seguridad de streaming depende de las IOPS de almacenamiento subyacentes y del ancho de banda de red disponible cuando la secuencia se dirige a una ubicación remota. Las instantáneas no tienen esta dependencia y la demanda de IOPS y ancho de banda de red se reduce significativamente.
- **Reutilización**: los comandos para desencadenar diferentes tipos de copia de seguridad de streaming son distintos para cada base de datos. Por lo tanto, los scripts no se pueden reutilizar fácilmente. Además, si usa diferentes tipos de copia de seguridad, asegúrese de evaluar la cadena de dependencias para mantener el ciclo de vida. En el caso de las instantáneas, es fácil escribir scripts, ya que no hay ninguna cadena de dependencias.
- **Retención a largo plazo**: las copias de seguridad completas son siempre beneficiosas para la retención a largo plazo, ya que se pueden trasladar y recuperar de forma independiente. Sin embargo, en el caso de las copias de seguridad operativas con retención a corto plazo, son favorables las instantáneas.

Por lo tanto, una instantánea diaria más registros con una copia de seguridad completa ocasional para la retención a largo plazo es la mejor directiva de copia de seguridad para las bases de datos.

### <a name="log-backup-strategy"></a>Estrategia de copia de seguridad de registros

El marco de scripts previos y posteriores mejorado se basa en la copia de seguridad de máquinas virtuales de Azure, que programa la copia de seguridad una vez al día. Por lo tanto, la ventana de pérdida de datos con RPO como 24 horas no es adecuada para las bases de datos de producción. Esta solución se complementa con una estrategia de copia de seguridad de registros en la que las copias de seguridad de registros se transmitan explícitamente.

[NFS en blob](../storage/blobs/network-file-system-protocol-support.md) y [NFS en AFS (versión preliminar)](../storage/files/files-nfs-protocol.md) facilitan el montaje de volúmenes directamente en las máquinas virtuales de base de datos y usan los clientes de base de datos para transferir copias de seguridad de registros. La ventana de pérdida de datos, es decir, RPO, se reduce a la frecuencia de las copias de seguridad de registros. Además, los destinos NFS no necesitan tener un alto rendimiento porque es posible que no haya que desencadenar streaming periódicos (completos e incrementales) de las copias de seguridad operativas después de obtener instantáneas coherentes con una base de datos.

>[!NOTE]
>El script previo mejorado normalmente se encarga de vaciar todas las transacciones de registro en tránsito al destino de copia de seguridad de registros antes de detener la base de datos para tomar una instantánea. Por lo tanto, las instantáneas son coherentes con la base de datos y confiables durante la recuperación.

### <a name="recovery-strategy"></a>Estrategia de recuperación

Una vez que se han tomado instantáneas coherentes con la base de datos y las copias de seguridad de registros se han transmitido a un volumen NFS, la estrategia de recuperación de la base de datos podría usar la funcionalidad de recuperación de copias de seguridad de máquinas virtuales de Azure. La capacidad de copias de seguridad de registros se aplica adicionalmente mediante el cliente de la base de datos. Estas son algunas opciones de estrategia de recuperación:

- Cree nuevas máquinas virtuales desde el punto de recuperación coherente con la base de datos. La máquina virtual ya debe tener conectado el punto de montaje del registro. Use clientes de base de datos para ejecutar comandos de recuperación para la recuperación a un momento dado.
- Cree discos desde un punto de recuperación coherente con la base de datos y adjúntelos a otra máquina virtual de destino. Después, monte el destino del registro y use clientes de base de datos para ejecutar comandos de recuperación para la recuperación a un momento dado.
- Use la opción de recuperación de archivos y genere un script. Ejecute el script en la máquina virtual de destino y conecte el punto de recuperación como discos iSCSI. A continuación, use clientes de base de datos para ejecutar las funciones de validación específicas de la base de datos en los discos conectados y valide los datos de copia de seguridad. Además, use clientes de base de datos para exportar o recuperar algunas tablas o archivos en lugar de recuperar toda la base de datos.
- Use la funcionalidad de restauración entre regiones para realizar las acciones anteriores desde la región emparejada secundaria durante un desastre regional.

## <a name="summary"></a>Resumen

Con las instantáneas coherentes con la base de datos y los registros cuya copia de seguridad se ha efectuado mediante una solución personalizada puede compilar una solución de copia de seguridad de base de datos rentable y eficaz que aproveche las ventajas de la copia de seguridad de máquinas virtuales de Azure y también reutilice las capacidades de los clientes de base de datos.
