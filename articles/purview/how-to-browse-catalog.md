---
title: Cómo navegar por el catálogo de datos
description: En este artículo se proporciona información general sobre cómo explorar el catálogo de datos de Azure Purview en función del tipo de recurso.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: ab5cb2856343d4fdcfcbd6c3c88d45889c191d2d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456842"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Navegación por el catálogo de datos de Azure Purview

La búsqueda de un catálogo de datos es una excelente herramienta para la detección de datos si un consumidor de datos sabe lo que busca, pero a menudo los usuarios no saben exactamente cómo se estructura su patrimonio de datos. El catálogo de datos de Azure Purview ofrece una experiencia de exploración que permite a los usuarios explorar qué datos están disponibles para ellos por colección o a través de la jerarquía de cada origen de datos del catálogo.

Para acceder a la experiencia de exploración, seleccione "Browse assets" (Examinar recursos) en la página principal del catálogo de datos.

:::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Página principal de Purview" border="true":::

## <a name="browse-by-collection"></a>Examen por colección

Examinar por colección le permite explorar las distintas colecciones para las que es lector o conservador de datos.

> [!NOTE]
> Solo verá las colecciones a las que tiene acceso. Para más información, consulte [Creación y administración de colecciones de trabajos](how-to-create-and-manage-collections.md).

:::image type="content" source="media/how-to-browse-catalog/browse-by-collection.png" alt-text="Captura de pantalla que muestra la página de examinar por colección" border="true":::

Una vez seleccionada una colección, se obtiene una lista de recursos de esa colección con las facetas y filtros disponibles en la búsqueda. Como una colección puede tener miles de recursos, la herramienta de examinar usa el motor de relevancia de búsqueda de Purview para poner en primer lugar los recursos más importantes.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-results.png" alt-text="Captura de pantalla que muestra los resultados de examinar por colección" border="true":::

Para determinadas anotaciones, puede hacer clic en los puntos suspensivos para elegir entre una condición AND o una condición OR. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="Captura de pantalla en la que se muestra cómo elegir entre la condición AND o la condición OR" border="true":::

Si la colección seleccionada no contiene los datos que está buscando, puede navegar fácilmente a las colecciones relacionadas o volver atrás y ver todo el árbol de colecciones.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-navigation.png" alt-text="Captura de pantalla que muestra cómo navegar entre colecciones" border="true":::

Cuando encuentre el recurso que está buscando, puede seleccionarlo para ver detalles adicionales, como el esquema, el linaje y una lista de clasificación detallada. Para más información sobre la página de detalles del recurso, consulte [Administración de recursos de catálogo](catalog-asset-details.md).

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Captura de pantalla que muestra la página de detalles del recurso" border="true":::

## <a name="browse-by-source-type"></a>Examinar por tipo de origen

Examinar por tipo de origen permite a los consumidores de datos explorar las jerarquías de orígenes de datos mediante una vista de explorador. Seleccione un tipo de origen para ver la lista de orígenes examinados.

Por ejemplo, puede encontrar fácilmente un conjunto de datos llamado *DateDimension* en una carpeta denominada *Dimensiones* en Azure Data Lake Storage Gen 2. Puede usar la experiencia "Browse by source type" (Examinar por tipo de origen) para ir a la cuenta de almacenamiento de ADLS Gen 2 y, a continuación, ir al servicio > contenedor > carpeta(s) para acceder a la carpeta *Dimensiones* determinada y, a continuación, ver la tabla *DateDimension*.

Se proporciona una experiencia de navegación nativa con un espacio de nombres jerárquico para cada origen de datos correspondiente.

> [!NOTE]
> Tras un examen con ámbito correcto, puede haber un retraso hasta que los recursos recién examinados aparezcan en la experiencia de exploración.


1. En la página **Browse by source types** (Examinar por tipos de origen), los iconos se clasifican por orígenes de datos. Para explorar aún más los recursos de cada origen de datos, seleccione el icono correspondiente.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.png" alt-text="Página Browse asset types (Examinar tipos de recursos)" border="true":::

   > [!TIP]
   > Algunos iconos son agrupaciones de una colección de orígenes de datos. Por ejemplo, el icono de cuenta de Azure Storage contiene todas las cuentas de Azure Blob Storage y Azure Data Lake Storage Gen2. El icono de Azure SQL Server mostrará los recursos de Azure SQL Server que contienen instancias de Azure SQL Database y de grupo de SQL dedicado de Azure ingeridas en el catálogo. 

1. En la página siguiente, se enumeran los activos de nivel superior en el tipo de datos elegido. Elija uno de los recursos para explorar aún más su contenido. Por ejemplo, después de seleccionar "Azure SQL Database", verá una lista de bases de datos con recursos en el catálogo de datos.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.png" alt-text="Página de exploración de Azure SQL Database" border="true":::

1. Se abrirá la vista del explorador. Inicie la exploración seleccionando el recurso en el panel izquierdo. Los recursos secundarios se mostrarán en el panel derecho de la página.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.png" alt-text="Vista del explorador" border="true":::

1. Para ver los detalles de un recurso, seleccione el nombre o el botón de puntos suspensivos en el extremo derecho.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.png" alt-text="Seleccione el botón de puntos suspensivos para ver la página de detalles del recurso" border="true":::

## <a name="next-steps"></a>Pasos siguientes

- [Cómo crear, importar y exportar términos del glosario](how-to-create-import-export-glossary.md)
- [Cómo administrar plantillas de términos para el glosario empresarial](how-to-manage-term-templates.md)
- [Cómo buscar en el catálogo de datos de Purview](how-to-search-catalog.md)
