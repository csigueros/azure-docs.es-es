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
ms.openlocfilehash: 22e4be00c67a854a53339ee5f6034b6d9d741022
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112234963"
---
Ahora puede usar la herramienta Explorador de datos en Azure Portal para crear una base de datos y un contenedor. 

1. Seleccione **Data Explorer** > **Nuevo contenedor**. 
    
    El área **Agregar contenedor** se muestra en el extremo derecho, pero es posible que haya que desplazarse hacia la derecha para verlo.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Explorador de datos de Azure Portal, panel Agregar contenedor":::

2. En la página **Agregar contenedor**, especifique la configuración del nuevo contenedor.

    |Configuración|Valor sugerido|Descripción
    |---|---|---|
    |**Identificador de base de datos**|ToDoList|Escriba *Tareas* como nombre de la nueva base de datos. Los nombres de base de datos tienen que tener entre 1 y 255 caracteres y no pueden contener `/, \\, #, ?` o espacios finales. Seleccione la opción **Share throughput across containers** (Compartir rendimiento entre contenedores) para compartir el rendimiento aprovisionado de la base de datos entre todos los contenedores de esta. Esta opción también le ayudará a reducir los costos. |
    | **Rendimiento de base de datos**| Puede aprovisionar rendimiento de **escalado automático** o **manual**. El rendimiento manual le permite escalar RU/s por su cuenta, mientras que el rendimiento de escalabilidad automática permite que el sistema escale RU/s en función del uso. En este ejemplo, seleccione **Manual**. <br><br> Deje el rendimiento en 400 unidades de solicitud por segundo (RU/s). Si quiere reducir la latencia, puede escalar verticalmente el rendimiento más adelante mediante la estimación de las RU/s necesarias con la [calculadora de capacidad](../estimate-ru-with-capacity-planner.md).<br><br>**Nota**: Este valor no está disponible cuando se crea un contenedor en una cuenta sin servidor. |
    |**ID de contenedor**|Elementos|Escriba *Elementos* como nombre del nuevo contenedor. Los identificadores de contenedor tienen los mismos requisitos de caracteres que los nombres de las bases de datos.|
    |**Clave de partición**| /categoría| El ejemplo que se describe en este artículo usa */category* como clave de partición.|

    En este ejemplo, no agregue **claves únicas** ni active el **almacén analítico**. Las claves únicas le permiten agregar una capa de integridad de datos a la base de datos mediante la garantía de unicidad de uno o varios valores por clave de partición. Para más información, consulte [Claves únicas en Azure Cosmos DB](../unique-keys.md). El [almacén analítico](../analytical-store-introduction.md) se usa para permitir el análisis a gran escala de datos operativos sin ningún efecto sobre las cargas de trabajo transaccionales.
    
    Seleccione **Aceptar**. El Explorador de datos muestra la nueva base de datos y el contenedor.