---
title: Descripción de los recursos de asignación de configuraciones de invitado
description: La configuración de invitado crea recursos de extensión denominados asignaciones de configuración de invitado que asignan configuraciones a máquinas.
ms.date: 08/15/2021
ms.topic: conceptual
ms.openlocfilehash: c106ca492166cc604607bfc8da3f9c7d7b3bff2d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868570"
---
# <a name="understand-guest-configuration-assignment-resources"></a>Descripción de los recursos de asignación de configuraciones de invitado

Cuando se asigna una instancia de Azure Policy, si se encuentra en la categoría "Configuración de invitado", se incluyen metadatos para describir una asignación de invitado.

[Hay disponible un tutorial de vídeo de este documento](https://youtu.be/DmCphySEB7A).

Puede imaginarse una asignación de invitado como un vínculo entre una máquina y un escenario de Azure Policy. Por ejemplo, el fragmento de código siguiente asocia la configuración de línea de base de Windows de Azure con la versión mínima `1.0.0` a cualquier máquina en el ámbito de la directiva.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

## <a name="how-azure-policy-uses-guest-configuration-assignments"></a>Uso de asignaciones de configuración de invitado por parte de Azure Policy

El servicio de configuración de invitado usa la información de los metadatos para crear automáticamente un recurso de auditoría para las definiciones con los efectos de directiva **AuditIfNotExists** o **DeployIfNotExists**. El tipo de recurso es `Microsoft.GuestConfiguration/guestConfigurationAssignments`. Azure Policy usa la propiedad **complianceStatus** del recurso de asignación de invitado para comunicar el estado de cumplimiento. Para más información, vea [Obtención de datos de cumplimiento](../how-to/get-compliance-data.md).

### <a name="deletion-of-guest-assignments-from-azure-policy"></a>Eliminación de asignaciones de invitado de Azure Policy

Cuando se elimina una asignación de Azure Policy, si la directiva ha creado una asignación de configuración de invitado, también se elimina esta.

## <a name="manually-creating-guest-configuration-assignments"></a>Creación manual de asignaciones de configuración de invitado

Los recursos de asignación de invitado de Azure Resource Manager pueden crearse mediante Azure Policy o cualquier SDK de cliente.

Una plantilla de implementación de ejemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2021-01-25",
      "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
      "name": "myMachine/Microsoft.GuestConfiguration/myConfig",
      "location": "westus2",
      "properties": {
        "guestConfiguration": {
          "name": "myConfig",
          "contentUri": "https://mystorageaccount.blob.core.windows.net/mystoragecontainer/myConfig.zip?sv=SASTOKEN",
          "contentHash": "SHA256HASH",
          "version": "1.0.0",
          "assignmentType": "ApplyAndMonitor",
          "configurationParameter": {}
        }
      }
    }    
  ]
}
```

En la tabla siguiente se describe cada propiedad de los recursos de asignación de invitado.

| Propiedad | Descripción |
|-|-|
| name | Nombre de la configuración dentro del archivo MOF del paquete de contenido. |
| contentUri | Ruta de acceso del URI HTTPS al paquete de contenido (.zip). |
| contentHash | Valor hash SHA256 del paquete de contenido, que se usa para comprobar que no ha cambiado. |
| version | Versión del paquete de contenido. Solo se usa para los paquetes integrados, no para los paquetes de contenido personalizados. |
| assignmentType | Comportamiento de la asignación. Valores permitidos: `Audit`, `ApplyandMonitor` y `ApplyandAutoCorrect`. |
| configurationParameter | Lista de tipo de recurso, nombre y valor de DSC del archivo MOF del paquete de contenido que se va a invalidar después de descargarse en la máquina. |

### <a name="deletion-of-manually-created-guest-configuration-assignments"></a>Eliminación de asignaciones de configuración de invitado creadas manualmente

Las asignaciones de configuración de invitado creadas por medio de cualquier método manual (como una implementación de plantilla de Azure Resource Manager) deben eliminarse manualmente.
La eliminación del recurso primario (máquina virtual o máquina habilitada para Arc) también elimina la asignación de configuración de invitado.

Para eliminar manualmente una asignación de configuración de invitado, use el ejemplo siguiente. Asegúrese de reemplazar todas las cadenas de ejemplo, indicadas mediante corchetes "\<\>".

```PowerShell
# First get details about the guest configuration assignment
$resourceDetails = @{
  ResourceGroupName = '<myResourceGroupName>'
  ResourceType      = 'Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments/'
  ResourceName      = '<myVMName>/Microsoft.GuestConfiguration'
  ApiVersion        = '2020-06-25'
}
$guestAssignment = Get-AzResource @resourceDetails

# Review details of the guest configuration assignment
$guestAssignment

# After reviewing properties of $guestAssignment to confirm
$guestAssignment | Remove-AzResource
```

## <a name="next-steps"></a>Pasos siguientes

- Lea la [introducción a la configuración de invitado](./guest-configuration.md).
- Configure un [entorno de desarrollo](../how-to/guest-configuration-create-setup.md) personalizado de paquetes de configuración de invitado.
- [Cree un artefacto de paquete](../how-to/guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](../how-to/guest-configuration-create-test.md) desde el entorno de desarrollo.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](../how-to/guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento de las asignaciones de directivas de configuración de invitado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
