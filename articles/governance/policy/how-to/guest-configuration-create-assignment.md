---
title: Creación de una asignación de configuración de invitado mediante plantillas
description: Aprenda a implementar configuraciones en máquinas directamente desde Azure Resource Manager.
ms.date: 08/09/2021
ms.topic: how-to
ms.openlocfilehash: aa7938a9421a9e7680af34af475585c4dc1c818a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868559"
---
# <a name="how-to-create-a-guest-configuration-assignment-using-templates"></a>Creación de una asignación de configuración de invitado mediante plantillas

La mejor manera de [asignar paquetes de configuración de invitado](../concepts/guest-configuration-assignments.md) a varias máquinas es usar [Azure Policy](./guest-configuration-create-definition.md). También puede asignar paquetes de configuración de invitado a una sola máquina.

## <a name="built-in-and-custom-configurations"></a>Configuraciones integradas y personalizadas

Para asignar un paquete de configuración de invitado a una sola máquina, modifique los ejemplos siguientes. Hay dos escenarios.

- Aplique una configuración personalizada a una máquina mediante un vínculo a un paquete que haya [publicado](./guest-configuration-create-publish.md).
- Aplique una configuración [integrada](../samples/built-in-packages.md) a una máquina, por ejemplo, una línea de base de Azure.

## <a name="extending-other-resource-types-such-as-arc-enabled-servers"></a>Extensión de otros tipos de recursos, como servidores habilitados para Arc

En cada una de las secciones siguientes, el ejemplo incluye una propiedad **type** donde el nombre comienza por `Microsoft.Compute/virtualMachines`. El proveedor de recursos de configuración de invitado `Microsoft.GuestConfiguration` es un [recurso de extensión](../../../azure-resource-manager/management/extension-resource-types.md) que debe hacer referencia a un tipo primario.

Para modificar el ejemplo en otros tipos de recursos, como [servidores habilitados para Arc](../../../azure-arc/servers/overview.md), cambie el tipo primario por el nombre del proveedor de recursos.
En el caso de los servidores habilitados para Arc, el proveedor de recursos es `Microsoft.HybridCompute/machines`.

Reemplace los siguientes campos "<>" por valores específicos de su entorno:

- **<vm_name>** : nombre del recurso de máquina donde se aplicará la configuración.
- **<configuration_name>** : nombre de la configuración que se aplicará.
- **<vm_location>** : región de Azure donde se creará la asignación de configuración de invitado.
- **<Url_to_Package.zip>** : para el paquete de contenido personalizado, un vínculo HTTPS al archivo .zip personalizado.
- **<SHA256_hash_of_package.zip>** : para el paquete de contenido personalizado, un hash SHA256 al archivo .zip personalizado.

## <a name="assign-a-configuration-using-an-azure-resource-manager-template"></a>Asignación de una configuración mediante una plantilla de Azure Resource Manager

Puede implementar una [plantilla de Azure Resource Manager](../../../azure-resource-manager/templates/deployment-tutorial-local-template.md?tabs=azure-powershell) que contenga recursos de asignación de configuración de invitado.

En el ejemplo siguiente se asigna una configuración personalizada.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "<configuration_name>",
                    "contentUri": "<Url_to_Package.zip>",
                    "contentHash": "<SHA256_hash_of_package.zip>",
                    "assignmentType": "ApplyAndMonitor"
                }
            }
        }
```

En el ejemplo siguiente se asigna la configuración integrada `AzureWindowBaseline`.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "AzureWindowsBaseline",
                    "version": "1.*",
                    "assignmentType": "ApplyAndMonitor",
                    "configurationParameter": [
                    {
                        "name": "Minimum Password Length;ExpectedValue",
                        "value": "16"
                    },
                    {
                        "name": "Minimum Password Length;RemediateValue",
                        "value": "16"
                    },
                    {
                        "name": "Maximum Password Age;ExpectedValue",
                        "value": "75"
                    },
                    {
                        "name": "Maximum Password Age;RemediateValue",
                        "value": "75"
                    }
                ]
                }
            }
        }
```

## <a name="assign-a-configuration-using-bicep"></a>Asignación de una configuración mediante Bicep

Puede usar [Azure Bicep](../../../azure-resource-manager/bicep/overview.md) para implementar asignaciones de configuración de invitado.

En el ejemplo siguiente se asigna una configuración personalizada.

```Bicep
resource myVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource myConfiguration 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: '<configuration_name>'
  scope: myVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: '<configuration_name>'
      contentUri: '<Url_to_Package.zip>'
      contentHash: '<SHA256_hash_of_package.zip>'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
    }
  }
}
```

En el ejemplo siguiente se asigna la configuración integrada `AzureWindowBaseline`.

```Bicep
resource myWindowsVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource AzureWindowsBaseline 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: 'AzureWindowsBaseline'
  scope: myWindowsVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: 'AzureWindowsBaseline'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
      configurationParameter: [
        {
          name: 'Minimum Password Length;ExpectedValue'
          value: '16'
        }
        {
          name: 'Minimum Password Length;RemediateValue'
          value: '16'
        }
        {
          name: 'Maximum Password Age;ExpectedValue'
          value: '75'
        }
        {
          name: 'Maximum Password Age;RemediateValue'
          value: '75'
        }
      ]
    }
  }
}
```

## <a name="assign-a-configuration-using-terraform"></a>Asignación de una configuración mediante Terraform

Puede usar [Terraform](https://www.terraform.io/) para [implementar](/azure/developer/terraform/get-started-windows-powershell) asignaciones de configuración de invitado.

> [!IMPORTANT]
> El proveedor de Terraform [azurerm_policy_virtual_machine_configuration_assignment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_configuration_policy_assignment) no se ha actualizado para admitir la propiedad `assignmentType`, por lo que solo se admiten configuraciones que realizan auditorías.

En el ejemplo siguiente se asigna una configuración personalizada.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "<configuration_name>" {
  name               = "<configuration_name>"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name            = "<configuration_name>"
    contentUri      =  '<Url_to_Package.zip>'
    contentHash     =  '<SHA256_hash_of_package.zip>'
    version         = "1.*"
    assignmentType  = "ApplyAndMonitor
  }
}
```

En el ejemplo siguiente se asigna la configuración integrada `AzureWindowBaseline`.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "AzureWindowsBaseline" {
  name               = "AzureWindowsBaseline"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name    = "AzureWindowsBaseline"
    version = "1.*"
    parameter {
      name  = "Minimum Password Length;ExpectedValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Length;RemediateValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Age;ExpectedValue"
      value = "75"
    }
    parameter {
      name  = "Minimum Password Age;RemediateValue"
      value = "75"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Lea la [introducción a la configuración de invitado](../concepts/guest-configuration.md).
- Configure un [entorno de desarrollo](../how-to/guest-configuration-create-setup.md) personalizado de paquetes de configuración de invitado.
- [Cree un artefacto de paquete](../how-to/guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](../how-to/guest-configuration-create-test.md) desde el entorno de desarrollo.
- [Publique el artefacto del paquete](./guest-configuration-create-publish.md) para que sea accesible para las máquinas.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](../how-to/guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Aprenda a ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
