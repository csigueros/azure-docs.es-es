---
title: Archivo de inclusión
description: Archivo de inclusión
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112234946"
---
Ahora, puede agregar datos al nuevo contenedor con el Explorador de datos.

1. En el **Explorador de datos**, expanda la base de datos **Tareas** y, después, el contenedor **Elementos**. Seleccione **Elementos** y, después, **Nuevo elemento**.

   ![Creación de documentos en el Explorador de datos en Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Ahora, agregue un documento al contenedor con la estructura siguiente.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Después de agregar el archivo JSON a la pestaña **Documentos**, seleccione **Guardar**.

    ![Copiar los datos JSON y seleccionar Guardar en Data Explorer en Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Cree y guarde un documento más donde insertará un valor único para la propiedad `id` y cambie las demás propiedades como corresponda. Los nuevos documentos pueden tener la estructura que quiera, ya que Azure Cosmos DB no impone ningún esquema en los datos.