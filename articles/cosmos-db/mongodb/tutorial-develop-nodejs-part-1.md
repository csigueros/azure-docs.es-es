---
title: Aplicación Node.js y Angular con la API de Azure Cosmos DB para MongoB (primera parte)
description: En esta serie de tutoriales en vídeo, aprenderá a crear una aplicación de MongoDB con Angular y Node en Azure Cosmos DB utilizando las mismas API que para MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: ec1275027c0a145da59e44017d596229d04e1298
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038211"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb"></a>Creación de una aplicación de Angular con la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En este tutorial de varias partes se muestra cómo crear una nueva aplicación escrita en Node.js con Express y Angular y cómo conectarla a la [cuenta de Cosmos configurada con la API de Cosmos DB para MongoDB](mongodb-introduction.md).

Azure Cosmos DB es una base de datos NoSQL rápida de Microsoft con API abiertas para cualquier escala. Permite desarrollar aplicaciones modernas con velocidad y disponibilidad respaldadas por un Acuerdo de Nivel de Servicio, escalabilidad automática e instantánea, y varias API de código abierto para muchos motores NoSQL.

En este tutorial de varias partes, se abordan las tareas siguientes:

> [!div class="checklist"]
> * [Creación de una aplicación Node.js Express con la CLI de Angular](tutorial-develop-nodejs-part-2.md)
> * [Creación de la interfaz de usuario con Angular](tutorial-develop-nodejs-part-3.md)
> * [Creación de una cuenta de Azure Cosmos DB mediante la CLI de Azure](tutorial-develop-nodejs-part-4.md) 
> * [Uso de Mongoose para conectarse a Azure Cosmos DB](tutorial-develop-nodejs-part-5.md)
> * [Incorporación de las funciones Post, Put y Delete a la aplicación](tutorial-develop-nodejs-part-6.md)

¿Quiere crear esta misma aplicación con React? Consulte la [serie de vídeos del tutorial de React](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Tutorial en vídeo

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Proyecto terminado 

Este tutorial le guía por los pasos necesarios para crear la aplicación paso a paso. Si desea descargar el proyecto finalizado, puede obtener la aplicación completa en el [repositorio de angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) en GitHub.

## <a name="next-steps"></a>Pasos siguientes

En esta parte del tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha leído información general sobre los pasos para crear una aplicación MEAN.js con Azure Cosmos DB. 

Puede continuar con la siguiente parte del tutorial para crear la aplicación Node.js Express.

> [!div class="nextstepaction"]
> [Creación de una aplicación Node.js Express con la CLI de Angular](tutorial-develop-nodejs-part-2.md)

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar la información sobre el clúster de bases de datos existente para planear la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).
