---
title: Solución de problemas con la herramienta Azure Application Consistent Snapshot para Azure NetApp Files | Microsoft Docs
description: Ofrece contenido de solución de problemas para el uso de la herramienta Azure Application Consistent Snapshot con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/17/2021
ms.author: phjensen
ms.openlocfilehash: 0fb0b0fc0734cc05952457e0e6fc6dc5ff5151b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614341"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool"></a>Solución de problemas con la herramienta Azure Application Consistent Snapshot

En este artículo se ofrece contenido de solución de problemas para usar la herramienta Azure Application Consistent Snapshot que se puede emplear con Azure NetApp Files y Azure (instancias grandes).

A continuación se indican algunos problemas comunes que pueden surgir al ejecutar los comandos. Siga las instrucciones de resolución mencionadas para solucionar el problema. Si el problema persiste, abra una solicitud de servicio técnico desde Azure Portal y asigne la solicitud a la cola de instancias grandes de SAP HANA para que responda el equipo de Soporte técnico de Microsoft.

## <a name="log-files"></a>Archivos de registro

Uno de los mejores orígenes de información para depurar los errores con AzAcSnap son los archivos de registro.  

### <a name="log-file-location"></a>Ubicación del archivo de registro

Los archivos de registro se almacenan en el directorio configurado según el parámetro `logPath` en el archivo de configuración de AzAcSnap.  El nombre de archivo de configuración predeterminado es `azacsnap.json` y el valor predeterminado de `logPath` es `"./logs"`, lo que significa que los archivos de registro se escriben en el directorio `./logs` de la ubicación en la que se ejecuta el comando `azacsnap`.  Al convertir `logPath` en una ubicación absoluta (por ejemplo, `/home/azacsnap/logs`), se asegurará de que `azacsnap` genera los registros en `/home/azacsnap/logs`, independientemente de dónde se haya ejecutado el comando `azacsnap`.

### <a name="log-file-naming"></a>Nomenclatura de los archivos de registro

El nombre del archivo de registro se basa en el nombre de la aplicación (por ejemplo, `azacsnap`), la opción de comando (`-c`) utilizada (por ejemplo, `backup`, `test`, `details`, etc.) y el nombre del archivo de configuración (por ejemplo, el valor predeterminado = `azacsnap.json`).  Por lo tanto, si usa el comando `-c backup`, el nombre del archivo de registro predeterminado es `azacsnap-backup-azacsnap.log` y se escribe en el directorio configurado por `logPath`.  

Esta convención de nomenclatura se estableció para permitir varios archivos de configuración, uno por cada base de datos, y facilitar la ubicación de los archivos de registro asociados.  Por lo tanto, si el nombre del archivo de configuración es `SID.json`, el nombre de archivo de resultados al usar las opciones de `azacsnap -c backup --configfile SID.json` será `azacsnap-backup-SID.log`.

### <a name="result-file-and-syslog"></a>Archivo de resultados y syslog

