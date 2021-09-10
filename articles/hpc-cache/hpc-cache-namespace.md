---
title: Uso del espacio de nombres agregado de Azure HPC Cache
description: Planeación del espacio de nombres virtual para Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 500cc23f2091c38006f7e58e150fb246f2a9ca64
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586912"
---
# <a name="plan-the-aggregated-namespace"></a>Planeamiento del espacio de nombres agregado

Azure HPC Cache permite que los clientes accedan a diversos sistemas de almacenamiento a través de un espacio de nombres virtual que oculta los detalles del sistema de almacenamiento back-end.

Después de agregar un destino de almacenamiento, configure una o varias rutas de acceso de espacio de nombres orientadas al cliente para el destino de almacenamiento. Las máquinas cliente montan esta ruta de acceso de archivo y pueden crear solicitudes de lectura de archivos en la memoria caché en lugar de montar el sistema de almacenamiento directamente.

Dado que la memoria caché de Azure HPC Cache administra este sistema de archivos virtual, puede cambiar el destino de almacenamiento sin cambiar la ruta de acceso orientada al cliente. Por ejemplo, podría reemplazar un sistema de almacenamiento en hardware por almacenamiento en la nube sin necesidad de volver a escribir los procedimientos orientados al cliente.

## <a name="aggregated-namespace-example"></a>Ejemplo de espacio de nombres agregado

Planee el espacio de nombres agregado para que los equipos cliente puedan alcanzar de forma sencilla la información que necesitan, y los administradores y los ingenieros de flujo de trabajo pueden distinguir fácilmente las rutas de acceso.

Por ejemplo, supongamos que cuenta con un sistema donde se usa una instancia de Azure HPC Cache para procesar los datos almacenados en Azure Blob Storage. El análisis necesita archivos de plantilla almacenados en un centro de datos local.

Los datos de plantilla se almacenan en un centro de datos y la información necesaria para este trabajo se almacena en estos subdirectorios:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

El sistema de almacenamiento del centro de datos expone estas exportaciones:

* */*
* */goldline*
* */goldline/templates*

Los datos que se van a analizar se han copiado en un contenedor de Azure Blob Storage denominado "sourcecollection" mediante la [utilidad CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir un acceso sencillo a través de la memoria caché, puede crear destinos de almacenamiento con estas rutas de acceso de espacio de nombres virtual:

| Sistema de almacenamiento de back-end <br/> (ruta de acceso de archivos NFS o contenedor de blobs) | Ruta de acceso del espacio de nombres virtual |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| recopilaciónDeOrigen                        | /source/               |

Un destino de almacenamiento NFS puede tener varias rutas de acceso de espacio de nombres virtual, siempre y cuando cada una haga referencia a una ruta de acceso de exportación única. (Lea [Rutas de acceso del espacio de nombres NFS](add-namespace-paths.md#nfs-namespace-paths) para más información sobre el uso de varias rutas de acceso de espacio de nombres con un destino de almacenamiento NFS).

Dado que las rutas de acceso de origen NFS son subdirectorios de la misma exportación, deberá definir varias rutas de acceso de espacio de nombres desde el mismo destino de almacenamiento.

| Nombre de host de destino de almacenamiento  | Ruta de exportación NFS     | Ruta de acceso del subdirectorio | Ruta de acceso del espacio de nombres    |
|--------------------------|---------------------|-------------------|-------------------|
| *Dirección IP o nombre de host* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *Dirección IP o nombre de host* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Una aplicación cliente puede montar la memoria caché y acceder fácilmente a las rutas de archivo del espacio de nombres agregado ``/source``, ``/templates/sku798`` y ``/templates/sku980``.

Un enfoque alternativo podría ser crear una ruta de acceso virtual como `/templates` que se vincula al directorio principal, `acme2017` y, a continuación, hacer que los clientes naveguen a los directorios individuales `sku798` y `sku980` después de montar la memoria caché. Sin embargo, no puede crear una ruta de acceso de espacio de nombres que sea un subdirectorio de otra ruta de acceso del espacio de nombres. Por lo tanto, si crea una ruta de acceso al directorio `acme2017` no puede crear rutas de acceso de espacio de nombres para acceder directamente a sus subdirectorios.

La página de configuración del **espacio de nombres de Azure HPC Cache** muestra el sistema de archivos orientado al cliente y permite agregar o editar rutas de acceso. Lea [Configuración del espacio de nombres agregado](add-namespace-paths.md) para obtener más detalles.

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya decidido cómo configurar el sistema de archivos virtual, siga estos pasos para crearlo:

* [Creación de destinos de almacenamiento](hpc-cache-add-storage.md) para agregar sus sistemas de almacenamiento de back-end a Azure HPC Cache
* [Agregar rutas de acceso de espacio de nombres](add-namespace-paths.md) para crear el espacio de nombres agregado que los equipos cliente usan para tener acceso a los archivos
