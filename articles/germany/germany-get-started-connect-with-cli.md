---
title: Conexión a Azure Alemania mediante la CLI de Azure | Microsoft Docs
description: Información acerca de la administración de suscripciones en Azure Alemania mediante la CLI de Azure
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurecli
ms.openlocfilehash: b5fb2f9b55fbe949e2c171ee7bea989c1741ab5d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029148"
---
# <a name="connect-to-azure-germany-by-using-azure-cli"></a>Conexión a Azure Alemania mediante la CLI de Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Para usar la interfaz de línea de comandos de Azure (CLI de Azure), es preciso conectarse a Azure Alemania, en lugar de a Azure global. La CLI de Azure Portal se puede usar para administrar una suscripción de gran tamaño a través de scripts o para acceder a características que no están actualmente disponibles en Azure Portal. Si ha usado la CLI de Azure en Azure global, básicamente es lo mismo.  

## <a name="azure-cli"></a>Azure CLI
Hay varias maneras de [instalar la CLI de Azure](/cli/azure/install-az-cli2).  

Para conectarse a Azure Alemania, configure la nube:

```azurecli
az cloud set --name AzureGermanCloud
```

Después de que se haya configurado la nube, puede iniciar sesión:

```azurecli
az login --username your-user-name@your-tenant.onmicrosoft.de
```

Para confirmar que la nube está configurada correctamente en AzureGermanCloud, ejecute cualquiera de los comandos siguientes y, después, compruebe que la marca `isActive` se establece en `true` para el elemento AzureGermanCloud:

```azurecli
az cloud list
```

```azurecli
az cloud list --output table
```

## <a name="azure-classic-cli"></a>CLI de Azure clásica
Hay varias formas de [instalar la CLI clásica de Azure](/cli/azure/install-azure-cli). Si Node ya está instalado, lo más sencillo es instalar el paquete npm.

Para instalar la CLI desde un paquete npm, asegúrese de que ha descargado e instalado la versión [más reciente de Node.js y npm](https://nodejs.org/en/download/package-manager/). A continuación, ejecute **npm install** para instalar el paquete **azure-cli**:

```bash
npm install -g azure-cli
```

En distribuciones de Linux, es posible que tenga que usar **sudo** para ejecutar correctamente el comando **npm**, tal como se muestra a continuación:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Si necesita instalar o actualizar Node.js y npm en la distribución de Linux o el SO, recomendamos instalar la versión LTS de Node.js más reciente (4.x). Si utiliza una versión anterior, podrían producirse errores de instalación.


Después de instalar la CLI de Azure, inicie sesión en Azure Alemania:

```console
azure login --username your-user-name@your-tenant.onmicrosoft.de  --environment AzureGermanCloud
```

Después de que haya iniciado sesión, puede ejecutar los comandos de la CLI de Azure tal y como lo haría normalmente:

```console
azure webapp list my-resource-group
```

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de cómo conectarse a Azure Alemania, consulte los siguientes recursos:

* [Conexión a Azure Alemania mediante PowerShell](./germany-get-started-connect-with-ps.md)
* [Conexión a Azure Alemania mediante Visual Studio](./germany-get-started-connect-with-vs.md)
* [Conexión a Azure Alemania mediante Azure Portal](./germany-get-started-connect-with-portal.md)