---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: b67065704846591a832ee92c7720ecef276ad944
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860543"
---
Los requisitos previos de este tutorial son los siguientes:

* Una cuenta de Azure que incluya una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
- [Visual Studio Code](https://code.visualstudio.com/) con las siguientes extensiones:
  * [Herramientas de Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [Python 3](https://www.python.org/downloads/) (3.6.9 o posterior), [Pip 3](https://pip.pypa.io/en/stable/installing/) y, opcionalmente, [venv](https://docs.python.org/3/library/venv.html). 

> [!Important]
> Este módulo de Custom Vision solo admite arquitecturas **Intel x86 y amd64**. Compruebe la arquitectura del dispositivo perimetral antes de continuar.

## <a name="set-up-azure-resources"></a>Configuración de los recursos de Azure

[![Implementación en Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]


