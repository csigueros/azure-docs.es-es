---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2021
ms.author: larryfr
ms.openlocfilehash: 2bd402b9905772b7a45be5ea433a214b6e5f76cf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553457"
---
La información de este artículo se basa en ejemplos de código que se encuentran en el repositorio [azureml-examples](https://github.com/azure/azureml-examples). Para ejecutar los comandos localmente sin tener que copiar y pegar los archivos YAML y otros, clone el repositorio y luego cambie los directorios al directorio `cli` del repositorio:

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Si aún no ha establecido los valores predeterminados de la CLI de Azure, guarde la configuración predeterminada. Para evitar pasar los valores de la suscripción, el área de trabajo y el grupo de recursos varias veces, ejecute los comandos siguientes. Reemplace los parámetros siguientes por los valores para su configuración específica:

* Reemplace `<subscription>` con la identificación de su suscripción de Azure.
* Reemplace `<workspace>` por el nombre del área de trabajo de Azure Machine Learning.
* Reemplace `<resource-group>` por el grupo de recursos de Azure que contiene el área de trabajo.
* Reemplace `<location>` por la región de Azure que contiene el área de trabajo.

> [!TIP]
> Puede ver cuáles son los valores predeterminados actuales mediante el comando `az configure -l`.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

