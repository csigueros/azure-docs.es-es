---
title: Creación de una Azure Policy para Azure Purview
description: En este artículo se describe cómo crear una excepción de Azure Policy para Purview y, al mismo tiempo, dejar las directivas existentes en su lugar para mantener la seguridad.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 10ac52f62da8f8c20ca79ad4d5bb3073eb2bfe69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111924"
---
# <a name="create-an-azure-policy-exception-for-purview"></a>Creación de una Azure Policy para Purview

Muchas suscripciones tienen [directivas de Azure](../governance/policy/overview.md) en su lugar que restringen la creación de algunos recursos. Esto es para mantener la seguridad y limpieza de la suscripción. Sin embargo, las cuentas de Purview implementan otros dos recursos de Azure cuando se crean: una cuenta de Azure Storage y un espacio de nombres de Event Hubs. Al crear la [cuenta de Purview,](create-catalog-portal.md) se implementarán estos recursos. Azure los administrará, por lo que no es necesario mantenerlos, pero tendrá que implementarlos.

Para mantener las directivas en la suscripción, pero permitir la creación de estos recursos administrados, puede crear una excepción de directiva.

## <a name="create-a-policy-exception-for-purview"></a>Creación de una excepción de directiva para Purview

1. Vaya a [Azure Portal](https://portal.azure.com) y busque **Directiva**

    :::image type="content" source="media/create-purview-portal-faq/search-for-policy.png" alt-text="Captura de pantalla que muestra Azure Portal barra de búsqueda, buscando la palabra clave Policy.":::

1. Consulte el tutorial [Creación de una definición de directiva personalizada](../governance/policy/tutorials/create-custom-policy-definition.md) o modifique la directiva existente para agregar dos excepciones con el operador `not` y la etiqueta `resourceBypass`:

    ```json
    {
    "mode": "All",
      "policyRule": {
        "if": {
          "anyOf": [
          {
            "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "tags['<resourceBypass>']",
                "exists": true
              }
            }]
          },
          {
            "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.EventHub/namespaces"
            },
            {
              "not": {
                "field": "tags['<resourceBypass>']",
                "exists": true
              }
            }]
          }]
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    ```
  
    > [!Note]
    > La etiqueta puede ser cualquier cosa que haya junto a `resourceBypass` y el usuario es quien decide si define un valor al crear Purview en los pasos posteriores, siempre que la directiva pueda detectar la etiqueta.

    :::image type="content" source="media/create-catalog-portal/policy-definition.png" alt-text="Captura de pantalla que muestra cómo crear una definición de directiva.":::

1. [Cree una asignación de directiva](../governance/policy/assign-policy-portal.md) mediante la directiva personalizada creada.

    :::image type="content" source="media/create-catalog-portal/policy-assignment.png" alt-text="Captura de pantalla que muestra cómo crear una asignación de directiva" lightbox="./media/create-catalog-portal/policy-assignment.png":::

> [!Note] 
> Si tiene **Azure Policy** y necesita agregar una excepción, como se hace en **Requisitos previos**, debe agregar la etiqueta correcta. Por ejemplo, puede agregar la etiqueta `resourceBypass`: :::image type="content" source="media/create-catalog-portal/add-purview-tag.png" alt-text="Agregar una etiqueta a la cuenta de Purview.":::

## <a name="next-steps"></a>Pasos siguientes

Para configurar Azure Purview mediante Private Link, consulte [Uso de puntos de conexión privados para la cuenta de Purview](./catalog-private-link.md).
