---
title: Copia de seguridad y recuperación de una base de datos de Oracle Database 19c en una máquina virtual Linux de Azure mediante Azure Backup
description: Aprenda a realizar una copia de seguridad y recuperar una base de datos de Oracle Database 19c con el servicio de Azure Backup.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 31bb35a096845c489b0f141a601ee604253e6ef9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738987"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Copia de seguridad y recuperación de una base de datos de Oracle Database 19c en una máquina virtual Linux de Azure mediante Azure Backup

En este artículo se muestra el uso de Azure Backup para tomar instantáneas de disco de los discos de la máquina virtual, que incluyen los archivos de base de datos y el área de recuperación rápida. Con Azure Backup puede tomar instantáneas completas de disco que pueden usarse como copias de seguridad y se almacenan en un [almacén de Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md).  Azure Backup también proporciona copias de seguridad coherentes con las aplicaciones, lo que garantiza que no se van a necesitar correcciones adicionales para restaurar los datos. La restauración de datos coherentes con la aplicación reduce el tiempo de restauración, lo que permite volver rápidamente a un estado de ejecución.

> [!div class="checklist"]
>
> * Creación de copias de seguridad de la base de datos con copias de seguridad coherentes con la aplicación
> * Restauración y recuperación de la base de datos a partir de un punto de recuperación
> * Restauración de la máquina virtual a partir de un punto de recuperación

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Para llevar a cabo el proceso de copia de seguridad y recuperación, primero debe crear una máquina virtual Linux que tenga una instancia de Oracle Database 19c instalada. La imagen de Marketplace que se usa actualmente para crear la máquina virtual es **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Siga los pasos de la [guía de inicio rápido para la creación de base de datos Oracle](./oracle-database-quick-create.md) a fin de crear una base de datos Oracle para completar este tutorial.


## <a name="prepare-the-environment"></a>Preparación del entorno

Para preparar el entorno, realice estos pasos:

