---
title: Cómo navegar por el catálogo de datos
description: En este artículo se proporciona información general sobre cómo explorar el catálogo de datos de Azure Purview en función del tipo de recurso.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: cf836be45374b881945a80f998d851c95c7a5abd
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110474189"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Navegación por el catálogo de datos de Azure Purview

En este artículo se describe cómo detectar datos en el catálogo de datos de Azure Purview mediante el espacio de nombres jerárquico del origen de datos.

## <a name="browse-experience"></a>Experiencia de navegación

Un consumidor de datos puede detectar datos mediante el conocido espacio de nombres jerárquico para cada uno de los orígenes de datos mediante una vista del explorador. Una vez que el origen de datos se registra y examina, la asignación de datos extrae información sobre la estructura (espacio de nombres jerárquico) del origen de datos. Esta información se usa para crear la experiencia de navegación para la detección de datos.

Por ejemplo, puede encontrar fácilmente un conjunto de datos llamado *DateDimension* en una carpeta denominada *Dimensiones* en Azure Data Lake Storage Gen 2. Puede usar la experiencia "Browse by Asset Type" (Examinar por tipo de recurso) para ir a la cuenta de almacenamiento de ADLS Gen 2 y, a continuación, ir al servicio > contenedor > carpeta(s) para acceder a la carpeta *Dimensiones* determinada y, a continuación, ver la tabla *DateDimension*.

Se proporciona una experiencia de navegación nativa con un espacio de nombres jerárquico para cada uno de los orígenes de datos correspondientes.

> [!NOTE]
> Tras el examen correcto, puede haber un retraso hasta que los recursos recién examinados aparezcan en la experiencia de exploración. Este retraso puede ser de hasta unas horas.

## <a name="browse-the-data-catalog-by-asset-type"></a>Navegación por el catálogo de datos por tipo de recurso

1. Puede seleccionar **Browse by asset type** (Examinar por tipo de recurso) en la página principal para examinar los recursos de datos.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Página principal de Purview" border="true":::

1. En la página **Browse asset types** (Examinar tipos de recursos), los iconos se clasifican por orígenes de datos. Para explorar aún más los recursos de cada origen de datos, seleccione el icono correspondiente.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Página Browse asset types (Examinar tipos de recursos)" border="true":::

> [!TIP]
> Algunos iconos son agrupaciones de una colección de orígenes de datos. Por ejemplo, el icono de cuenta de Azure Storage contiene todas las cuentas de Azure Blob Storage y Azure Data Lake Storage Gen2. El icono de Azure SQL Server mostrará los recursos de Azure SQL Server que contienen instancias de Azure SQL Database y de grupo de SQL dedicado de Azure ingeridas en el catálogo. 

1. En la página siguiente, se enumeran los activos de nivel superior en el tipo de datos elegido. Elija uno de los recursos para explorar aún más su contenido.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Página de exploración por tipo de recurso específico. Se muestra un ejemplo de una cuenta de Azure Storage" border="true":::

1. Se abrirá la vista del explorador. Inicie la exploración seleccionando el recurso en el panel izquierdo. Los recursos secundarios se mostrarán en el panel derecho de la página.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Vista del explorador" border="true":::

1. Para ver los detalles de un recurso, seleccione el nombre o el botón de puntos suspensivos en el extremo derecho.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Seleccione el botón de puntos suspensivos para ver la página de detalles del recurso" border="true":::

## <a name="view-related-data-assets"></a>Visualización de los recursos de datos relacionados

En la página de detalles del recurso también podrá ver otros recursos de datos relacionados. Por ejemplo, puede ir a la carpeta principal de *DateDimension* para ver la carpeta *Dimensiones* o, incluso, ir hasta el contenedor para ver los recursos en la jerarquía específica.

1. En la página de detalles del recurso, seleccione la pestaña **Related** (Relacionados) para ver otros recursos relacionados.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Abrir la pestaña Related (Relacionados)" border="true":::

1. La jerarquía completa del recurso actual se mostrará en la parte izquierda.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Estructura jerárquica" border="true":::

1. Seleccione cualquier nivel de la jerarquía para mostrar los recursos inmediatos en ese nivel.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Seleccionar una jerarquía distinta" border="true":::

## <a name="next-steps"></a>Pasos siguientes

- [Cómo crear, importar y exportar términos del glosario](how-to-create-import-export-glossary.md)
- [Cómo administrar plantillas de términos para el glosario empresarial](how-to-manage-term-templates.md)
