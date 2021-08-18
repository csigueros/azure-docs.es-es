---
title: Uso de la fuente de cambios de Azure Cosmos DB con Azure Functions
description: Uso de Azure Functions para conectarse a la fuente de cambios de Azure Cosmos DB. Más adelante, puede crear funciones de Azure reactivas que se desencadenan en cada nuevo evento.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: a751313d5c7f43401a9c031bf502a9a5d7f79612
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113355328"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquitecturas basadas en eventos sin servidor con Azure Cosmos DB y Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Functions proporciona la manera más sencilla de conectarse a la [fuente de cambios](change-feed.md). Puede crear pequeñas funciones reactivas de Azure Functions que se desencadenarán automáticamente en cada nuevo evento en la fuente de cambios de su contenedor de Azure Cosmos.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Funciones basadas en eventos sin servidor que funcionan con el desencadenador de Azure Functions para Cosmos DB" border="false":::

Con el [desencadenador de Azure Functions para Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md), puede aprovechar el escalado del [procesador de fuente de cambios](./change-feed-processor.md) y la funcionalidad de detección de eventos confiable sin necesidad de mantener ninguna [infraestructura de trabajo](./change-feed-processor.md). Céntrese en la lógica de Azure Functions sin preocuparse por el resto de la canalización de origen de eventos. Incluso puede combinar el desencadenador con cualquier otro [enlace de Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Actualmente, el uso del desencadenador de Azure Functions para Cosmos DB se admite solo con Core (SQL) API.

## <a name="requirements"></a>Requisitos

Para implementar un flujo basado en eventos sin servidor, necesita:

* **El contenedor supervisado**: el contenedor supervisado es el contenedor de Azure Cosmos que se está supervisando y almacena los datos a partir de los cuales se genera la fuente de cambios. Todas las inserciones y actualizaciones realizadas en el contenedor supervisado se reflejan en la fuente de cambios del contenedor.
* **El contenedor de concesión**: el contenedor de concesión mantiene el estado entre instancias de Azure Functions sin servidor múltiples y dinámicas, y permite un escalado dinámico. Este contenedor de concesión se puede crear manual o automáticamente mediante el desencadenador de Azure Functions para Cosmos DB. Para crear automáticamente el contenedor de concesión, establezca la marca *CreateLeaseCollectionIfNotExists* en la [configuración](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Los contenedores de concesión con particiones deben tener una definición de clave de partición `/id`.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Creación del desencadenador de Azure Functions para Cosmos DB

Ahora se admite la creación de una función de Azure con un desencadenador de Azure Functions para Cosmos DB en todos los IDE de Azure Functions e integraciones de la CLI:

* [Extensión de Visual Studio](../azure-functions/functions-develop-vs.md) para usuarios de Visual Studio.
* [Extensión de Visual Studio Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01) para usuarios de Visual Studio Code.
* Y, finalmente, [herramientas de la CLI de Core](../azure-functions/functions-run-local.md#create-func) para una experiencia independiente del IDE multiplataforma.

## <a name="run-your-trigger-locally"></a>Ejecución local del desencadenador

Puede ejecutar la [función de Azure localmente](../azure-functions/functions-develop-local.md) con el [Emulador de Azure Cosmos DB](./local-emulator.md) para crear y desarrollar flujos basados en eventos sin servidor, sin tener una suscripción de Azure ni incurrir en gastos.

Si desea probar escenarios en vivo en la nube, puede [probar Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) sin necesidad de tener tarjetas de crédito ni suscripciones de Azure.

## <a name="next-steps"></a>Pasos siguientes

Ahora, puede seguir aprendiendo acerca de las fuentes de cambios en los siguientes artículos:

* [Introducción a la fuente de cambios](change-feed.md)
* [Maneras de leer la fuente de cambios](read-change-feed.md)
* [Uso de la biblioteca de procesadores de fuente de cambios](change-feed-processor.md)
* [Trabajo con la biblioteca de procesadores de fuente de cambios](change-feed-processor.md)
* [Informática de base de datos sin servidor con Azure Cosmos DB y Azure Functions](serverless-computing-database.md)
