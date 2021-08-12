---
title: Copia de seguridad de una base de datos de Oracle Database 19c en una máquina virtual Linux de Azure con RMAN y Azure Storage
description: Aprenda a hacer una copia de seguridad de una base de datos de Oracle Database 19c en el almacenamiento en la nube de Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 44d1345a8c02c2cde5d0bc34d1b509af321c42c0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952284"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Copia de seguridad y recuperación de una base de datos de Oracle Database 19c en una máquina virtual Linux de Azure mediante Azure Storage

En este artículo se muestra el uso de Azure Storage como un medio para hacer una copia de seguridad de una base de datos de Oracle que se ejecuta en una máquina virtual de Azure y restaurarla. Hará una copia de seguridad de la base de datos mediante Oracle RMAN en Azure File Storage montado en la máquina virtual con el protocolo SMB. El uso de Azure Storage para los medios de copia de seguridad es extremadamente rentable y eficaz. Sin embargo, en el caso de las bases de datos de gran tamaño, Azure Backup ofrece una mejor solución.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Para llevar a cabo el proceso de copia de seguridad y recuperación, primero debe crear una máquina virtual Linux que tenga una instancia de Oracle Database 19c instalada. La imagen de Marketplace que se usa actualmente para crear la máquina virtual es **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Siga los pasos de la [guía de inicio rápido para la creación de base de datos Oracle](./oracle-database-quick-create.md) a fin de crear una base de datos Oracle para completar este tutorial.

## <a name="prepare-the-database-environment"></a>Preparación del entorno de la base de datos

1. Para crear una sesión de Secure Shell (SSH) con la máquina virtual, use el siguiente comando. Reemplace la combinación de dirección IP y nombre de host con el valor `publicIpAddress` para la máquina virtual.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Cambie al usuario ***root***:
 
   ```bash
   sudo su -
   ```
    
3. Agregue el usuario de Oracle al archivo ***/etc/sudoers***:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. En este paso se presupone que tiene una instancia de Oracle (test) que se ejecuta en una máquina virtual denominada *vmoracle19c*.

   Cambie el usuario al usuario *oracle*:

   ```bash
   sudo su - oracle
   ```
    
