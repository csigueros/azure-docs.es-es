---
title: Restauración y copia de seguridad periódicas de Azure Service Fabric independiente
description: Use la característica de copia de seguridad periódica y restauración de Service Fabric para habilitar la copia de seguridad periódica de los datos de su aplicación.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 78a906e1e2261b0d117c7042b1ac387e2e98f045
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444571"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Restauración y copia de seguridad periódicas de Service Fabric independiente
> [!div class="op_single_selector"]
> * [Clústeres en Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clústeres independientes](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric es una plataforma de sistemas distribuidos que facilita el desarrollo y la administración de aplicaciones en la nube basadas en microservicios, confiables y distribuidas. Permite la ejecución de microservicios sin estado y con estado. Los servicios con estado pueden mantener el estado mutable y autoritativo tras la solicitud y la respuesta o una transacción completa. Si un servicio con estado deja de funcionar durante mucho tiempo o pierde información debido a un desastre, es posible que tenga que restaurarse a alguna copia de seguridad reciente de su estado con el fin de seguir proporcionando servicios una vez que vuelva a activarse.

Service Fabric replica el estado en varios nodos para asegurarse de que el servicio ofrece alta disponibilidad. Incluso si se produce un error en un nodo del clúster, el servicio sigue estando disponible. Sin embargo, en algunos casos, aún se desea que los datos del servicio sean de confianza frente a los errores mayores.
 
Por ejemplo, es posible que un servicio deba realizar copias de seguridad de los datos como medida de protección en los escenarios siguientes:
- En caso de que se produzca una pérdida permanente de todo un clúster de Service Fabric.
- En caso de que se produzca una pérdida permanente de la mayoría de las réplicas de una partición del servicio.
- Errores administrativos por los cuales el estado se elimina o daña accidentalmente. Por ejemplo, un administrador con suficientes privilegios elimina el servicio por error.
- Errores en el servicio que causan daños en los datos. Por ejemplo, esto puede suceder cuando una actualización de código de servicio empieza a escribir datos defectuosos en una colección confiable. En tal caso, es posible que el código y los datos se tengan que revertir a un estado anterior.
- Procesamiento de datos sin conexión. Podría ser conveniente procesar los datos para inteligencia empresarial sin conexión de forma independiente del servicio que los genera.

Service Fabric proporciona una API integrada para realizar [copias de seguridad y restauraciones](service-fabric-reliable-services-backup-restore.md) a un momento dado. Los desarrolladores de aplicaciones pueden usar estas API para hacer una copia de seguridad del estado del servicio periódicamente. Además, si los administradores de servicios quieren desencadenar una copia de seguridad desde el exterior del servicio en un momento específico, como antes de la actualización de la aplicación, los desarrolladores deben exponer la copia de seguridad (y la restauración) como una API del servicio. El mantenimiento de las copias de seguridad supone un costo adicional. Por ejemplo, es posible que quiera hacer cinco copias de seguridad incrementales cada media hora, seguidas de una copia de seguridad completa. Tras la copia de seguridad completa, puede eliminar las copias de seguridad incrementales anteriores. Este enfoque requiere un código adicional que conlleva costos adicionales durante el desarrollo de aplicaciones.

La realización de copias de seguridad de los datos de la aplicación de forma periódica es una necesidad básica para la administración de una aplicación distribuida y para la protección frente a la pérdida de datos o la pérdida prolongada de la disponibilidad del servicio. Service Fabric proporciona un servicio de restauración y copia de seguridad opcional, que le permite configurar la copia de seguridad periódica de Reliable Services con estado (incluidos los servicios de actor) sin tener que escribir ningún código adicional. También facilita la restauración de las copias de seguridad realizadas previamente. 

Service Fabric proporciona un conjunto de API para lograr la siguiente funcionalidad relacionada con la característica de restauración y copia de seguridad periódica:

- Programación de copias de seguridad periódicas de los servicios de confianza con estado y Reliable Actors con compatibilidad con la carga de la copia de seguridad en ubicaciones de almacenamiento (externas). Ubicaciones de almacenamiento admitidas
    - Azure Storage
    - Recurso compartido de archivos (local)
- Enumeración de las copias de seguridad
- Desencadenamiento de una copia de seguridad no planeada de una partición
- Restauración de una partición mediante la copia de seguridad anterior
- Suspensión temporal de las copias de seguridad
- Administración de la retención de copias de seguridad (próximamente)

## <a name="prerequisites"></a>Prerrequisitos
* Un clúster de Service Fabric con la versión 6.4 de Fabric o versiones posteriores. Consulte este [artículo](service-fabric-cluster-creation-for-windows-server.md) para ver los pasos para descargar el paquete necesario.
* Se requiere el certificado X.509 para el cifrado de secretos a fin de conectarse al almacenamiento y almacenar las copias de seguridad. Consulte este [artículo](service-fabric-windows-cluster-x509-security.md) para saber cómo adquirir o crear un certificado X.509 autofirmado.

* Aplicación con estado de confianza de Service Fabric compilada con la versión 3.0 del SDK de Service Fabric o una versión posterior. En el caso de las aplicaciones destinadas a .NET Core 2.0, la aplicación debe crearse con la versión 3.1 del SDK para Service Fabric o una versión posterior.
* Instale el módulo Microsoft.ServiceFabric.PowerShell.Http (versión preliminar) para realizar llamadas de configuración.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

> [!NOTE]
> Si la versión de PowerShellGet es inferior a la 1.6.0, deberá actualizar para agregar compatibilidad con la marca *-AllowPrerelease*:
>
> `Install-Module -Name PowerShellGet -Force`

* Asegúrese de que el clúster esté conectado mediante el comando `Connect-SFCluster` antes de realizar una solicitud de configuración con el módulo Microsoft.ServiceFabric.PowerShell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Habilitación del servicio de copia de seguridad y restauración
Primero debe habilitar el _servicio de copia de seguridad y restauración_ en el clúster. Obtenga la plantilla del clúster que desea implementar. Puede usar las [plantillas de ejemplo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Habilite el _servicio de copia de seguridad y restauración_ realizando los pasos siguientes:

1. Compruebe que `apiversion` está establecido en `10-2017` en el archivo de configuración del clúster y, si no es así, actualícelo como se muestra en el siguiente fragmento de código:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Ahora, habilite el _servicio de copia de seguridad y restauración_ mediante la adición de la siguiente sección `addonFeatures` en la sección `properties`, como se muestra en el siguiente fragmento de código: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configure el certificado X.509 para el cifrado de credenciales. Esto es importante para asegurarse de que las credenciales proporcionadas para conectarse al almacenamiento, en caso de haberlas, se cifran antes de continuar. Configure el certificado de cifrado mediante la adición de la siguiente sección `BackupRestoreService` en la sección `fabricSettings`, como se muestra en el siguiente fragmento de código: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            },
            {
                "name": "SecretEncryptionCertX509StoreName",
                "value": "My"
            }]
        }
        ...
    }
    ```
    > [!NOTE]
    > \[Thumbprint\] debe reemplazarse por la huella digital de certificado válida que se usará en el cifrado.
    >
4. Una vez actualizado el archivo de configuración del clúster con los cambios anteriores, aplíquelos y deje que se complete la actualización o la implementación. Cuando haya terminado, el _servicio de copia de seguridad y restauración_ empezará a ejecutarse en el clúster. El URI de este servicio es `fabric:/System/BackupRestoreService` y el servicio puede estar ubicado en la sección de servicio del sistema de Service Fabric Explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitación de la copia de seguridad periódica del servicio de confianza con estado y Reliable Actors
Vamos a examinar los pasos para habilitar la copia de seguridad periódica del servicio de confianza con estado y Reliable Actors. Con estos pasos se asume que:
- El clúster está configurado con el servicio de copia de seguridad y restauración.
- Se implementa un servicio de confianza con estado en el clúster. Para las finalidades de esta guía de inicio rápido, el URI de la aplicación es `fabric:/SampleApp` y el URI del servicio de confianza con estado que pertenece a esta aplicación es `fabric:/SampleApp/MyStatefulService`. Este servicio se implementa con una única partición y el id. de partición es `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Creación de una directiva de copia de seguridad

