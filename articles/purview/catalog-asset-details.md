---
title: Visualización, edición y eliminación de recursos
description: En esta guía aprenderá a ver y editar los detalles de los recursos.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8004ef319efc08610f9c1a5de16b7c430d51d666
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209220"
---
# <a name="view-edit-and-delete-assets-in-purview-catalog"></a>Visualización, edición y eliminación de recursos en el catálogo de Purview

En este artículo se explica cómo puede ver los recursos y sus detalles relevantes. También explica cómo editar y eliminar recursos del catálogo.

## <a name="prerequisites"></a>Requisitos previos

- Configure los orígenes de datos y examine los recursos del catálogo.
- *O bien*, use las API de Purview Atlas para ingerir recursos en el catálogo. 

## <a name="viewing-asset-details"></a>Visualización de detalles de recursos

Puede detectar los recursos en Purview mediante uno de los siguientes métodos:
- [Navegando por el catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Buscando en el catálogo de datos de Azure Purview](how-to-search-catalog.md)

Una vez que encuentre el recurso que busca, puede ver todos sus detalles, editarlos o eliminarlos como se describe en las secciones siguientes.

## <a name="asset-details-tabs-explained"></a>Descripción de las pestañas de detalles de recursos

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="Pestañas de detalles de recursos":::

- **Información general:** en la pestaña de información general se tratan los detalles básicos de un recurso, como la descripción, la clasificación, la jerarquía y los términos del glosario.
- **Propiedades:** la pestaña de propiedades cubre tanto las propiedades básicas de un recurso como las avanzadas.
- **Esquema:** el esquema del recurso, incluidos los nombres de columna, los tipos de datos, las clasificaciones de nivel de columna, los términos y las descripciones se representan en la pestaña de esquema.
- **Linaje:** esta pestaña contiene los detalles de los gráficos de linaje de los recursos en los que están disponibles.
- **Contactos:** cada recurso puede tener un propietario y un experto asignados que se pueden visualizar y administrar desde la pestaña de contactos.
- **Related** (Relacionado): esta pestaña le permite navegar por los recursos relacionados con el recurso que está visualizando actualmente. 

## <a name="asset-overview"></a>Información general sobre recursos
La sección de información general de los detalles de recursos proporciona una vista resumida de un recurso. En las secciones siguientes se explican las distintas partes de la página de información general.

### <a name="asset-hierarchy"></a>Jerarquía de recursos

Puede ver la jerarquía de recursos completa en la pestaña de información general. Por ejemplo: si navega a una tabla SQL, puede ver el esquema, la base de datos y el servidor al que pertenece la tabla.

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="Jerarquía de recursos":::

### <a name="asset-classifications"></a>Clasificaciones de recursos

Las clasificaciones de recursos identifican el tipo de datos que se representan y se aplican manualmente o durante un examen. Por ejemplo, algunas clasificaciones admitidas son los números de id. nacional o de pasaporte. (Para obtener una lista completa de las clasificaciones, consulte la [página de clasificaciones admitidas](supported-classifications.md)). La pestaña de información general refleja las clasificaciones de nivel de recurso y las clasificaciones de nivel de columna que se han aplicado, que también puede ver como parte del esquema.

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="Clasificaciones de recursos":::

### <a name="asset-description"></a>Descripción de recursos

Puede ver la descripción de un recurso en la sección de información general. Para añadir una descripción de recurso, puede [editar el recurso](#editing-assets)

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="Descripción de recursos":::

### <a name="asset-glossary-terms"></a>Términos del glosario de recursos

Los términos del glosario de recursos son un vocabulario administrado de términos empresariales que se pueden usar para clasificar y relacionar recursos en su entorno. Por ejemplo, términos como "cliente", "comprador", "centro de coste" o cualquier término que dé a los usuarios contexto sobre los datos. Para obtener más información, consulte la [página de glosario empresarial](concept-business-glossary.md). Puede ver los términos de glosario de un recurso en la sección de información general y puede agregar un término de glosario a un recurso [editando el recurso](#editing-assets).

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="Términos de glosario de recursos":::

## <a name="editing-assets"></a>Edición de recursos

Para editar un recurso, seleccione el icono de edición de la esquina superior izquierda del recurso.

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="Botones de edición y eliminación de recursos":::

En el nivel de recurso, puede editar o agregar una descripción, clasificación o término de glosario desde la pestaña de información general de la pantalla de edición.

Puede ir a la pestaña de esquema en la pantalla de edición para actualizar el nombre de columna, el tipo de dato, la clasificación de nivel de columna, los términos o la descripción del recurso.

Puede ir a la pestaña de contacto de la pantalla de edición para actualizar los propietarios y expertos en el recurso. Puede buscar por nombre completo, correo electrónico o alias de la persona dentro de Azure Active Directory.

### <a name="edit-behavior-explained"></a>Descripción del comportamiento de edición

Si hace una actualización a nivel de recurso, como agregar al recurso una descripción, una clasificación a nivel de recurso, un término del glosario o un contacto, los exámenes posteriores actualizarán el esquema de recursos (el analizador detectará nuevas columnas y clasificaciones en ejecuciones de examen posteriores).

Si hace una actualización a nivel de columna, como agregar una descripción, una clasificación de nivel de columna, un término del glosario o actualizar el tipo de dato o el nombre de la columna, los exámenes posteriores **no** actualizarán el esquema de recursos (el analizador **no detectará** nuevas columnas y clasificaciones en ejecuciones de examen posteriores).

## <a name="deleting-assets"></a>Eliminación de recursos

Para eliminar un recurso, seleccione el icono de eliminación bajo el nombre del recurso.

### <a name="delete-behavior-explained"></a>Descripción del proceso de eliminación

Todos los recursos que elimine mediante el botón de eliminación se eliminarán permanentemente. Sin embargo, si ejecuta un **examen completo** en el origen desde el que se ingiere el recurso en el catálogo, el recurso se volverá a ingerir y podrá detectarlo mediante el catálogo de Purview.

Si tiene un examen programado (semanal o mensual) en el origen, el **recurso eliminado no se volverá a ingerir** en el catálogo a menos que un usuario final modifique el recurso desde la ejecución anterior del examen.   Por ejemplo, si se eliminó una tabla SQL de Purview, pero después de eliminar la tabla un usuario agregó una nueva columna a la tabla en SQL, en el siguiente examen el recurso se volverá a examinar e ingerir en el catálogo.

Si elimina un recurso, solo se eliminará dicho recurso. Purview no admite actualmente eliminaciones en cascada. Por ejemplo, si elimina un recurso de cuenta de almacenamiento en el catálogo, los contenedores, las carpetas y los archivos que se encuentren en él no se eliminarán. 

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
