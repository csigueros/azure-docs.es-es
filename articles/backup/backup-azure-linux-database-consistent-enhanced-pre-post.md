---
title: Instantáneas coherentes con la base de datos mediante un marco de scripts previos y posteriores mejorado
description: Conozca cómo Azure Backup permite tomar instantáneas coherentes con la base de datos gracias al aprovechamiento de la copia de seguridad de máquinas virtuales de Azure y el empleo de scripts previos y posteriores empaquetados
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5b53a85521188a876b7a0d7368245c9d28910f39
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440122"
---
# <a name="enhanced-pre-post-scripts-for-database-consistent-snapshot"></a>Scripts previos y posteriores mejorados para unas instantáneas coherentes con la base de datos

El servicio Azure Backup ya proporciona un [marco de scripts _previos y posteriores_](/azure/backup/backup-azure-linux-app-consistent) para lograr la coherencia de la aplicación en máquinas virtuales Linux mediante Azure Backup. Esto implica invocar a un script previo (para detener las aplicaciones) antes de tomar una instantánea de los discos y llamar a un script posterior (comandos para liberar las aplicaciones) una vez completada la instantánea para devolver las aplicaciones al modo normal. Los clientes deben crear estos scripts previos y posteriores y proporcionar la información relevante en el archivo *VMSnapshotScriptPluginConfig.js* del directorio `etc/azure`. Pero a los clientes les preocupaban la creación, la depuración y el mantenimiento de sus scripts y consideraban ese trabajo como sobrecarga. Por eso, Azure Backup ha decidido proporcionar una experiencia _mejorada_ de scripts previos y posteriores para marcar las bases de datos de modo que los usuarios puedan obtener instantáneas coherentes con la aplicación cada día sin ninguna sobrecarga adicional. 

A continuación se indican las principales ventajas del nuevo marco de scripts previos y posteriores "mejorado".

- Estos scripts previos y posteriores se instalan directamente en las máquinas virtuales de Azure junto con la extensión de copia de seguridad. No es necesario crearlos ni preocuparse por descargarlos desde una ubicación externa.
- Puede seguir viendo la definición o el contenido de los scripts previos y posteriores en [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts). Puede enviar cambios, y el equipo de Azure Backup los comprobará, revisará y combinará si es necesario para que las ventajas estén disponibles para todos los usuarios.
- También puede optar por agregar nuevos scripts previos y posteriores para cualquier otra base de datos mediante [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts). Con suficiente información y pruebas, el equipo de Azure Backup puede decidir combinarlos y proporcionárselos también a los demás usuarios.
- El marco ya es sólido para abordar escenarios como errores de ejecución de scripts previos o bloqueos. En cualquier caso, se llama al script posterior para revertir todos los cambios realizados en el script previo.
- El marco también proporciona un canal de _mensajería_ para que las herramientas externas escuchen y preparen su propio plan de acción sobre cualquier mensaje o evento.

## <a name="support-matrix"></a>Matrices compatibles

El marco mejorado cubre la siguiente lista de bases de datos:

- [Oracle (versión preliminar)](/azure/virtual-machines/workloads/oracle/oracle-database-backup-azure-backup)
- MySQL (versión preliminar)

## <a name="prerequisites"></a>Prerrequisitos

Solo tiene que editar un archivo de configuración, *workload.conf* en `/etc/azure`, y proporcionar información de conexión para que Azure Backup pueda conectarse a la aplicación correspondiente y ejecutar scripts previos y posteriores. El archivo de configuración tiene los siguientes parámetros.

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

Estos parámetros se explican a continuación.

