---
title: Creación de un recurso de Personalizer
description: En este artículo, aprenderá a crear un recurso de Personalizer en Azure Portal para cada bucle de comentarios.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: b901153653b292a77ff19a25f065cf225e54f76e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831562"
---
# <a name="create-a-personalizer-resource"></a>Creación de un recurso de Personalizer

Un recurso de Personalizer es lo mismo que un bucle de aprendizaje de Personalizer. Se crea un solo recurso, o bucle de aprendizaje, para cada dominio de asunto o área de contenido que tenga. No utilice varias áreas de contenido en el mismo bucle, ya que esto confundirá el bucle de aprendizaje y proporcionará predicciones insuficientes.

Si desea que Personalizer seleccione el mejor contenido para más de un área de contenido de una página web, use un bucle de aprendizaje diferente para cada uno.


## <a name="create-a-resource-in-the-azure-portal"></a>Crear un recurso en Azure Portal

Crear un recurso de Personalizer para cada bucle de comentarios.

1. Inicie sesión en el [portal de Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). El vínculo anterior le lleva a la página **Crear** del servicio Personalizer.
1. Escriba el nombre del servicio, seleccione la suscripción, la ubicación, el plan de tarifa y el grupo de recursos.

    > [!div class="mx-imgBorder"]
    > ![Use Azure Portal para crear el recurso de Personalizer, también denominado bucle de aprendizaje.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Seleccione **Crear** para crear el recurso.

1. Una vez implementado el recurso, seleccione el botón **Ir al recurso** para ir al recurso de Personalizer.

1. Seleccione la página **Inicio rápido** de su recurso y, luego, copie los valores del punto de conexión y la clave. Tanto el punto de conexión como la clave serán necesarios para usar las API Rank y Reward.

1. Seleccione la página **Configuración** del nuevo recurso para [configurar el bucle de aprendizaje](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Creación de un recurso con la CLI de Azure

1. Inicie sesión en la CLI de Azure con el siguiente comando:

    ```azurecli-interactive
    az login
    ```

1. Cree un grupo de recursos, una agrupación lógica para administrar todos los recursos de Azure que piensa usar con el recurso de Personalizer.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Cree un nuevo recurso Personalizer, el _bucle de aprendizaje_, con el siguiente comando para un grupo de recursos existente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Esta acción devuelve un objeto JSON, que incluye el **punto de conexión del recurso**.

1. Use el siguiente comando de la CLI de Azure para obtener su **clave de recurso**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Tanto el punto de conexión como la clave serán necesarios para usar las API Rank y Reward.

## <a name="next-steps"></a>Pasos siguientes

* [Configuración](how-to-settings.md) del bucle de aprendizaje de Personalizer
