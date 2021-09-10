---
title: Conexión a Azure Synapse Link para Azure Cosmos DB
description: Aprenda a conectar una base de datos de Azure Cosmos DB a un área de trabajo de Synapse con Azure Synapse Link.
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: f2c687d3124e19bd91321391396499675052662b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113046"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Conexión a Azure Synapse Link para Azure Cosmos DB

En este artículo se describe cómo acceder a una base de datos de Azure Cosmos DB desde Azure Synapse Analytics Studio con Azure Synapse Link.

## <a name="prerequisites"></a>Requisitos previos

Para poder conectar una base de datos de Azure Cosmos DB a un área de trabajo, se necesita:

* Una base de datos de Azure Cosmos DB existente o crear una siguiendo los pasos descritos en [Inicio rápido: Administración de una cuenta de Azure Cosmos DB](../../cosmos-db/how-to-manage-database-account.md).
* Un área de trabajo de Azure Synapse existente o crear una siguiendo los pasos descritos en [Inicio rápido: Creación de un área de trabajo de Synapse](../quickstart-create-workspace.md).

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Habilitación de Synapse Link en una cuenta de base de datos de Azure Cosmos DB

Para ejecutar análisis a gran escala en Azure Cosmos DB sin que afecte al rendimiento operativo, le recomendamos que habilite Synapse Link para Azure Cosmos DB. Synapse Link aporta la funcionalidad de HTAP a un contenedor y compatibilidad integrada en Azure Synapse.

## <a name="go-to-synapse-studio"></a>Ir a Synapse Studio

En un área de trabajo de Azure Synapse, seleccione **Iniciar Synapse Studio**. En la página principal de Synapse Studio, seleccione **Datos** y pasará a Data Object Explorer (Explorador de objetos de datos).

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Conexión de una base de datos de Azure Cosmos DB a un área de trabajo de Azure Synapse

La conexión de una base de datos de Azure Cosmos DB se realiza como un servicio vinculado. Con un servicio vinculado de Azure Cosmos DB, puede examinar y explorar datos, así como leer y escribir contenido de Apache Spark para Azure Synapse Analytics o SQL en Azure Cosmos DB.

En Data Object Explorer (Explorador de objetos de datos), puede conectarse directamente a una base de datos de Azure Cosmos DB siguiendo estos pasos:

1. Seleccione el icono **+** junto a **Datos**.
1. Seleccione **Connect to external data** (Conectar a datos externos).
1. Seleccione la API a la que desea conectarse, por ejemplo, **SQL API** o **API para MongoDB**.
1. Seleccione **Continuar**.
1. Use un nombre descriptivo para designar al servicio vinculado. El nombre aparecerá en Data Object Explorer (Explorador de objetos de datos) y se usará en los entornos de ejecución de Azure Synapse para conectarse a la base de datos y a los contenedores.
1. Seleccione el **nombre de la cuenta de Azure Cosmos DB** y el **nombre de la base de datos**.
1. (Opcional) Si no se especifica ninguna región, las operaciones del entorno de ejecución de Azure Synapse se enrutarán hacia la región más cercana en la que esté habilitado el almacén analítico. También puede establecer manualmente en qué región quiere que los usuarios accedan al almacén analítico de Azure Cosmos DB. Seleccione **Additional connection properties** (Propiedades de conexión adicionales) y, a continuación, **New** (Nuevo). En **Property Name** (Nombre de propiedad), escriba **PreferredRegions**. Establezca el **valor** en la región que desee, por ejemplo, **WestUS2**. (No hay espacios entre las palabras y el número).
1. Seleccione **Crear**.

Las bases de datos de Azure Cosmos DB aparecen en la pestaña **Linked** (Vinculado) en la sección **Azure Cosmos DB**. Con Azure Cosmos DB, puede diferenciar un contenedor habilitado para HTAP de un contenedor solo de OLTP mediante los iconos siguientes:

**Contenedor solo de OLTP**:

![Visualización que muestra el icono del contenedor OLTP.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Contenedor habilitado para HTAP**:

![Visualización que muestra el icono del contenedor HTAP.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interacción rápida con las acciones generadas por el código

Al hacer clic con el botón derecho en un contenedor, tendrá una lista de gestos que desencadenarán un entorno de ejecución de Spark o SQL. La escritura en un contenedor se realizará a través del almacén transaccional de Azure Cosmos DB y consumirá unidades de solicitud.  

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda qué se admite entre Azure Synapse y Azure  DB](./concept-synapse-link-cosmos-db-support.md)
* [Configuración de puntos de conexión privados para el almacén analítico de Azure Cosmos DB](../../cosmos-db/analytical-store-private-endpoints.md)
* [Información sobre cómo realizar consultas en el almacén analítico con Spark 3](./how-to-query-analytical-store-spark-3.md)
* [Información sobre cómo realizar consultas en el almacén analítico con Spark 2](./how-to-query-analytical-store-spark.md)
