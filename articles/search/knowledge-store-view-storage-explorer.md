---
title: Visualización de un almacén de conocimiento con el Explorador de Storage
titleSuffix: Azure Cognitive Search
description: Visualice y analice un almacén de conocimiento de Azure Cognitive Search con el Explorador de Storage de Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: d87ec3fa05e1740b5cba7b4230894f1df822ac1f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784661"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visualización de un almacén de conocimiento con el Explorador de Storage

Un [almacén de conocimiento](knowledge-store-concept-intro.md) es contenido creado por un conjunto de aptitudes de Azure Cognitive Search aptitudes y guardado en Azure Storage. En este artículo, aprenderá a ver el contenido de un almacén de conocimiento con el Explorador de Storage en Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Comience con un almacén de conocimiento existente creado en [Azure Portal](knowledge-store-create-portal.md) o mediante las [API REST](knowledge-store-create-rest.md). Tanto el portal como los tutoriales de REST crean un almacén de conocimiento en Azure Table Storage.

## <a name="start-storage-explorer"></a>Inicie el Explorador de Storage.

1. En Azure Portal, [abra la cuenta de almacenamiento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que usó para crear el almacén de conocimiento.

1. En el panel de navegación izquierdo de la cuenta de almacenamiento, seleccione **Explorador de Storage**.

## <a name="edit-and-query-tables"></a>Edición y consulta de tablas

1. Expanda la lista **TABLAS** para mostrar una lista de las proyecciones de tablas de Azure que se crearon cuando creó el almacén de conocimiento. Si usó el inicio rápido o el artículo de REST para crear el almacén de conocimiento, las tablas contendrán contenido relacionado con las reseñas de clientes de un hotel europeo.

1. Seleccione una tabla de la lista.

   ![Visualización de tablas en el Explorador de Storage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visualización de tablas en el Explorador de Storage")

1. Para cambiar el tipo de datos, el nombre de propiedad o los valores de datos individuales de la tabla, haga clic en **Editar**.

   ![Edición de tablas en el Explorador de Storage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Edición de tablas en el Explorador de Storage")

1. Para ejecutar consultas, seleccione **Consultar** en la barra de comandos y escriba las condiciones.

   ![Consulta de tablas en el Explorador de Storage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Consulta de tablas en el Explorador de Storage")

En Explorador de Storage, solo puede consultar una tabla a la vez mediante la [sintaxis de consulta admitida](/rest/api/storageservices/Querying-Tables-and-Entities). Para realizar consultas entre tablas, considere la posibilidad de usar Power BI en su lugar.

## <a name="next-steps"></a>Pasos siguientes

Conecte este almacén de conocimiento a Power BI para crear visualizaciones que incluyan varias tablas.

> [!div class="nextstepaction"]
> [Conexión con Power BI](knowledge-store-connect-power-bi.md)
