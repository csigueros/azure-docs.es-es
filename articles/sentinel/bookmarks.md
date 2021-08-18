---
title: Uso de marcadores de búsqueda para las investigaciones de datos en Azure Sentinel
description: En este artículo se describe cómo usar los marcadores de búsqueda de Azure Sentinel para llevar un seguimiento de los datos.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 0fecd1fca22b003892817f8fd33db88b1eea6127
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729763"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Seguimiento de los datos durante una búsqueda con Azure Sentinel

La búsqueda de amenazas suele conllevar revisar una infinidad de datos de registro en busca de pruebas que evidencien comportamientos malintencionados. Durante este proceso, los investigadores dan con eventos que quieren recordar, volver a ver y analizar como parte de la validación de posibles hipótesis y para entender la historia completa de un riesgo.

Los marcadores de búsqueda de Azure Sentinel le ayudarán a eso precisamente, ya que conservan las consultas que se han ejecutado en **Azure Sentinel - Logs** (Azure Sentinel: registros), así como los resultados de consulta que considere importantes. También puede registrar las observaciones realizadas dentro de un contexto y hacer referencia a sus hallazgos agregando notas y etiquetas. Los datos marcados están visibles tanto para usted como para sus compañeros de equipo para, así, colaborar de forma más sencilla.

Puede volver a ver los datos marcados en cualquier momento en la pestaña **Marcadores** de la página **Búsqueda**. Puede usar opciones de filtrado y de búsqueda para encontrar rápidamente datos concretos de la investigación actual. Si lo desea, también puede ver los datos marcados directamente en la tabla **HuntingBookmark** del área de trabajo de Log Analytics. Por ejemplo:

> [!div class="mx-imgBorder"]
> ![Vista de la tabla HuntingBookmark](./media/bookmarks/bookmark-table.png)

La visualización de los marcadores de la table le permite filtrar, resumir y combinar los datos marcados con otros orígenes de datos, lo que facilita la búsqueda de pruebas definitivas.

Actualmente en versión preliminar, si encuentra algo que debe abordarse con urgencia mientras realiza una búsqueda en los registros, en un par de clics, puede crear un marcador y promoverlo a un incidente, o bien agregar el marcador a un incidente ya existente. Para más información acerca de los incidentes, consulte [Tutorial: Investigación de incidentes con Azure Sentinel](investigate-cases.md). 

También en versión preliminar, puede visualizar los datos marcados haciendo clic en **Investigar** en los detalles del marcador. Esto inicia la experiencia de investigación, en la que puede ver, investigar y comunicar visualmente sus hallazgos con un diagrama gráfico de entidades y una escala de tiempo interactivos.

## <a name="add-a-bookmark"></a>Agregar un marcador

1. En Azure Portal, vaya a **Sentinel** > **Administración de amenazas** > **Búsqueda** para ejecutar consultas con el fin de detectar comportamientos sospechosos y anómalos.

2. Seleccione una de las consultas de búsqueda y, a la derecha, en los detalles de la consulta de búsqueda, seleccione **Ejecutar consulta**. 

3. Seleccione **View query results** (Ver resultados de la consulta). Por ejemplo:
    
    > [!div class="mx-imgBorder"]
    > ![visualización de los resultados de la consulta desde la búsqueda de Azure Sentinel](./media/bookmarks/new-processes-observed-example.png)
    
    Esta acción abre los resultados de la consulta en el panel **Registros**.

4. En la lista de resultados de la consulta de registro, use las casillas para seleccionar una o más filas que contengan la información que le interese.

5. Seleccione **Agregar marcador**:
    
    > [!div class="mx-imgBorder"]
    > ![Agregar un marcador de búsqueda a una consulta](./media/bookmarks/add-hunting-bookmark.png)

6. A la derecha, en el panel **Agregar marcador**, puede actualizar el nombre del marcador y agregar etiquetas y notas que le ayuden a identificar el interés que tiene el elemento.

7. En la sección **Información de consulta**, use los cuadros desplegables para extraer información de los resultados de consulta para los tipos de entidad **cuenta**, **host** y **dirección IP**. Esta acción asigna el tipo de entidad seleccionado a una columna específica del resultado de la consulta. Por ejemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Asignar tipos de entidad para el marcador de búsqueda](./media/bookmarks/map-entity-types-bookmark.png)
    
    Para ver el marcador del gráfico de investigación (actualmente en versión preliminar), debe asignar al menos un tipo de entidad que sea de **cuenta**, **host** o **dirección IP**. 

5. Haga clic en **Guardar** para confirmar los cambios y agregar el marcador. Todos los datos marcados se comparten con otros investigadores, y es un primer paso hacia una experiencia de investigación colaborativa.

 
> [!NOTE]
> Los resultados de la consulta de registro admiten marcadores cada vez que se abre este panel desde Azure Sentinel. Por ejemplo, seleccione **General** > **Registros** en la barra de navegación, seleccione vínculos de eventos en el gráfico de investigación o seleccione un identificador de alerta en los detalles completos de un incidente (actualmente en versión preliminar). No se pueden crear marcadores si el panel **Registros** se abre desde otras ubicaciones como, por ejemplo, directamente desde Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Ver y actualizar marcadores 

1. En Azure Portal, vaya a **Sentinel** > **Administración de amenazas** > **Búsqueda**. 

