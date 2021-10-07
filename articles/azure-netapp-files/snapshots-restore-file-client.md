---
title: Restauración de un archivo a partir de una instantánea mediante un cliente con Azure NetApp Files | Microsoft Docs
description: Describe cómo restaurar un archivo a partir de una instantánea mediante un cliente con el volumen montado mediante Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: db7336901ae349a6a50f71e2adf0102100344871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699514"
---
# <a name="restore-a-file-from-a-snapshot-using-a-client-with-azure-netapp-files"></a>Restauración de un archivo a partir de una instantánea mediante un cliente con Azure NetApp Files | Microsoft Docs

Las [instantáneas](snapshots-introduction.md) permiten la recuperación a un momento dado de los volúmenes. Si no desea [restaurar la instantánea completa en un volumen](snapshots-restore-new-volume.md), tiene la opción de restaurar un archivo desde una instantánea mediante un cliente que tenga el volumen montado.  

El volumen montado contiene un directorio de instantáneas denominado `.snapshot` (en clientes NFS) o `~snapshot` (en clientes SMB) que es accesible para el cliente. El directorio de instantáneas contiene subdirectorios correspondientes a las instantáneas del volumen. Cada subdirectorio contiene los archivos de la instantánea. Si elimina o sobrescribe accidentalmente un archivo, puede restaurarlo en el directorio principal de lectura y escritura copiando el archivo de un subdirectorio de instantánea en el directorio de lectura y escritura. 

Puede controlar el acceso a los directorios de instantáneas mediante la opción [Ocultar la ruta de acceso de la instantánea](snapshots-edit-hide-path.md). Esta opción controla si el directorio debe ocultarse para los clientes. Por lo tanto, también controla el acceso a los archivos y carpetas de las instantáneas.  

NFSv 4.1 no muestra el directorio `.snapshot` (`ls -la`). Sin embargo, aunque no establezca la opción Ocultar la ruta de acceso de la instantánea, todavía puede acceder al directorio `.snapshot` a través de NFSv 4.1 mediante el comando `cd <snapshot-path>` desde la línea de comandos del cliente. 

## <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Restauración de un archivo mediante un cliente NFS de Linux 

1. Utilice el comando de Linux `ls` para mostrar el archivo que desea restaurar desde el directorio `.snapshot`. 

    Por ejemplo:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Utilice el comando `cp` para copiar el archivo en el directorio principal.  

    Por ejemplo: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

## <a name="restore-a-file-by-using-a-windows-client"></a>Restauración un archivo mediante un cliente Windows 

1. Si el directorio `~snapshot` del volumen está oculto, [muestre los elementos ocultos](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) en el directorio principal para ver `~snapshot`.

    ![Captura de pantalla que muestra los elementos ocultos de un directorio.](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Vaya al subdirectorio de `~snapshot` para buscar el archivo que desea restaurar.  Haga clic con el botón derecho en el archivo. Seleccione **Copiar**.  

    ![Captura de pantalla que muestra cómo copiar un archivo para restaurarlo.](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Vuelva al directorio principal. Haga clic con el botón derecho en el directorio principal y seleccione `Paste` para pegar el archivo en el directorio.

    ![Captura de pantalla que muestra cómo pegar un archivo para restaurarlo.](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. También puede hacer clic con el botón derecho en el directorio principal, seleccionar **Propiedades**, hacer clic en la pestaña **Versiones anteriores** para ver la lista de instantáneas y seleccionar **Restaurar** para restaurar un archivo.  

    ![Captura de pantalla que muestra las versiones anteriores de las propiedades.](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las instantáneas](snapshots-introduction.md) 
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo Instantáneas de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Información general sobre instantáneas de Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)