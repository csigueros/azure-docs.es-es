---
title: Edición masiva de recursos para la clasificación de etiquetas, términos de glosario y modificar contactos
description: Aprenda a editar en masa recursos en Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 511c104080acf27e233fecfa3fae7efa814b9bba
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133469"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>Edición masiva de recursos para anotar clasificaciones, términos de glosario y modificar contactos

En este artículo se describe cómo etiquetar varios términos de glosario, clasificaciones, propietarios y expertos en una lista de recursos seleccionados de una sola vez.

## <a name="add-assets-to-view-selected-list-using-search"></a>Adición de recursos a la lista View selected (Ver selección) mediante la función de búsqueda

1. Busque el recurso de datos que quiere agregar a la lista de edición en masa.

1. En la página de resultados de la búsqueda, mantenga el puntero sobre el recurso que quiere agregar a la lista de edición en masa **View selected** (Ver selección); verá una casilla.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Captura de pantalla de la casilla.":::

1. Active la casilla para agregarlo a la lista de edición en masa **View selected** (Ver selección). Una vez agregado, verá el icono de elementos seleccionados en la parte inferior de la página.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Captura de pantalla de la lista.":::

1. Repita los pasos anteriores para agregar todos los recursos de datos a la lista.

## <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Adición de recursos a la lista View selected (Ver selección) de la página de detalles de recursos

También puede agregar un recurso a la lista de edición en masa en la página de detalles del recurso. Active la casilla situada en la esquina superior derecha para agregar el recurso a la lista de edición en masa.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Captura de pantalla del recurso.":::

## <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Edite en masa recursos de la lista View selected (Ver selección) para agregar, reemplazar o quitar términos del glosario.

1. Cuando haya terminado de identificar todos los recursos de datos que se deben editar en masa, seleccione la lista **View selected** (Ver selección) en la página de resultados de la búsqueda o en la página de detalles de recursos.

    :::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Captura de pantalla de la vista.":::

1. Revise la lista y seleccione **Deselect** (Anular selección) si quiere quitar algún recurso de ella.

    :::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Captura de pantalla con el botón de anulación de selección resaltado.":::

1. Seleccione **Bulk edit** (Edición en masa) para agregar, quitar o reemplazar términos del glosario de todos los recursos seleccionados.

    :::image type="content" source="media/how-to-bulk-edit-assets/bulk-edit.png" alt-text="Captura de pantalla con el botón de edición en masa resaltado.":::

1. Para agregar términos del glosario, en Operation (Operación), seleccione **Add** (Agregar). En New value (Nuevo valor), seleccione todos los términos del glosario que quiera agregar. Cuando haya finalizado, seleccione Apply (Aplicar).
    - La operación de adición anexará un nuevo valor a la lista de términos del glosario ya etiquetados en los recursos de datos.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Captura de pantalla de la función Add (Agregar).":::

1. Para reemplazar los términos del glosario, en Operation (Operación), seleccione **Replace with** (Reemplazar por). En New value (Nuevo valor), seleccione todos los términos del glosario que quiera reemplazar. Cuando haya finalizado, seleccione Apply (Aplicar).
    - En New value (Nuevo valor), la operación de reemplazo sustituirá todos los términos del glosario de los recursos de datos seleccionados por los términos seleccionados.
   
1. Para quitar términos de un glosario, en Operation (Operación), seleccione **Remove** (Quitar). Cuando haya finalizado, seleccione Apply (Aplicar).
    - La operación de eliminación quitará todos los términos del glosario de los recursos de datos seleccionados.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Captura de pantalla de la eliminación de términos.":::

1. Repita los pasos anteriores para clasificaciones, propietarios y expertos.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Captura de pantalla de las clasificaciones y los contactos.":::

1. Cuando haya terminado, seleccione **Close** (Cerrar) o **Remove all and close** (Quitar todo y cerrar) para cerrar la hoja de edición en masa. La primera opción no quitará los recursos seleccionados, mientras que la segunda quitará todos los recursos seleccionados.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Captura de pantalla de la opción Close (Cerrar).":::

   > [!Important]
   > El número recomendado de recursos para la edición en masa es de 25. Si selecciona más de 25, pueden producirse problemas de rendimiento.
   > El cuadro **View Selected** (Ver selección) solo estará visible si hay al menos un recurso seleccionado.

## <a name="next-steps"></a>Pasos siguientes

Lea el [Tutorial: Creación e importación de términos del glosario](how-to-create-import-export-glossary.md) para más información.
