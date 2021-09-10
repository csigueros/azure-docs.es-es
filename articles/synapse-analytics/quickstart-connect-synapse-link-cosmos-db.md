---
title: 'Inicio rápido: Conexión a Azure Synapse Link para Azure Cosmos DB'
description: Conexión de una instancia de Azure Cosmos DB a un área de trabajo de Synapse con Synapse Link
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 6d89fd1aa0cb0047057340dd85ec1c0d318016b7
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106797"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Inicio rápido: Conexión a Azure Synapse Link para Azure Cosmos DB

En este artículo se describe cómo acceder a una base de datos de Azure Cosmos DB desde Azure Synapse Analytics Studio con Synapse Link. 

## <a name="prerequisites"></a>Requisitos previos

Antes de conectar una cuenta de Azure Cosmos DB al área de trabajo, debe contar con lo siguiente.

* Una cuenta de Azure Cosmos DB existente o deberá seguir [este inicio rápido](../cosmos-db/how-to-manage-database-account.md) para crear una nueva.
* Una área de trabajo de Synapse existente o deberá seguir [este inicio rápido](./quickstart-create-workspace.md) para crear una nueva. 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Habilitación del almacén analítico de Azure Cosmos DB

Para ejecutar análisis a gran escala en Azure Cosmos DB sin afectar al rendimiento operativo, le recomendamos que habilite Synapse Link para Azure Cosmos DB. Esta función aporta la funcionalidad de HTAP a un contenedor y compatibilidad integrada en Azure Synapse. Siga este inicio rápido para habilitar Synapse Link para contenedores de Cosmos DB.

## <a name="navigate-to-synapse-studio"></a>Navegación a Synapse Studio

En el área de trabajo de Synapse, seleccione **Iniciar Synapse Studio**. En la página principal de Synapse Studio, seleccione **Datos y se le dirigirá al **Data Object Explorer** (Explorador de objetos de datos).

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Conexión de una base de datos de Azure Cosmos DB a un área de trabajo de Synapse

La conexión de una base de datos de Azure Cosmos DB se realiza como un servicio vinculado. Un servicio vinculado de Cosmos DB permite a los usuarios examinar y explorar datos, y leer y escribir contenido de Apache Spark para Azure Synapse Analytics o SQL en Azure Cosmos DB.

En Data Object Explorer (Explorador de objetos de datos), puede conectarse directamente a una base de datos de Azure Cosmos DB siguiendo estos pasos:

1. Seleccione el icono ***+*** cerca de la opción Datos.
2. Seleccione **Connect to external data** (Conectar a datos externos).
3. Seleccione la API a la que quiere conectarse. SQL o MongoDB
4. Seleccione ***Continuar***
5. Asigne un nombre al servicio vinculado. El nombre se mostrará en el Explorador de objetos y lo usarán los tiempos de ejecución de Synapse para conectarse a la base de datos y a los contenedores. Le recomendamos que use un nombre descriptivo.
6. Seleccione el **nombre de la cuenta de Cosmos DB** y el **nombre de la base de datos**.
7. (Opcional) Si no se especifica ninguna región, las operaciones de tiempo de ejecución de Synapse se enrutarán hacia la región más cercana en la que esté habilitado el almacén analítico. Sin embargo, puede establecer manualmente en qué región quiere que los usuarios accedan al almacén analítico de Cosmos DB. Seleccione **Propiedades de conexión adicionales** y, a continuación, **Nuevo**. En **Nombre de propiedad**, escriba **_PreferredRegions_ *_ y establezca el _* valor** en la región que quiera (por ejemplo: WestUS2; no hay ningún espacio entre las palabras y los números).
8. Seleccione ***Crear***

Las bases de datos de Azure Cosmos DB están visibles en la pestaña **Vinculado** de la sección Azure Cosmos DB. Puede diferenciar un contenedor de Azure Cosmos DB habilitado para HTAP de un contenedor solo de OLTP mediante los iconos siguientes:

**Contenedor de Synapse**:

![Contenedor de HTAP](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Contenedor solo de OLTP**:

![Contenedor de OLTP](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interacción rápida con las acciones generadas por el código

Al hacer clic con el botón derecho en un contenedor, tendrá una lista de gestos que desencadenarán un tiempo de ejecución de Spark o SQL. La escritura en un contenedor se realizará a través del almacén transaccional de Azure Cosmos DB y consumirá unidades de solicitud.  

## <a name="next-steps"></a>Pasos siguientes

* [Información sobre compatibilidad entre Synapse y Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Obtenga más información sobre cómo consultar un almacén analítico con Apache Spark 3 para Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark-3.md)
* [Obtenga más información sobre cómo consultar un almacén analítico con Apache Spark 2 para Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)