|Parámetro  |Mandatory  |Explicación  |
|---------|---------|---------|
|workload_name     |   sí      |     Tendrá el nombre de la base de datos cuya copia de seguridad coherente con la aplicación tiene que efectuarse. Los valores actuales admitidos son `oracle` o `mysql`.    |
|command_path/configuration_path     |         |   Tendrá la ruta de acceso al binario de la carga de trabajo. No es un campo obligatorio si el binario de la carga de trabajo se establece como variable de ruta de acceso.      |
|linux_user     |    sí     |   Tendrá el nombre de usuario del linux_user que accede al inicio de sesión de usuario de la base de datos. Si no se establece este valor, root se considera el usuario predeterminado.      |
|credString     |         |     Esto representa a la cadena de credencial para conectarse a la base de datos. Tendrá toda la cadena de inicio de sesión.    |
|ipc_folder     |         |   La carga de trabajo solo puede escribir en determinadas rutas de acceso del sistema de archivos. Esta ruta de acceso de carpeta debe proporcionarse aquí para que el script previo pueda escribir algunos estados en esta ruta de acceso de archivo.      |
|timeout     |    sí     |     Es el tiempo máximo hasta el que la base de datos está en estado inactivo. El valor predeterminado es de 90 segundos. No se recomienda establecer ningún valor inferior a 60 segundos.    |

