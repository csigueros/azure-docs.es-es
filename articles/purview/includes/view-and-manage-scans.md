---
author: whhender
ms.author: whhender
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 6/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ea867a13772b4a0d10d21d3ca732320b404d9c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010900"
---
### <a name="view-your-scans-and-scan-runs"></a>Visualización de los exámenes y las ejecuciones de exámenes

Para ver los exámenes existentes, haga lo siguiente:

1. Vaya a [Purview Studio](https://web.purview.azure.com/resource/). Seleccione la **Mapa de datos** en el panel izquierdo.

1. Seleccione el origen de datos que desee. Verá una lista de exámenes existentes en ese origen de datos en **Exámenes recientes** o puede ver todos los exámenes en la pestaña **Exámenes**.

1. Seleccione el examen que tiene los resultados que quiere ver.

1. En esta página se muestran todas las ejecuciones de exámenes anteriores, junto con el estado y las métricas de cada ejecución del examen. También mostrará si el análisis se ha programado o es manual, a cuántos recursos se han aplicado clasificaciones, cuántos recursos totales se han detectado, la hora de inicio y finalización del examen y la duración total del examen.

### <a name="manage-your-scans---edit-delete-or-cancel"></a>Administración de exámenes: editar, eliminar o cancelar

Para administrar o eliminar un examen, haga lo siguiente:

1. Vaya a [Purview Studio](https://web.purview.azure.com/resource/). Seleccione la **Mapa de datos** en el panel izquierdo.

1. Seleccione el origen de datos que desee. Verá una lista de exámenes existentes en ese origen de datos en **Exámenes recientes** o puede ver todos los exámenes en la pestaña **Exámenes**.

1. Seleccione el examen que desea administrar. Para editar el examen, puede seleccionar **Edit scan** (Editar examen).

1. Para cancelar un examen en curso, puede seleccionar **Cancelar ejecución del examen**.

1. Para eliminar el examen, seleccione **Eliminar examen**.

> [!NOTE]
> * Al eliminar el examen no se eliminan los recursos del catálogo creados a partir de examenes anteriores.
> * El recurso no se volverá a actualizar con los cambios de esquema si ha cambiado la tabla de origen y se vuelve a examinar la tabla de origen después de editar la descripción en la pestaña de esquema de Purview.
