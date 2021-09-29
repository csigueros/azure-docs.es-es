---
title: 'Ejemplo de script de la CLI de Azure: suscripción a una suscripción de Azure | Microsoft Docs'
description: En este artículo se proporciona un script de la CLI de Azure de ejemplo que muestra cómo suscribirse a los eventos de Azure Event Grid con la CLI de Azure.
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b12e7e530f83d599037713b2f372a49e9a4b927
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649443"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Suscripción a eventos de una suscripción de Azure con la CLI de Azure

Este script crea una suscripción de Event Grid a los eventos de una suscripción de Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

El script de ejemplo de la versión preliminar requiere la extensión de Event Grid. Para instalarla, ejecute `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Script de ejemplo: estable

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Script de ejemplo: extensión de la versión preliminar

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Explicación del script

Este script usa el siguiente comando para crear la suscripción de eventos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Cree una suscripción de Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create): versión de la extensión | Cree una suscripción de Event Grid. |

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre la consulta de suscripciones, consulte [Consulta de suscripciones de Event Grid](../query-event-subscriptions.md).
* Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
