---
title: 'Actualización de una dirección IP pública: CLI de Azure'
description: En este artículo, aprenderá a actualizar la dirección IP pública de una SKU básica mediante la CLI de Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 8408f90bfa4512a5d42c77c13b3648b0bca9646e
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439478"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-cli"></a>Actualización de una dirección IP pública mediante la CLI de Azure

Las direcciones IP públicas de Azure se crean con una SKU básica o estándar. La SKU determina su funcionalidad, lo que incluye el método de asignación, la compatibilidad con características y los recursos a los que se pueden asociar. 

En este artículo, aprenderá a actualizar en Azure Portal la dirección IP pública de una SKU básica estática a una SKU estándar mediante la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* La dirección IP pública de una SKU básica **estática** en la suscripción. Para más información, consulte el artículo [Creación de una dirección IP pública - Azure Portal](create-public-ip-portal.md#create-a-basic-sku-public-ip-address).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="upgrade-public-ip-address"></a>Actualización de una dirección IP pública

En esta sección, usará la CLI de Azure y actualizará la dirección IP pública de la SKU básica estática a la SKU estándar.

```azurecli-interactive
az network public-ip update \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard

```
> [!NOTE]
> La dirección IP pública básica que va a actualizar debe tener el tipo de asignación estática. Recibirá la advertencia de que la IP no se puede actualizar si intenta actualizar una dirección IP asignada dinámicamente.

> [!WARNING]
> No se puede revertir la actualización de una IP pública básica a una SKU estándar. Las IP públicas actualizadas de una SKU básica a estándar siguen sin tener [zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) garantizadas.

## <a name="verify-upgrade"></a>Comprobación de la actualización

En esta sección, comprobará que la dirección IP pública es ahora la SKU estándar.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myBasicPublicIP \
  --query sku \
  --output tsv

```
Este comando debe mostrar **Estándar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha actualizado una dirección IP pública de SKU básica a una SKU estándar.

Para más información sobre direcciones IP públicas en Azure, consulte:

- [Direcciones IP públicas en Azure](public-ip-addresses.md)
- [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md)

