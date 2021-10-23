---
title: Solución de problemas de conclusiones basadas en libros de Azure Monitor
description: Proporciona una guía de solución de problemas de conclusiones basadas en libros de Azure Monitor para servicios como Azure Key Vault, Azure CosmosDB, Azure Storage y Azure Cache for Redis.
services: azure-monitor
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 005d59f2f9afb8dfa19c642ef89386be51005fd7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131835"
---
# <a name="troubleshooting-workbook-based-insights"></a>Solución de problemas de conclusiones basadas en libros

Este artículo le ayudará con el diagnóstico y la solución de algunos de los problemas habituales que pueden producirse al usar conclusiones basadas en libros de Azure Monitor.


## <a name="why-can-i-only-see-200-resources"></a>¿Por qué solo puedo ver doscientos recursos?

El número de recursos seleccionados tiene un límite de doscientos, con independencia del número de suscripciones seleccionadas.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>¿Qué ocurre al hacer clic en un icono anclado recientemente en el panel?

* Si hace clic en cualquier parte del icono, le llevará a la pestaña desde la que se ancló el icono. Por ejemplo, si ancla un gráfico en la pestaña "Información general", al hacer clic en el icono del panel se abrirá esa vista predeterminada; sin embargo, si ancla un gráfico a partir de su propia copia guardada, se abrirá la vista de la copia guardada.
* El icono de filtro en la parte superior izquierda del icono abre la pestaña "Configurar las opciones del icono".
* El icono de puntos suspensivos de la parte superior derecha ofrece las opciones "Personalizar los datos del icono", "Personalizar", "Actualizar" y "Quitar del panel".

## <a name="what-happens-when-i-save-a-workbook"></a>¿Qué ocurre cuando se guarda un libro?

* Al guardar un libro, se crea una nueva copia con las ediciones realizadas y se cambia el título. Al guardar no se sobrescribe el libro; el libro actual siempre será la vista predeterminada.
* Un **libro** no guardado es simplemente la vista predeterminada.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>¿Por qué no se ven todas las suscripciones en el portal?

El portal solo mostrará datos de las suscripciones seleccionadas al iniciarlo. Para cambiar la selección de las suscripciones, vaya a la parte superior derecha y haga clic en el cuaderno con un icono de filtro. Esta opción mostrará la pestaña **Directorio + suscripción**.

![Directorio + suscripción](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>¿Qué es el intervalo de tiempo?

El intervalo de tiempo muestra los datos de un período de tiempo determinado. Por ejemplo, si el intervalo de tiempo es de 24 horas, se mostrarán los datos de las últimas 24 horas.

## <a name="what-is-time-granularity-time-grain"></a>¿Qué es la granularidad de tiempo (el intervalo de agregación)?

La granularidad de tiempo es la diferencia de tiempo entre dos puntos de datos. Por ejemplo, si el intervalo de agregación se establece en 1 segundo, las métricas se recopilarán cada segundo.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>¿Cuál es la granularidad de tiempo una vez que se ancla cualquier parte de los libros a un panel?

La granularidad de tiempo predeterminada se establece en automático, actualmente no se puede cambiar.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>¿Cómo se puede cambiar el intervalo de tiempo del paso de un libro en el panel?

De forma predeterminada, el intervalo de tiempo en el icono del panel se establece en 24 horas. Para cambiarlo, haga clic en los puntos suspensivos de la parte superior derecha, seleccione **Personalizar los datos del icono**, active la casilla "Anular la configuración de tiempo del panel en el nivel de icono" y, a continuación, elija un intervalo de tiempo en el menú desplegable.  

![Seleccione los puntos suspensivos en la esquina derecha del icono y elija Personalizar los datos del icono.](./media/storage-insights-overview/fqa-data-settings.png)

![En Configurar las opciones del icono, seleccione la lista desplegable Intervalo de tiempo para cambiar el intervalo de tiempo.](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>¿Cómo se cambia el título del libro o un paso del libro anclado a un panel?

El título del libro o un paso del libro anclado a un panel conserva el mismo nombre que tenía en el libro. Para cambiar el título, debe guardar su propia copia del libro. Después, podrá asignar un nombre al libro antes de presionar Guardar.

![Seleccione Guardar en la parte superior para guardar una copia del libro y cambiar su nombre.](./media/storage-insights-overview/fqa-change-workbook-name.png)

Para cambiar el nombre de un paso en el libro guardado, seleccione Editar en el paso y, a continuación, seleccione el engranaje en la parte inferior de la configuración.

![Seleccione Editar en la parte inferior de un paso del libro para abrir la configuración.](./media/storage-insights-overview/fqa-edit.png)
![En la configuración, seleccione el engranaje en la parte inferior para poder cambiar el nombre del paso.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los escenarios para los que están concebidos los libros, cómo crear informes y personalizar los ya existentes y otros muchos temas en el artículo [Crear informes interactivos con libros de Azure Monitor](../visualize/workbooks-overview.md).
