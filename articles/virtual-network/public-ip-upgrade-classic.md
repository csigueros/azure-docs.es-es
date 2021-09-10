---
title: Migración de una dirección IP reservada clásica a una dirección IP pública
titleSuffix: Azure Virtual Network
description: En este artículo, aprenderá a actualizar una dirección IP reservada del modelo de implementación clásica a una dirección IP pública de Azure Resource Manager.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 05355db2d5227db3bf695b059df638e8bc07f4bd
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439422"
---
# <a name="migrate-a-classic-reserved-ip-address-to-a-public-ip-address"></a>Migración de una dirección IP reservada clásica a una dirección IP pública

Para beneficiarse de las nuevas funcionalidades de Azure Resource Manager, puede migrar una dirección IP estática pública existente, llamada IP reservada, del modelo clásico a Azure Resource Manager.  La dirección IP pública migrada será un tipo de SKU de nivel básico. 

En este artículo, aprenderá a actualizar una dirección IP reservada clásica a una dirección IP pública básica.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* La suscripción a Azure registrada para la migración. Para más información, consulte [Migración a Resource Manager con PowerShell](../virtual-machines/migration-classic-resource-manager-ps.md).
* Una dirección IP reservada del modelo de implementación clásica.
* Módulo Service Management de Azure PowerShell instalado para PowerShell. Para más información, consulte [Instalación del módulo Service Management de Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Instrucciones de la CLI de Azure clásica instalada para la CLI de Azure. Para más información, consulte [Instalación de la CLI clásica de Azure](/cli/azure/install-classic-cli).

## <a name="azure-powershell-service-management-module"></a>Módulo Service Management de Azure PowerShell

En esta sección, usará el módulo Service Management de Azure PowerShell para migrar una dirección IP reservada clásica a una dirección IP pública estática de Azure Resource Manager.

> [!NOTE]
> La dirección IP reservada debe quitarse de cualquier servicio en la nube al que esté asociada la dirección IP.

```azurepowershell-interactive
$validate = Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Validate
$validate.ValidationMessages

```

El comando anterior muestra las advertencias y errores que bloqueen la migración. Si la validación es correcta, puede continuar con los pasos siguientes para **preparar** y **confirmar** la migración:

```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Prepare
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Commit
```
Se crea un nuevo grupo de recursos en Azure Resource Manager utilizando el nombre de la dirección IP reservada migrada. En el ejemplo anterior, el grupo de recursos es **myReservedIP-Migrated**.

## <a name="azure-classic-cli"></a>CLI de Azure clásica

En esta sección, usará la CLI de Azure clásica para migrar una dirección IP reservada clásica a una dirección IP pública estática de Azure Resource Manager.

> [!NOTE]
> La dirección IP reservada debe quitarse de cualquier servicio en la nube al que esté asociada la dirección IP.

```azurecli-interactive
azure network reserved-ip validate migration myReservedIP

```
El comando anterior muestra las advertencias y errores que bloqueen la migración. Si la validación es correcta, puede continuar con los pasos siguientes para **preparar** y **confirmar** la migración:

```azurecli-interactive
azure network reserved-ip prepare-migration myReservedIP
azure network reserved-ip commit-migration myReservedIP
```
Se crea un nuevo grupo de recursos en Azure Resource Manager utilizando el nombre de la dirección IP reservada migrada. En el ejemplo anterior, el grupo de recursos es **myReservedIP-Migrated**.

## <a name="next-steps"></a>Pasos siguientes


Para más información sobre direcciones IP públicas en Azure, consulte:

- [Direcciones IP públicas en Azure](public-ip-addresses.md)
- [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md)