El primer paso consiste en crear una directiva de copia de seguridad que describa la programación de las copias de seguridad, el almacenamiento de destino para los datos de la copia de seguridad, el nombre de la directiva y el número máximo de copias de seguridad incrementales permitidas antes de desencadenar la copia de seguridad completa y la directiva de retención para el almacenamiento de copia de seguridad. 

Para el almacenamiento de copia de seguridad, cree un recurso compartido de archivos y conceda acceso de lectura y escritura a este recurso compartido de archivos para todas las máquinas de nodo de Service Fabric. En este ejemplo se asume que el recurso compartido denominado `BackupStore` existe en `StorageServer`.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usa el módulo Microsoft.ServiceFabric.PowerShell.Http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Llamada a REST mediante PowerShell

Ejecute el siguiente script de PowerShell para invocar la API REST necesaria para crear la nueva directiva.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Uso de Service Fabric Explorer

1. En Service Fabric Explorer, vaya a la pestaña Backups (Copias de seguridad) y seleccione Actions > Create Backup Policy (Acciones > Crear directiva de copia de seguridad).

    ![Crear directiva de copia de seguridad][6]

2. Rellene la información. En el caso de clústeres independientes, se debe seleccionar FileShare.

    ![Crear directiva de copia de seguridad: FileShare][7]