Para la opción de comando `-c backup`, AzAcSnap escribe en un archivo `*.result` y en el registro del sistema (`/var/log/messages`) con el comando `logger`.  El nombre del archivo `*.result` tiene el mismo nombre de base que el [archivo de registro](#log-file-naming) y entra en la misma [ubicación que el archivo de registro](#log-file-location).  Se trata de un archivo de salida simple de una línea como en los ejemplos siguientes.

Salida de ejemplo del archivo `*.result`.
```output
Database # 1 (PR1) : completed ok
```

Salida de ejemplo del archivo `/var/log/messages`.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Error de comunicación con Azure NetApp Files

Cuando se valida la comunicación con Azure NetApp Files, se puede producir un error o agotarse el tiempo de espera.  Asegúrese de que las reglas de firewall no bloqueen el tráfico de salida del sistema que ejecuta AzAcSnap en las siguientes direcciones y puertos TCP/IP:

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

### <a name="testing-communication-using-cloud-shell"></a>Prueba de la comunicación mediante Cloud Shell

Puede probar que la entidad de servicio está configurada correctamente mediante Cloud Shell desde Azure Portal. Esto probará que la configuración es correcta omitiendo los controles de red dentro de una red virtual o máquina virtual. 

**Solución:**

1. Abra una sesión de [Cloud Shell](/azure/cloud-shell/overview) en Azure Portal. 
1. Cree un directorio de prueba (por ejemplo, `mkdir azacsnap`).
1. Ejecute cd en el directorio azacsnap y descargue la versión más reciente de la herramienta azacsnap.
    
    ```bash
    wget https://aka.ms/azacsnapinstaller
    ```
   
    ```output
    ----<snip>----
    HTTP request sent, awaiting response... 200 OK
    Length: 24402411 (23M) [application/octet-stream]
    Saving to: ‘azacsnapinstaller’

    azacsnapinstaller 100%[=================================================================================>] 23.27M 5.94MB/s in 5.3s

    2021-09-02 23:46:18 (4.40 MB/s) - ‘azacsnapinstaller’ saved [24402411/24402411]
    ```
    
1. Convierta el instalador en ejecutable. (por ejemplo, `chmod +x azacsnapinstaller`)
1. Extraiga el archivo binario para realizar pruebas.

    ```bash
    ./azacsnapinstaller -X -d .
    ```
    
    ```output
    +-----------------------------------------------------------+
    | Azure Application Consistent Snapshot Tool Installer |
    +-----------------------------------------------------------+
    |-> Installer version '5.0.2_Build_20210827.19086'
    |-> Extracting commands into ..
    |-> Cleaning up .NET extract dir
    ```

1. Con el icono de carga y descarga de Cloud Shell, cargue el archivo de entidad de servicio (por ejemplo, `azureauth.json`) y el archivo de configuración de AzAcSnap para realizar pruebas (por ejemplo, `azacsnap.json`).
1. Ejecute la prueba de almacenamiento desde la consola de Azure Cloud Shell. 

    > [!NOTE]
    > El comando de prueba puede tardar unos 90 segundos en completarse.

    ```bash
    ./azacsnap -c test --test storage
    ```

    ```output
    BEGIN : Test process started for 'storage'
    BEGIN : Storage test snapshots on 'data' volumes
    BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
    PASSED: Task#1/1 Storage test successful for Volume
    END : Storage tests complete
    END : Test process complete for 'storage'
    ```

## <a name="problems-with-sap-hana"></a>Problemas con SAP HANA

### <a name="running-the-test-command-fails"></a>Se produce un error al ejecutar el comando de prueba.

Al validar la comunicación con SAP HANA mediante la ejecución de una prueba con `azacsnap -c test --test hana`, se muestra el siguiente error:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Solución:**

1. Compruebe el archivo de configuración (por ejemplo, `azacsnap.json`) de cada instancia de HANA para asegurarse de que los valores de la base de datos de SAP HANA sean correctos.
1. Intente ejecutar el siguiente comando para comprobar si el comando `hdbsql` está en la ruta de acceso y puede conectarse al servidor de SAP HANA. En el ejemplo siguiente se muestra la ejecución correcta del comando y su salida.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    En este ejemplo, el comando `hdbsql` no está en el `$PATH` del usuario.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    En este ejemplo, el comando `hdbsql` se agrega temporalmente al `$PATH` del usuario, pero cuando se ejecuta muestra que la clave de conexión no se ha configurado correctamente con el comando `hdbuserstore Set` (consulte la guía de introducción para más información):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (AZACSNAP)
    ```

    > [!NOTE]
    > Para la incorporación permanente al `$PATH` del usuario, actualice el archivo `$HOME/.profile` del usuario.

### <a name="insufficient-privilege"></a>Privilegio insuficiente

Si al ejecutar `azacsnap` aparece un error de tipo `* 258: insufficient privilege`, compruebe que se hayan asignado los privilegios adecuados al usuario de base de datos "AZACSNAP" (suponiendo que este sea el usuario creado según la [guía de instalación](azacsnap-installation.md#enable-communication-with-database)).  Compruebe el privilegio actual del usuario con el siguiente comando:

```bash
hdbsql -U AZACSNAP "select GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE from sys.granted_privileges "' | grep -i -e GRANTEE -e azacsnap
```

```output
GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE
"AZACSNAP","USER","BACKUP ADMIN","TRUE","FALSE"
"AZACSNAP","USER","CATALOG READ","TRUE","FALSE"
"AZACSNAP","USER","CREATE ANY","TRUE","TRUE"
```

El error también puede proporcionar información adicional para ayudar a determinar los privilegios SAP HANA necesarios, como la salida de `Detailed info for this error can be found with guid '99X9999X99X9999X99X99XX999XXX999' SQLSTATE: HY000`.  En este caso, siga las instrucciones de SAP en [SAP Help Portal: GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/9a73c4c017744288b8d6f3b9bc0db043.html), que recomienda usar la siguiente consulta SQL para determinar los detalles sobre el privilegio necesario.

```sql
CALL SYS.GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS ('99X9999X99X9999X99X99XX999XXX999', ?)
```

```output
GUID,CREATE_TIME,CONNECTION_ID,SESSION_USER_NAME,CHECKED_USER_NAME,PRIVILEGE,IS_MISSING_ANALYTIC_PRIVILEGE,IS_MISSING_GRANT_OPTION,DATABASE_NAME,SCHEMA_NAME,OBJECT_NAME,OBJECT_TYPE
"99X9999X99X9999X99X99XX999XXX999","2021-01-01 01:00:00.180000000",120212,"AZACSNAP","AZACSNAP","DATABASE ADMIN or DATABASE BACKUP ADMIN","FALSE","FALSE","","","",""
```

En el ejemplo anterior, al agregar el privilegio "DATABASE BACKUP ADMIN" al usuario AZACSNAP de SYSTEMDB se debería resolver el error de privilegios insuficientes.

### <a name="the-hdbuserstore-location"></a>Ubicación de `hdbuserstore`

Al configurar la comunicación con SAP HANA se usa el programa `hdbuserstore` para crear la configuración de comunicación segura.  El programa `hdbuserstore` suele encontrarse en `/usr/sap/<SID>/SYS/exe/hdb/` o en `/usr/sap/hdbclient`.  Normalmente, el instalador agrega la ubicación correcta al `$PATH` del usuario de `azacsnap`.

## <a name="failed-test-with-storage"></a>Prueba no superada con el almacenamiento

El comando `azacsnap -c test --test storage` no se completa correctamente.

### <a name="azure-large-instance"></a>Azure (instancias grandes)

El ejemplo siguiente procede de la ejecución de `azacsnap` en SAP HANA en Azure Large Instance:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Solución:** El error anterior suele mostrarse cuando el usuario de almacenamiento de Azure Large Instance no tiene acceso al almacenamiento subyacente. Para permitir el acceso al almacenamiento con el usuario de almacenamiento proporcionado, ejecute el comando `ssh` y valide la comunicación con la plataforma de almacenamiento.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Ejemplo con la salida esperada:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>No se puede establecer la autenticidad del host "172.18.18.11 (172.18.18.11)".

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Solución:** No seleccione Yes (Sí). Asegúrese de que la dirección IP de almacenamiento sea correcta. Si el problema persiste, confirme la dirección IP de almacenamiento con el equipo de Microsoft Operations.

### <a name="azure-netapp-files"></a>Azure NetApp Files

El ejemplo siguiente procede de la ejecución de `azacsnap` en una máquina virtual con Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Solución:**

1. Compruebe la existencia del archivo de la entidad de servicio, `azureauth.json`, como se estableció en el archivo de configuración `azacsnap.json`.
1. Compruebe el archivo de registro (por ejemplo, `logs/azacsnap-test-azacsnap.log`) para ver si la entidad de servicio (`azureauth.json`) tiene el contenido correcto.  Ejemplo de registro a continuación:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Compruebe el archivo de registro (por ejemplo, `logs/azacsnap-test-azacsnap.log`) para ver si la entidad de servicio (`azureauth.json`) ha expirado. Ejemplo de registro a continuación:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Pasos siguientes

- [Sugerencias](azacsnap-tips.md)
