---
title: Cómo realizar búsquedas en Data Catalog
description: En este artículo, se proporciona información general sobre cómo buscar en el catálogo de datos de Azure Purview.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0473c75678631fee158cfabd33406b863971bf97
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455776"
---
# <a name="search-the-azure-purview-data-catalog"></a>Búsqueda en Azure Purview Data Catalog

Después de examinar e ingerir datos en el mapa de datos de Azure Purview, los consumidores de datos deben encontrar fácilmente los datos necesarios para sus cargas de trabajo de análisis o gobernanza. Esta operación puede llevar mucho tiempo, ya que es posible que no sepa dónde encontrar los datos que quiere. Incluso después de encontrarlos, puede que tenga dudas sobre si puede confiar en ellos y aceptar sus dependencias.

El objetivo de la búsqueda en Azure Purview es acelerar el proceso de detección de datos, a fin de encontrar rápidamente los datos que realmente importan. En este artículo se describe cómo realizar búsquedas en el catálogo de datos de Azure Purview para encontrar rápidamente los datos que está buscando.

## <a name="search-the-catalog-for-assets"></a>Búsqueda de recursos en el catálogo

Se puede acceder rápidamente a la barra de búsqueda desde la barra superior de la experiencia de usuario de Purview Studio. En la página principal del catálogo de datos, la barra de búsqueda se encuentra en el centro de la pantalla.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Captura de pantalla que muestra la ubicación de la barra de búsqueda de Azure Purview" border="true":::

Al hacer clic en la barra de búsqueda, se le presentará el historial de búsqueda y los recursos a los que se ha accedido recientemente en el catálogo de datos. Esto le permite recoger rápidamente una exploración de datos anterior que ya se ha realizado.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Captura de pantalla que muestra la barra de búsqueda antes de que se hayan escrito las palabras clave" border="true":::

Escriba las palabras clave que ayuden a identificar el recurso, como su nombre, tipo de datos, clasificaciones y términos del glosario. A medida que escribe las palabras clave de búsqueda, Purview sugiere dinámicamente recursos y búsquedas que pueden ajustarse a sus necesidades. Para completar la búsqueda, haga clic en "Ver resultados de la búsqueda" o presione "Entrar".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Captura de pantalla que muestra la barra de búsqueda cuando un usuario escribe palabras clave" border="true":::

Una vez que especifique la búsqueda, Purview devuelve una lista de recursos de datos para los que un usuario es un lector de datos que coincide con las palabras clave especificadas.

El motor de importancia de Purview ordena todas las coincidencias y las clasifica en función de lo que cree que es útil para un usuario. Por ejemplo, una tabla que coincide con varias palabras clave a las que un administrador de datos ha asignado términos del glosario y dado una descripción, probablemente será más interesante para un consumidor de datos que una carpeta que no se ha anotado. Un gran conjunto de factores determinan la puntuación de importancia de un recurso y el equipo de búsqueda de Purview está ajustando constantemente el motor de importancia para asegurarse de que los principales resultados de búsqueda tengan valor para el usuario.

Si los resultados principales no incluyen los recursos que busca, puede usar las facetas del lado izquierdo para filtrar por metadatos empresariales como los términos del glosario, las clasificaciones y la colección que los contiene. Si está interesado en un tipo de origen de datos determinado, como Azure Data Lake Storage Gen2 o Azure SQL Database, puede usar el filtro de tipo de origen para restringir la búsqueda.

> [!NOTE]
> La búsqueda solo devolverá recursos de colecciones para las que sea lector o conservador de datos. Para más información, consulte [Creación y administración de colecciones en Azure Purview](how-to-create-and-manage-collections.md).

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Captura de pantalla que muestra los resultados de una búsqueda" border="true":::

Para determinadas anotaciones, puede hacer clic en los puntos suspensivos para elegir entre una condición AND o una condición OR. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="Captura de pantalla en la que se muestra cómo elegir entre la condición AND o la condición OR" border="true":::