2. Seleccione la pestaña **Marcadores** para ver la lista de marcadores.

3. Use las opciones de filtro o el cuadro de búsqueda para ayudarle a encontrar un marcador específico.

4. Seleccione marcadores individuales y vea detalles sobre ese marcador en el panel de detalles de la derecha.

5. Realice los cambios necesarios y estos se guardarán automáticamente.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Exploración de marcadores en el gráfico de investigación

> [!IMPORTANT]
> La exploración de marcadores en el gráfico de investigación y el gráfico en sí se encuentran actualmente en versión preliminar pública.
> Estas características se ofrecen sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. En Azure Portal, vaya a **Sentinel** > **Administración de amenazas** > **Búsqueda** > **Marcadores** y seleccione los marcadores que desea investigar.

2. En los detalles del marcador, asegúrese de que al menos una entidad está asignada. Por ejemplo, para **ENTIDADES**, puede ver las entradas para **IP**, **Máquina** o **Cuenta**.

3. Haga clic en **Investigar** para ver el marcador en el gráfico de investigación.

Para obtener instrucciones sobre cómo usar el gráfico de investigación, consulte [Use the investigation graph to deep dive](investigate-cases.md#use-the-investigation-graph-to-deep-dive) (Uso del gráfico de investigación para un análisis a fondo).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Agregar marcadores a un incidente nuevo o a otro ya existente

> [!IMPORTANT]
> La adición de marcadores a un incidente nuevo o ya existente se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. En Azure Portal, vaya a **Sentinel** > **Administración de amenazas** > **Búsqueda** > **Marcadores** y seleccione los marcadores que desea agregar a un incidente.

2. Seleccione **Acciones de incidente (versión preliminar)** en la barra de comandos:
    
    > [!div class="mx-imgBorder"]
    > ![Agregar marcadores a un incidente](./media/bookmarks/incident-actions.png)

3. Seleccione **Crear incidente** o **Agregar a incidente existente**, según sea necesario. A continuación:
    
    - Para un nuevo incidente: Opcionalmente, actualice los detalles del incidente y, a continuación, seleccione **Crear**.
    - Para agregar un marcador a un incidente existente: Seleccione un incidente y, a continuación, seleccione **Agregar**. 

Para ver el marcador en el incidente: Vaya a **Sentinel** > **Administración de amenazas** > **Incidentes** y seleccione el incidente con el marcador. Seleccione **Ver detalles completos** y, a continuación, seleccione la pestaña **Marcadores**.

> [!TIP]
> Como alternativa a la opción **Acciones de incidente (versión preliminar)** de la barra de comandos, puede usar el menú contextual (**...**) para uno o varios marcadores y seleccionar las opciones para **Crear incidente**, **Agregar a incidente existente** y **Quitar del incidente**. 

## <a name="view-bookmarked-data-in-logs"></a>Ver datos marcados en registros

Para ver consultas y resultados marcados o sus historiales, seleccione el marcador que quiera en la pestaña **Búsqueda** > **Marcadores** y use los vínculos que aparecen en el panel de detalles: 

- Seleccione **Ver consulta de origen** para ver la consulta de origen en el panel **Registros**.

- Haga clic en **View bookmark logs** (Ver registros de marcador) para ver todos los metadatos de marcadores, como quién realizó la actualización, los valores actualizados y la hora en que se produjo la actualización.

También puede ver los datos sin formato de todos los marcadores seleccionando **Registros de marcadores** de la barra de comandos en la pestaña **Búsqueda** > **Marcadores**:

> [!div class="mx-imgBorder"]
> ![Registros de marcadores](./media/bookmarks/bookmark-logs.png)

Esta vista muestra todos los marcadores con los metadatos asociados. Puede usar consultas en el [lenguaje de consulta de palabras clave](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) para filtrar hasta encontrar la versión más reciente del marcador específico que está buscando.

> [!NOTE]
> Puede haber un retraso importante (medido en minutos) desde que crea un marcador hasta que aparece en la pestaña **Marcadores**.

## <a name="delete-a-bookmark"></a>Eliminar un marcador
 
1.  En Azure Portal, vaya a **Sentinel** > **Administración de amenazas** > **Búsqueda** > **Marcadores** y seleccione los marcadores que desea eliminar. 

2. Haga clic con el botón derecho en las selecciones y seleccione la opción para eliminar el marcador o los marcadores. Por ejemplo, **Eliminar marcador** si ha seleccionado un solo marcador y **Delete 2 bookmarks** (Eliminar 2 marcadores) si ha seleccionado dos marcadores.
    
Al eliminar el marcador, este se quita de la lista de la pestaña **Bookmarks** (Marcadores). La tabla **HuntingBookmark** del área de trabajo de Log Analytics seguirá conteniendo entradas de marcadores anteriores, pero la entrada más reciente cambiará el valor de **SoftDelete** a true, lo que permite excluir marcadores antiguos del filtro. Cuando un marcador se elimina, no se quitan las entidades de la experiencia de investigación que estén relacionadas con otros marcadores o alertas. 


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a realizar una investigación de búsqueda usando marcadores en Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:


- [Búsqueda de amenazas con Azure Sentinel, versión preliminar](hunting.md)
- [Uso de cuadernos de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)