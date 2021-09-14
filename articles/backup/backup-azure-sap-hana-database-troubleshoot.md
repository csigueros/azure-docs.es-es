---
title: Solución de problemas de errores de copia de seguridad de bases de datos de SAP HANA
description: En este artículo se describe cómo se solucionan los errores comunes que pueden producirse al usar Azure Backup para realizar copias de seguridad de bases de datos de SAP HANA.
ms.topic: troubleshooting
ms.date: 05/31/2021
ms.openlocfilehash: 1af52116a08344d36a9364ac9e3b4f468e0abb83
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451968"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Solución de problemas al realizar copias de seguridad de bases de datos de SAP HANA en Azure

En este artículo se proporciona información para la solución de problemas al realizar copias de seguridad de bases de datos de SAP HANA en Azure Virtual Machines. Para más información sobre los escenarios de copia de seguridad de SAP HANA actualmente compatibles, consulte [Compatibilidad con los escenarios](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Requisitos previos y permisos

Consulte las secciones sobre los [requisitos previos](tutorial-backup-sap-hana-db.md#prerequisites) y [Qué hace el script de registro previo](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) antes de configurar las copias de seguridad.

## <a name="common-user-errors"></a>Errores de usuario comunes

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Mensaje de error**      | `Azure Backup does not have required role  privileges to carry out Backup and Restore operations`    |
| ---------------------- | ------------------------------------------------------------ |
| **Causas posibles:**    | Se producirá este error en todas las operaciones cuando el usuario de copia de seguridad (AZUREWLBACKUPHANAUSER) no tenga asignado el rol **SAP_INTERNAL_HANA_SUPPORT** o se haya sobrescrito el rol.                          |
| **Acción recomendada** | Descargue y ejecute el [script de registro previo](https://aka.ms/scriptforpermsonhana) en la instancia de SAP HANA o asigne manualmente el rol **SAP_INTERNAL_HANA_SUPPORT** al usuario de copia de seguridad (AZUREWLBACKUPHANAUSER).<br><br>**Nota**<br><br>Si usa HANA 2.0 SPS04 Rev 46 y versiones posteriores, este error no se produce porque el uso del rol **SAP_INTERNAL_HANA_SUPPORT** está en desuso en estas versiones de HANA. |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| **Mensaje de error**      | `Failed to connect to HANA system`                        |
| ------------------ | ------------------------------------------------------------ |
| **Causas posibles:**    | <ul><li>Error en la conexión a la instancia de HANA</li><li>Base de datos del sistema sin conexión</li><li>La base de datos del inquilino está sin conexión</li><li>El usuario de copia de seguridad (AZUREWLBACKUPHANAUSER) no tiene permisos o privilegios suficientes.</li></ul> |
| **Acción recomendada** | Compruebe que el sistema está en funcionamiento. Si las bases de datos están en funcionamiento, asegúrese de que los permisos necesarios estén establecidos mediante la descarga y ejecución del [script de registro previo](https://aka.ms/scriptforpermsonhana) en la instancia de SAP HANA. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| **Mensaje de error**      | `The specified SAP HANA instance is either invalid or can't be found`  |
| ------------------ | ------------------------------------------------------------ |
| **Causas posibles:**    | <ul><li>La instancia de SAP HANA especificada no es válida o no se puede encontrar.</li><li>No se puede realizar una copia de seguridad de varias instancias de SAP HANA en una máquina virtual de Azure única.</li></ul> |
| **Acción recomendada** | <ul><li>Asegúrese de que solo se ejecuta una instancia de HANA en la máquina virtual de Azure.</li><li>Ejecute el script desde el panel Discover DB (Detectar base de datos) (también puede encontrarlo [aquí](https://aka.ms/scriptforpermsonhana)) con la instancia correcta de SAP HANA para resolver el problema.</li></ul> |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| **Mensaje de error**      | `Backup log chain is broken`                                    |
| ------------------ | ------------------------------------------------------------ |
| **Causas posibles:**    | La interrupción de la cadena de registro de LSN de HANA se puede desencadenar por varias razones, entre las que se incluyen:<ul><li>Error en la llamada de Azure Storage para confirmar la copia de seguridad.</li><li>La base de datos del inquilino está sin conexión.</li><li>La actualización de la extensión ha finalizado un trabajo de copia de seguridad en curso.</li><li>No se puede conectar a Azure Storage durante la copia de seguridad.</li><li>SAP HANA ha revertido una transacción en el proceso de copia de seguridad.</li><li>Se ha completado una copia de seguridad, pero el catálogo aún no se ha actualizado correctamente en el sistema HANA.</li><li>Error de copia de seguridad desde la perspectiva de Azure Backup, pero correcta desde la perspectiva de HANA: es posible que el destino de la copia de seguridad de registros o del catálogo se haya actualizado desde backint al sistema de archivos o que se haya cambiado el ejecutable backint.</li></ul> |
| **Acción recomendada** | Para resolver este problema, Azure Backup desencadena una copia de seguridad completa de recuperación automática. Mientras esta copia de seguridad de recuperación automática está en curso, todas las copias de seguridad de registros desencadenadas por HANA producirán el error **OperationCancelledBecauseConflictingAutohealOperationRunningUserError**. Una vez completada la copia de seguridad completa de la recuperación automática, los registros y todas las demás copias de seguridad comenzarán a funcionar según lo previsto.<br>Si no ve una copia de seguridad completa de recuperación automática desencadenada o una copia de seguridad correcta (completa, diferencial o incremental) en un plazo de 24 horas, póngase en contacto con el soporte técnico de Microsoft.</br> |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| **Mensaje de error**      | `SDC to MDC upgrade detected.`                                   |
| ------------------ | ------------------------------------------------------------ |
| **Causas posibles:**    | Cuando se actualiza un sistema SDC a MDC, se produce este error en las copias de seguridad. |
| **Acción recomendada** | Para resolver el problema, consulte [Actualización de SDC a MDC](#sdc-to-mdc-upgrade-with-a-change-in-sid). |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| **Mensaje de error**      | `Backups will fail with this error when the Backint Configuration is incorrectly updated.`                       |
| ------------------ | ------------------------------------------------------------ |
| **Causas posibles:**    | El cliente modifica o actualiza la configuración de backint actualizada durante el flujo Configurar protección de Azure Backup. |
| **Acción recomendada** | Compruebe si se establecieron estos parámetros (BackInt):<br><ul><li>[catalog_backup_using_backint:true]</li><li>[enable_accumulated_catalog_backup:false]</li><li>[parallel_data_backup_backint_channels:1]</li><li>[log_backup_timeout_s:900)]</li><li>[backint_response_timeout:7200]</li></ul>Si los parámetros basados en backint están presentes en el nivel HOST, quítelos. Sin embargo, si los parámetros no están presentes en el nivel HOST, pero se han modificado manualmente en un nivel de base de datos, asegúrese de que los valores de nivel de base de datos están establecidos arriba. O bien, ejecute la [detención de la protección y conservación de los datos de copia de seguridad](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) en Azure Portal y, después, seleccione Reanudar copia de seguridad. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|**Mensaje de error**  | `The source and target systems for restore are incompatible.`  |
|---------|---------|
|**Causas posibles:**   | El flujo de restauración produce este error cuando las bases de datos de HANA de origen y de destino y los sistemas no son compatibles. |
|Acción recomendada   |   Asegúrese de que el escenario de restauración no está incluido en la siguiente lista de posibles restauraciones incompatibles:<br> **Caso 1:** No se puede cambiar el nombre de SYSTEMDB durante la restauración.<br>**Caso 2:** Origen SDC y destino MDC: La base de datos de origen no se puede restaurar como SYSTEMDB o base de datos de inquilino en el destino. <br> **Caso 3:** Origen MDC y destino SDC: La base de datos de origen (SYSTEMDB o base de datos de inquilino) no se puede restaurar en el destino.<br>Para más información, consulte la nota **1642148** de [SAP Support Launchpad](https://launchpad.support.sap.com). |

### <a name="usererrorhanapodoesnotexist"></a>UserErrorHANAPODoesNotExist

**Mensaje de error** | `Database configured for backup does not exist.`
--------- | --------------------------------
**Causas posibles:** | Si se elimina una base de datos que se ha configurado para la copia de seguridad, se producirá un error en todas las copias de seguridad programadas y ad hoc de esta base de datos.
**Acción recomendada** | Compruebe si se ha eliminado la base de datos. Vuelva a crear la base de datos o [detenga la protección](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (con o sin conservar los datos) de la base de datos.

### <a name="usererrorinsufficientprivilegeofdatabaseuser"></a>UserErrorInsufficientPrivilegeOfDatabaseUser

**Mensaje de error** |    `Azure Backup does not have enough privileges to carry out Backup and Restore operations.`
---------- | ---------
**Causas posibles:** | El usuario de copia de seguridad (AZUREWLBACKUPHANAUSER) creado por el script de registro previo no tiene uno o varios de los roles siguientes asignados:<ul><li>Para MDC, DATABASE ADMIN y BACKUP ADMIN (para HANA 2.0 SPS05 y versiones posteriores) para crear nuevas bases de datos durante la restauración.</li><li>Para SDC, BACKUP ADMIN para crear nuevas bases de datos durante la restauración.</li><li>CATALOG READ para leer el catálogo de copia de seguridad.</li><li>SAP_INTERNAL_HANA_SUPPORT para acceder a algunas tablas privadas. Solo es necesario para las versiones de SDC y MDC anteriores a HANA 2.0 SPS04 rev. 46. Esto no es necesario para HANA 2.0 SPS04 rev. 46 y versiones posteriores, ya que se obtiene la información necesaria de las tablas públicas con la corrección del equipo de HANA.</li></ul>
**Acción recomendada** | Para resolver el problema, agregue los roles y permisos necesarios manualmente al usuario de copia de seguridad (AZUREWLBACKUPHANAUSER) o descargue y vuelva a ejecutar el script de registro previo en la [instancia de SAP HANA](https://aka.ms/scriptforpermsonhana).

### <a name="usererrordatabaseuserpasswordexpired"></a>UserErrorDatabaseUserPasswordExpired

**Mensaje de error** | `Database/Backup user's password expired.`
----------- | -----------
**Causas posibles:** | El usuario de base de datos o copia de seguridad creado por el script de registro previo no establece la expiración de la contraseña. Sin embargo, si se modificó, es posible que vea este error.
**Acción recomendada** | Descargue y vuelva a ejecutar el [script de registro previo](https://aka.ms/scriptforpermsonhana) en la instancia de SAP HANA para resolver el problema.

### <a name="usererrorinconsistentssfs"></a>UserErrorInconsistentSSFS

**Mensaje de error** | `SAP HANA error`
------------ | ----------
**Causas posibles:** | Se recibió un error de sistema de archivos de almacenamiento seguro (SSFS) incoherente del motor de SAP HANA.
**Acción recomendada** | Trabaje con el equipo de SAP HANA para corregir este problema. Para más información, consulte la nota de SAP **0002097613**.

### <a name="usererrorcannotconnecttoazureactivedirectoryservice"></a>UserErrorCannotConnectToAzureActiveDirectoryService

**Mensaje de error** | `Unable to connect to the AAD service from the HANA system.`
--------- | --------
**Causas posibles:** | La configuración de firewall o proxy como cuenta de servicio de complemento de la extensión de Backup no permite la conexión de salida a AAD.
**Acción recomendada** | Corrija la configuración del firewall o proxy para que la conexión de salida a AAD sea correcta.

### <a name="usererrormisconfiguredsslcastore"></a>UserErrorMisConfiguredSslCaStore

**Mensaje de error** | `Misconfigured CA store`
-------- | -------
**Causas posibles:** | El proceso de host del complemento de la extensión de copia de seguridad no puede acceder al almacén de la entidad de certificación raíz (en _/var/lib/ca-certificates/ca-bundle.pem_ en el caso de SLES).
**Acción recomendada** | Corrija el problema del almacén de CA mediante `chmod o+r` para restaurar el permiso original.  A continuación, reinicie el servicio de host del complemento para que las copias de seguridad y restauraciones se realicen correctamente.

### <a name="usererrorbackupfailedasremedialbackupinprogress"></a>UserErrorBackupFailedAsRemedialBackupInProgress

**Mensaje de error** | `Remedial Backup in progress.`
---------- | -------
**Causas posibles:** | Azure Backup desencadena una copia de seguridad completa de corrección para controlar la interrupción en la cadena de registros del LSN. Mientras esta corrección completa está en curso, las copias de seguridad (completa, diferencial o incremental) desencadenadas a través del portal o la CLI producirán este error.
**Acción recomendada** | Espere a que la copia de seguridad completa correctiva finalice correctamente antes de desencadenar otra copia de seguridad.

### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

**Mensaje de error** | `Conflicting operation in progress.`
----------- | -------------
**Causas posibles:** | Se desencadena una copia de seguridad completa, diferencial o incremental a través del portal, la CLI o los clientes nativos de HANA, mientras que otra copia de seguridad completa, diferencial o incremental ya está en curso.
**Acción recomendada** | Espere a que se complete el trabajo de copia de seguridad activo antes de desencadenar una nueva copia de seguridad completa o diferencial.

### <a name="operationcancelledbecauseconflictingautohealoperationrunning-usererror"></a>OperationCancelledBecauseConflictingAutohealOperationRunning UserError

**Mensaje de error** | `Auto-heal Full backup in progress.`
------- | -------
**Causas posibles:** | Azure Backup desencadena una copia de seguridad completa de recuperación automática para resolver **UserErrorHANALSNValidationFailure**. Mientras esta copia de seguridad de recuperación automática está en curso, todas las copias de seguridad del registro desencadenadas por HANA producirán el error **OperationCancelledBecauseConflictingAutohealOperationRunningUserError**.<br>Una vez completada la copia de seguridad completa de la recuperación automática, los registros y todas las demás copias de seguridad comenzarán a funcionar según lo previsto.</br>
**Acción recomendada** | Espere a que se complete la copia de seguridad completa de recuperación automática antes de desencadenar una nueva copia de seguridad completa o diferencial.

### <a name="usererrorhanaprescriptnotrun"></a>UserErrorHanaPreScriptNotRun

**Mensaje de error** | `Pre-registration script not run.`
--------- | --------
**Causas posibles:** | No se ejecutó el script de registro previo de SAP HANA para configurar el entorno.
**Acción recomendada** | Descargue y ejecute el [script de registro previo](https://aka.ms/scriptforpermsonhana) en la instancia de SAP HANA.


### <a name="usererrortargetpoexistsoverwritenotspecified"></a>UserErrorTargetPOExistsOverwriteNotSpecified

**Mensaje de error** | `Target database cannot be overwritten for Restore.`
------- | -------
**Causas posibles:** | La base de datos de destino existe, pero no se puede sobrescribir. No se ha establecido Forzar la sobrescritura en el flujo de restauración en el portal o la CLI.
**Acción recomendada** | Restaure la base de datos con la opción Forzar sobrescritura seleccionada o restaure en otra base de datos de destino.

### <a name="usererrorrecoverysysscriptfailedtotriggerrestore"></a>UserErrorRecoverySysScriptFailedToTriggerRestore

**Mensaje de error** | `RecoverySys.py could not be run successfully to restore System DB.`
-------- | ---------
**Causas posibles:** | Las posibles causas de que se produzca un error en la restauración de la base de datos del sistema son:<ul><li>Azure Backup no puede encontrar **Recoverysys.py** en la máquina de HANA. Esto sucede cuando el entorno de HANA no está configurado correctamente.</li><li>**Recoverysys.py** está presente, pero al desencadenar este script no se pudo invocar a HANA para realizar la restauración.</li><li>Recoverysys.py ha invocado correctamente a HANA para realizar la restauración, pero HANA no ha podido realizarla.</li></ul>
**Acción recomendada** | <ul><li>Para el problema 1, trabaje con el equipo de SAP HANA para corregir el problema.</li><li>Para 2 y 3, vea el seguimiento del registro mediante la ejecución del comando HDSetting.sh en el símbolo del sistema sid-adm. Por ejemplo, _/usr/sap/SID/HDB00/HDBSetting.sh_.</li></ul>Comparta los resultados con el equipo de SAP HANA para solucionar el problema.

### <a name="usererrordbnamenotincorrectformat"></a>UserErrorDBNameNotInCorrectFormat

**Mensaje de error** | `Restored database name not in correct format.`
--------- | --------
**Causas posibles:** | El nombre de la base de datos restaurada que ha proporcionado no tiene el formato aceptable o esperado.
**Acción recomendada** | Asegúrese de que el nombre de la base de datos restaurada comienza por una letra y no debe contener ningún símbolo que no sean dígitos o un carácter de subrayado.<br>Puede contener un máximo de 127 caracteres y no comenzar por "\_SYS_\".

### <a name="usererrordefaultsidadmdirectorychanged"></a>UserErrorDefaultSidAdmDirectoryChanged

**Mensaje de error** | `Default sid-adm directory changed.`
------- | -------
**Causas posibles:** | Se cambió el directorio **sid-adm** predeterminado y **HDBSetting.sh** no está disponible en este directorio predeterminado.
**Acción recomendada** | Si HXE es el SID, asegúrese de que la variable de entorno HOME esté establecida en _/usr/sap/HXE/home_ como usuario **sid-adm**.

### <a name="usererrorhdbsettingsscriptnotfound"></a>UserErrorHDBsettingsScriptNotFound

**Mensaje de error** | `HDBSetting.sh file cannot be found.`
--------- | -------
**Causas posibles:** | Se produjo un error al restaurar las bases de datos del sistema porque el entorno del usuario **&lt;sid&gt;adm** no encontró el archivo **HDBsettings.sh** para desencadenar la restauración.
**Acción recomendada** | Trabaje con el equipo de SAP HANA para corregir este problema.<br><br>Si HXE es el SID, asegúrese de que la variable de entorno HOME esté establecida en _/usr/sap/HXE/home_ como usuario **sid-adm**.

## <a name="restore-checks"></a>Comprobaciones de restauración

### <a name="single-container-database-sdc-restore"></a>Restauración de una base de datos de contenedor único (SDC)

Ocúpese de las entradas mientras se realiza la restauración de una base de datos de contenedor único (SDC) para HANA a otra máquina de SDC. El nombre de la base de datos debe estar en minúscula con "sdc" anexado entre corchetes. La instancia de HANA se mostrará en mayúsculas.

Supongamos que hay una copia de seguridad de una instancia "H21" de HANA de SDC. En la página de elementos de copia de seguridad aparecerá el nombre del elemento de copia de seguridad como **"h21(sdc)"** . Si intenta restaurar esta base de datos en otra SDC de destino, por ejemplo, H11, es necesario proporcionar las entradas siguientes.

![Nombre de base de datos SDC restaurada](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenga en cuenta los siguientes puntos:

- De manera predeterminada, el nombre de la base de datos restaurada se rellenará con el nombre del elemento de copia de seguridad. En este caso, h21(sdc).
- Si selecciona H11 como destino, el nombre de la base de datos restaurada no cambiará de manera automática. **Se debe editar para cambiarlo a h11(sdc)** . Con respecto a SDC, el nombre de la base de datos restaurada será el identificador de la instancia de destino en minúsculas más "sdc" entre corchetes.
- Dado que SDC solo puede tener una base de datos única, también debe seleccionar la casilla para permitir reemplazar los datos existentes de la base de datos por los datos del punto de recuperación.
- Linux distingue mayúsculas de minúsculas. De este modo, tiene que tener cuidado y respetar su uso.

### <a name="multiple-container-database-mdc-restore"></a>Restauración de base de datos de varios contenedores (MDC)

En las bases de datos de varios contenedores para HANA, la configuración estándar es SYSTEMDB + 1 o más bases de datos de inquilino. Restaurar toda una instancia de SAP HANA significa restaurar tanto SYSTEMDB como las bases de datos de inquilino. Primero se restaura SYSTEMDB y, luego, la base de datos de inquilino. Básicamente, restaurar la base de datos del sistema significa reemplazar la información del sistema del destino seleccionado. Esta restauración también reemplaza la información relacionada con BackInt en la instancia de destino. Así pues, una vez que la base de datos del sistema se restaura en una instancia de destino, vuelva a ejecutar el script de registro previo. Solo entonces las restauraciones de bases de datos de inquilino subsiguientes se realizarán correctamente.

## <a name="back-up-a-replicated-vm"></a>Copia de seguridad de una VM replicada

### <a name="scenario-1"></a>Escenario 1

La máquina virtual original se replicó mediante Azure Site Recovery o una copia de seguridad de la máquina virtual de Azure. La nueva máquina virtual se creó para simular la máquina virtual anterior. Es decir, la configuración es exactamente la misma. (Esto se debe a que la máquina virtual original se eliminó y la restauración se realizó desde la copia de seguridad de la máquina virtual o Azure Site Recovery).

Este escenario podría incluir dos posibles casos. Obtenga información sobre cómo realizar una copia de seguridad de la máquina virtual replicada en ambos casos:

1. La máquina virtual recién creada tiene el mismo nombre y se encuentra en el mismo grupo de recursos y en la misma suscripción que la máquina virtual que se eliminó.

    - La extensión ya está presente en la máquina virtual, pero ninguno de los servicios puede verla.
    - Ejecute el script de registro previo.
    - Vuelva a registrar la extensión para la misma máquina en Azure Portal (**Copia de seguridad** -> **Ver detalles** -> seleccione la máquina virtual de Azure pertinente -> Volver a registrar).
    - Se debe comenzar a crear correctamente la copia de seguridad de las bases de datos que ya tienen una copia de seguridad (de la máquina virtual eliminada).

2. La máquina virtual recién creada tiene:

    - un nombre diferente al de la máquina virtual eliminada.
    - el mismo nombre que la máquina virtual eliminada, pero se encuentra en otro grupo de recursos o suscripción (en comparación con la máquina virtual eliminada).

    Si es este el caso, siga estos pasos:

    - La extensión ya está presente en la máquina virtual, pero ninguno de los servicios puede verla.
    - Ejecute el script de registro previo.
    - Si detecta y protege las bases de datos nuevas, comenzarán a mostrarse bases de datos activas duplicadas en el portal. Para evitar esta situación, [detenga la protección con la conservación de datos](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para las bases de datos anteriores. Después, continúe con los pasos restantes.
    - Detecte las bases de datos para habilitar la copia de seguridad.
    - Habilite las copias de seguridad en estas bases de datos.
    - Las bases de datos existentes con copia de seguridad (de la máquina virtual eliminada) seguirán almacenadas en el almacén (y sus copias de seguridad se conservarán según la directiva).

### <a name="scenario-2"></a>Escenario 2

La máquina virtual original se replicó mediante Azure Site Recovery o una copia de seguridad de la máquina virtual de Azure. La nueva máquina virtual se creó a partir del contenido, con el fin de usarse como plantilla. Se trata de una nueva máquina virtual con un nuevo SID.

Siga estos pasos para habilitar las copias de seguridad en la máquina virtual nueva:

- La extensión ya está presente en la máquina virtual, pero ninguno de los servicios puede verla.
- Ejecute el script de registro previo. En función del SID de la nueva máquina virtual, pueden surgir dos escenarios:
  - La máquina virtual original y la nueva tienen el mismo SID. El script de registro previo se ejecutará correctamente.
  - La máquina virtual original y la nueva tienen SID diferentes. El script de registro previo generará un error. Póngase en contacto con el soporte técnico para obtener ayuda en este caso.
- Detecte las bases de datos de las que quiere realizar copias de seguridad.
- Habilite las copias de seguridad en estas bases de datos.

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Actualización de la versión de SDC o MDC en la misma máquina virtual

Las actualizaciones del sistema operativo, el cambio de versión de SDC o el cambio de versión de MDC que no ocasionan un cambio de SID se pueden administrar de la siguiente manera:

- Asegúrese de que la nueva versión del sistema operativo, de SDC o de MDC [sea compatible actualmente con Azure Backup](sap-hana-backup-support-matrix.md#scenario-support).
- [Detenga la protección de datos con la conservación de datos](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para la base de datos.
- Realice la actualización.
- Vuelva a ejecutar el script de registro previo. A menudo, el proceso de actualización puede eliminar los [roles necesarios](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). La ejecución del script de registro previo le ayudará a comprobar todos los roles necesarios.
- Reanude la protección de la base de datos nuevamente.

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Actualización de SDC a MDC sin cambios en el SID

Las actualizaciones de SDC a MDC que no provocan un cambio de SID se pueden administrar de la siguiente manera:

- Asegúrese de que la nueva versión de MDC [sea compatible actualmente con Azure Backup](sap-hana-backup-support-matrix.md#scenario-support).
- [Detenga la protección con la conservación de datos](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para la base de datos SDC anterior.
- Realice la actualización. Después de la finalización, el sistema HANA es ahora MDC con una base de datos de sistema y bases de datos de inquilino.
- Vuelva a ejecutar el [script de registro previo](https://aka.ms/scriptforpermsonhana).
- Vuelva a registrar la extensión para la misma máquina en Azure Portal (**Copia de seguridad** -> **Ver detalles** -> seleccione la máquina virtual de Azure pertinente -> Volver a registrar).
- Seleccione **Volver a detectar bases de datos** para la misma VM. Esta acción debería mostrar las nuevas bases de datos en el paso 3 como SYSTEMDB y base de datos de inquilino, no SDC.
- La base de datos SDC anterior seguirá existiendo en el almacén y se conservarán los datos de copia de seguridad antiguos según la directiva.
- Configure la copia de seguridad de estas bases de datos.

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Actualización de SDC a MDC con un cambios de SID

Las actualizaciones de SDC a MDC que provocan un cambio de SID se pueden administrar de la siguiente manera:

- Asegúrese de que la nueva versión de MDC [sea compatible actualmente con Azure Backup](sap-hana-backup-support-matrix.md#scenario-support).
- **Detenga la protección con la conservación de datos** para la base de datos SDC anterior.
- Realice la actualización. Después de la finalización, el sistema HANA es ahora MDC con una base de datos de sistema y bases de datos de inquilino.
- Vuelva a ejecutar el [script de registro previo](https://aka.ms/scriptforpermsonhana) con los detalles correctos (nuevo SID y MDC). Debido al cambio en el SID, puede que surjan problemas con la ejecución correcta del script. Póngase en contacto con el soporte técnico de Azure Backup si tiene problemas.
- Vuelva a registrar la extensión para la misma máquina en Azure Portal (**Copia de seguridad** -> **Ver detalles** -> seleccione la máquina virtual de Azure pertinente -> Volver a registrar).
- Seleccione **Volver a detectar bases de datos** para la misma VM. Esta acción debería mostrar las nuevas bases de datos en el paso 3 como SYSTEMDB y base de datos de inquilino, no SDC.
- La base de datos SDC anterior seguirá existiendo en el almacén y se conservarán los datos de copia de seguridad antiguos según la directiva.
- Configure la copia de seguridad de estas bases de datos.

## <a name="re-registration-failures"></a>Errores de repetición del registro

Compruebe uno o varios de los siguientes síntomas antes de desencadenar la operación de repetición del registro:

- Se producen errores en todas las operaciones (tales como copia de seguridad, restauración y configuración de copia de seguridad) en la máquina virtual con uno de los códigos de error siguientes: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Si el área **Estado de copia de seguridad** del elemento de copia de seguridad se muestra como **no accesible**, descarte todas las demás causas que podrían dar lugar al mismo estado:

  - Falta de permiso para realizar operaciones relacionadas con la copia de seguridad en la máquina virtual
  - La máquina virtual está apagada, por lo que no se pueden realizar copias de seguridad.
  - Problemas de red

Estos síntomas pueden surgir por uno o varios de los siguientes motivos:

- Se ha eliminado o desinstalado una extensión del portal.
- Se ha restaurado la máquina virtual retrocediendo en el tiempo a través de la restauración del disco o discos en contexto.
- Se ha apagado la máquina virtual durante un largo período, por lo que la configuración de la extensión en ella ha caducado.
- Se ha eliminado la máquina virtual, y se ha creado otra con el mismo nombre y en el mismo grupo de recursos que la máquina virtual eliminada.

En los escenarios anteriores, se recomienda desencadenar una operación de nuevo registro en la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

- Revise las [preguntas frecuentes](./sap-hana-faq-backup-azure-vm.yml) sobre cómo hacer copias de seguridad de las bases de datos SAP HANA en máquinas virtuales de Azure.