1. [Conecte a la máquina virtual](#connect-to-the-vm).
1. [Configure Azure File Storage](#setup-azure-files-storage-for-the-oracle-archived-redo-log-files).
1. [Prepare la base de datos](#prepare-the-databases).

### <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

1. Para crear una sesión de Secure Shell (SSH) con la máquina virtual, use el siguiente comando. Reemplace la combinación de dirección IP y nombre de host con el valor `<publicIpAddress>` para la máquina virtual.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Cambie al usuario *root*:

   ```bash
   sudo su -
   ```
    
1. Agregue el usuario de Oracle al archivo */etc/sudoers*:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="setup-azure-files-storage-for-the-oracle-archived-redo-log-files"></a>Configuración de Azure Files Storage para los archivos de registro de la fase de puesta al día archivados de Oracle

Estos archivos desempeñan un rol fundamental en la recuperación de la base de datos, ya que almacenan las transacciones confirmadas necesarias para su puesta al día a partir de una instantánea de base de datos tomada en el pasado. En modo archivelog, la base de datos archiva el contenido de los archivos de registro de la fase de puesta al día en línea cuando se llenan y cambian. Junto con una copia de seguridad, son necesarios para lograr la restauración a un momento dado cuando se ha perdido la base de datos.  
   
Oracle proporciona la funcionalidad de archivar archivos de registro de la fase de puesta al día en diferentes ubicaciones y el procedimiento recomendado del sector aconseja que al menos uno de esos destinos esté en el almacenamiento remoto, por lo que es independiente del almacenamiento del host y está protegido con instantáneas independientes. Azure Files es una excelente opción para esos requisitos.

Un recurso compartido de archivos de Azure Files es el almacenamiento que se puede conectar a una máquina virtual Linux o Windows como componente normal del sistema de archivos, mediante los protocolos SMB o NFS (versión preliminar). 
   
Para configurar un recurso compartido de archivos de Azure Files en Linux mediante el protocolo SMB 3.0 (se recomienda) para su uso como almacenamiento de registro de archivo, consulte la [guía de procedimientos Uso de Azure Files con Linux](../../../storage/files/storage-how-to-use-files-linux.md). Cuando haya completado la configuración, vuelva a esta guía y complete todos los pasos restantes.

### <a name="prepare-the-databases"></a>Preparación de las bases de datos

En este paso se da por supuesto que ha seguido el [inicio rápido creación de bases de datos de Oracle](./oracle-database-quick-create.md) y que tiene una instancia de Oracle denominada `oratest1` que se ejecuta en una máquina virtual denominada `vmoracle19c` y que usa el script "oraenv" estándar de Oracle con su dependencia en el archivo de configuración estándar de Oracle "/etc/oratab" para configurar variables de entorno en una sesión de shell.

Realice los pasos siguientes para cada base de datos de la máquina virtual:

1. Cambie el usuario al usuario *oracle*:
 
   ```bash
    sudo su - oracle
    ```
    
1. Antes de conectarse, debe establecer la variable de ORACLE_SID mediante la ejecución del script `oraenv`, que le pedirá que escriba el nombre ORACLE_SID entorno:
    
    ```bash
    $ . oraenv
    ```

1.   Adición del recurso compartido de Azure Files como un destino de archivo de registro de archivo de base de datos adicional
     
     En este paso se da por supuesto que ha configurado y montado un recurso compartido de Azure Files en la máquina virtual Linux, por ejemplo, en un directorio de punto de montaje denominado `/backup`.

     Para cada base de datos instalada en la máquina virtual, cree un subdirectorio con nombre después del SID de la base de datos siguiendo este ejemplo.
     
     En este ejemplo, el nombre del punto de montaje es `/backup` y el identificador es `oratest1`, por lo que crearemos el subdirectorio `/backup/oratest1` y cambiaremos la propiedad al usuario de Oracle. Sustituya **/backup/SID** por el nombre del punto de montaje y el identificador de seguridad de la base de datos. 

     ```bash
     sudo mkdir /backup/oratest1
     sudo chown oracle:oinstall /backup/oratest1
     ```
    
1. Conéctese a la base de datos:
    
   ```bash
   sqlplus / as sysdba
   ```

1.  Inicie la base de datos, si no se está ejecutando aún. 
   
    ```bash
    SQL> startup
    ```
   
1. Establezca el primer destino de registro de archivo de la base de datos en el directorio fileshare que creó anteriormente:

   ```bash
   SQL> alter system set log_archive_dest_1='LOCATION=/backup/oratest1' scope=both;
   ```

1. Defina el objetivo de punto de recuperación (RPO) de la base de datos.

    Para lograr un RPO coherente, se debe tener en cuenta la frecuencia con la que se archivarán los archivos de registro de la fase de puesta al día en línea. La frecuencia de generación del archivo de registro se controla mediante:
    - El tamaño de los archivos de registro de la fase de puesta al día en línea. Cuando un archivo de registro en línea se llena, se cambia y se archiva. Cuanto mayor sea el archivo de registro en línea, más tiempo tarda en rellenarse, lo que reduce la frecuencia de generación de archivos.
    - La configuración del parámetro ARCHIVE_LAG_TARGET controla el número máximo de segundos permitidos antes de que el archivo de registro en línea actual se deba cambiar y archivar. 

    Para minimizar la frecuencia de conmutación y archivado, junto con la operación del punto de comprobación que lo acompaña, los archivos de registro de fase de puesta al día en línea de Oracle suelen tener un tamaño bastante grande (1024 MB, 4096 MB, 8192 MB, etc.). En un entorno de base de datos ocupado, es probable que los registros cambien y se archiven cada pocos segundos o minutos, pero en una base de datos menos activa pueden pasar horas o días antes de que se archiven las transacciones más recientes, lo que reduciría considerablemente la frecuencia de archivado. Por consiguiente, se recomienda configurar ARCHIVE_LAG_TARGET para garantizar un RPO coherente. Un valor de 5 minutos (300 segundos) es un valor razonable para ARCHIVE_LAG_TARGET, lo que garantiza que cualquier operación de recuperación de la base de datos se pueda recuperar en un plazo de 5 minutos, o menos, del momento del error.

    Para configurar ARCHIVE_LAG_TARGET:

    ```bash 
    SQL> alter system set archive_lag_target=300 scope=both;
    ```

    Para conocer mejor cómo se implementan bases de datos de Oracle de alta disponibilidad en Azure con un objetivo de punto de recuperación cero, consulte [Arquitecturas de referencia para Oracle Database](./oracle-reference-architecture.md).

1.  Asegúrese de que la base de datos está en modo de registro de archivo para habilitar las copias de seguridad en línea.

     Compruebe primero el estado del archivo de registro:

     ```bash
     SQL> SELECT log_mode FROM v$database;

     LOG_MODE
     ------------
     NOARCHIVELOG
     ```

     Si está en modo NOARCHIVELOG, ejecute los siguientes comandos:

     ```bash
     SQL> SHUTDOWN IMMEDIATE;
     SQL> STARTUP MOUNT;
     SQL> ALTER DATABASE ARCHIVELOG;
     SQL> ALTER DATABASE OPEN;
     SQL> ALTER SYSTEM SWITCH LOGFILE;
     ```
1. Cree una tabla para probar las operaciones de copia de seguridad y restauración:
   ```bash
   SQL> create user scott identified by tiger quota 100M on users;
   SQL> grant create session, create table to scott;
   SQL> connect scott/tiger
   SQL> create table scott_table(col1 number, col2 varchar2(50));
   SQL> insert into scott_table VALUES(1,'Line 1');
   SQL> commit;
   SQL> quit
   ```
## <a name="using-azure-backup"></a>Uso de Azure Backup 

El servicio Azure Backup proporciona soluciones sencillas, seguras y rentables tanto para realizar copias de seguridad de datos de la nube de Microsoft Azure como para recuperarlos. Azure Backup proporciona copias de seguridad independientes y aisladas para evitar la destrucción accidental de datos originales. Las copias de seguridad se almacenan en un almacén de Recovery Services con administración integrada de puntos de recuperación. La configuración y la escalabilidad son sencillas, las copias de seguridad están optimizadas y puede restaurarlas fácilmente cuando sea necesario.

El servicio Azure Backup proporciona un [marco](../../../backup/backup-azure-linux-app-consistent.md) para lograr la coherencia de la aplicación durante las copias de seguridad de las máquinas virtuales Windows y Linux para diversas aplicaciones, como Oracle y MySQL. Esto implica invocar un script previo (para poner en modo inactivo las aplicaciones) antes de tomar una instantánea de los discos y llamar un script posterior (para liberar las aplicaciones) una vez completada la instantánea. 

Ahora se ha mejorado el marco de trabajo para que los scripts previos y posteriores empaquetados para aplicaciones seleccionadas, como Oracle, los proporciona el servicio Azure Backup y se cargan previamente en la imagen de Linux, por lo que no es necesario instalar nada. Los usuarios de Azure Backup solo tienen que asignar un nombre a la aplicación y, a continuación, la copia de seguridad de la máquina virtual de Azure invocará automáticamente los scripts previos/posteriores correspondientes. El equipo de Azure Backup dará mantenimiento a los scripts previos y posteriores empaquetados, por lo que los usuarios podrán estar seguros de la compatibilidad, propiedad y validez de estos scripts. Actualmente, las aplicaciones compatibles con el marco mejorado son *Oracle* y *MySQL*.

> [!Note]
> El marco mejorado ejecutará los scripts previos y posteriores en todas las bases de datos de Oracle instaladas en la máquina virtual cada vez que se ejecute una copia de seguridad. 
>
> El parámetro `configuration_path` del archivo **workload.conf** apunta a la ubicación del archivo /etc/oratab de Oracle (o un archivo definido por el usuario que sigue la sintaxisde oratab). Consulte [Configuración de las copias de seguridad coherentes con la aplicación](#set-up-application-consistent-backups) para obtener más información.
> 
> Azure Backup ejecutará los scripts de copia de seguridad anteriores y posteriores para cada base de datos enumerada en el archivo al que apunta configuration_path, excepto las líneas que comienzan por # (tratado como comentario) o +ASM (instancia de Oracle Automatic Storage Management).
> 
> El marco de Azure Backup mejorado realiza copias de seguridad en línea de bases de datos de Oracle que funcionan en modo ARCHIVELOG. Los scripts anteriores y posteriores usan los comandos ALTER DATABASE BEGIN/END BACKUP para lograr la coherencia de la aplicación. 
>
> Las bases de datos en modo NOARCHIVELOG deben cerrarse correctamente antes de que comience la instantánea para que la copia de seguridad de la base de datos sea coherente.


En esta sección, usará el marco de Azure Backup para tomar instantáneas coherentes con la aplicación de la máquina virtual en ejecución y la base de datos de Oracle. La base de datos se pondrá en modo de copia de seguridad, lo que permitirá que se produzca una copia de seguridad en línea coherente con las transacciones mientras Azure Backup toma una instantánea de los discos de máquina virtual. La instantánea será una copia completa del almacenamiento y no una instantánea incremental o de copia por escritura, por lo que es un medio eficaz para restaurar la base de datos. La ventaja de usar instantáneas de Azure Backup coherentes con la aplicación es que son muy rápidas de tomar independientemente del tamaño de la base de datos, y una instantánea se puede usar para las operaciones de restauración tan pronto como se toma, sin tener que esperar a que se transfiera al almacén de Recovery Services.

Para usar Azure Backup para crear una copia de seguridad de la base de datos, siga estos pasos:

1. [Preparación del entorno para una copia de seguridad coherente con la aplicación](#prepare-the-environment-for-an-application-consistent-backup)
1. [Configuración de las copias de seguridad coherentes con la aplicación](#set-up-application-consistent-backups)
1. [Desencadenamiento de una copia de seguridad coherente con la aplicación de la máquina virtual](#trigger-an-application-consistent-backup-of-the-vm)

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Preparación del entorno para una copia de seguridad coherente con la aplicación

> [!Note] 
> La base de datos de Oracle emplea la separación de roles de trabajo para proporcionar separación de las tareas con privilegios mínimos. Para lograrlo, se asocian de grupos de sistemas operativos independientes con roles administrativos de base de datos independientes. A continuación, a los usuarios del sistema operativo se les pueden conceder distintos privilegios en la base de datos en función de su pertenencia a grupos del sistema operativo. 
>
> El rol de base de datos `SYSBACKUP` (el nombre genérico es OSBACKUPDBA) se usa para proporcionar privilegios limitados para realizar operaciones de copia de seguridad en la base de datos y es necesario para Azure Backup.
>
> Durante la instalación de Oracle, el nombre del grupo del sistema operativo recomendado para asociarlo con el rol SYSBACKUP es `backupdba`, pero se puede usar cualquier nombre, por lo que primero es preciso determinar el nombre del grupo del sistema operativo que representa el rol SYSBACKUP de Oracle.

1. Cambie al usuario *oracle*:
   ```bash
   sudo su - oracle
   ```

1. Establezca el entorno de Oracle:
   ```bash
   export ORACLE_SID=oratest1
   export ORAENV_ASK=NO
   . oraenv
   ```

1. Determine el nombre del grupo del sistema operativo que representa el rol SYSBACKUP de Oracle:
   ```bash
   grep "define SS_BKP" $ORACLE_HOME/rdbms/lib/config.c
   ```
   El resultado será similar al siguiente: 
   ```output
   #define SS_BKP_GRP "backupdba"
   ```

   En la salida, el valor entre comillas dobles, en este ejemplo `backupdba`, es el nombre del grupo del sistema operativo Linux en el que se autentica externamente el rol SYSBACKUP de Oracle. Anote este valor.

1. Ejecute el siguiente comando para comprobar si el grupo del sistema operativo existe. Sustituya \<group name\> por el valor devuelto por el comando anterior (sin comillas):
   ```bash
   grep <group name> /etc/group
   ```
   La salida será similar a la siguiente, en nuestro ejemplo se usa `backupdba`: 
   ```output
   backupdba:x:54324:oracle
   ```

   > [!IMPORTANT] 
   > Si la salida no coincide con el valor del grupo del sistema operativo Oracle recuperado en el paso 3, deberá crear el grupo de sistema operativo que representa el rol SYSBACKUP de Oracle. Sustituya `<group name>` por el nombre del grupo recuperado en el paso 3:
   >   ```bash
   >   sudo groupadd <group name>
   >   ```

1. Cree el usuario de copia de seguridad `azbackup`, que pertenece al grupo del sistema operativo que ha comprobado o creado en los pasos anteriores. Sustituya \<group name\> por el nombre del grupo comprobado:

   ```bash
   sudo useradd -g <group name> azbackup
   ```

1. Configure la autenticación externa para el nuevo usuario de copia de seguridad. 

   El usuario de copia de seguridad `azbackup` debe poder acceder a la base de datos mediante la autenticación externa, para no necesitar contraseña. Para ello, debe crear un usuario de base de datos que se autentique externamente mediante `azbackup`. La base de datos usa un prefijo para el nombre de usuario que necesita encontrar.

   Siga los pasos que se indican a continuación en todas las bases de datos instaladas en la máquina virtual:
 
   Inicie sesión en la base de datos con sqlplus y compruebe la configuración predeterminada de la autenticación externa:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   La salida debe ser similar a la de este ejemplo, en el que se muestra `ops$` como prefijo del nombre de usuario de la base de datos: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Cree el usuario de base de datos ***ops$azbackup*** para la autenticación externa al usuario `azbackup` y conceda privilegios sysbackup:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Si recibe el error `ORA-46953: The password file is not in the 12.2 format.` al ejecutar la instrucción `GRANT`, siga estos pasos para migrar el archivo orapwd al formato 12.2. Tenga en cuenta que tendrá que realizar esta operación en todas las bases de datos de Oracle de la máquina  virtual:
   >
   > 1. Salga de sqlplus.
   > 1. Mueva el archivo de contraseña con el formato antiguo a un nuevo nombre.
   > 1. Migre el archivo de contraseña.
   > 1. Quite el archivo antiguo.
   > 1. Ejecute los comandos siguientes:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapworatest1 $ORACLE_HOME/dbs/orapworatest1.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapworatest1 input_file=$ORACLE_HOME/dbs/orapworatest1.tmp
   >    rm $ORACLE_HOME/dbs/orapworatest1.tmp
   >    ```
   >
   > 1. Vuelva a ejecutar la operación `GRANT` en sqlplus.
   >
   
1. Cree un procedimiento almacenado para registrar los mensajes de la copia de seguridad en el registro de alertas de la base de datos:

   Realice lo siguiente para cada base de datos instalada en la máquina virtual:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```

### <a name="set-up-application-consistent-backups"></a>Configuración de las copias de seguridad coherentes con la aplicación  

1. Primero cambie al usuario raíz:
   ```bash
   sudo su -
   ```

1. Busque la carpeta "/etc/azure". Si no está presente, cree el directorio de trabajo de copia de seguridad coherente con la aplicación:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      mkdir /etc/azure
   fi
   ```

1. Busque "workload.conf" dentro de la carpeta. Si no está presente, cree un archivo en el directorio */etc/azure* denominado *workload.conf* con el siguiente contenido, que debe comenzar por `[workload]`. Si el archivo ya está presente, simplemente edite los campos de forma que coincida con el contenido siguiente. En caso contrario, el siguiente comando creará el archivo y rellenará el contenido:

   ```bash
   echo "[workload]
   workload_name = oracle
   configuration_path = /etc/oratab
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

   > [!IMPORTANT]
   > Este es el formato que usa workload.conf:
   > * Azure Backup utiliza el parámetro **workload_name** para determinar el tipo de carga de trabajo de la base de datos. En este caso, si se selecciona Oracle, se permite que Azure Backup ejecute los comandos de coherencia previos y posteriores correctos para las bases de datos de Oracle.
   > * El parámetro **timeout** indica el tiempo máximo, en segundos, que tendrá cada base de datos para completar las instantáneas de almacenamiento.
   > * El parámetro **linux_user** indica la cuenta de usuario de Linux que Azure Backup usará para ejecutar operaciones de inactividad de la base de datos. Este usuario, `azbackup`, lo creó anteriormente.
   > * El parámetro **configuration_path** indica el nombre de la ruta de acceso absoluta de un archivo de texto de la máquina virtual, en la que cada línea enumera una instancia de base de datos que se ejecuta en la máquina virtual. Suele ser el archivo `/etc/oratab`, que lo genera Oracle durante la instalación de la base de datos, pero puede ser cualquier archivo con el nombre que elija; sin embargo, debe seguir estas reglas de formato:
   >   * Un archivo de texto con cada campo delimitado por el carácter de dos puntos, `:`
   >   * El primer campo de cada línea es el nombre de ORACLE_SID
   >   * El segundo campo de cada línea es el nombre de la ruta de acceso absoluta para ORACLE_HOME para ORACLE_SID
   >   * Todo el texto que aparezca después de estos dos primeros campos se omitirá.
   >   * Si la línea comienza con un carácter de libra o almohadilla (`#`), se omitirá toda la línea como comentario.
   >   * Si el primer campo tiene el valor `+ASM`, que indica una instancia de Administración automática de almacenamiento, se omite. 


### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Desencadenamiento de una copia de seguridad coherente con la aplicación de la máquina virtual

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  En Azure Portal, vaya a su grupo de recursos **rg-oracle** y haga clic en la máquina virtual **vmoracle19c**.

1.  En la hoja **Copia de seguridad**, cree un nuevo **almacén de Recovery Services** en el grupo de recursos **rg-oracle** con el nombre **myVault**.
    En **Elegir directiva de copia de seguridad**, use **(nuevo) DailyPolicy**. Si quiere cambiar la frecuencia de la copia de seguridad o el período de retención, seleccione **Crear una nueva directiva** en su lugar.

    ![Página para agregar almacenes de Recovery Services](./media/oracle-backup-recovery/recovery-service-01.png)

1.  Para continuar, haga clic en **Habilitar copia de seguridad**.

    > [!IMPORTANT]
    > Tras hacer clic en **Enable backup** (Habilitar copia de seguridad), el proceso no se inicia hasta que expira el tiempo programado. Para configurar la copia de seguridad inmediata, complete el paso siguiente.

1. En la página del grupo de recursos, haga clic en el almacén de Recovery Services recién creado, **myVault**. Sugerencia: Es posible que deba actualizar la página para que aparezca.

1.  En la hoja **myVault - elementos de copia de seguridad**, en **Recuento de elementos de copia de seguridad**, seleccione el número de elementos de copia de seguridad.

    ![Página de detalles del almacén myVault de Recovery Services](./media/oracle-backup-recovery/recovery-service-02.png)

1.  En la hoja **Elementos de copia de seguridad (Azure Virtual Machine)** , en el lado derecho de la página, haga clic en el botón de puntos suspensivos ( **...** ) y después en **Realizar copia de seguridad ahora**.

    ![Comando Realizar copia de seguridad ahora de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery-service-03.png)

1.  Acepte el valor predeterminado de Conservar la copia de seguridad hasta y haga clic en el botón **Aceptar**. Espere a que finalice el proceso de copia de seguridad. 

    Para ver el estado del trabajo de copia de seguridad, haga clic en **Trabajos de copia de seguridad**.

    ![Página del trabajo de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery-service-04.png)

    El estado del trabajo de copia de seguridad aparece en la siguiente imagen:

    ![Página del trabajo de los almacenes de Recovery Services con el estado](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Tenga en cuenta que, aunque solo se necesitan unos segundos para ejecutar la instantánea, puede tardar algún tiempo en transferirse al almacén y el trabajo de copia de seguridad no se completará hasta que finaliza la transferencia.

1. Para una copia de seguridad coherente con la aplicación, resuelva los errores en el archivo de registro. El archivo de registro se encuentra en /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Creación y configuración de un almacén de Recovery Services:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

1. Habilite la protección de copias de seguridad de la máquina virtual:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

1. Desencadene una copia de seguridad para que se ejecute ahora en lugar de esperar a que la copia de seguridad se desencadene según la programación predeterminada (5 A. M. UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Puede supervisar el progreso del trabajo de copia de seguridad mediante `az backup job list` y `az backup job show`.

---

## <a name="recovery"></a>Recuperación

Para recuperar la base de datos, complete estos pasos:

1. [Elimine los archivos de la base de datos](#remove-the-database-files).
1. [Genere un script de restauración desde el almacén de Recovery Services](#generate-a-restore-script-from-the-recovery-services-vault).
1. [Monte el punto de restauración](#mount-the-restore-point).
1. [Realice la recuperación](#perform-recovery).

### <a name="remove-the-database-files"></a>Eliminación de los archivos de la base de datos 

Más adelante en este artículo obtendrá información sobre cómo probar el proceso de recuperación. Para poder probarlo, tiene que eliminar los archivos de la base de datos.

1.  Vuelva al usuario de Oracle:
    ```bash
    su - oracle
    ```

1. Cierre la instancia de Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

1.  Quite los archivos de datos y los archivos de control de la base de datos para simular un error:

    ```bash
    cd /u02/oradata/ORATEST1
    rm -f *.dbf *.ctl
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Genere un script de restauración desde el almacén de Recovery Services.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. En Azure Portal, busque el almacén *myVault* de Recovery Services y selecciónelo.

    ![Elementos de copia de seguridad del almacén myVault de Recovery Services](./media/oracle-backup-recovery/recovery-service-06.png)

1. En la hoja **Información general**, seleccione **Elementos de copia de seguridad** y seleccione **Máquina virtual de Azure**, que debe tener un número de elementos de copia de seguridad distinto de cero.

    ![Número de elementos de copia de seguridad de máquinas virtuales de Azure en los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

1. En la página Elementos de copia de seguridad (Azure Virtual Machines), se muestra la máquina virtual **vmoracle19c**. Haga clic en los puntos suspensivos de la derecha para abrir el menú y seleccione **Recuperación de archivos**.

    ![Captura de pantalla de la página de recuperación de archivos de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery-service-08.png)

1. En el panel **Recuperación de archivos (versión preliminar)** , haga clic en **Descargar script**. Después, guarde el archivo de descarga (.py) en una carpeta en el equipo cliente. Se genera una contraseña para ejecutar el script. Copie la contraseña en un archivo para uso posterior. 

    ![Operaciones de guardado del archivo de script descargado](./media/oracle-backup-recovery/recovery-service-09.png)

1. Copie el archivo .py en la máquina virtual.

    En el ejemplo siguiente se muestra cómo usar un comando de copia de seguridad (scp) para mover el archivo a la máquina virtual. También puede copiar el contenido en el portapapeles y pegarlo en un nuevo archivo que se haya configurado en la máquina virtual.

    > [!IMPORTANT]
    > En el ejemplo siguiente, asegúrese de actualizar los valores de dirección IP y carpeta. Los valores deben corresponder a la carpeta donde se guarda el archivo.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para enumerar los puntos de recuperación de la máquina virtual, use az backup recovery point list. En este ejemplo, seleccionamos el punto de recuperación más reciente para la máquina virtual denominada vmoracle19c, que está protegida en el almacén de Recovery Services denominado myVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Para obtener el script que conecta el punto de recuperación a la máquina virtual o lo monta en esta, use az backup restore files mount-rp. En el siguiente ejemplo se obtiene el script para la máquina virtual denominada vmoracle19c, que está protegida en el almacén de Recovery Services denominado myVault.

Reemplace myRecoveryPointName por el nombre del punto de recuperación que obtuvo en el comando anterior:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

El script se descarga y se muestra una contraseña, como en el ejemplo siguiente:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Copie el archivo .py en la máquina virtual.

En el ejemplo siguiente se muestra cómo usar un comando de copia de seguridad (scp) para mover el archivo a la máquina virtual. También puede copiar el contenido en el portapapeles y pegarlo en un nuevo archivo que se haya configurado en la máquina virtual.

> [!IMPORTANT]
> En el ejemplo siguiente, asegúrese de actualizar los valores de dirección IP y carpeta. Los valores deben corresponder a la carpeta donde se guarda el archivo.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Montaje del punto de restauración

1. Cambie al usuario root:
   ```bash
   sudo su -
   ``````
1. Cree un punto de montaje de restauración y copie el script en él.

    En el ejemplo siguiente, cree un directorio */restore* para el montaje de la instantánea, mueva el archivo al directorio y cambie el archivo para que sea propiedad del usuario raíz y sea ejecutable.

    ```bash 
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Ahora ejecute el script para restaurar la copia de seguridad. Se le pedirá que proporcione la contraseña generada en Azure Portal. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    En el ejemplo siguiente se muestra lo que verá después de ejecutar el script anterior. Cuando se le pregunte si desea continuar, escriba **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

1. Se confirmará el acceso a los volúmenes montados.

    Para salir, escriba **q** y busque los volúmenes montados. Para crear una lista de los volúmenes agregados, en un símbolo del sistema, escriba **df -h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Realizar la recuperación
Realice los pasos siguientes para cada base de datos de la máquina virtual:

1. Restaure los archivos de base de datos que faltaban a su ubicación:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/oradata/ORATEST1
    cp * /u02/oradata/ORATEST1
    cd /u02/oradata/ORATEST1
    chown -R oracle:oinstall *
    ```
1. Vuelva al usuario de Oracle
   ```bash
   sudo su - oracle
   ```
1. Inicie la instancia de base de datos y monte el archivo de control que se va a leer:
   ```bash
   sqlplus / as sysdba
   SQL> startup mount
   SQL> quit
   ```

1. Conéctese a la base de datos con sysbackup:
   ```bash
   sqlplus / as sysbackup
   ```
1. Inicie la recuperación automática de la base de datos:

   ```bash
   SQL> recover automatic database until cancel using backup controlfile;
   ```
   > [!IMPORTANT]
   > Tenga en cuenta que es importante especificar la sintaxis USING BACKUP CONTROLFILE para informar al comando RECOVER AUTOMATIC DATABASE de que la recuperación no debe detenerse en el número de cambio del sistema (SCN) de Oracle registrado en el archivo de control de la base de datos restaurada. El archivo de control de la base de datos restaurada era una instantánea, junto con el resto de la base de datos, y el SCN almacenado en él es desde el momento de la instantánea. Puede haber transacciones registradas después de este momento y queremos realizar la recuperación al momento dado de la última transacción confirmada en la base de datos.

   Cuando la recuperación se complete correctamente, verá el mensaje `Media recovery complete`. Sin embargo, al usar la cláusula BACKUP CONTROLFILE, el comando de recuperación omitirá los archivos de registro en línea y es posible que haya cambios en el registro de la fase de puesta al día en línea actual necesario para completar la recuperación a un momento dado. En esta situación, es posible que vea mensajes similares a estos:

   ```output
   SQL> recover automatic database until cancel using backup controlfile;
   ORA-00279: change 2172930 generated at 04/08/2021 12:27:06 needed for thread 1
   ORA-00289: suggestion :
   /u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc
   ORA-00280: change 2172930 for thread 1 is in sequence #13
   ORA-00278: log file
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc' no
   longer needed for this recovery
   ORA-00308: cannot open archived log
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc'
   ORA-27037: unable to obtain file status
   Linux-x86_64 Error: 2: No such file or directory
   Additional information: 7

   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   ```
   
   > [!IMPORTANT]
   > Tenga en cuenta que si el registro de la fase de puesta al día en línea actual se ha perdido o está dañado y no se puede usar, puede cancelar la recuperación en este momento. 

   Para corregirlo, puede identificar cuál es el registro en línea actual que no se ha archivado y especificar el nombre de archivo completo en el símbolo del sistema.


   Abra una conexión SSH nueva 
   ```bash
   ssh azureuser@<IP Address>
   ```
   Cambie al usuario de Oracle y establezca el identificador de seguridad de Oracle
   ```bash
   sudo su - oracle
   export ORACLE_SID=oratest1
   ```
   
   Conéctese a la base de datos y ejecute la siguiente consulta para buscar el archivo de registro en línea 
   ```bash
   sqlplus / as sysdba
   SQL> column member format a45
   SQL> set linesize 500  
   SQL> select l.SEQUENCE#, to_char(l.FIRST_CHANGE#,'999999999999999') as CHK_CHANGE, l.group#, l.archived, l.status, f.member
   from v$log l, v$logfile f
   where l.group# = f.group#;
   ```

   El resultado será similar al siguiente. 
   ```output
   SEQUENCE#  CHK_CHANGE           GROUP# ARC STATUS            MEMBER
   ---------- ---------------- ---------- --- ---------------- ---------------------------------------------
           13          2172929          1 NO  CURRENT          /u02/oradata/ORATEST1/redo01.log
           12          2151934          3 YES INACTIVE         /u02/oradata/ORATEST1/redo03.log
           11          2071784          2 YES INACTIVE         /u02/oradata/ORATEST1/redo02.log
   ```
   Copie la ruta de acceso del archivo de registro y el nombre de archivo del registro en línea CURRENT; en este ejemplo, es `/u02/oradata/ORATEST1/redo01.log`. Vuelva a la sesión de SSH que ejecuta el comando recover, especifique la información del archivo de registro y presione Retorno:

   ```bash
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   ```

   Debería ver que se aplica el archivo de registro y se completa la recuperación. Escriba CANCEL para salir del comando de recuperación:
   ```output
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   Log applied.
   Media recovery complete.
   ```

1. Abra la base de datos
   
   > [!IMPORTANT]
   > La opción RESETLOGS es necesaria cuando el comando RECOVER usa la opción USING BACKUP CONTROLFILE. RESETLOGS crea una encarnación de la base de datos mediante el restablecimiento del historial de la fase de puesta al día, ya que no hay forma de determinar la cantidad de encarnación previa de la base de datos que se omitió en la recuperación.

   ```bash
   SQL> alter database open resetlogs;
   ```

   
1. Compruebe que el contenido de la base de datos se haya recuperado completamente:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

1. Desmonte el punto de restauración.

   Cuando todas las bases de datos de la máquina virtual se hayan recuperado correctamente, puede desmontar el punto de restauración. Esto se puede hacer en la máquina virtual mediante el comando `unmount` o en Azure Portal desde la hoja Recuperación de archivos. También puede desmontar los volúmenes de recuperación si ejecuta de nuevo el script de Python con la opción **-clean**.

   En la máquina virtual mediante desmontaje:
   ```bash
   sudo umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    En la hoja **Recuperación de archivos (versión preliminar)** de Azure Portal, haga clic en **Desmontar discos**.

    ![Comando Unmount Disks (Desmontar discos)](./media/oracle-backup-recovery/recovery-service-10.png)
    


## <a name="restore-the-entire-vm"></a>Restauración de toda la máquina virtual

En lugar de restaurar los archivos eliminados de los almacenes de Recovery Services, puede restaurar toda la máquina virtual.

Para restaurar toda la máquina virtual, complete estos pasos:

1. [Detenga y elimine una máquina virtual](#stop-and-delete-the-vm).
1. [Recupere la máquina virtual](#recover-the-vm).
1. [Establezca la dirección IP pública](#set-the-public-ip-address).
1. [Realice la recuperación de la base de datos](#perform-database-recovery).

### <a name="stop-and-delete-the-vm"></a>Detención y eliminación de una máquina virtual

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. En Azure Portal, vaya a la máquina virtual **vmoracle19c** y, a continuación, seleccione **Detener**.

1. Cuando la máquina virtual deje de ejecutarse, seleccione **Eliminar** y, a continuación, **Sí**.

   ![Comando Eliminar almacén](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Detenga y desasigne la máquina virtual:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

1. Elimine la máquina virtual. Cuando se le solicite, escriba "y" (sí):

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Recuperación de la máquina virtual

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Cree una cuenta de almacenamiento para almacenamiento provisional en Azure Portal.

   1. En Azure Portal, seleccione **+ Crear un recurso** y busque y seleccione **Cuenta de almacenamiento**.
    
      ![Captura de pantalla que muestra dónde se crean los recursos.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. En la página Crear cuenta de almacenamiento, elija el grupo de recursos existente **rg-oracle**, asigne el nombre **oracrestore** a la cuenta de almacenamiento y elija **Storage V2 (generalpurpose V2)** para el tipo de cuenta. Cambie la Replicación a **Almacenamiento con redundancia local (LRS)** y establezca el Rendimiento en **Estándar**. Asegúrese de que la ubicación está establecida en la misma región que todos los demás recursos del grupo. 
    
      ![Página de adición de cuenta de almacenamiento](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Haga clic en Revisar y crear y, a continuación, en Crear.

1. En Azure Portal, busque el almacén *myVault* de Recovery Services y haga clic en él.

    ![Elementos de copia de seguridad del almacén myVault de Recovery Services](./media/oracle-backup-recovery/recovery-service-06.png)
    
1.  En la hoja **Información general**, seleccione **Elementos de copia de seguridad** y seleccione **Máquina virtual de Azure**, que debe tener un número de elementos de copia de seguridad distinto de cero.

    ![Número de elementos de copia de seguridad de máquinas virtuales de Azure en los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

1.  En la página Elementos de copia de seguridad (Azure Virtual Machines), se muestra la máquina virtual **vmoracle19c**. Haga clic en el nombre de la máquina virtual.

    ![Página de la máquina virtual de recuperación](./media/oracle-backup-recovery/recover-vm-02.png)

1.  En la hoja **vmoracle19c**, elija un punto de restauración que tenga un tipo de coherencia **Coherente con la aplicación** y haga clic en el botón de puntos suspensivos ( **...** ) de la derecha para abrir el menú.  En el menú, haga clic en **Restaurar VM**.

    ![Comando Restaurar máquina virtual](./media/oracle-backup-recovery/recover-vm-03.png)

1.  En la hoja **Restaurar máquina virtual**, elija **Crear nuevo** y **Crear nueva máquina virtual**. Escriba el nombre de la máquina virtual **vmoracle19c** y elija la red virtual **vmoracle19cVNET**. La subred se rellenará automáticamente en función de la red virtual que seleccione. El proceso de restauración de VM requiere una cuenta de almacenamiento de Azure en el mismo grupo de recursos y región. Puede elegir la cuenta de almacenamiento **orarestore** que configuró antes.

    ![Valores de configuración de la restauración](./media/oracle-backup-recovery/recover-vm-04.png)

1.  Para restaurar la máquina virtual, haga clic en el botón **Restaurar**.

1.  Para ver el estado del proceso de restauración, haga clic en **Trabajos** y después en **Trabajos de copia de seguridad**.

    ![Comando de estado de los trabajos de copia de seguridad](./media/oracle-backup-recovery/recover-vm-05.png)

    Haga clic en la operación de restauración **En curso** para mostrar el estado del proceso de restauración:

    ![Estado del proceso de restauración](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para configurar la cuenta de almacenamiento y el recurso compartido de archivos, ejecute los siguientes comandos en la CLI de Azure.

1. Cree la cuenta de almacenamiento en el mismo grupo de recursos y ubicación que la máquina virtual:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

1. Recupere la lista de puntos de recuperación disponibles. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

1. Restaure el punto de recuperación en la cuenta de almacenamiento. Sustituya `<myRecoveryPointName>` por un punto de recuperación de la lista que se generó en el paso anterior:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

1. Recupere los detalles del trabajo de restauración. El siguiente comando obtiene más detalles sobre el trabajo restaurado desencadenado, incluido su nombre, que se necesita para recuperar el URI de la plantilla. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   El resultado será similar al siguiente `(Note down the name of the restore job)`:

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

1. Recupere los detalles del URI que se usará para volver a crear la máquina virtual. Sustituya el nombre del trabajo de restauración del paso anterior por `<RestoreJobName>`.

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   La salida es parecida a esta:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Se muestra el nombre de la plantilla, que se encuentra al final del URI del blob de la plantilla, que en este ejemplo es `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json`, y el nombre del contenedor de blobs, que es `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2`. 

   Use estos valores en el siguiente comando para asignar variables como preparación para la creación de la máquina virtual. Se genera una clave SAS para el contenedor de almacenamiento con una duración de 30 minutos.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Ahora, implemente la plantilla para crear la máquina virtual.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Se le solicitará que proporcione un nombre para la máquina virtual.

---

### <a name="set-the-public-ip-address"></a>Establecimiento de la dirección IP pública

Una vez restaurada la máquina virtual, debe reasignar la dirección IP original a la nueva máquina virtual.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  En Azure Portal, vaya a la máquina virtual **vmoracle19c**. Observará que se le ha asignado una nueva dirección IP pública y un adaptador de red similar a vmoracle19c-NIC-XXXXXXXXXXXX, pero no tiene una dirección DNS. Cuando se elimina la máquina virtual original, se conservan su adaptador de red e IP pública y se vuelven a asociar a la nueva máquina virtual en los pasos siguientes.

    ![Lista de direcciones IP públicas](./media/oracle-backup-recovery/create-ip-01.png)

1.  Parada de la máquina virtual

    ![Creación de la dirección IP](./media/oracle-backup-recovery/create-ip-02.png)

1.  Vaya a **Redes**.

    ![Asociación de la dirección IP](./media/oracle-backup-recovery/create-ip-03.png)

1.  Haga clic en **Asociar interfaz de red**, elija el adaptador de red original **vmoracle19cVMNic, al que sigue asociada la dirección IP pública original y haga clic en **Aceptar**.

    ![Selección del tipo de recurso y los valores de NIC](./media/oracle-backup-recovery/create-ip-04.png)

1.  Ahora debe desasociar el adaptador de red que se creó con la operación de restauración de la máquina virtual, ya que está configurado como interfaz principal. Haga clic en **Desasociar interfaz de red** y elija el nuevo adaptador de red similar a **vmoracle19c-NIC-XXXXXXXXXXXX** y, a continuación, haga clic en **Aceptar**.

    ![Captura de pantalla que muestra dónde se selecciona Desasociar interfaz de red.](./media/oracle-backup-recovery/create-ip-05.png)
    
    La máquina virtual recreada tendrá ahora el adaptador de red original, que está asociado con la dirección IP y las reglas de grupo de seguridad de red originales.
    
    ![Valor de la dirección IP](./media/oracle-backup-recovery/create-ip-06.png)
    
1.  Vuelva a **Información general** y haga clic en **Iniciar**. 

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Detenga y desasigne la máquina virtual:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

1. Enumere el adaptador de red actual de la máquina virtual generado en la restauración.

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   La salida tendrá un aspecto similar al siguiente, que muestra el nombre del adaptador de red generado por la restauración como `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`.

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

1. Asocie el adaptador de red original, que debe tener un nombre con el formato `<VMName>VMNic`; en este caso, `vmoracle19cVMNic`. La dirección IP pública original sigue conectada a este adaptador de red y se restaurará en la máquina virtual cuando se vuelva a asociar el adaptador de red. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

1. Desasociación de adaptador de red generado por la restauración

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```
1. Inicie la máquina virtual:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="perform-database-recovery"></a>Realización de la recuperación de la base de datos
En primer lugar, vuelva a conectarse a la máquina virtual:

```azurecli
ssh azureuser@<publicIpAddress>
```

Una vez restaurada toda la máquina virtual, es importante recuperar cada base de datos de la máquina virtual mediante los pasos siguientes en cada una de ellas:

1. Es posible que la instancia se esté ejecutando, ya que el inicio automático ha intentado iniciar la base de datos durante el arranque de la máquina virtual. Sin embargo, la base de datos requiere de una recuperación y es probable que solo esté en fase de montaje, por lo que primero se ejecuta un cierre preparatorios seguido del inicio de una fase de montaje.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    ```
    
1. Realización de la recuperación de la base de datos
   > [!IMPORTANT]
   > Tenga en cuenta que es importante especificar la sintaxis USING BACKUP CONTROLFILE para informar al comando RECOVER AUTOMATIC DATABASE de que la recuperación no debe detenerse en el número de cambio del sistema (SCN) de Oracle registrado en el archivo de control de la base de datos restaurada. El archivo de control de la base de datos restaurada era una instantánea, junto con el resto de la base de datos, y el SCN almacenado en él es desde el momento de la instantánea. Puede haber transacciones registradas después de este momento y queremos realizar la recuperación al momento dado de la última transacción confirmada en la base de datos.
    
    ```bash
    SQL> recover automatic database using backup controlfile until cancel;
    ```
   Cuando se haya aplicado el último archivo de registro de archivo disponible, escriba `CANCEL` para finalizar la recuperación.

1. Abra la base de datos
   > [!IMPORTANT]
   > La opción RESETLOGS es necesaria cuando el comando RECOVER usa la opción USING BACKUP CONTROLFILE. RESETLOGS crea una encarnación de la base de datos mediante el restablecimiento del historial de la fase de puesta al día, ya que no hay forma de determinar la cantidad de encarnación previa de la base de datos que se omitió en la recuperación.
   
    ```bash 
    SQL> alter database open resetlogs;
    ```
   

1. Compruebe que el contenido de la base de datos se haya recuperado:

    ```bash
    SQL> select * from scott.scott_table;
    ```

La creación y recuperación de la copia de seguridad de la base de datos de Oracle Database 19c en una máquina virtual Linux de Azure han terminado.

En la documentación de Oracle se puede encontrar más información sobre los comandos y conceptos de Oracle, que incluye:

   * [Realización de copias de seguridad administradas por el usuario de Oracle de toda la base de datos](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-database-backups.html#GUID-65C5E03A-E906-47EB-92AF-6DC273DBD0A8)
   * [Realización de la recuperación completa de una base de datos administrada por el usuario](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-66D07694-533F-4E3A-BA83-DD461B68DB56)
   * [Comando STARTUP de Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html#GUID-275013B7-CAE2-4619-9A0F-40DB71B61FE8)
   * [Comando RECOVER de Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-54B59888-8683-4CD9-B144-B0BB68887572)
   * [Comando ALTER DATABASE de Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-DATABASE.html#GUID-8069872F-E680-4511-ADD8-A4E30AF67986)
   * [Parámetro LOG_ARCHIVE_DEST_n de Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/LOG_ARCHIVE_DEST_n.html#GUID-10BD97BF-6295-4E85-A1A3-854E15E05A44)
   * [Parámetro ARCHIVE_LAG_TARGET de Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ARCHIVE_LAG_TARGET.html#GUID-405D335F-5549-4E02-AFB9-434A24465F0B)


## <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Cuando deje de necesitar la máquina virtual, puede usar los siguientes comandos para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados:

1. Deshabilitación de la eliminación temporal de copias de seguridad en el almacén

    ```azurecli
    az backup vault backup-properties set --name myVault --resource-group rg-oracle --soft-delete-feature-state disable
    ```

1. Detención de la protección de la máquina virtual y eliminación de copias de seguridad

    ```azurecli
    az backup protection disable --resource-group rg-oracle --vault-name myVault --container-name vmoracle19c --item-name vmoracle19c --delete-backup-data true --yes
    ```

1. Eliminación del grupo de recursos, incluidos todos los recursos

    ```azurecli
    az group delete --name rg-oracle
    ```

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)

[Ejemplos de la CLI de Azure para implementación de máquinas virtuales](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
