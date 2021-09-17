---
title: Configuración de ASM de Oracle en una máquina virtual Linux en Azure | Microsoft Docs
description: Ponga en funcionamiento rápidamente ASM de Oracle en el entorno de Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 7bd7b8e13ef6c44952f682497a4f4a5cdc2ebc3f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688091"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configuración de ASM de Oracle en una máquina virtual Linux en Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux 

Las máquinas virtuales de Azure proporcionan un entorno informático completamente configurable y flexible. En este tutorial se aborda la implementación de máquinas virtuales de Azure básicas combinada con la instalación y configuración de Automated Storage Management (ASM) de Oracle.  Aprenderá a:

> [!div class="checklist"]
> * Crear y conectarse a una máquina virtual de base de datos de Oracle
> * Instalar y configurar Automated Storage Management de Oracle
> * Instalar y configurar la infraestructura Grid de Oracle
> * Inicializar una instalación de ASM de Oracle
> * Crear una base de datos de Oracle administrada por ASM


Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparación del entorno

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para crear un grupo de recursos, use el comando [az group create](/cli/azure/group). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En este ejemplo, se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Crear una VM

Para crear una máquina virtual basada en la imagen de base de datos de Oracle y configurarla para usar ASM de Oracle, use el comando [az vm create](/cli/azure/vm). 

En el ejemplo siguiente se crea una máquina virtual denominada myVM que tiene un tamaño de Standard_DS2_v2 con cuatro discos de datos adjuntos de 50 GB cada uno. También se crean claves SSH si aún no existen en la ubicación de claves predeterminada.  Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Después de crear la máquina virtual, la CLI de Azure muestra información similar al ejemplo siguiente. Tome nota del valor de `publicIpAddress`, Use esta dirección para acceder a la máquina virtual.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Para crear una sesión de SSH con la máquina virtual y configurar opciones adicionales, use el siguiente comando. Reemplace la dirección IP por el valor `publicIpAddress` para la máquina virtual.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalación de ASM de Oracle

Para instalar ASM de Oracle, complete los pasos siguientes. 

Para más información sobre cómo instalar ASM de Oracle, consulte [Oracle ASMLib Downloads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html) (Descargas de Oracle ASMLib para Oracle Linux 6).  

1. Debe iniciar sesión como raíz para poder continuar con la instalación de ASM:

   ```bash
   sudo su -
   ```
   
2. Ejecute estos comandos adicionales para instalar los componentes de ASM de Oracle:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Compruebe que ASM de Oracle esté instalado:

   ```bash
   rpm -qa |grep oracleasm
   ```

    La salida de este comando debe enumerar los componentes siguientes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM requiere roles y usuarios específicos para poder funcionar correctamente. Los siguientes comandos crean las cuentas de usuario y los grupos que son requisitos previos: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Compruebe que los usuarios y grupos se han creado correctamente:

   ```bash
   id grid
   ```

    La salida de este comando debe enumerar los usuarios y grupos siguientes:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Cree una carpeta para el usuario *grid* y cambie el propietario:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configuración de ASM de Oracle

Para este tutorial, el usuario predeterminado es *grid* y el grupo predeterminado, *asmadmin*. Asegúrese de que el usuario *oracle* forme parte del grupo asmadmin. Para configurar la instalación de ASM de Oracle, complete los pasos siguientes:

1. Configurar el controlador de la biblioteca ASM de Oracle implica definir el usuario predeterminado (grid) y el grupo predeterminado (asmadmin), además de configurar la unidad que se va a iniciar en el arranque (seleccione y) y para examinar los discos en el arranque (seleccione y). Tiene que responder a los mensajes del comando siguiente:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   La salida de este comando debe ser similar a la siguiente, deteniéndose con mensajes que se deben responder.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Vea la configuración del disco:

   ```bash
   cat /proc/partitions
   ```

   La salida de este comando debe ser similar a la lista siguiente de discos disponibles

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Para dar formato al disco */dev/sdc*, ejecute el siguiente comando y responda a los mensajes con:
   - *n* para la nueva partición
   - *p* para la partición principal
   - *1* para seleccionar la primera partición
   - Presione `enter` para el primer cilindro predeterminado
   - Presione `enter` para el último cilindro predeterminado
   - Presione *w* para escribir los cambios en la tabla de particiones  

   ```bash
   fdisk /dev/sdc
   ```
   
   Con las respuestas proporcionadas anteriormente, la salida del comando `fdisk` debe ser similar a la siguiente:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Repita el comando `fdisk` anterior para `/dev/sdd`, `/dev/sde` y `/dev/sdf`.

