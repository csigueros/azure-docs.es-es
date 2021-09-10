---
title: Uso de listas de seguimiento de Azure Sentinel
description: En este artículo se describe cómo usar listas de seguimiento de Azure Sentinel para crear listas de permitidos o de bloqueados, enriquecer datos de eventos y ayudar a investigar amenazas.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 07/11/2021
ms.openlocfilehash: be35be9211ce9d183ab19d768d504798b7150a33
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179374"
---
# <a name="use-azure-sentinel-watchlists"></a>Uso de listas de seguimiento de Azure Sentinel

Las listas de seguimiento de Azure Sentinel permiten la recopilación de datos de orígenes de datos externos para la correlación con los eventos en el entorno de Azure Sentinel. Una vez que crea las listas de seguimiento, puede usarlas búsquedas, reglas de detección, búsqueda de amenazas y cuaderno de estrategias de respuesta. Las listas de seguimiento se almacenan en el área de trabajo de Azure Sentinel como pares de nombre-valor, y se almacenan en caché para obtener un rendimiento óptimo de las consultas y una baja latencia.

> [!IMPORTANT]
> Actualmente, las características indicadas están en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

Algunos escenarios habituales para usar listas de seguimiento son:

- **Investigación de amenazas** y respuesta a los incidentes con una rápida importación de direcciones IP, hashes de archivo y otros datos de archivos CSV. Una vez que se importan, puede usar los pares nombre-valor de las listas de seguimiento para combinaciones y filtros en reglas de alerta, búsqueda de amenazas, libros, cuadernos y consultas generales.

- **Importación de datos comerciales** como una lista de reproducción. Por ejemplo, importe listas de usuarios con privilegios de acceso del sistema o de empleados despedidos. A continuación, use la lista de seguimiento para crear listas de permitidos y de bloqueados que se usen para detectar o impedir que dichos usuarios inicien sesión en la red.

- **Reducción de la fatiga por alerta**. Cree listas de permitidos para suprimir las alertas de un grupo de usuarios, como usuarios de direcciones IP autorizadas que realizan tareas que normalmente desencadenarían la alerta, y evitar que eventos benignos se conviertan en alertas.

- **Enriquecimiento de datos de eventos**. Use listas de seguimiento para enriquecer los datos de eventos con combinaciones de nombre-valor obtenidas de orígenes de datos externos.

> [!NOTE]
> - El uso de listas de seguimiento debe limitarse a los datos de referencia, ya que no están diseñadas para grandes volúmenes de datos.
>
> - El **número total de elementos activos** de todas las listas de seguimiento de una sola área de trabajo está limitado actualmente a **10 millones**. Los elementos eliminados no se tienen en cuenta en este total. Si necesita hacer referencia a grandes volúmenes de datos, considere la posibilidad de ingerirlos usando [registros personalizados](../azure-monitor/agents/data-sources-custom-logs.md).
>
> - Solo se puede hacer referencia a listas de seguimiento desde dentro de la misma área de trabajo. Actualmente, no se admiten los escenarios entre áreas de trabajo o Lighthouse.

## <a name="create-a-new-watchlist"></a>Creación de una nueva lista de seguimiento

1. En Azure Portal, vaya a **Azure Sentinel** > **Configuración** > **Lista de reproducción** y, a continuación, seleccione **+Agregar nuevo**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="nueva lista de seguimiento" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. En la página **General**, proporcione el nombre, la descripción y el alias de la lista de seguimiento y, a continuación, seleccione **Siguiente: Origen**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="página general de la lista de seguimiento":::

1. En la página **Origen**, seleccione el tipo de conjunto de datos (actualmente solo está disponible CSV), escriba el número de líneas **antes de la fila de encabezado** en el archivo de datos y, a continuación, elija un archivo para cargarlo de una de estas dos maneras:
    1. Haga clic en el vínculo **Buscar archivos** en el cuadro **Cargar archivo** y seleccione el archivo de datos que desea cargar.
    1. Arrastre y coloque el archivo de datos en el cuadro **Cargar archivo**.

    Verá una vista previa de las primeras 50 filas de resultados en la pantalla del Asistente.

1. En el campo **SearchKey**, escriba el nombre de una columna en la lista de visualización que espera usar como combinación con otros datos o como un objeto frecuente de búsquedas. Por ejemplo, si su lista de seguimiento de servidores contiene nombres de país y los códigos de dos letras correspondientes, y espera usar los códigos a menudo para búsquedas o combinaciones, use la columna **Código** como SearchKey.

1. <a name="review-and-create"></a>Seleccione **Siguiente: Revisar y crear**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="página de origen de la lista de seguimiento" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Las cargas de archivos están limitadas actualmente a archivos de hasta 3,8 MB de tamaño.

1. Revise la información, compruebe que es correcta, espere el mensaje *Validación superada* y, a continuación, seleccione **Crear**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="página de revisión de la lista de seguimiento":::

    Una vez que se crea la lista de seguimiento, aparece una notificación.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="notificación de creación correcta de la lista de seguimiento" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="create-a-new-watchlist-using-a-template-public-preview"></a>Creación de una lista de seguimiento usando una plantilla (versión preliminar pública)

1. En Azure Portal, vaya a **Azure Sentinel** > **Configuración** > **Lista de seguimiento** > **Plantillas (versión preliminar)** .

1. Seleccione una plantilla de la lista para ver los detalles a la derecha. A continuación, seleccione **Crear a partir de la plantilla** para crear la lista de seguimiento.

    :::image type="content" source="./media/watchlists/create-watchlist-from-template.png" alt-text="Creación de una lista de seguimiento a partir de una plantilla integrada" lightbox="./media/watchlists/create-watchlist-from-template.png":::