> [!NOTE]
> La definición JSON es una plantilla que el servicio Azure Backup puede modificar para adaptarse a una base de datos determinada. Para entender el archivo de configuración de cada base de datos, vea el [manual detallado de cada base de datos](#support-matrix).

En general, la experiencia del cliente para usar el marco de scripts previos y posteriores mejorado es la siguiente:

- Preparación del entorno de la base de datos
- Editar el archivo de configuración
- Desencadenamiento de la copia de seguridad de máquina virtual
- Restauración de máquinas virtuales, discos o archivos según sea necesario desde el punto de recuperación coherente con la aplicación

## <a name="build-a-database-backup-strategy"></a>Creación de una estrategia de copia de seguridad de base de datos

### <a name="using-snapshots-instead-of-streaming"></a>Uso de instantáneas en lugar de streaming

Normalmente, los administradores de bases de datos usan copias de seguridad de streaming (por ejemplo, completas, diferenciales o incrementales) y registros en su estrategia de copia de seguridad. A continuación se indican algunos de los aspectos clave del diseño.

- **Rendimiento y costo**: una copia de seguridad completa diaria más registros sería lo más rápido durante la restauración, pero tiene un costo considerable. La inclusión de copias de seguridad diferenciales o incrementales reduce el costo, pero puede afectar al rendimiento de la restauración.
- **Impacto sobre la base de datos o la infraestructura**: el rendimiento de una copia de seguridad de streaming depende de las IOPS de almacenamiento subyacentes y del ancho de banda de red disponible cuando la secuencia se dirige a una ubicación remota.
- **Reutilización**: los comandos para desencadenar diferentes tipos de copia de seguridad de streaming son distintos para cada base de datos. Por lo tanto, los scripts no se pueden reutilizar fácilmente. Además, si usa diferentes tipos de copia de seguridad, debe comprender la cadena de dependencias para mantener el ciclo de vida. Por supuesto, algunos clientes de base de datos se encargan de estos aspectos, pero eso significa que los datos de copia de seguridad también son propensos a ser eliminados por estos clientes.
- **Retención a largo plazo**: las copias de seguridad completas son definitivamente positivas para la retención a largo plazo, ya que se pueden mover y recuperar de forma independiente en cualquier momento dado. La retención de instantáneas ayuda a una restauración rápida, lo que evita problemas de rendimiento debido a una copia de seguridad de streaming.

- **Rendimiento y costo**: proporcionan copia de seguridad y restauración inmediata e instantáneas gracias a la copia de seguridad de máquinas virtuales de Azure; además, son _incrementales_ y, por tanto, también rentables. Con una instantánea actuando como copia de seguridad completa durante la restauración, es posible que no necesite copias de seguridad completas e incrementales de streaming y ahorre en costos de almacenamiento de las copias de seguridad operativas.
- **Impacto sobre la base de datos o la infraestructura**: las instantáneas tienen un impacto mínimo sobre la base de datos y la infraestructura.
- **Reutilización:** solo debe aprender un comando por instantánea de base de datos. No se requiere ninguna cadena de dependencias. El script previo mejorado se encarga del comando.
- **Retención a corto plazo:** no se pueden usar para la retención a largo plazo (debido al vínculo con el almacenamiento subyacente), pero son mejores para la retención a corto plazo y las copias de seguridad operativas diarias.
 
El requisito clave es asegurarse de que la base de datos está implicada antes y después de la instantánea, con los comandos correctos para inmovilizar y reanudar, y que estos se tienen en cuenta en los scripts previos y posteriores.

### <a name="log-backup-strategy"></a>Estrategia de copia de seguridad de registros

El marco de scripts previos y posteriores mejorado se basa en la copia de seguridad de máquinas virtuales de Azure, que programa la copia de seguridad una vez al día. Por lo tanto, la ventana de pérdida de datos con RPO como 24 horas no es aceptable para las bases de datos de producción. Esta solución debe complementarse con una estrategia de copia de seguridad de registros en la que las copias de seguridad de registros se transmitan explícitamente. Con la llegada de [NFS en blob](/azure/storage/blobs/network-file-system-protocol-support) y [NFS en AFS (versión preliminar)](/azure/storage/files/files-nfs-protocol), resulta más sencillo montar volúmenes directamente en las máquinas virtuales de base de datos y usar los clientes de base de datos para transferir copias de seguridad de registros. La ventana de pérdida de datos, es decir, RPO, se reduce a la frecuencia de las copias de seguridad de registros. Además, estos destinos NFS no necesitan tener un alto rendimiento porque, como se ha mencionado anteriormente, es posible que no haya que desencadenar streaming periódicos (completos e incrementales) de las copias de seguridad operativas después de obtener instantáneas coherentes con una base de datos.

>[!NOTE]
>El script previo mejorado normalmente se encarga de vaciar todas las transacciones de registro en tránsito al destino de copia de seguridad de registros antes de detener la base de datos para tomar una instantánea. Esto significa que las instantáneas deben ser coherentes con la base de datos y confiables durante la recuperación.

### <a name="recovery-strategy"></a>Estrategia de recuperación

Una vez que se han tomado instantáneas coherentes con la base de datos y las copias de seguridad de registros se han transmitido a un volumen NFS, la estrategia de recuperación de la base de datos podría aprovechar la funcionalidad de recuperación de la copia de seguridad de máquinas virtuales de Azure y la posibilidad de aplicar copias de seguridad de registros sobre ella mediante el cliente de base de datos. Estas son algunas opciones de estrategia de recuperación:

- Cree nuevas máquinas virtuales desde el punto de recuperación coherente con la base de datos. La máquina virtual ya debe tener el punto de montaje del registro y estar conectada a él. Use clientes de base de datos para ejecutar comandos de recuperación para la recuperación a un momento dado del registro.
- Cree discos desde el punto de recuperación coherente con la base de datos. Asocie a otra máquina virtual de destino. Después, monte el destino del registro y use clientes de base de datos para ejecutar comandos de recuperación para la recuperación a un momento dado.
- Use la opción de recuperación de archivos y genere un script. Ejecute el script en la máquina virtual de destino y conecte el punto de recuperación como discos iSCSI. Use clientes de base de datos para ejecutar funciones de validación específicas de la base de datos en los discos conectados y valide los datos de copia de seguridad. Además, use clientes de base de datos para exportar o recuperar algunas tablas o archivos en lugar de recuperar toda la base de datos.
- Use la funcionalidad de restauración entre regiones para realizar todas o alguna de las acciones anteriores desde la región emparejada secundaria durante un desastre regional.

## <a name="summary"></a>Resumen

Con las instantáneas coherentes con la base de datos y los registros cuya copia de seguridad se ha efectuado mediante una solución personalizada puede compilar una solución de copia de seguridad de base de datos rentable y eficaz que aproveche las ventajas de la copia de seguridad de máquinas virtuales de Azure y también reutilice las capacidades de los clientes de base de datos.

