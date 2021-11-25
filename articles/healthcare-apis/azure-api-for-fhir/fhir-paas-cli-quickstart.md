---
title: 'Inicio rápido: Implementación de Azure API for FHIR mediante la CLI de Azure'
description: En este inicio rápido va a aprender a implementar Azure API for FHIR en Azure mediante la CLI de Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: zxue
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: de3d4a64829e76ab451460ba153a3af9632aa786
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069757"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Inicio rápido: Implementación de Azure API for FHIR mediante la CLI de Azure

En este inicio rápido va a aprender a implementar Azure API for FHIR en Azure mediante la CLI de Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>Incorporación de la extensión HealthcareAPIs

```azurecli-interactive
az extension add --name healthcareapis
```

Obtenga una lista de comandos para HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Creación del grupo de recursos de Azure

Elija un nombre para el grupo de recursos que contendrá Azure API for FHIR y créelo:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Implementación de Azure API for FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Captura de la instrucción de funcionalidad de la API de FHIR

Obtenga una instrucción de funcionalidad de la API de FHIR con:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine el grupo de recursos mediante los siguientes pasos:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado Azure API for FHIR en su suscripción. Para establecer opciones de configuración adicionales en Azure API for FHIR, continúe con la guía paso a paso de configuración adicional. Si está listo para empezar a usar Azure API for FHIR, lea más información sobre cómo registrar aplicaciones.

>[!div class="nextstepaction"]
>[Configuración adicional en Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Introducción al registro de aplicaciones](fhir-app-registration.md)