1. Continúe en el **Asistente para lista de seguimiento**:

    - Cuando use una plantilla de lista de seguimiento, los campos **Nombre**, **Descripción** y **Alias de lista de seguimiento** de la lista son de solo lectura.

    - Seleccione **Descargar esquema** para descargar un archivo CSV que contenga el esquema pertinente esperado para la plantilla de lista de seguimiento seleccionada.

    Cada plantilla de lista de seguimiento integrada tiene su propio conjunto de datos enumerados en el archivo CSV asociado a la plantilla. Para obtener más información, consulte [Esquemas de lista de seguimiento integrados](watchlist-schemas.md).

1.  Rellene la versión local del archivo CSV y vuelva a cargarlo en el asistente.

1. Continúe tal y como haría al [crear una lista de seguimiento desde cero](#review-and-create) y, a continuación, use su lista con [consultas](#use-watchlists-in-queries) y [reglas de análisis](#use-watchlists-in-analytics-rules).

## <a name="use-watchlists-in-queries"></a>Uso de listas de seguimiento en consultas

> [!TIP]
> Para obtener un rendimiento óptimo de las consultas, use **SearchKey** (que representa el campo que definió al crear la lista de reproducción) como clave para las combinaciones en las consultas. Observe el ejemplo siguiente.

1. En Azure Portal, vaya a **Azure Sentinel** > **Configuration** > **Watchlist** (Lista de seguimiento), seleccione la lista de seguimiento que quiere usar y, a continuación, seleccione **Ver en Log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="uso de listas de seguimiento en consultas" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png" :::

1. Los elementos de la lista de seguimiento se extraen automáticamente para la consulta y aparecerán en la pestaña **Resultados**. En el ejemplo siguiente se muestran los resultados de la extracción de los campos **Nombre** y **Dirección IP**. **SearchKey** se muestra como su propia columna.

    > [!NOTE]
    > La marca de tiempo en las consultas se omitirá tanto en la UI de la consulta como en las alertas programadas.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="consultas con campos de la lista de seguimiento" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::

1. Puede consultar los datos de cualquier tabla comparándolos con los datos de una lista de reproducción tratando a esta última como una tabla de combinaciones y búsquedas. Use **SearchKey** como clave para la combinación.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('mywatchlist') 
     on $left.RemoteIPCountry == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="consultas en comparación con listas de reproducción como búsqueda" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>Uso de listas de seguimiento en reglas de análisis

> [!TIP]
> Para obtener un rendimiento óptimo de las consultas, use **SearchKey** (que representa el campo que definió al crear la lista de seguimiento) como clave para las combinaciones en las consultas. Observe el ejemplo siguiente.

Para usar listas de seguimiento en reglas de análisis, desde el Azure Portal, vaya a **Azure Sentinel** > **Configuración** > **Análisis** y cree una regla con la función `_GetWatchlist('<watchlist>')` en la consulta.

1. En este ejemplo, cree una lista de reproducción denominada "ipwatchlist" con los valores siguientes:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="lista de cuatro elementos de la lista de reproducción":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-other.png" alt-text="crear lista de reproducción con cuatro elementos":::

1. A continuación, cree la regla de análisis.  En este ejemplo, solo se incluyen eventos de direcciones IP en la lista de reproducción:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    //Use SearchKey for the best performance
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project SearchKey)
    )
    ```

    :::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="uso de listas de seguimiento en reglas de análisis":::

## <a name="view-list-of-watchlists-aliases"></a>Visualización de la lista de alias de listas de seguimiento

Para obtener una lista de los alias de las listas de seguimiento, en Azure Portal, vaya a **Azure Sentinel** > **General** > **Registros** y ejecute la consulta siguiente: `_GetWatchlistAlias`. Puede ver la lista de alias en la pestaña **Resultados**.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="enumeración de listas de seguimiento" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="manage-your-watchlist-in-the-azure-sentinel-portal"></a>Administración de una lista de seguimiento en el portal de Azure Sentinel

También puede ver, editar y crear elementos de listas de seguimiento directamente desde la hoja "Lista de seguimiento" del portal de Azure Sentinel.

1. Para editar su lista de seguimiento, vaya a **Azure Sentinel > Configuración > Lista de seguimiento** y seleccione la lista que quiera editar. A continuación, seleccione **Editar elementos de la lista de seguimiento** en el panel de detalles.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit.png" alt-text="Captura de pantalla en la que se muestra cómo editar una lista de seguimiento" lightbox="./media/watchlists/sentinel-watchlist-edit.png":::

1. Para editar un elemento de la lista de seguimiento, marque la casilla de dicho elemento, edítelo y seleccione **Guardar**. Seleccione **Sí** en el mensaje de confirmación.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-change.png" alt-text="Captura de pantalla en la que se muestra cómo marcar y editar un elemento de una lista de seguimiento":::

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-confirm.png" alt-text="Captura de pantalla en la que se muestra cómo confirmar los cambios":::

1. Para agregar un nuevo elemento a la lista de seguimiento, seleccione **Agregar nuevo** en la pantalla **Editar elementos de la lista de seguimiento**, rellene los campos del panel **Agregar elemento de la lista de seguimiento** y seleccione **Agregar** en la parte inferior del panel.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-add.png" alt-text="Captura de pantalla en la que se muestra cómo agregar un nuevo elemento a la lista de seguimiento":::

## <a name="next-steps"></a>Pasos siguientes
En este documento, aprendió a usar las listas de seguimiento en Azure Sentinel para enriquecer los datos y mejorar las investigaciones. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.