5. Compruebe la configuración del disco:

   ```bash
   cat /proc/partitions
   ```

   La salida del comando debe tener el siguiente aspecto:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Compruebe el estado del servicio ASM de Oracle e inícielo:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   La salida del comando debe tener el siguiente aspecto:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Cree discos de ASM de Oracle:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   La salida del comando debe tener el siguiente aspecto:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Enumere discos de ASM de Oracle:

   ```bash
   service oracleasm listdisks
   ```

   La salida del comando debe enumerar los siguientes discos de ASM de Oracle:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Cambie las contraseñas de los usuarios root, oracle y grid. **Tome nota de estas contraseñas nuevas** ya que se van a usar posteriormente durante la instalación.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Cambie los permisos de carpeta:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Descarga y preparación de Oracle Grid Infrastructure

Para descargar y preparar el software Oracle Grid Infrastructure, complete los pasos siguientes:

1. Descargue Oracle Grid Infrastructure de la [página de descargas de ASM de Oracle](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html). 

   Bajo el título de la descarga **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** descargue los dos archivos .zip.

2. Después de descargar los archivos .zip en el equipo cliente, puede usar el protocolo de copia segura (SCP) para copiar los archivos a la máquina virtual:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Use SSH para volver a la máquina virtual de Oracle en Azure y mover los archivos .zip a la carpeta /opt. A continuación, cambie el propietario de los archivos:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Descomprima los archivos. (Instale la herramienta de descompresión de Linux si aún no está instalada).

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Cambie el permiso:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Actualice el espacio de intercambio configurado. Los componentes de Oracle Grid necesitan al menos 6,8 GB de espacio de intercambio para instalar Grid. El tamaño de archivo de intercambio predeterminado para las imágenes de Oracle Linux en Azure es solo de 2048 MB. Tendrá que aumentar `ResourceDisk.SwapSizeMB` en el archivo `/etc/waagent.conf` y reiniciar el servicio WALinuxAgent para que la configuración actualizada surta efecto. Dado que es un archivo de solo lectura, debe cambiar los permisos de archivo para habilitar el acceso de escritura.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Busque `ResourceDisk.SwapSizeMB` y cambie el valor a **8192**. Tendrá que presionar `insert` para entrar en modo de inserción, escribir el valor de **8192** y, después, presionar `esc` para volver al modo de comando. Para escribir los cambios y cerrar el archivo, escriba `:wq` y presione `enter`.
   
   > [!NOTE]
   > Se recomienda encarecidamente usar siempre `WALinuxAgent` para configurar el espacio de intercambio de modo que siempre se cree en el disco efímero (disco temporal) para tener el mejor rendimiento. Para más información, vea [Cómo agregar un archivo de intercambio en máquinas virtuales de Azure para Linux](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Preparar el cliente local y la máquina virtual para ejecutar x11
La configuración de ASM de Oracle requiere una interfaz gráfica para completar la instalación y configuración. Para facilitar esta instalación se usa el protocolo x11. Si usa un sistema de cliente (Mac o Linux) que ya tiene funcionalidades de X11 habilitadas y configuradas, puede omitir esta instalación y configuración exclusiva para equipos Windows. 

1. [Descargue PuTTY](https://www.putty.org/) y [Xming](https://xming.en.softonic.com/) en el equipo Windows. Debe completar la instalación de estas dos aplicaciones con los valores predeterminados antes de continuar.

2. Después de instalar PuTTY, abra un símbolo del sistema, cambie a la carpeta de PuTTY (por ejemplo, C:\Archivos de programa\PuTTY) y ejecute `puttygen.exe` para generar una clave.

3. En PuTTY Key Generator:
   
   1. Haga clic en el botón `Generate` para generar una clave.
   2. Copie el contenido de la clave (Ctrl+C).
   3. Haga clic en el botón `Save private key`.
   4. Ignore la advertencia sobre la protección de la clave con una frase de contraseña y, después, haga clic en `OK`.

   ![Captura de pantalla de PuTTY Key Generator](./media/oracle-asm/puttykeygen.png)

4. En la máquina virtual, ejecute estos comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Cree un archivo llamado `authorized_keys`. Pegue el contenido de la clave en este archivo y guárdelo.

   > [!NOTE]
   > La clave debe contener la cadena `ssh-rsa`. Además, el contenido de la clave debe ser una sola línea de texto.
   >  

6. En el sistema cliente, inicie PuTTY. En el panel **Category** (Categoría), vaya a **Connection** > **SSH** > **Auth** (Conexión > SSH > Autenticación). En el cuadro **Private key file for authentication** (Archivo de clave privada para autenticación), vaya a la clave que generó antes.

   ![Captura de pantalla de las opciones de autenticación de SSH](./media/oracle-asm/setprivatekey.png)

7. En el panel **Category** (Categoría), vaya a **Connection** > **SSH** > **X11** (Conexión > SSH > X11). Seleccione la casilla **Enable X11 forwarding** (Habilitar reenvío a X11).

   ![Captura de pantalla de las opciones de reenvío a SSH X11](./media/oracle-asm/enablex11.png)

8. En el panel **Category** (Categoría), vaya a **Session** (Sesión). Escriba la `<publicIPaddress>` de la máquina virtual de ASM de Oracle en el cuadro de diálogo de nombre de host, rellene un nuevo nombre de `Saved Session` y, después, haga clic en `Save`.  Una vez guardado, haga clic en `open` para conectarse a la máquina virtual de ASM de Oracle.  La primera vez que se conecte se le advertirá que el sistema remoto no se almacena en caché en el registro. Haga clic en `yes` para agregarlo y continuar.

   ![Captura de pantalla de las opciones de sesión de PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalación de Oracle Grid Infrastructure

Para instalar Oracle Grid Infrastructure, complete los pasos siguientes:

1. Inicie sesión como **grid**. (Debe poder iniciar sesión sin que se le solicite una contraseña). 

   > [!NOTE]
   > Si está ejecutando Windows, asegúrese de que ha iniciado Xming antes de comenzar la instalación.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Se abre el instalador de Oracle Grid Infrastructure 12c versión 1. (Se puede tardar unos minutos en iniciar el instalador).

2. En la página **Select Installation Option** (Seleccionar opción de instalación), seleccione **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Instalar y configurar Oracle Grid Infrastructure para un servidor independiente).

   ![Captura de pantalla de la página Select Installation Option (Seleccionar opción de instalación) en el instalador](./media/oracle-asm/install01.png)

3. En la página **Select Product Languages** (Seleccionar idiomas de producto), seleccione **English** (Inglés) o el idioma que quiera.  Haga clic en `next`.

4. En la página **Create ASM Disk Group** (Crear grupo de discos de ASM):
   - Escriba un nombre para el grupo de discos.
   - En **Redundancy** (Redundancia), seleccione **External** (Externa).
   - En **Allocation Unit Size** (Tamaño de la unidad de asignación), seleccione **4**.
   - En **Add Disks** (Agregar discos), seleccione **ORCLASMSP**.
   - Haga clic en `next`.

5. En la página **Specify ASM Password** (Especificar contraseña de ASM), seleccione la opción **Use same passwords for these accounts** (Usar las mismas contraseñas para estas cuentas) y escriba una contraseña.

   ![Captura de pantalla de la página Specify ASM Password (Especificar contraseña de ASM) en el instalador](./media/oracle-asm/install04.png)

6. En la página **Specify Management Options** (Especificar opciones de administración), tiene la opción de configurar el Control de EM en la nube. Se va a omitir esta opción, haga clic en `next` para continuar. 

7. En la página **Privileged Operating System Groups** (Grupos de sistemas operativos con privilegios), use la configuración predeterminada. Haga clic en `next` para continuar.

8. En la página **Specify Installation Location** (Especificar ubicación de instalación), use la configuración predeterminada. Haga clic en `next` para continuar.

9. En la página **Create Inventory** (Crear inventario), cambie el directorio de inventario a `/u01/app/grid/oraInventory`. Haga clic en `next` para continuar.

   ![Captura de pantalla de la página Create Inventory (Crear inventario) en el instalador](./media/oracle-asm/install08.png)

10. En la página **Root script execution configuration** (Configuración de ejecución de script de root), active la casilla **Automatically run configuration scripts** (Ejecutar scripts de configuración automáticamente). Después, seleccione la opción **Use "root" user credential** (Usar las credenciales del usuario "root") y escriba la contraseña del usuario root.

    ![Captura de pantalla de la página Root script execution configuration (Configuración de ejecución de script de root) en el instalador](./media/oracle-asm/install09.png)

11. En la página **Perform Prerequisite Checks** (Realizar comprobaciones de requisitos previos), el programa de instalación actual producirá errores. Este es un comportamiento esperado. Seleccione `Fix & Check Again`.

12. En el cuadro de diálogo **Fixup Script** (Script de corrección), haga clic en `OK`.

13. En la página **Summary** (Resumen), revise las opciones de configuración seleccionadas y después haga clic en `Install`.

    ![Captura de pantalla de la página Summary (Resumen) en el instalador](./media/oracle-asm/install12.png)

14. Aparece un cuadro de diálogo de advertencia para informar que los scripts de configuración se deben ejecutar como un usuario con privilegios. Haga clic en `Yes` para continuar.

15. En la página **Finish** (Finalizar), haga clic en `Close` para finalizar la instalación.

## <a name="set-up-your-oracle-asm-installation"></a>Configuración de la instalación de ASM de Oracle

Para configurar la instalación de ASM de Oracle, complete los pasos siguientes:

1. Asegúrese de que sigue conectado como **grid** desde su sesión de X11. Es posible que tenga que presionar `enter` para actualizar el terminal. Después, inicie el asistente de configuración de Automated Storage Management de Oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Se abre el asistente para la configuración de ASM de Oracle.

2. En el cuadro de diálogo **Configure ASM: Disk Groups** (Configurar ASM: grupos de discos), haga clic en el botón `Create` y, después, haga clic en `Show Advanced Options`.

3. En el cuadro de diálogo **Create Disk Group** (Crear grupo de discos):

   - Escriba el nombre del grupo de discos **DATA**.
   - En **Select Member Disks** (Seleccionar discos miembro), seleccione **ORCL_DATA** y **ORCL_DATA1**.
   - En **Allocation Unit Size** (Tamaño de la unidad de asignación), seleccione **4**.
   - Haga clic en `ok` para crear el grupo de discos.
   - Haga clic en `ok` para cerrar la ventana de confirmación.

   ![Captura de pantalla del cuadro de diálogo Create Disk Group (Crear grupo de discos)](./media/oracle-asm/asm02.png)

4. En el cuadro de diálogo **Configure ASM: Disk Groups** (Configurar ASM: grupos de discos), haga clic en el botón `Create` y, después, haga clic en `Show Advanced Options`.

5. En el cuadro de diálogo **Create Disk Group** (Crear grupo de discos):

   - Escriba el nombre del grupo de discos **FRA**.
   - En **Redundancy** (Redundancia), seleccione **External (none)** (Externa [ninguna]).
   - En **Select Member Disks** (Seleccionar discos miembro), seleccione **ORCL_FRA**.
   - En **Allocation Unit Size** (Tamaño de la unidad de asignación), seleccione **4**.
   - Haga clic en `ok` para crear el grupo de discos.
   - Haga clic en `ok` para cerrar la ventana de confirmación.

   ![Captura de pantalla del cuadro de diálogo Crear grupo de discos y donde se resalta la opción Externa (ninguna).](./media/oracle-asm/asm04.png)

6. Seleccione **Exit** (Salir) para cerrar el asistente para la configuración de ASM.

   ![Captura de pantalla del cuadro de diálogo Configure ASM: Disk Groups (Configurar ASM: grupos de discos) con el botón Salir](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Creación de la base de datos

El software de base de datos de Oracle ya está instalado en la imagen de Azure Marketplace. Para crear una base de datos, complete los siguientes pasos:

1. Cambie de usuario al superusuario Oracle e inicialice el agente de escucha para el registro:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Se abre el asistente para la configuración de la base de datos.

2. En la página **Database Operation** (Operación de base de datos), haga clic en `Create Database`.

3. En la página **Creation Mode** (Modo de creación):

   - Escriba un nombre para la base de datos.
   - Para **Storage Type** (Tipo de almacenamiento), asegúrese de que **Automatic Storage Management (ASM)** está seleccionado.
   - Para **Database Files Location** (Ubicación de archivos de base de datos), use la ubicación sugerida de ASM predeterminada.
   - Para **Fast Recovery Area** (Área de recuperación rápida), use la ubicación sugerida de ASM predeterminada.
   - Escriba una **contraseña administrativa** y **confirme la contraseña**.
   - Asegúrese de que `create as container database` está seleccionado.
   - Escriba un valor de `pluggable database name`.

4. En la página **Summary** (Resumen), revise la configuración seleccionada y haga clic en `Finish` para crear la base de datos.

   ![Captura de pantalla de la página Summary (Resumen)](./media/oracle-asm/createdb03.png)

5. Se ha creado la base de datos. En la página **Finish** (Finalizar), tiene la opción de desbloquear cuentas adicionales para usar esta base de datos y cambiar las contraseñas. Si quiere hacerlo, seleccione **Password Management** (Administración de contraseñas); de lo contrario, haga clic en `close`.

## <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Automated Storage Management de Oracle se ha configurado correctamente en la imagen de base de datos de Oracle desde Azure Marketplace.  Cuando ya no necesite esta máquina virtual, puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Configuración de Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configuración de Oracle GoldenGate](Configure-oracle-golden-gate.md)

Revise [Diseño de una base de datos de Oracle](oracle-design.md)
