---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c85888c04859769d82b9c5ebe4691797e404adf
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367567"
---
Durante el proceso de etiquetado de datos, es posible que desee agregar más etiquetas para clasificar los elementos.  Por ejemplo, puede que desee agregar una etiqueta "Desconocido" u "Otro" para indicar confusión.

Siga estos pasos para agregar una o varias etiquetas a un proyecto:

1. Seleccione el proyecto en la página principal **Data Labeling** (Etiquetado de datos).
1. En la parte superior derecha de la página, cambie **En ejecución** a **En pausa** para detener la actividad de los etiquetadores.
1. Seleccione la pestaña **Details** (Detalles).
1. En la lista de la izquierda, seleccione **Label classes** (Clases de etiquetas).
1. En la parte superior de la lista, seleccione **+ Add Labels** (+ Agregar etiquetas) ![Add a label](../articles/machine-learning/media/how-to-create-labeling-projects/add-label.png) (Agregar una etiqueta).
1. En el formulario, agregue la nueva etiqueta. A continuación, elija cómo continuar con el proyecto.  Como ha cambiado las etiquetas disponibles, elija cómo tratar los datos ya etiquetados:
    * Vuelva a empezar y quite todas las etiquetas existentes.  Elija esta opción si desea empezar a etiquetar desde el principio con el nuevo conjunto de etiquetas completo. 
    * Vuelva a empezar y conserve todas las etiquetas existentes.  Elija esta opción para marcar todos los datos como sin etiquetar, pero mantener las etiquetas existentes como predeterminadas para las imágenes etiquetadas previamente.
    * Continúe y conserve todas las etiquetas existentes. Elija esta opción para mantener todos los datos ya etiquetados como están y empezar a usar la nueva etiqueta para los datos que aún no se hayan etiquetado.
1. Modifique la página de instrucciones según sea necesario para las nuevas etiquetas.
1. Una vez que haya agregado todas las etiquetas nuevas, en la parte superior derecha de la página, cambie **En pausa** a **En ejecución** para reiniciar el proyecto. 