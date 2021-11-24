---
title: Uso de marcadores de búsqueda para las investigaciones de datos en Microsoft Sentinel
description: En este artículo se describe cómo usar los marcadores de búsqueda de Microsoft Sentinel para llevar un seguimiento de los datos.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 7ddfa01141ac95b78371d8fbe0a239e09057481a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524747"
---
# <a name="keep-track-of-data-during-hunting-with-microsoft-sentinel"></a>Realizar un seguimiento de los datos durante la búsqueda con Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

La búsqueda de amenazas suele conllevar revisar una infinidad de datos de registro en busca de pruebas que evidencien comportamientos malintencionados. Durante este proceso, los investigadores dan con eventos que quieren recordar, volver a ver y analizar como parte de la validación de posibles hipótesis y para entender la historia completa de un riesgo.

Los marcadores de búsqueda de Microsoft Sentinel le ayudarán a eso precisamente, ya que conservan las consultas que se han ejecutado en **Microsoft Sentinel: registros**, así como los resultados de consulta que considere importantes. También puede registrar las observaciones realizadas dentro de un contexto y hacer referencia a sus hallazgos agregando notas y etiquetas. Los datos marcados están visibles tanto para usted como para sus compañeros de equipo para, así, colaborar de forma más sencilla.

Ahora puede identificar y solucionar brechas en la cobertura de la técnica MITRE ATT&CK en todas las consultas de búsqueda, mediante la asignación de consultas de búsqueda personalizadas a técnicas de MITRE ATT&CK.

> [!IMPORTANT]
>
> La asignación de técnicas de MITRE ATT&CK a marcadores se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