### <a name="enable-periodic-backup"></a>Habilitación de la copia de seguridad periódica
Después de definir la directiva para satisfacer los requisitos de protección de datos de la aplicación, la directiva de copia de seguridad debe asociarse a la aplicación. En función de los requisitos, la directiva de copia de seguridad puede asociarse a una aplicación, un servicio o una partición.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usa el módulo Microsoft.ServiceFabric.PowerShell.Http

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Llamada a REST mediante PowerShell
Ejecute el siguiente script de PowerShell para invocar la API REST necesaria para asociar la directiva de copia de seguridad con el nombre `BackupPolicy1` que se ha creado en el paso anterior con la aplicación `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Uso de Service Fabric Explorer

1. Seleccione una aplicación y vaya a las acciones. Haga clic en "Habilitar o actualizar la copia de seguridad de aplicaciones".

    ![Habilitar la copia de seguridad de aplicaciones][3] 

2. Por último, seleccione la directiva que quiera y seleccione *Habilitar copia de seguridad*.

    ![Seleccionar la directiva][4]

### <a name="verify-that-periodic-backups-are-working"></a>Comprobación del funcionamiento de las copias de seguridad periódicas

Después de habilitar la copia de seguridad para la aplicación, empezará a realizarse la copia de seguridad periódicamente de todas las particiones que pertenecen a los servicios de confianza con estado y Reliable Actors en la aplicación según la directiva de copia de seguridad asociada.

![Evento de estado de copia de seguridad de la partición][0]

### <a name="list-backups"></a>Enumeración de las copias de seguridad

Las copias de seguridad asociadas a todas las particiones que pertenecen a los servicios de confianza con estado y Reliable Actors de la aplicación pueden enumerarse con _GetBackups_ API. En función de los requisitos, las copias de seguridad pueden enumerarse para una aplicación, un servicio o una partición.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usa el módulo Microsoft.ServiceFabric.PowerShell.Http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Llamada a REST mediante PowerShell

Ejecute el siguiente script de PowerShell para invocar la API de HTTP a fin de enumerar las copias de seguridad creadas para todas las particiones dentro de la aplicación `SampleApp`.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Salida de ejemplo de la ejecución anterior:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

#### <a name="using-service-fabric-explorer"></a>Uso de Service Fabric Explorer

Para ver las copias de seguridad en Service Fabric Explorer, vaya a una partición y seleccione la pestaña Copias de seguridad.

![Enumeración de las copias de seguridad][5]

## <a name="limitation-caveats"></a>Limitaciones o advertencias
- Los cmdlets de PowerShell para Service Fabric están en modo de versión preliminar.
- Sin compatibilidad con los clústeres de Service Fabric en Linux.

## <a name="next-steps"></a>Pasos siguientes
- [Información sobre la configuración de la copia de seguridad periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referencia de la API REST de restauración de copias de seguridad](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
