---
title: Copia de seguridad y restauración del sistema operativo de SAP HANA en SKU de tipo II de Azure (instancias grandes)| Microsoft Docs
description: Realización de copia de seguridad y restauración del sistema operativo de SAP HANA en SKU de tipo II de Azure (instancias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: jaawasth
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: jaawasth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 245c371a002ebfdad4dd0765ba3d283c81c86f03
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631709"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Copia de seguridad y restauración del sistema operativo para SKU de tipo II de los sellos de revisión 3

En este documento se describen los pasos para realizar una copia de seguridad y una restauración de nivel de archivo del sistema operativo para las **SKU de tipo II** de HANA (instancias grandes) de la revisión 3. 

>[!Important]
> **Este artículo no se aplica a las implementaciones de SKU de tipo II en sellos de HANA (instancias grandes) de la revisión 4.** Se puede realizar una copia de seguridad de los LUN de arranque de las unidades de HANA (instancias grandes) de tipo II que se implementan en los sellos de HANA (instancias grandes) de la revisión 4 con instantáneas de almacenamiento, ya que este es el caso con las SKU de tipo I en sellos de la revisión 3.


>[!NOTE]
> * Los scripts de copia de seguridad del sistema operativo usan la utilidad xfsdump.  
> * En este documento se admite una copia de seguridad completa del sistema de archivos raíz y **no se admiten copias de seguridad incrementales**.
> * Asegúrese de que al crear una copia de seguridad, no se escribe ningún archivo en el mismo sistema.  De lo contrario, es posible que los archivos que se escriben durante la copia de seguridad no se incluyan en ella.
> * La copia de seguridad de ReaR está en desuso para las SKU de tipo II de HANA (instancias grandes) de la revisión 3.
> * Este procedimiento se ha probado en varios escenarios de daños en el sistema operativo. Pero, como cliente, es el único responsable del sistema operativo; se recomienda realizar pruebas exhaustivas antes de basarse en esta documentación para el escenario.
> * Este proceso se ha probado en el sistema operativo SLES.
> * No se admiten las actualizaciones de versiones principales, como SLES 12.x a SLES 15x.
> * Para completar una restauración del sistema operativo con este proceso, necesitará ayuda de Microsoft, ya que para la recuperación es necesario acceder a la consola. Cree una incidencia de soporte técnico con Microsoft para facilitar la recuperación.


## <a name="how-to-take-a-manual-backup"></a>Creación de una copia de seguridad manual

Para realizar una copia de seguridad manual:

1. Instale la herramienta de copia de seguridad.
   ```
   zypper in xfsdump
   ```

2. Cree una copia de seguridad completa. 
   ```
   xfsdump -l 0 -f /data1/xfs_dump /
   ```

   La siguiente pantalla muestra la copia de seguridad manual de ejemplo:
   
    [![Captura del volcado de memoria](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png)](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png#lightbox)


3. Importante: Guarde también una copia de seguridad en volúmenes NFS, en el escenario en el que la partición data1 también resulta dañada.
   ```
   cp /data1/xfs_dump /osbackup/
   ```

4. Para excluir directorios y archivos normales del volcado, etiquete los archivos con chattr.
   * chattr -R +d directorio
   * chattr +d archivo
   * Ejecución de xfsdump con la opción "-e"
   * Tenga en cuenta que no es posible excluir sistemas de archivos nfs [ntfs]




## <a name="how-to-restore-a-backup"></a>Restauración de una copia de seguridad

>[!NOTE]
> * Para este paso se necesita la participación del equipo de operaciones de Microsoft.
> * Para completar una restauración del sistema operativo con este proceso, se necesita ayuda de Microsoft, ya que para la recuperación es necesario acceder a la consola. Cree una incidencia de soporte técnico con Microsoft para facilitar la recuperación.
> * Se restaurará el sistema de archivos completo:

1. Monte el archivo .iso del sistema operativo en el sistema.

2. Active el modo de rescate.

3. Monte la partición data1 (o el volumen nfs, en función de dónde se almacene el volcado) en modo de lectura y escritura.
   ```
   mount -o rw /dev/md126p4 /mnt1
   ```
4. Monte Root en modo de lectura y escritura.
   ```
   mount -o rw /dev/md126p2 /mnt2
   ```
5. Restaure el sistema de archivos.
   ```
   xfsrestore -f /mnt1/xfs_dump /mnt2
   ```
   [![Captura de pantalla de la restauración](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png)](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png#lightbox)
6. Reinicie el sistema.
   ```
   reboot
   ```

Si se produce un error en las comprobaciones posteriores, póngase en contacto con el proveedor del sistema operativo y Microsoft para acceder a la consola.

## <a name="post-restore-check"></a>Comprobación posterior a la restauración

1. Asegúrese de que el sistema tiene atributos completos restaurados.
   * La red está operativa.
   * Los volúmenes NFS se han montado.
2. Asegúrese de que RAID está configurado; reemplácelo por el dispositivo RAID.
   ```
   mdadm -D /dev/md126
   ```
   [![Estado de RAID](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png)](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png#lightbox)

3. Asegúrese de que los discos RAID están sincronizados y de que la configuración está en un estado limpio.
   * Los discos RAID necesitan tiempo para sincronizarse; es posible que la sincronización continúe durante unos minutos antes de que se complete al 100 %.

4. Inicie HANA DB y compruebe que HANA funciona según lo previsto.

5. Asegúrese de que HANA aparece y de que no hay errores.
   ```
   hdbinfo
   ```
   [![Estado de HANA](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png)](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png#lightbox)

6. Si se produce un error en las comprobaciones posteriores, póngase en contacto con el proveedor del sistema operativo y Microsoft para acceder a la consola.