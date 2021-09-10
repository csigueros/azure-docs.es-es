---
title: Prevención contra la pérdida de datos
description: Las funcionalidades de prevención de pérdida de datos de Cognitive Services permiten a los clientes configurar la lista de direcciones URL de salida a las que sus recursos de Cognitive Services pueden acceder. Esta configuración crea otro nivel de control para los clientes para evitar la pérdida de datos.
author: gclarkmt
ms.author: gregc
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: template-concept
ms.openlocfilehash: c9a5ac391607249393d362f0e0b5b007989ea138
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2021
ms.locfileid: "113601389"
---
# <a name="configure-data-loss-prevention-for-azure-cognitive-services"></a>Configuración de la prevención de pérdida de datos para Azure Cognitive Services

Las funcionalidades de prevención de pérdida de datos de Cognitive Services permiten a los clientes configurar la lista de direcciones URL de salida a las que sus recursos de Cognitive Services pueden acceder. De esta forma, los clientes obtienen otro nivel de control para evitar la pérdida de datos. En este artículo, se tratan los pasos necesarios para habilitar la característica de prevención de pérdida de datos para los recursos de Cognitive Services.

## <a name="prerequisites"></a>Requisitos previos

Antes de hacer una solicitud, necesita una cuenta de Azure y una suscripción a Azure Cognitive Services. Si ya tiene una cuenta, siga adelante y vaya a la sección siguiente. Si no tiene una cuenta, tenemos una guía para que la configure en unos minutos: [Creación de una cuenta de Cognitive Services para Azure](cognitive-services-apis-create-account.md).

Puede obtener la clave de suscripción en [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) después de [crear la cuenta](https://azure.microsoft.com/free/cognitive-services/).

## <a name="enabling-data-loss-prevention"></a>Habilitación de la prevención de pérdida de datos

Para habilitar la prevención de pérdida de datos, es necesario seguir dos pasos. Primero, la propiedad restrictOutboundNetworkAccess debe establecerse en true. Cuando esté establecida en true, también debe proporcionar la lista de direcciones URL aprobadas. La lista de direcciones URL se agrega a la propiedad allowedFqdnList. La propiedad allowedFqdnList contiene una matriz de direcciones URL separadas por comas.

>[!NOTE]
>
> * El valor de la propiedad `allowedFqdnList` admite 1000 direcciones URL como máximo.
> * La propiedad admite direcciones IP y nombres de dominio completos, es decir, `www.microsoft.com`.
> * La lista actualizada puede tardar hasta 15 minutos en aplicarse. 

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Instale la [CLI de Azure](/cli/azure/install-azure-cli) e [inicie sesión](/cli/azure/authenticate-azure-cli), o bien seleccione **Probar**.

1. Vea los detalles del recurso de Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
    ```

1. Vea las propiedades actuales del recurso de Cognitive Services.

    ```azurecli-interactive
    az rest -m get \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
    ```

1. Configure la propiedad restrictOutboundNetworkAccess y actualice el valor de FqdnList permitido con las direcciones URL aprobadas.

    ```azurecli-interactive
    az rest -m patch \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
        -b '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale [Azure PowerShell](/powershell/azure/install-az-ps) e [inicie sesión](/powershell/azure/authenticate-azureps), o bien seleccione **Probar**.

1. Muestre las propiedades actuales del recurso de Cognitive Services.

    ```azurepowershell-interactive
    $getParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Method = 'GET'
    }
    Invoke-AzRestMethod @getParams
    ```

1. Configure la propiedad restrictOutboundNetworkAccess y actualice el valor de FqdnList permitido con las direcciones URL aprobadas.

    ```azurepowershell-interactive
    $patchParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Payload = '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
        Method = 'PATCH'
    }
    Invoke-AzRestMethod @patchParams
    ```

---

## <a name="supported-services"></a>Servicios admitidos

Los siguientes servicios admiten la configuración de la prevención de pérdida de datos:

- Computer Vision
- Content Moderator
- Custom Vision
- Caras
- Form Recognizer
- Speech Service
- QnA Maker

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de redes virtuales](cognitive-services-virtual-networks.md)