5. Antes de conectarse, tiene que definir la variable de entorno ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   También debe agregar la variable ORACLE_SID al archivo `.bashrc` de usuarios de `oracle` para los inicios de sesión futuros con el comando siguiente:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Inicie el cliente de escucha de Oracle si todavía no está en ejecución:
    
   ```bash
   $ lsnrctl start
   ```

    La salida debería tener un aspecto similar al ejemplo siguiente:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  Cree la ubicación del área de recuperación rápida (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Conéctese a la base de datos:

    ```bash
    sqlplus / as sysdba
    ```

9.  Inicie la base de datos si todavía no está en ejecución:

    ```bash
    SQL> startup
    ```

10. Establezca las variables de entorno de la base de datos para el área de recuperación rápida:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Asegúrese de que la base de datos está en modo de registro de archivo para habilitar las copias de seguridad en línea.
    Compruebe primero el estado del archivo de registro:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Y si está en modo NOARCHIVELOG, ejecute los comandos siguientes en sqlplus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Cree una tabla para probar las operaciones de copia de seguridad y restauración:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Copia de seguridad en Azure Files

Siga estos pasos para hacer una copia de seguridad en Azure Files:

1. Configure Azure File Storage.
1. Monte el recurso compartido de archivos de Azure Storage en su máquina virtual.
1. Realice una copia de seguridad de la base de datos.
1. Restaure y recupere la base de datos.

### <a name="set-up-azure-file-storage"></a>Configuración de Azure File Storage

En este paso, hará una copia de seguridad de la base de datos de Oracle mediante Oracle Recovery Manager (RMAN) en el almacenamiento de Azure Files. Los recursos compartidos de archivos de Azure son recursos compartidos de archivos totalmente administrados en la nube. Se pueden acceder mediante el protocolo de Bloque de mensajes del servidor (SMB) o el protocolo Network File System (NFS). En este paso se describe cómo crear un recurso compartido de archivos que usa el protocolo SMB para su montaje en la máquina virtual. Para información sobre el montaje con NFS, consulte [Montaje de Blob Storage con el protocolo Network File System (NFS) 3.0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Al montar la instancia de Azure Files, usaremos `cache=none` para deshabilitar el almacenamiento en caché de los datos del recurso compartido. Y para asegurarse de que los archivos creados en el recurso compartido son propiedad del usuario de Oracle, establezca también las opciones `uid=oracle` y `gid=oinstall`. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

En primer lugar, configure la cuenta de almacenamiento.

1. Configuración de File Storage en Azure Portal

    En Azure Portal, seleccione * **+ Crear un recurso** _ y busque y seleccione *_Cuenta de almacenamiento_**.
    
    ![Captura de pantalla que muestra dónde se crean los recursos y se selecciona Cuenta de almacenamiento.](./media/oracle-backup-recovery/storage-1.png)
    
2. En la página Crear cuenta de almacenamiento, elija el grupo de recursos existente ***rg-oracle** _, asigne el nombre _*_oracbkup1_*_ a la cuenta de almacenamiento y elija _*_Storage V2 (generalpurpose v2)_*_  en Tipo de cuenta. Cambie el valor de Replicación a _*_Almacenamiento con redundancia local (LRS)_*_ y en Rendimiento seleccione _*_Estándar_**. Asegúrese de que la ubicación está establecida en la misma región que todos los demás recursos del grupo. 
    
    ![Captura de pantalla que muestra dónde se elige un grupo de recursos existente.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Haga clic en la pestaña ***Avanzado** _ y, en Azure Files, en _*_Large file shares_*_ (Recursos compartidos de archivos grandes), seleccione _*_Enabled_** (Habilitado). Haga clic en Revisar y crear y, a continuación, en Crear.
    
    ![Captura de pantalla en la que se muestra dónde seleccionar Enabled (Habilitado) en Large file shares (Recursos compartidos de archivos grandes).](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Una vez creada la cuenta de almacenamiento, vaya al recurso y elija ***Recursos compartidos de archivos***.
    
    ![Captura de pantalla que muestra dónde seleccionar Recursos compartidos de archivos.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Haga clic en * **+ Recurso compartido de archivos** __ y, en la hoja _*_Nuevo recurso compartido de archivos_*_ asigne al recurso compartido de archivos el nombre _*_orabkup1_*_. En _*_Cuota_*_, especifique _*_10240_*_ GiB y active la casilla _*_Transacción optimizada_*_ como nivel. La cuota refleja un límite superior hasta el que puede crecer el recurso compartido de archivos. Debido a que se usa el almacenamiento Estándar, los recursos son de tipo Pago por uso y no se aprovisionan, por lo que si establece el almacenamiento en 10 TiB no generará costos más allá de lo que use. Si la estrategia de copia de seguridad requiere más almacenamiento, debe establecer la cuota en un nivel adecuado que permita contener todas las copias de seguridad.   Cuando haya completado la hoja Nuevo recurso compartido de archivos, haga clic en _*_Crear_**.
    
    ![Captura de pantalla en la que se muestra dónde agregar un recurso compartido de archivos nuevo.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Después de la creación, haga clic en ***orabkup1*** en la página de configuración del recurso compartido de archivos. 
    Haga clic en la pestaña ***Conectar** _ para abrir la hoja Conectar y, después, haga clic en la pestaña _ *_Linux_**. Copie los comandos proporcionados para montar el recurso compartido de archivos mediante el protocolo SMB. 
    
    ![Página para agregar una cuenta de almacenamiento](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para configurar la cuenta de almacenamiento y el recurso compartido de archivos, ejecute los comandos siguientes en la CLI de Azure.

1. Cree la cuenta de almacenamiento en el mismo grupo de recursos y ubicación que la máquina virtual:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Cree un recurso compartido de archivos en la cuenta de almacenamiento con una cuota de 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Recupere la clave principal (key1) de la cuenta de almacenamiento que se necesita al montar el recurso compartido de archivos en la máquina virtual:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Montaje del recurso compartido de archivos de Azure Storage en una máquina virtual

1. Cree el punto de montaje:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Establezca las credenciales:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Sustituya `<Your Storage Account Key1>` por la clave de la cuenta de almacenamiento que se recuperó anteriormente, antes de ejecutar el comando siguiente:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Cambie los permisos en el archivo de credenciales:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Agregue el montaje al archivo /etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Ejecute los comandos para montar el almacenamiento de Azure Files mediante el protocolo SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Si recibe un error similar a:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   es posible que el paquete CIFS no esté instalado en el host de Linux. 
   
   Para comprobar si CIS está instalado, ejecute el comando siguiente:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Si el comando no devuelve ninguna salida, instale el paquete CIFS con el comando siguiente:

   ```bash 
   sudo yum install cifs-utils
   ```

   Ahora vuelva a ejecutar el comando de montaje anterior para montar el almacenamiento de Azure Files.

6. Use este comando para comprobar si el recurso compartido de archivos se montó correctamente:

   ```bash
   df -h
   ```

   La salida debería tener un aspecto similar al siguiente:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Copia de seguridad de la base de datos

En esta sección, usaremos Oracle Recovery Manager (RMAN) para hacer una copia de seguridad completa de la base de datos y los registros de archivos, y escribiremos la copia de seguridad como un conjunto de copia de seguridad en el recurso compartido de archivos de Azure que se montó anteriormente. 

1. Configure RMAN para que haga copias de seguridad en el punto de montaje de Azure Files:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Como los recursos compartidos de archivos estándar de Azure tienen un tamaño de archivo máximo de 1 TiB, el tamaño de los segmentos de la copia de seguridad de RMAN estará limitado a 1 TiB. (Tenga en cuenta que los recursos compartidos de archivos Premium tienen un límite de tamaño de archivo máximo de 4 TiB. Para más información, consulte [Objetivos de escalabilidad y rendimiento de Azure Files](../../../storage/files/storage-files-scale-targets.md)).

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Confirme los detalles de las modificaciones de la configuración:

    ```bash
    RMAN> show all;
    ```

4. Ahora, ejecute la copia de seguridad. El comando siguiente tomará una copia de seguridad completa de la base de datos, incluidos los archivos de registro de archivo, como conjunto de copias de seguridad en formato comprimido:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Hizo una copia de seguridad de la base de datos en línea con Oracle RMAN, la que se encuentra ubicada en Azure File Storage. Este método tiene la ventaja de usar las características de RMAN al almacenar copias de seguridad en Azure File Storage, donde se puede acceder a ellas desde otras máquinas virtuales, lo que resulta útil si necesita clonar la base de datos.  
    
Aunque el uso de RMAN y Azure File Storage para hacer la copia de seguridad de la base de datos tiene muchas ventajas, el tiempo de copia de seguridad y restauración está vinculado al tamaño de la base de datos, por lo que estas operaciones pueden tardar un tiempo considerable en el caso de las bases de datos de gran tamaño.  Un mecanismo de copia de seguridad alternativo, con copias de seguridad de VM coherentes con la aplicación de Azure Backup, usa la tecnología de instantáneas para realizar las copias de seguridad, lo que tiene la ventaja de hacer copias de seguridad muy rápidas, independientemente del tamaño de la base de datos. La integración con un almacén de Recovery Services proporciona almacenamiento seguro de las copias de seguridad de Oracle Database en el almacenamiento en la nube de Azure accesible desde otras máquinas virtuales y regiones de Azure. 

### <a name="restore-and-recover-the-database"></a>Restauración y recuperación de una base de datos

1.  Cierre la instancia de Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Quite los archivos de datos de la base de datos:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Los siguientes comandos usan RMAN para restaurar los archivos de datos que faltan y recuperar la base de datos:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Compruebe que el contenido de la base de datos se haya recuperado completamente:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


El proceso de copia de seguridad y recuperación de la base de datos de Oracle Database 19c en una máquina virtual Linux de Azure terminó.

## <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Cuando ya no necesite la máquina virtual, puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)

[Ejemplos de la CLI de Azure para implementación de máquinas virtuales](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
