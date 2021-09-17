---
title: Configuración de identidades administradas en grupos de Batch
description: Obtenga información sobre cómo habilitar identidades administradas asignadas por el usuario en grupos de Batch y cómo usar identidades administradas en los nodos.
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 903e173a6028e6bb574dfba618661da802702c2d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444807"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Configuración de identidades administradas en grupos de Batch

Las [identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md) eliminan la necesidad de que los desarrolladores administren credenciales, al proporcionar una identidad para el recurso de Azure en Azure Active Directory (Azure AD) y usarla para obtener tokens de Azure Active Directory (Azure AD).

En este tema se explica cómo habilitar identidades administradas asignadas por el usuario en grupos de Batch y cómo usar identidades administradas en los nodos.

> [!IMPORTANT]
> Los grupos deben configurarse con la opción [Configuración de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) para poder usar identidades administradas.
>
> La creación de grupos con identidades administradas se puede realizar mediante la [biblioteca de administración de .NET de Batch](/dotnet/api/overview/azure/batch#management-library), pero actualmente no es compatible con la [biblioteca cliente de .NET de Batch](/dotnet/api/overview/azure/batch#client-library).

## <a name="create-a-user-assigned-identity"></a>Creación de una identidad asignada por el usuario

En primer lugar, [cree la identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) en el mismo inquilino que la cuenta de Batch. No es necesario que esta identidad administrada esté en el mismo grupo de recursos ni en la misma suscripción.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Creación de un grupo de Batch con identidades administradas asignadas por el usuario

Después de haber creado una o más identidades administradas asignadas por el usuario, puede crear un grupo de Batch con esa identidad administrada mediante la [biblioteca de administración de .NET de Batch](/dotnet/api/overview/azure/batch#management-library).

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        },
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, UserAssignedIdentities>
            {
                ["Your Identity Resource Id"] =
                    new UserAssignedIdentities()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Uso de identidades administradas asignadas por el usuario en nodos de Batch

Muchas de las tecnologías de Azure Batch que acceden a otros recursos de Azure, como Azure Storage o Azure Container Registry, admiten identidades administradas. Para obtener más información sobre cómo usar las identidades administradas con Azure Batch, consulte los siguientes vínculos:

- [Archivos de recursos](resource-files.md)
- [archivos de salida](batch-task-output-files.md#specify-output-files-using-managed-identity)
- [Azure Container Registry](batch-docker-container-workloads.md#managed-identity-support-for-acr)
- [Sistema de archivos de contenedor de blobs de Azure](virtual-file-mount.md#azure-blob-container)

También puede configurar las tareas manualmente para que las identidades administradas puedan acceder directamente a los [recursos de Azure que admiten identidades administradas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

En los nodos de Batch, puede obtener tokens de identidades administradas y usarlos para autenticarse a través de la autenticación de Azure AD a través de [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md).

En Windows, el script de PowerShell para obtener un token de acceso para la autenticación es:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Para Linux, el script de Bash es:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Para obtener más información, consulte [Cómo usar identidades administradas de recursos de Azure en una máquina virtual de Azure para adquirir un token de acceso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre las [identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Aprenda a usar [claves administradas por el cliente con identidades administradas por el usuario](batch-customer-managed-key.md).
- Aprenda a [habilitar la rotación automática de certificados en un grupo de Batch](automatic-certificate-rotation.md).