Cuando encuentre el recurso que está buscando, puede seleccionarlo para ver detalles como el esquema, el linaje y una lista de clasificación detallada. Para más información sobre la página de detalles del recurso, consulte [Visualización, edición y eliminación de recursos en el catálogo de Purview](catalog-asset-details.md).

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Captura de pantalla que muestra la página de detalles del recurso" border="true":::

## <a name="bulk-edit-search-results"></a>Resultados de la búsqueda de edición masiva

Si desea realizar cambios en varios recursos devueltos por la búsqueda, Azure Purview le permite modificar términos del glosario, clasificaciones y contactos de forma masiva. Para más información, consulte la guía [Edición masiva de recursos para anotar clasificaciones, términos de glosario y modificar contactos](how-to-bulk-edit-assets.md).

## <a name="browse-the-data-catalog"></a>Exploración del catálogo de datos

Aunque la búsqueda es excelente si se sabe lo que se busca, hay ocasiones en las que los consumidores de datos desean explorar los datos que hay disponibles para ellos. El catálogo de datos de Azure Purview ofrece una experiencia de exploración que permite a los usuarios explorar qué datos están disponibles para ellos por cada colección o mediante un recorrido por la jerarquía de cada origen de datos del catálogo. Para más información, consulte [Navegación por el catálogo de datos de Azure Purview](how-to-browse-catalog.md).

## <a name="search-query-syntax"></a>Sintaxis de la consulta de búsqueda

Todas las consultas de búsqueda se componen de palabras clave y operadores. Una palabra clave es un elemento que formaría parte de las propiedades de un recurso. Algunas posibles palabras clave pueden ser una clasificación, un término del glosario, una descripción del recurso o un nombre de recurso. Una palabra clave puede ser solo una parte de la propiedad para la que está buscando una coincidencia. Use las palabras clave y los operadores incluidos a continuación para asegurarse de que Azure Purview devuelve los recursos que está buscando. 

A continuación se muestran los operadores que puede usar para crear una consulta de búsqueda. Los operadores se pueden combinar tantas veces como sea necesario en una misma consulta.

| Operator | Definición | Ejemplo |
| -------- | ---------- | ------- |
| O BIEN | Especifica que un recurso debe tener al menos una de las dos palabras clave. Debe estar en mayúsculas. Un espacio en blanco también es un operador OR.  | La consulta `hive OR database` devuelve los recursos que contienen la palabra "hive" o "database" o ambas. |
| y | Especifica que un recurso debe tener ambas palabras clave. Debe estar en mayúsculas. | La consulta `hive AND database` devuelve los recursos que contienen las palabras "hive" y "database". |
| NOT | Especifica que un recurso no puede contener la palabra clave a la derecha de la cláusula NOT. | La consulta `hive NOT database` devuelve los recursos que contienen la palabra "hive", pero no la palabra "database". |
| () | Agrupa un conjunto de palabras clave y operadores. Al combinar varios operadores, los paréntesis especifican el orden de las operaciones. | La consulta `hive AND (database OR warehouse)` devuelve recursos que contienen la palabra "hive" y la palabra "database" o "warehouse", o ambas. |
| "" | Especifica el contenido exacto de una frase con la que la consulta debe coincidir. | La consulta `"hive database"` devuelve los recursos que contienen la frase "hive database" en sus propiedades. |
| * | Carácter comodín que coincide con uno o varios caracteres; no puede ser el primer carácter de una palabra clave. | La consulta `dat*` devuelve los recursos que tienen propiedades que comienzan por "dat", como "data" o "database". |
| ? | Carácter comodín que coincide con un único carácter. No puede ser el primer carácter de una palabra clave. | La consulta `dat?` devuelve los recursos que tienen propiedades que comienzan por "dat" y son de cuatro letras, como "date" o "data". |

> [!Note]
> Especifique siempre los operadores booleanos de texto (**AND**, **OR**, **NOT**) en mayúsculas. De lo contrario, el caso no es importante, ni tampoco los espacios adicionales.

## <a name="next-steps"></a>Pasos siguientes

- [Cómo crear, importar y exportar términos del glosario](how-to-create-import-export-glossary.md)
- [Cómo administrar plantillas de términos para el glosario empresarial](how-to-manage-term-templates.md)
