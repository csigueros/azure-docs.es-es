---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2021
ms.author: glenga
ms.openlocfilehash: 162cfe73e57c314ed1b8200d5ecacebc92cfc517
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669723"
---
Los siguientes pasos usan [APT](https://wiki.debian.org/Apt) para instalar Core Tools en la distribución de Ubuntu/Debian Linux. Para otras distribuciones de Linux, consulte el [archivo Léame de Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instale la clave GPG del repositorio de paquetes de Microsoft para validar la integridad del paquete:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configure la lista de origen de APT antes de actualizar APT.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Compruebe si el archivo `/etc/apt/sources.list.d/dotnetdev.list` contiene una de las cadenas que se indican a continuación con la versión de Linux apropiada:

    | Distribución de Linux | Versión |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Inicie la actualización del origen de APT:

    ```bash
    sudo apt-get update
    ```