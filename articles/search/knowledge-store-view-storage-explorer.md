---
title: Visualización de un almacén de conocimiento con el Explorador de Storage
titleSuffix: Azure Cognitive Search
description: Visualice y analice un almacén de conocimiento de Azure Cognitive Search con el Explorador de Storage de Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: ee6e17e6fe52125d75f2782b1fa77215045787a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740213"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visualización de un almacén de conocimiento con el Explorador de Storage

Un [almacén de conocimiento](knowledge-store-concept-intro.md) se crea mediante un conjunto de aptitudes y se guarda en Azure Storage. En este artículo, aprenderá a ver el contenido de un almacén de conocimiento con el Explorador de Storage en Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

+ Cree un almacén de conocimiento en [Azure Portal](knowledge-store-create-portal.md) o [Postman y las API de REST](knowledge-store-create-rest.md).

+ También necesitará el nombre de la cuenta de Azure Storage que tiene el almacén de conocimiento, junto con su clave de acceso de Azure Portal.

## <a name="start-storage-explorer"></a>Inicie el Explorador de Storage.

1. En Azure Portal, [abra la cuenta de almacenamiento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que usó para crear el almacén de conocimiento.

1. En el panel de navegación izquierdo de la cuenta de almacenamiento, haga clic en **Explorador de Storage**.

## <a name="view-edit-and-query-tables"></a>Visualización, edición y consulta de tablas

Tanto el portal como los tutoriales de REST crean un almacén de conocimiento en Table Storage.

1. Expanda la lista **TABLAS** para mostrar una lista de las proyecciones de tablas de Azure que se crearon cuando creó el almacén de conocimiento. Las tablas deben incluir contenido relacionado con las revisiones de hoteles.

1. Seleccione una tabla para ver los datos enriquecidos, incluidas las frases clave y las puntuaciones de opinión.

   ![Visualización de tablas en el Explorador de Storage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visualización de tablas en el Explorador de Storage")

1. Para cambiar el tipo de datos de cualquier valor de tabla o para cambiar los valores individuales de la tabla, haga clic en **Editar**. Al cambiar el tipo de datos de una columna de una fila de la tabla, se aplicará a todas las filas.

   ![Edición de tablas en el Explorador de Storage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Edición de tablas en el Explorador de Storage")

1. Para ejecutar consultas, haga clic en **Consultar** en la barra de comandos y escriba las condiciones.  

   ![Consulta de tablas en el Explorador de Storage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Consulta de tablas en el Explorador de Storage")

## <a name="next-steps"></a>Pasos siguientes

Conecte este almacén de conocimiento a Power BI para un análisis más profundo, o bien continúe con el código, mediante la API REST y Postman para crear un almacén de información diferente.

> [!div class="nextstepaction"]
> [Conexión con Power BI](knowledge-store-connect-power-bi.md)
