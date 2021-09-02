---
title: Archivo de inclusión
description: archivo de inclusión
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "113224780"
---
Ahora puede usar la herramienta Explorador de datos en Azure Portal para crear una base de datos y una tabla. 

1. Seleccione **Explorador de datos** > **Nueva tabla**. 
    
    El área **Agregar tabla** se muestra en el extremo derecho, pero es posible que tenga que desplazarse hacia la derecha para verla.

    ![Explorador de datos en Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. En la página **Agregar tabla**, escriba la configuración de la nueva tabla.

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Id. de tabla|sample-table|Identificador de la nueva tabla. Los nombres de tabla tienen los mismos requisitos de caracteres que los identificadores de base de datos. Los nombres de bases de datos deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final.
    Throughput|400 RU|Cambie el rendimiento a 400 unidades de solicitud por segundo (RU/s). Si quiere reducir la latencia, puede escalar verticalmente el rendimiento más adelante.

3. Seleccione **Aceptar**.

4. El Explorador de datos muestra la nueva base de datos y la tabla.

   ![El Explorador de datos de Azure Portal mostrando la nueva base de datos y la colección](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)