También puede investigar más tipos de entidades mientras busca con marcadores mediante la asignación del conjunto completo de tipos de entidad e identificadores admitidos por Microsoft Sentinel Analytics en las consultas personalizadas. Esto le permite USAR marcadores para explorar las entidades devueltas en los resultados de las consultas de búsqueda mediante las [páginas de entidad](entities-in-azure-sentinel.md#entity-pages), los [incidentes](investigate-cases.md) y el [gráfico de investigación](investigate-cases.md#use-the-investigation-graph-to-deep-dive). Si un marcador captura los resultados de una consulta de búsqueda, hereda automáticamente la técnica MITRE ATT&CK de la consulta y las asignaciones de entidades.

> [!IMPORTANT]
>
> La asignación de un conjunto ampliado de tipos de entidad e identificadores a marcadores se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>

Si encuentra algo que deba abordarse con urgencia mientras busca en los registros, puede crear fácilmente un marcador y promoverlo a un incidente o agregarlo a un incidente existente. Para obtener más información sobre los incidentes, consulte [Investigación de incidentes con Microsoft Sentinel](investigate-cases.md).

Si ha encontrado algo que merece la pena marcar, pero no es urgente, puede crear un marcador y, a continuación, volver a visitar los datos marcados en cualquier momento en la pestaña **Marcadores** del panel **Búsqueda**. Puede usar opciones de filtrado y de búsqueda para encontrar rápidamente datos concretos de la investigación actual.

Puede ver los datos marcados seleccionando **Investigar** en los detalles del marcador. Esto inicia la experiencia de investigación, en la que puede ver, investigar y comunicar visualmente sus hallazgos con un diagrama gráfico de entidades y una escala de tiempo interactivos.

Si lo desea, también puede ver los datos marcados directamente en la tabla **HuntingBookmark** del área de trabajo de Log Analytics. Por ejemplo:

:::image type="content" source="media/bookmarks/bookmark-table.png" alt-text="Captura de pantalla de la visualización de la tabla de marcadores de búsqueda." lightbox="media/bookmarks/bookmark-table.png":::

La visualización de los marcadores de la table le permite filtrar, resumir y combinar los datos marcados con otros orígenes de datos, lo que facilita la búsqueda de pruebas definitivas.

## <a name="add-a-bookmark"></a>Agregar un marcador

1. En Azure Portal, vaya a **Microsoft Sentinel** > **Administración de amenazas** > **Búsqueda** para ejecutar consultas con el fin de detectar comportamientos sospechosos y anómalos.

1. Seleccione una de las consultas de búsqueda y, a la derecha, en los detalles de la consulta de búsqueda, seleccione **Ejecutar consulta**.

1. Seleccione **View query results** (Ver resultados de la consulta). Por ejemplo:

    :::image type="content" source="media/bookmarks/new-processes-observed-example.png" alt-text="Captura de pantalla de la visualización de los resultados de la consulta de búsqueda de Microsoft Sentinel.":::

    Esta acción abre los resultados de la consulta en el panel **Registros**.

1. En la lista de resultados de la consulta de registro, use las casillas para seleccionar una o más filas que contengan la información que le interese.

1. Seleccione **Agregar marcador**:

    :::image type="content" source="media/bookmarks/add-hunting-bookmark.png" alt-text="Captura de pantalla de la adición de un marcador de búsqueda a la consulta." lightbox="media/bookmarks/add-hunting-bookmark.png":::

1. A la derecha, en el panel **Agregar marcador**, puede actualizar el nombre del marcador y agregar etiquetas y notas que le ayuden a identificar el interés que tiene el elemento.

1. **(Versión preliminar)** Opcionalmente, los marcadores se pueden asignar a las técnicas o subtécnicas de MITRE ATT&CK. Las asignaciones de MITRE ATT&CK se heredan de los valores asignados en las consultas de búsqueda, pero también puede crearlas manualmente. Seleccione la táctica MITRE ATT&CK asociada a la técnica deseada del menú desplegable en la sección **Tácticas y técnicas (versión preliminar)** del panel **Agregar marcador**. El menú se expandirá para mostrar todas las técnicas MITRE ATT&CK, y podrá seleccionar varias técnicas y subtécnicas en este menú.

    :::image type="content" source="media/bookmarks/mitre-attack-mapping.png" alt-text="Captura de pantalla de cómo asignar tácticas y técnicas de Mitre Attack a los marcadores.":::

1. **(Versión preliminar)** Ya puede extraer un conjunto ampliado de entidades de los resultados de consultas marcadas para realizar una investigación más profunda. En la sección **Asignación de entidades (versión preliminar)** , use los menús desplegables para seleccionar los [identificadores y tipos de entidades](entities-reference.md). A continuación, asigne la columna en los resultados de la consulta que contiene el identificador correspondiente. Por ejemplo:

    :::image type="content" source="media/bookmarks/map-entity-types-bookmark.png" alt-text="Captura de pantalla para asignar tipos de entidad a los marcadores de búsqueda.":::

    Para ver el marcador en el gráfico de investigación, debe asignar al menos una entidad. Todavía se admiten las asignaciones de entidades a los tipos de entidades de cuenta, host, IP y URL creadas antes de esta versión preliminar, lo que conserva la compatibilidad con versiones anteriores.

1. Haga clic en **Guardar** para confirmar los cambios y agregar el marcador. Todos los datos marcados se comparten con otros analistas, y es un primer paso hacia una experiencia de investigación colaborativa.

> [!NOTE]
> Los resultados de la consulta de registro admiten marcadores cada vez que se abre este panel desde Microsoft Sentinel. Por ejemplo, seleccione **General** > **Registros** en la barra de navegación, seleccione vínculos de eventos en el gráfico de investigación o seleccione un identificador de alerta en los detalles completos de un incidente (actualmente en versión preliminar). No se pueden crear marcadores si el panel **Registros** se abre desde otras ubicaciones como, por ejemplo, directamente desde Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Ver y actualizar marcadores

1. En Azure Portal, vaya a **Microsoft Sentinel** > **Administración de amenazas** > **Búsqueda**.

2. Seleccione la pestaña **Marcadores** para ver la lista de marcadores.

3. Use las opciones de filtro o el cuadro de búsqueda para ayudarle a encontrar un marcador específico.

4. Seleccione marcadores individuales y vea detalles sobre ese marcador en el panel de detalles de la derecha.

5. Realice los cambios necesarios y estos se guardarán automáticamente.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Exploración de marcadores en el gráfico de investigación

1. En Azure Portal, vaya a la pestaña **Microsoft Sentinel** > **Administración de amenazas** > **Búsqueda** > **Marcadores** y seleccione los marcadores que desea investigar.

2. En los detalles del marcador, asegúrese de que al menos una entidad está asignada.

3. Haga clic en **Investigar** para ver el marcador en el gráfico de investigación.

Para obtener instrucciones sobre cómo usar el gráfico de investigación, consulte [Use the investigation graph to deep dive](investigate-cases.md#use-the-investigation-graph-to-deep-dive) (Uso del gráfico de investigación para un análisis a fondo).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Agregar marcadores a un incidente nuevo o a otro ya existente

1. En Azure Portal, vaya a la pestaña **Microsoft Sentinel** > **Administración de amenazas** > **Búsqueda** > **Marcadores** y seleccione los marcadores que desea agregar a un incidente.

2. Seleccione **Acciones de incidente** en la barra de comandos:

    :::image type="content" source="media/bookmarks/incident-actions.png" alt-text="Captura de pantalla de la adición de marcadores al incidente.":::

3. Seleccione **Crear incidente nuevo** o **Agregar a incidente existente**, según sea necesario. A continuación:

    - Para un nuevo incidente: Opcionalmente, actualice los detalles del incidente y, a continuación, seleccione **Crear**.
    - Para agregar un marcador a un incidente existente: Seleccione un incidente y, a continuación, seleccione **Agregar**.

Para ver el marcador del incidente, vaya a **Microsoft Sentinel** > **Administración de amenazas** > **Incidentes** y seleccione el incidente con su marcador. Seleccione **Ver detalles completos** y, a continuación, seleccione la pestaña **Marcadores**.

> [!TIP]
> Como alternativa a la opción **Acciones de incidente** de la barra de comandos, puede usar el menú contextual ( **...** ) para uno o varios marcadores y seleccionar las opciones para **Crear incidente**, **Agregar a incidente existente** y **Quitar del incidente**.

## <a name="view-bookmarked-data-in-logs"></a>Ver datos marcados en registros

Para ver consultas y resultados marcados o sus historiales, seleccione el marcador que quiera en la pestaña **Búsqueda** > **Marcadores** y use los vínculos que aparecen en el panel de detalles:

- Seleccione **Ver consulta de origen** para ver la consulta de origen en el panel **Registros**.

- Haga clic en **View bookmark logs** (Ver registros de marcador) para ver todos los metadatos de marcadores, como quién realizó la actualización, los valores actualizados y la hora en que se produjo la actualización.

También puede ver los datos sin formato de todos los marcadores seleccionando **Registros de marcadores** de la barra de comandos en la pestaña **Búsqueda** > **Marcadores**:

:::image type="content" source="media/bookmarks/bookmark-logs.png" alt-text="Captura de pantalla del comando de registros de marcadores.":::

Esta vista muestra todos los marcadores con los metadatos asociados. Puede usar las consultas en el [lenguaje de consulta de Kusto](/azure/data-explorer/kql-quick-reference) (KQL) para filtrar hasta encontrar la versión más reciente del marcador específico que está buscando.

> [!NOTE]
> Puede haber un retraso importante (medido en minutos) desde que crea un marcador hasta que aparece en la pestaña **Marcadores**.

## <a name="delete-a-bookmark"></a>Eliminar un marcador

1. En Azure Portal, vaya a la pestaña **Microsoft Sentinel** > **Administración de amenazas** > **Búsqueda** > **Marcadores** y seleccione los marcadores que desea eliminar.

2. Haga clic con el botón derecho en el contenido seleccionado y use la opción para eliminar el número de marcadores que ha seleccionado.

Al eliminar el marcador, este se quita de la lista de la pestaña **Bookmarks** (Marcadores). La tabla **HuntingBookmark** del área de trabajo de Log Analytics seguirá conteniendo entradas de marcadores anteriores, pero la entrada más reciente cambiará el valor de **SoftDelete** a true, lo que permite excluir marcadores antiguos del filtro. Cuando un marcador se elimina, no se quitan las entidades de la experiencia de investigación que estén relacionadas con otros marcadores o alertas.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a ejecutar una investigación de búsqueda usando marcadores en Microsoft Sentinel. Para obtener más información sobre Microsoft Sentinel, consulte los siguientes artículos:

- [Búsqueda de amenazas con Azure Sentinel, versión preliminar](hunting.md)
- [Uso de cuadernos de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)
- [Búsqueda de amenazas con Microsoft Sentinel (módulo de aprendizaje)](/learn/modules/hunt-threats-sentinel/)
