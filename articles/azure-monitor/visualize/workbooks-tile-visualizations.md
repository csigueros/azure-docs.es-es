---
title: Visualizaciones de iconos de libros de Azure Monitor
description: Información sobre todas las visualizaciones de iconos de libros de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: a2900b055f95372080d73b80e794b3b5d909db06
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069964"
---
# <a name="tile-visualizations"></a>Visualizaciones de mosaico

Los iconos son una manera útil de presentar los datos de resumen de los libros. La imagen siguiente muestra un caso de uso común de iconos con el resumen a nivel de aplicación sobre una cuadrícula detallada.

[![Captura de pantalla de la vista de un resumen de iconos](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Los iconos del libro permiten mostrar un título, un subtítulo, texto grande, iconos, degradados basados en métricas, minigráficos o minibarras, pies de página, etc.

## <a name="adding-a-tile"></a>Adición de un mosaico

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas _Editar_.
2. Seleccione **Agregar** y *Agregar consulta* para agregar un control de consulta de registro al libro.
3. Seleccione el tipo de consulta **Registro**, el tipo de recurso (por ejemplo, Application Insights) y los recursos de destino.
4. Use el editor de consultas para especificar el KQL para el análisis.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Establezca el tamaño en **Completo**.
6. Establezca la visualización en **Iconos**.
7. Seleccione el botón **Configuración de icono** para abrir el panel de configuración.
    1. En *Título*, establezca:
        * Usar columna: `name`.
    2. En *Izquierda*, establezca:
        * Usar columna: `Requests`.
        * Representador de columnas: `Big Number`.
        * Paleta de colores: `Green to Red`
        * Valor mínimo: `0`.
    3. En *Inferior*, establezca:
        * Usar columna: `appName`.
8. Haga clic en el botón **Guardar y cerrar** de la parte inferior del panel.

[![Captura de pantalla de la vista de resumen de icono con la consulta y la configuración de icono anteriores.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Los iconos en modo de lectura:

[![Captura de pantalla de la vista de resumen de iconos en modo de lectura.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Minigráficos en iconos

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas _Editar_.
2. Agregue un parámetro de intervalo de tiempo llamado `TimeRange`.
    1. Seleccione **Agregar** y *Agregar parámetros*.
    2. En el control de parámetro, seleccione **Agregar parámetro**.
    3. Escriba `TimeRange` en el campo *Nombre de parámetro* y elija `Time range picker` para *Tipo de parámetro*.
    4. Seleccione **Guardar** en la parte superior del panel y, a continuación, **Edición finalizada** en el control de parámetro.
3. Seleccione **Agregar** y *Agregar consulta* para agregar un control de consulta de registro bajo el control de parámetro.
4. Seleccione el tipo de consulta **Registro**, el tipo de recurso (por ejemplo, Application Insights) y los recursos de destino.
5. Use el editor de consultas para especificar el KQL para el análisis.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Seleccione **Ejecutar consulta**. (Asegúrese de establecer `TimeRange` en el valor que prefiera antes de ejecutar la consulta.)
7. Establezca *Visualización* en "Iconos".
8. Establezca *Tamaño* en "Completo".
9. Seleccione **Configuración de icono**.
    1. En *Icono*, establezca:
        * Usar columna: `name`.
    2. En *Subtítulo*, establezca:
        *  Usar columna: `appNAme`.
    3. En *Izquierda*, establezca:
        *  Usar columna:`Requests`.
        * Representador de columnas: `Big Number`.
        * Paleta de colores: `Green to Red`.
        * Valor mínimo: `0`.
    4. En *Inferior*, establezca:
        * Usar columna:`Trend`.
        * Representador de columnas: `Spark line`.
        * Paleta de colores: `Green to Red`.
        * Valor mínimo: `0`.
10. Seleccione **Cerrar y guardar** en la parte inferior de la página.

![Captura de pantalla de la visualización de iconos con un minigráfico](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Tamaños de iconos

El autor tiene una opción para establecer el ancho del icono en la configuración de icono.

* `fixed` (valor predeterminado)

    El comportamiento predeterminado de los iconos es el mismo ancho fijo, aproximadamente unos 160 píxeles de ancho, más el espacio que haya alrededor de los iconos.

    ![Captura de pantalla que muestra iconos de ancho fijo](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Sin embargo, cada título se reducirá o aumentará para ajustarse a su contenido, los iconos se limitan al ancho de la vista de iconos (sin desplazamiento horizontal).

    ![Captura de pantalla que muestra iconos de ancho automático](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Cada título siempre tendrá el ancho completo de la vista de iconos, un título por línea.

     ![Captura de pantalla que muestra iconos de ancho de tamaño completo](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Pasos siguientes

* Los iconos también admiten el representador de la barra compuesta. Para más información, visite la [documentación de la barra compuesta](workbooks-composite-bar.md).
* Para más información sobre los parámetros de tiempo como `TimeRange`, consulte la [documentación de los parámetros de tiempo del libro](workbooks-time.md).