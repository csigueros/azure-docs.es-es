---
title: Migración de las herramientas de administración de Azure desde Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar las herramientas de administración de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 06/22/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 157dded56e3323ed6389f84bd173ba435e8bdc5b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "117029069"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>Migración de los recursos de las herramientas de administración en Azure global

[!INCLUDE [closure info](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar las herramientas de administración de Azure desde Azure Alemania a Azure global.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager puede ayudarle a realizar una migración perfecta. Sin embargo, no puede migrar los perfiles de Traffic Manager que cree en Azure Alemania en Azure global. (Durante una migración, se migran los puntos de conexión de Traffic Manager al entorno de destino, por lo que se necesita actualizar el perfil de Traffic Manager de todas formas).

Puede definir puntos de conexión adicionales en el entorno de destino mediante Traffic Manager mientras se ejecuta en el entorno de origen. Cuando Traffic Manager se ejecuta en el nuevo entorno, todavía puede definir los puntos de conexión que aún no han migrado en el entorno de origen. Este escenario se conoce como [escenario Blue-Green](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/). Este escenario implica los pasos siguientes:

1. Cree un nuevo perfil de Traffic Manager en Azure global.
1. Defina los puntos de conexión en Azure Alemania.
1. Cambie el registro CNAME de DNS en el nuevo perfil de Traffic Manager.
1. Desactive el perfil de Traffic Manager antiguo.
1. Migre y configure los puntos de conexión. Para cada punto de conexión en Azure Alemania:
   1. Migre el punto de conexión a Azure global.
   1. Cambie el perfil de Traffic Manager para usar el nuevo punto de conexión.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Traffic Manager](../traffic-manager/index.yml).
- Revise la [introducción a Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Aprenda a [crear un perfil de Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Obtenga información sobre el [escenario Blue-Green](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/).

## <a name="azure-backup"></a>Azure Backup

El servicio Azure Backup proporciona soluciones sencillas, seguras y rentables tanto para realizar copias de seguridad de datos de la nube de Microsoft Azure como para recuperarlos. El movimiento de datos de copia de seguridad está ahora habilitado desde las regiones de Centro de Alemania (GEC) y Nordeste de Alemania (GNE) a Centro-oeste de Alemania (GWC) mediante los cmdlets de PowerShell.

### <a name="prerequisite-for-moving-hybrid-workloads"></a>Requisito previo para mover cargas de trabajo híbridas

Una vez que se inicia la operación de traslado, las copias de seguridad se detienen en los almacenes existentes. Por lo tanto, es importante proteger los datos en un nuevo almacén de GWC para cargas de trabajo híbridas (servidor Data Protection Manager [DPM], Azure Backup Server [MABS] o Microsoft Azure Recovery Services [MARS]) antes de empezar a mover los datos de copia de seguridad desde las regiones.
Para empezar a proteger en un almacén nuevo:

1. Cree un almacén (VaultN) en GWC.
1. Vuelva a registrar el servidor DPM/MABS/agente de MARS en VaultN.
1. Asigne una directiva y empiece a realizar copias de seguridad.

La copia de seguridad inicial será una copia completa seguida de copias de seguridad incrementales.

>[!Important]
>- Antes de iniciar la operación de traslado de datos de copia de seguridad, asegúrese de que se ha completado la primera copia de seguridad total en VaultN.
>- Para DPM/MABS, conserve la frase de contraseña del almacén original en una ubicación segura, ya que la necesitará para restaurar datos desde el almacén de destino. No se puede realizar la restauración desde el almacén de origen sin la frase de contraseña original.

### <a name="step-1-download-the-resources"></a>Paso 1: Descargar los recursos

Descargue e instale los recursos necesarios.

1. [Descargue](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3) la última versión de PowerShell (PowerShell 7).
1. Use la versión 4.2.0 del módulo Az.RecoveryServices disponible en Azure Cloud Shell.
1. [Actualice](https://aka.ms/azurebackup_agent) todos los agentes de MARS a la última versión.
1. Valide su frase de contraseña. Si necesita regenerarla, siga los [pasos de validación](https://support.microsoft.com/topic/mandatory-update-for-azure-backup-for-microsoft-azure-recovery-services-agent-411e371c-aace-e134-de6b-bf9fda48026e#section-3).

### <a name="step-2-create-a-target-vault-in-gwc"></a>Paso 2: Crear un almacén de destino en GWC

Cree un almacén de destino (Almacén 2) en GWC. Para aprender cómo crear un almacén, consulte [Creación y configuración de un almacén de Recovery Services](../backup/backup-create-rs-vault.md).

>[!Note]
>- Asegúrese de que el almacén no tenga elementos protegidos.
>- Asegúrese de que el almacén de destino tenga la redundancia necesaria: almacenamiento con redundancia local (LRS) o almacenamiento con redundancia geográfica (GRS).

### <a name="step-3---use-powershell-to-trigger-backup-data-move"></a>Paso 3: Usar PowerShell para desencadenar el movimiento de los datos de copia de seguridad

#### <a name="get-the-source-vault-from-gne-or-gec"></a>Obtener el almacén de origen de GNE o GEC

Ejecute estos cmdlets:

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$srcVault = Get-AzRecoveryServicesVault -name “srcVault” -ResourceGroupName “TestSourceRG”`

>[!Note]
>- `srcVault` = Almacén de origen
>- `TestSourceRG` = Grupo de recursos de origen

#### <a name="get-the-target-vault-in-gwc"></a>Obtener el almacén de destino en GWC

Ejecute estos cmdlets:

1. `Connect-AzAccount`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$trgVault = Get-AzRecoveryServicesVault -name “targetVault” -ResourceGroupName “TestTargetRG”`

>[!Note]
>- `targetVault` = Almacén de destino
>- `TestTargetRG` = Grupo de recursos de prueba

#### <a name="perform-validation"></a>Realizar la validación
 
Ejecute estos cmdlets:

1. `$validated = $false`
1. `$validated = Test-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault`

#### <a name="initializeprepare-ds-move"></a>Inicializar o preparar el movimiento de DS

Ejecute estos cmdlets:

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -SubscriptionName $srcSub`
1. ```azurepowershell
   if($validated) {
           $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault
                       }
   ```
1. `$corr`

#### <a name="trigger-ds-move"></a>Desencadenar el movimiento de DS

Ejecute estos cmdlets:

1. `Connect-AzAccount`
1. `Set-AzContext -SubscriptionName $trgSub`
1. `Copy-AzRecoveryServicesVault - CorrelationIdForDataMove $corr -SourceVault $srcVault -TargetVault $trgVault -Force`

Puede supervisar la operación con el cmdlet `Get-AzRecoveryServicesBackupJob`.

>[!Note] 
>- Durante la operación de movimiento de datos de copia de seguridad, todos los elementos de copia de seguridad se pasan a un estado transitorio. En este estado, no se crean puntos de recuperación (RP) ni se limpian los puntos de recuperación antiguos.
>- Cuando esta característica esté habilitada en GEC y GNE, se recomienda realizar estos pasos en un almacén pequeño y validar el movimiento. Si se valida correctamente, realice estos pasos en todos los almacenes.
>- Además de desencadenarse el movimiento de datos de copia de seguridad para todo el almacén, el movimiento se realiza por contenedor (máquinas virtuales, servidores DPM y MABS y agentes de MARS). Realice un seguimiento del progreso de los movimientos por contenedor en la sección **Trabajos**. 

 ![supervisión del progreso de los trabajos de movimiento](./media/germany-migration-management-tools/track-move-jobs.png)

Durante la operación de movimiento, se bloquean las acciones siguientes en el almacén de origen:

- Nuevas copias de seguridad programadas
- Detención de la copia de seguridad con la eliminación de datos
- Eliminación de datos
- Reanudación de la copia de seguridad
- Modificación de directivas

### <a name="step-4-check-the-status-of-the-move-job"></a>Paso 4: Comprobar el estado del trabajo de movimiento

La operación de movimiento de datos de copia de seguridad se realiza por contenedor. En el caso de las copias de seguridad de máquinas virtuales de Azure, dichas copias se consideran contenedores. Para indicar el progreso de la operación de movimiento de datos de copia de seguridad, se crea un trabajo para cada contenedor.

Para supervisar los trabajos, ejecute estos cmdlets:

1. `Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `$Jobs = Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `Get-AzRecoveryServicesBackupJobDetail -Job $Jobs[0] -VaultId $trgVault.ID`
1. `$JobDetails.ErrorDetails`

### <a name="step-5-post-move-operations"></a>Paso 5: Operaciones posteriores al traslado

Una vez completada la operación de movimiento de datos de copia de seguridad de todos los contenedores al almacén de destino, no se requiere ninguna otra acción para las copias de seguridad de las máquinas virtuales.



#### <a name="verify-the-movement-of-containers-is-complete"></a>Comprobar que el movimiento de los contenedores se ha completado

Para comprobar si todos los contenedores del almacén de origen se han movido al almacén de destino, vaya a este último y compruebe todos los contenedores de ese almacén.

Ejecute el cmdlet siguiente para enumerar todas las máquinas virtuales que se han movido del almacén de origen al de destino:

```azurepowershell
Get-AzRecoveryServicesBackupContainer -BackupManagementType “AzureVM” -VaultId $trgVault.ID
```

#### <a name="verify-the-movement-of-policies-is-complete"></a>Comprobar que el movimiento de las directivas se ha completado

Una vez que los datos de copia de seguridad se muevan correctamente a la nueva región, todas las directivas que se habían aplicado a los elementos de copia de seguridad de las máquinas virtuales de Azure en el almacén de origen se aplican al almacén de destino.

Para comprobar si todas las directivas se han movido del almacén de origen al de destino, vaya a este último y ejecute el cmdlet siguiente para obtener la lista de todas las directivas trasladadas:

```azurepowershell
Get-AzRecoveryServicesBackupProtectionPolicy -VaultId $trgVault.ID
```

Estas directivas siguen aplicándose en los datos de copia de seguridad después de la operación de traslado para que continúe la administración del ciclo de vida de los puntos de recuperación movidos.

Para evitar la limpieza repentina de varios puntos de recuperación (que pueden haber expirado durante el proceso de traslado o que pueden expirar inmediatamente después de este), la limpieza de los puntos de recuperación más antiguos (RP) se pausa durante un período de 10 días después del traslado. Durante este período, no se le facturarán los datos adicionales en los que incurran los puntos de recuperación antiguos.

>[!Important]
>Si necesita recuperar algo de los puntos de recuperación anteriores, recupérelo inmediatamente dentro de este plazo de 10 días. Una vez completado este período de seguridad, las directivas que se aplican a cada uno de los elementos de copia de seguridad entrarán en vigor y se aplicará la limpieza de los puntos de recuperación antiguos.

#### <a name="restore-operations"></a>Operaciones de restauración

**Restauración de máquinas virtuales de Azure**

Las máquinas virtuales de Azure pueden restaurarse desde los puntos de recuperación del almacén de destino.

#### <a name="configure-mars-agent"></a>Configuración del agente de MARS

1. Vuelva a registrarse en el almacén de destino.
1. Restaure desde los puntos de recuperación.
1. Vuelva a registrarse después de la recuperación en el nuevo almacén (VaultN) y reanude las copias de seguridad.

>[!Note]
>Mientras el agente de MARS esté registrado en el almacén de destino, no se realiza ninguna copia de seguridad nueva.

#### <a name="configure-dpmmabs"></a>Configuración de DPM/MABS

**Recomendado**

Use el método DPM externo para realizar la restauración. Para obtener más información, vea [Recuperación de datos de Azure Backup Server](../backup/backup-azure-alternate-dpm-server.md).

>[!Note]
>- No se admite la recuperación de la ubicación original (OLR).
>- Las copias de seguridad continuarán en VaultN para todas las máquinas registradas.

**Otra opción**

Para la recuperación de la ubicación original (OLR):

1. Vuelva a registrar el servidor DPM o MABS en el almacén de destino.
1. Realice una operación de restauración.
1. Vuelva a registrar el servidor DPM o MABS en el almacén nuevo.

>[!Note]
>Limitaciones del uso de DPM: <br><br> <ul><li>La operación de copia de seguridad de todas las máquinas registradas en el servidor DPM se detiene al conectar dicho servidor al almacén de destino.</li><li>Una vez que el servidor DPM se vuelve a registrar en el nuevo almacén después de la restauración, se realizan comprobaciones de coherencia (el tiempo necesario para completarlas dependerá de la cantidad de datos) antes de reanudar las copias de seguridad.</li></ul>

### <a name="error-codes"></a>Códigos de error

#### <a name="usererrorconflictingdatamoveonvault"></a>UserErrorConflictingDataMoveOnVault 

**Mensaje:** hay otra operación de movimiento de datos que se está ejecutando actualmente en el almacén. 

**Escenario:** está intentando realizar la operación de movimiento de datos en un almacén de origen, mientras que ya se está ejecutando otra operación de movimiento de datos en el mismo almacén de origen.

**Acción recomendada:** espere hasta que se complete la operación de movimiento de datos actual y vuelva a intentarlo.

#### <a name="usererroroperationnotallowedduringdatamove"></a>UserErrorOperationNotAllowedDuringDataMove

**Mensaje**: no se permite esta operación porque ya hay una operación de movimiento de datos en curso. 

**Escenarios:** mientras la operación de movimiento de datos está en curso, no se permiten las operaciones siguientes en el almacén de origen:
 
- Detener la copia de seguridad con conservación de datos 
- Detener la copia de seguridad con eliminación de datos 
- Eliminar datos de la copia de seguridad 
- Reanudar copia de seguridad 
- Modificar la directiva

**Acción recomendada:** espere hasta que se complete la operación de movimiento de datos y vuelva a intentarlo. Obtenga [más información](#azure-backup) sobre las operaciones admitidas.

#### <a name="usererrornocontainersfoundfordatamove"></a>UserErrorNoContainersFoundForDataMove 

**Mensaje:** no hay ningún contenedor en este almacén que se admita para la operación de movimiento de datos. 

**Escenarios:** este mensaje se muestra si:

- El almacén de origen no tiene ningún contenedor. 
- El almacén de origen solo tiene contenedores no admitidos. 
- El almacén de origen tiene todos los contenedores que se han movido previamente a algún almacén de destino y se ha pasado el valor IgnoreMoved = true en la API.

**Acción recomendada:** [obtenga información](#azure-backup) sobre los contenedores admitidos para el movimiento de datos.

#### <a name="usererrordatamovenotsupportedatcontainerlevel"></a>UserErrorDataMoveNotSupportedAtContainerLevel 

**Mensaje:** la operación de movimiento de datos no se admite en el nivel de contenedor. 

**Escenario:** ha elegido una operación de movimiento de datos de nivel de contenedor. 

**Acción recomendada:** pruebe a realizar la operación de movimiento de datos de nivel de almacén.

### <a name="usererrordatamovenotallowedcontainer-registrationinprogress"></a>UserErrorDataMoveNotAllowedContainer RegistrationInProgress 

**Mensaje:** no se permite la operación de movimiento de datos porque se está ejecutando una operación de registro de contenedor en el almacén de origen. 

**Escenario:** una operación de registro de contenedor está en curso en el almacén de origen cuando se intentan mover los datos. 

**Acción recomendada:** pruebe a realizar la operación de movimiento de datos más tarde.

#### <a name="usererrordatamovenotallowedtargetvaultnotempty"></a>UserErrorDataMoveNotAllowedTargetVaultNotEmpty 

**Mensaje:** no se permite la operación de movimiento de datos porque el almacén de destino ya tiene algunos contenedores registrados. 

**Escenario:** el almacén de destino elegido ya tiene algunos contenedores registrados. 

**Acción recomendada:** pruebe a realizar la operación de movimiento de datos en un almacén de destino vacío.

#### <a name="usererrorunsupportedsourceregionfordatamove"></a>UserErrorUnsupportedSourceRegionForDataMove 

**Mensaje:** la operación de movimiento de datos no se admite para esta región. 

**Escenario:** la región de origen no es válida.

**Acción recomendada:** compruebe la [lista de regiones admitidas](#azure-backup) para el movimiento de datos.

#### <a name="usererrorunsupportedtargetregionfordatamove"></a>UserErrorUnsupportedTargetRegionForDataMove 

**Mensaje:** la operación de movimiento de datos no se admite en esta región.

**Escenario:** el identificador de la región de destino no es válido. 

**Acción recomendada:** compruebe la [lista de regiones admitidas](#azure-backup) para el movimiento de datos.


#### <a name="usererrordatamovetargetvaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveTargetVaultWithPrivate EndpointNotSupported 

**Mensaje:** los datos no se pueden mover porque el almacén de destino seleccionado tiene puntos de conexión privados. 

**Escenario:** los puntos de conexión privados están habilitados en el almacén de destino. 

**Acción recomendada:** elimine los puntos de conexión privados y reintente la operación de movimiento. Obtenga [más información](#azure-backup) sobre las operaciones admitidas.

### <a name="usererrordatamovesourcevaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveSourceVaultWithPrivate EndpointNotSupported 

**Mensaje:** los datos no se pueden mover porque el almacén de origen seleccionado tiene puntos de conexión privados.

**Escenario:** los puntos de conexión privados están habilitados en el almacén de origen.

**Acción recomendada:** elimine los puntos de conexión privados y reintente la operación de movimiento. Obtenga [más información](../backup/private-endpoints.md#deleting-private-endpoints) sobre las operaciones admitidas.

#### <a name="usererrordatamovesourcevaultwithcmk-notsupported"></a>UserErrorDataMoveSourceVaultWithCMK NotSupported 

**Mensaje:** los datos no se pueden mover porque el almacén de origen seleccionado está habilitado para el cifrado. 

**Escenario:** las claves administradas por el cliente (CMK) están habilitadas en el almacén de origen.

**Acción recomendada:** [obtenga información](#azure-backup) sobre las operaciones admitidas.

#### <a name="usererrordatamovetargetvaultwithcmknotsupported"></a>UserErrorDataMoveTargetVaultWithCMKNotSupported 

**Mensaje:** los datos no se pueden mover porque el almacén de destino seleccionado está habilitado para el cifrado. 

**Escenario:** las claves administradas por el cliente (CMK) están habilitadas en el almacén de destino.

**Acción recomendada:** [obtenga información](#azure-backup) sobre las operaciones admitidas.

## <a name="scheduler"></a>Scheduler

Azure Scheduler se va a retirar. Para crear trabajos de programación, puede usar [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en Azure global en su lugar.

Para obtener más información:

- Para obtener más información, complete los [tutoriales de Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md).
- Revise la [introducción a Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="network-watcher"></a>Network Watcher

En este momento no se admite la migración de una instancia de Azure Network Watcher desde Azure Alemania a Azure global. Se recomienda crear y configurar una nueva instancia de Network Watcher en Azure global. A continuación, compare los resultados entre los entornos antiguo y nuevo. 

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Network Watcher](../network-watcher/index.yml).
- Revise la [introducción a Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).
- Más información sobre los [registros de flujo de los grupos de seguridad de red](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Obtenga información sobre el [monitor de conexión](../network-watcher/connection-monitor.md).

## <a name="site-recovery"></a>Site Recovery

No se puede migrar la configuración actual de Azure Site Recovery en Azure global. Debe configurar una nueva solución de Site Recovery en Azure global.

Para obtener más información sobre Site Recovery y cómo aprender a migrar las máquinas virtuales de Azure Alemania a Global Azure, consulte [Uso de Site Recovery](./germany-migration-compute.md#compute-iaas).

Actualice sus conocimientos completando estos tutoriales paso a paso:

- [Recuperación ante desastres de Azure a Azure](../site-recovery/azure-to-azure-about-networking.md)
- [Recuperación ante desastres de VMware en Azure](../site-recovery/site-recovery-deployment-planner.md)
- [Recuperación ante desastres de Hyper en Azure](../site-recovery/hyper-v-deployment-planner-overview.md)

## <a name="azure-policies"></a>Directivas de Azure

No puede migrar directamente directivas de Azure Alemania a Azure global. Durante una migración, normalmente cambia el ámbito de las directivas asignadas. Esto se aplica especialmente cuando la suscripción es diferente en el entorno de destino, como en este escenario. Sin embargo, puede conservar las definiciones de directiva y volver a usarlas en Azure global.

En la CLI de Azure, ejecute el siguiente comando para enumerar todas las directivas en el entorno actual.

> [!NOTE]
> No olvide cambiar el entorno AzureGermanCloud en la CLI de Azure antes de ejecutar los comandos siguientes.


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

Exporte solo las directivas que tengan **personalizado** como valor de **PolicyType**. Exporte **policyRule** a un archivo. En el ejemplo siguiente se exporta la directiva personalizada "Solo permitir Centro de Alemania" (versión abreviada: `allowgconly`) a un archivo en la carpeta actual: 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

El archivo de exportación será similar al ejemplo siguiente:

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

A continuación, cambie el entorno de Azure global. Modifique la regla de directiva mediante la edición del archivo. Por ejemplo, cambie `germanycentral` a `westeurope`.

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Creación de una nueva directiva:

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

Ahora tiene una nueva directiva denominada `allowweonly`. La directiva solo permite Oeste de Europa como región.

Asigne la directiva a los ámbitos en el nuevo entorno según corresponda. Para documentar las asignaciones antiguas de Azure Alemania, ejecute el comando siguiente:

```azurecli
az policy assignment list
```

Para obtener más información:

- Actualice sus conocimientos completando el [tutorial de las directivas de Azure](../governance/policy/tutorials/create-and-manage.md).
- Obtenga información sobre cómo [ver las directivas mediante la CLI de Azure](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli) o [ver las directivas mediante el uso de PowerShell](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell).
- Obtenga información sobre cómo [crear una definición de directiva mediante la CLI de Azure](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli) o cómo [crear una definición de directiva mediante PowerShell](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="where-can-i-move-the-backup-data"></a>¿Dónde puedo mover los datos de copia de seguridad?

Puede mover los datos de las copias de seguridad desde los almacenes de Recovery Services (RSV) en Centro de Alemania (GEC) y Nordeste de Alemania (GNE) a Centro-oeste de Alemania (GWC).

### <a name="what-backup-data-can-i-move"></a>¿Qué datos de copia de seguridad puedo mover?

A partir del 21 de junio de 2021, puede mover los datos de copia de seguridad de las cargas de trabajo siguientes de una región a otra:

- Azure Virtual Machines
- Cargas de trabajo híbridas
- Copia de seguridad de archivos o carpetas mediante el agente de Microsoft Azure Recovery Services (MARS)
- Servidor de Data Protection Manager (DPM)
- Servidor de Azure Backup (MABS)

### <a name="how-can-i-move-backup-data-to-another-region"></a>¿Cómo puedo mover datos de copia de seguridad a otra región?

Para asegurarse de que los datos de las regiones existentes no se pierdan, Azure Backup ha habilitado el movimiento de datos de copia de seguridad de GEC y GNE a GWC.

Mientras se produce la migración, las copias de seguridad se detendrán en GEC y GNE. Por lo tanto, es esencial proteger las cargas de trabajo en la nueva región antes de iniciar la operación de migración.

### <a name="what-to-do-if-the-backup-data-move-operation-fails"></a>¿Qué debo hacer si se produce un error en la operación de movimiento de datos de copia de seguridad?

Pueden producirse errores de los datos de copia de seguridad debido a los escenarios siguientes:

| Mensajes de error    | Causas |
| --- | --- |
| Proporcione un almacén de destino vacío. El almacén de destino no debe tener ningún elemento de copia de seguridad ni contenedores de copia de seguridad. | Ha elegido un almacén de destino que ya tiene algunos elementos protegidos. |
| Los datos de Azure Backup solo pueden moverse a las regiones de destino admitidas. | Ha elegido un almacén de destino de una región que no es una de las admitidas para el traslado. |

Debe reintentar realizar la copia de seguridad desde cero mediante la ejecución del mismo comando (que se indica a continuación) con un nuevo almacén de destino vacío o bien puede reintentar mover los elementos con errores desde el almacén de origen mediante su indicación con una marca.

```azurepowershell
   if($validated) {
                              $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault -RetryOnlyFailed
                      }
```

### <a name="is-there-a-cost-involved-in-moving-this-backup-data"></a>¿Implica algún costo mover estos datos de copia de seguridad?

No. Mover los datos de copia de seguridad de una región a otra no tiene ningún costo adicional. Azure Backup asume el costo de mover los datos entre regiones. Una vez completada la operación de movimiento, solo tendrá un período de 10 días sin facturación. Después de este período, la facturación se iniciará en el almacén de destino.

### <a name="if-i-face-issues-in-moving-backup-data-whom-should-i-contact"></a>En caso de problemas al mover datos de copia de seguridad, ¿con quién debo ponerme en contacto?

Para cualquier problema con el traslado de datos de copia de seguridad de GEC o GNE a GWC, escríbanos a [GESupportAzBackup@microsoft.com](mailto:GESupportAzBackup@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Elementos multimedia](./germany-migration-media.md)
