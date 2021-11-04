---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 549104e9a0721d8edb58ad57cede0e4790cad4cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455876"
---
## <a name="add-azure-cli-extensions"></a>Adición de extensiones de la CLI de Azure

Inicie el entorno de Bash en [Azure Cloud Shell](../articles/cloud-shell/quickstart.md).

[![Iniciar Cloud Shell en una nueva ventana](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Dado que estos comandos de la CLI aún no forman parte del conjunto de la CLI principal, agréguelos con los siguientes comandos:

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```