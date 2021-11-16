---
title: Visualización de un almacén de conocimiento
titleSuffix: Azure Cognitive Search
description: Vea un almacén de conocimiento mediante el explorador de almacenamiento en Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: d3a66bacfe746b73d269cd8a36fedf0c6b821cdd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563670"
---
# <a name="view-a-knowledge-store-with-storage-browser"></a>Visualización de un almacén de conocimiento con el explorador de almacenamiento

Un [almacén de conocimiento](knowledge-store-concept-intro.md) es contenido creado por un conjunto de aptitudes de Azure Cognitive Search aptitudes y guardado en Azure Storage. En este artículo, aprenderá a ver el contenido de un almacén de conocimiento con el explorador de almacenamiento en Azure Portal.

Comience con un almacén de conocimiento existente creado en [Azure Portal](knowledge-store-create-portal.md) o mediante las [API REST](knowledge-store-create-rest.md). Tanto el portal como los tutoriales de REST crean un almacén de conocimiento en Azure Table Storage.

## <a name="start-storage-browser"></a>Inicio del explorador de almacenamiento

1. En Azure Portal, [abra la cuenta de almacenamiento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que usó para crear el almacén de conocimiento.

1. En el panel de navegación izquierdo de la cuenta de almacenamiento, seleccione **Explorador de almacenamiento**.

## <a name="view-and-edit-tables"></a>Visualización y edición de tablas

1. Expanda **Tablas** para buscar las proyecciones de tabla del almacén de conocimiento. Si usó el inicio rápido o el artículo de REST para crear el almacén de conocimiento, las tablas contendrán contenido relacionado con las reseñas de clientes de un hotel europeo.

   :::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Captura de pantalla del explorador de almacenamiento" border="true":::

1. Seleccione una tabla de la lista para ver el contenido.

1. Para reorganizar el orden de las columnas o eliminar una columna, seleccione **Editar columnas** en la parte superior de la página.

En el explorador de almacenamiento, solo puede consultar una tabla a la vez mediante la [sintaxis de consulta admitida](/rest/api/storageservices/Querying-Tables-and-Entities). Para realizar consultas entre tablas, considere la posibilidad de usar Power BI en su lugar.

## <a name="next-steps"></a>Pasos siguientes

Conecte este almacén de conocimiento a Power BI para crear visualizaciones que incluyan varias tablas.

> [!div class="nextstepaction"]
> [Conexión con Power BI](knowledge-store-connect-power-bi.md)
