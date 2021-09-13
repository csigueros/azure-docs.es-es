---
title: Creación de una cuenta de Azure Purview en las preguntas más frecuentes (P+F) del portal
description: En este artículo se responden las preguntas más frecuentes sobre la creación de cuentas de Purview a través del portal.
author: nayenama
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: bf148408d733dffad862eecf51cc06455846b19a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398200"
---
# <a name="faq-about-creating-purview-accounts-via-portal"></a>Preguntas más frecuentes sobre la creación de cuentas de Purview a través del portal

En este artículo se responden preguntas comunes que los clientes y los equipos de campo suelen hacer sobre la creación de una cuenta de Purview mediante Azure Portal: [Creación de una cuenta de Azure Purview](create-catalog-portal.md).

## <a name="common-questions"></a>Preguntas frecuentes

Consulte las respuestas a las siguientes preguntas comunes.

### <a name="azure-policy-blocking-from-creating-storage-and-event-hub-namespace"></a>Directiva de Azure que impide la creación de almacenamiento y de espacio de nombres de eventos 

* Si **Azure Policy** impide a todas las aplicaciones crear una **cuenta de Storage** y un **espacio de nombres de EventHub**, debe crear una excepción de directiva mediante etiqueta, que se especificará durante el proceso de creación de una cuenta de Purview. La razón principal es que con cada cuenta de Purview creada se debe crear un grupo de recursos administrado y dentro de este, una cuenta de Storage y un espacio de nombres de EventHub.

   >[!IMPORTANT]
   >No es preciso realizar este paso si no se tiene Azure Policy o si una instancia de Azure Policy no bloquea la creación de una **cuenta de Storage** y un **espacio de nombres de EventHub**.

    1. Vaya a Azure Portal y busque **Directiva**.
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
