---
title: Publicación de artefactos de paquetes de configuración de invitado personalizados
description: Aprenda a publicar un archivo de paquete de configuración de invitado en Azure Blob Storage y obtener un token de SAS para el acceso seguro.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: aa20bc3d9c47258c6ebedd2419cf830ba47c1c1c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868567"
---
# <a name="how-to-publish-custom-guest-configuration-package-artifacts"></a>Publicación de artefactos de paquetes de configuración de invitado personalizados

Antes de comenzar, es una buena idea leer la página de información general de la [configuración de invitado](../concepts/guest-configuration.md).

Los paquetes .zip personalizados de la configuración de invitado deben almacenarse en una ubicación a la que las máquinas administradas puedan acceder a través de HTTPS. Algunos ejemplos son repositorios de GitHub, repositorios de Azure, Azure Storage o un servidor web dentro del centro de datos privado.

Los paquetes de configuración que admiten `Audit` y `AuditandSet` se publican de la misma manera. No es necesario hacer nada especial durante la publicación en función del modo de paquete.

## <a name="publish-a-configuration-package"></a>Publicación de un paquete de configuración

La ubicación preferida para almacenar un paquete de configuración es Azure Blob Storage.
No hay ningún requisito especial para la cuenta de almacenamiento, pero es una buena idea hospedar el archivo en una región cercana a las máquinas. Si prefiere no hacer público el paquete, puede incluir un [token de SAS](../../../storage/common/storage-sas-overview.md) en la dirección URL o implementar un [punto de conexión de servicio](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) para las máquinas de una red privada.

Si no tiene una cuenta de almacenamiento, use el ejemplo siguiente para crear una.

```powershell
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

El comando `Publish-GuestConfigurationPackage` carga el paquete de configuración en Azure Blob Storage y recupera un token de SAS para que se pueda acceder a él de forma segura.

Parámetros del cmdlet `Publish-GuestConfigurationPackage`:

- **Ruta de acceso**: Ubicación del paquete que se va a publicar.
- **ResourceGroupName**: Nombre del grupo de recursos donde se encuentra la cuenta de almacenamiento.
- **StorageAccountName**: Nombre de la cuenta de almacenamiento donde se debe publicar el paquete.
- **StorageContainerName** (valor predeterminado: _guestconfiguration_): Nombre del contenedor de almacenamiento en la cuenta de almacenamiento.
- **Force**: Permite sobrescribir el paquete existente en la cuenta de almacenamiento con el mismo nombre.

En el ejemplo siguiente se publica el paquete en un contenedor de almacenamiento denominado "guestconfiguration".

```powershell
Publish-GuestConfigurationPackage -Path ./MyConfig.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName | % ContentUri
```

## <a name="next-steps"></a>Pasos siguientes

- [Pruebe el artefacto de paquete](./guest-configuration-create-test.md) desde el entorno de desarrollo.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
