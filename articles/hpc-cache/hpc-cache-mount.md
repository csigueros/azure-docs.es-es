---
title: Montaje de una instancia de Azure HPC Cache
description: Conexión de clientes a un servicio de Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: v-erkel
ms.openlocfilehash: ab66fc29a29461e6808686aa53b520144b39a1e1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274204"
---
# <a name="mount-the-azure-hpc-cache"></a>Montaje de la instancia de Azure HPC Cache

Después de crear la memoria caché, los clientes NFS pueden acceder a ella con un sencillo comando `mount`. El comando conecta una ruta de acceso de destino de almacenamiento específica de Azure HPC Cache a un directorio local en el equipo cliente.

El comando mount se compone de estos elementos:

* Una de las direcciones de montaje de la caché (que aparece en la página información general de la caché)
* Una ruta de acceso de espacio de nombres virtual que establezca para el destino de almacenamiento (que aparece en la página de espacio de nombres de caché)
* Ruta de acceso local que se va a usar en el cliente
* Parámetros del comando que optimizan el éxito de este tipo de montaje de NFS

La página **Instrucciones de montaje** de la memoria caché recopila la información y las opciones recomendadas y crea un prototipo de comando mount que puede copiar. Consulte [Uso de la utilidad de instrucciones de montaje](#use-the-mount-instructions-utility) para más información.

## <a name="prepare-clients"></a>Preparación de los clientes

Asegúrese de que los clientes pueden montar la instancia de Azure HPC Cache según las instrucciones de esta sección.

### <a name="provide-network-access"></a>Acceso de red

Los equipos cliente deben tener acceso de red a la red virtual y la subred privada de la memoria caché.

Por ejemplo, cree máquinas virtuales cliente dentro de la misma red virtual, o bien use un punto de conexión, una puerta de enlace u otra solución de la red virtual para acceder desde fuera. (Recuerde que no se debe hospedar nada que no sea la propia memoria caché dentro de la subred de la memoria caché).

### <a name="install-utilities"></a>Instalación de utilidades

Instale el software de utilidad de Linux adecuado para admitir el comando de montaje de NFS:

* Para Red Hat Enterprise Linux o SuSE: `sudo yum install -y nfs-utils`
* Para Ubuntu o Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Creación de una ruta de acceso local

Cree una ruta de acceso al directorio local en cada cliente para conectarse a la memoria caché. Cree una ruta de acceso para cada ruta de acceso del espacio de nombres que desee montar.

Ejemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

La página [Instrucciones de montaje](#use-the-mount-instructions-utility) de Azure Portal incluye un comando de prototipo que puede copiar.

Cuando conecte la máquina cliente a la memoria caché, asociará esta ruta de acceso a una ruta de acceso del espacio de nombres virtual, que representa una exportación del destino de almacenamiento. Cree directorios para cada una de las rutas de acceso del espacio de nombres virtual que usará el cliente.

## <a name="use-the-mount-instructions-utility"></a>Uso de la utilidad de instrucciones de montaje

Puede usar la página **Instrucciones de montaje** de Azure Portal para crear un comando de montaje que se pueda copiar. Abra la página de la sección **Configurar** de la vista de memoria caché del portal.

Antes de usar el comando en un cliente, asegúrese de que el cliente cumpla los requisitos previos y de que tenga el software necesario para usar el comando `mount` de NFS, tal y como se ha descrito anteriormente en [Preparación de los clientes](#prepare-clients).

![captura de pantalla de una instancia de Azure HPC Cache en el portal, con la página Configurar > Instrucciones de montaje cargada](media/mount-instructions.png)

Siga este procedimiento para crear el comando de montaje.

1. Personalice el campo **Ruta de acceso del cliente**. En este campo se proporciona un comando de ejemplo que puede usar para crear una ruta de acceso local en el cliente. El cliente tiene acceso al contenido de Azure HPC Cache localmente en este directorio.

   Haga clic en el campo y edite el comando para que contenga el nombre del directorio que desee. El nombre aparece al final de la cadena, después de `sudo mkdir -p`.

   ![Captura de pantalla del campo Ruta de acceso del cliente con cursor colocado al final](media/mount-edit-client.png)

   Después de que termine de editar el campo, el comando de montaje al final de la página se actualiza con la nueva ruta de acceso del cliente.

1. Elija la **Dirección de montaje de caché** en la lista. En este menú se enumeran todos los [puntos de montaje de cliente](#find-mount-command-components) de la memoria caché.

   Equilibre la carga del cliente entre todas las direcciones de montaje disponibles con el fin de mejorar el rendimiento de la memoria caché.

   ![Captura de pantalla del campo Dirección de montaje de caché con el selector que muestra tres direcciones IP entre las cuales elegir](media/mount-select-ip.png)

1. Elija la **Ruta de acceso del espacio de nombres virtual** que se va a usar para el cliente. Estas rutas de acceso se vinculan a las exportaciones del sistema de almacenamiento de back-end.

   ![Captura de pantalla que muestra el campo "Virtual namespace path" (Ruta de acceso del espacio de nombres virtual) con el selector abierto.](media/mount-select-target.png)

   Puede ver y cambiar las rutas de acceso del espacio de nombres virtual en la página **Espacio de nombres** del portal. Lea [Configuración del espacio de nombres agregado](add-namespace-paths.md) para ver cómo.

   Para obtener más información sobre la característica de espacio de nombres agregado de Azure HPC Cache, lea [Planeamiento del espacio de nombres agregado](hpc-cache-namespace.md).

1. El campo **Comando de montaje** se rellena automáticamente con un comando de montaje personalizado que utiliza la dirección de montaje, la ruta de acceso del espacio de nombres virtual y la ruta de acceso del cliente que ha establecido en los campos anteriores.

   Haga clic en el símbolo de copia en el lado derecho del campo para copiarlo automáticamente en el portapapeles.

   ![Captura de pantalla del campo del comando prototype mount, que muestra el texto que aparece al pasar el cursor por encima del botón "Copiar en portapapeles"](media/mount-command-copy.png)

   Más adelante se muestran comandos de montaje alternativos, que tienen la misma ruta de acceso de cliente y de espacio de nombres, pero que usan direcciones de montaje de caché diferentes. Para obtener el mejor rendimiento, debe montar los clientes uniformemente en todas las direcciones disponibles en la instancia de HPC Cache.

1. Use el comando mount copiado en la máquina cliente para conectarla a Azure HPC Cache. Puede emitir el comando directamente desde la línea de comandos del cliente o incluir el comando mount en un script o una plantilla de instalación del cliente.

## <a name="understand-mount-command-syntax"></a>Descripción de la sintaxis del comando mount

El comando mount tiene el siguiente formato:

> sudo mount {*opciones*} *dirección_de_montaje_de_caché*:/*ruta_de_acceso_del_espacio_de_nombres* *ruta_de_acceso_local*

Ejemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Si este comando se ejecuta correctamente, el contenido de la exportación de almacenamiento será visible en el directorio ``hpccache`` del cliente.

### <a name="mount-command-options"></a>Opciones del comando de montaje

Para garantizar un montaje sólido del cliente, pase esta configuración y argumentos en el comando de montaje:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Configuración recomendada del comando de montaje | Descripción |
--- | ---
``hard`` | Los montajes flexibles en Azure HPC Cache están asociados con errores de la aplicación y la posible pérdida de datos.
``proto=tcp`` | Esta opción es compatible con el control adecuado de los errores de la red NFS.
``mountproto=tcp`` | Esta opción es compatible con el control adecuado de errores de red para las operaciones de montaje.
``retry=<value>`` | Configure ``retry=30`` para evitar errores de montaje transitorios. (Se recomienda usar un valor diferente en los montajes de primer plano).

### <a name="find-mount-command-components"></a>Búsqueda de componentes del comando mount

Si desea crear un comando de montaje sin usar la página **Instrucciones de montaje**, puede encontrar las direcciones de montaje en la página **Información general** de la memoria caché y las rutas de acceso del espacio de nombres virtual en la página **Espacio de nombres**.

![captura de pantalla de la página de información general de la instancia de Azure HPC Cache, con un cuadro de resaltado alrededor de la lista de direcciones de montaje en la parte inferior derecha](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Las direcciones de montaje de la caché corresponden a las interfaces de red dentro de la subred de la caché. En un grupo de recursos, estas NIC aparecen con nombres que terminan en `-cluster-nic-` y en un número. No las modifique ni elimine o la caché dejará de estar disponible.

Las rutas de acceso del espacio de nombres virtual se muestran en la página de configuración de **Espacio de nombres**.

![Captura de pantalla de la página del portal Configuración > Espacio de nombres con un cuadro de resaltado alrededor de la primera columna de la tabla: "Ruta de acceso del espacio de nombres"](media/view-namespace-paths.png)

## <a name="use-all-available-mount-addresses"></a>Uso de todas las direcciones de montaje disponibles

Debe repartir el tráfico de cliente entre todas las direcciones IP enumeradas para la memoria caché. Si monta todos los clientes en una sola dirección, el rendimiento de la memoria caché se verá afectado.

Puede seleccionar direcciones de montaje diferentes para distintos clientes manualmente o mediante la creación de un script. También puede usar un servidor DNS configurado para DNS de tipo round robin (RRDNS) para rotar automáticamente los montajes de cliente entre todas las direcciones disponibles. Lea [Equilibrio de carga del tráfico de HPC Cache](client-load-balancing.md) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre cómo usar todo el rendimiento de la caché [equilibrando la carga del cliente](client-load-balancing.md).
* Para mover datos a los destinos de almacenamiento de la caché, lea [Rellenado del nuevo almacenamiento de blobs de Azure](hpc-cache-ingest.md).
