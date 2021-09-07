---
title: Instalación de la extensión `arcdata`
description: Instale la extensión `arcdata` para la CLI de Azure (az)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 5e1ddfdc13ff7bf11a2a1293ebf39c4c527c6d0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750282"
---
# <a name="install-arcdata-azure-cli-extension"></a>Instalación de la extensión `arcdata` de la CLI de Azure

> [!IMPORTANT]
> Si va a actualizar a una nueva versión, asegúrese de actualizar también a la versión más reciente de CLI de Azure y la extensión `arcdata`.


## <a name="install-latest-azure-cli"></a>Instalación de la CLI de Azure más reciente 

Para obtener la CLI de Azure más reciente, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).


## <a name="add-arcdata-extension"></a>Adición de la extensión `arcdata`

Para agregar la extensión, ejecute el comando siguiente: 

```azurecli
az extension add --name arcdata 
```

[Más información sobre las extensiones de la CLI de Azure](/cli/azure/azure-cli-extensions-overview)

## <a name="update-arcdata-extension"></a>Actualización de la extensión `arcdata`

Si ya tiene la extensión, puede actualizarla con el siguiente comando:

```azurecli
az extension update --name arcdata
```

## <a name="next-steps"></a>Pasos siguientes

[Creación del controlador de datos de Azure Arc](create-data-controller.md)
