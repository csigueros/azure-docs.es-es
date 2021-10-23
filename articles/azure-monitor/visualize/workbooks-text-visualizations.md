---
title: Visualizaciones de texto de libros de Azure Monitor
description: Aprenda sobre todas las visualizaciones de texto de libros de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 9b58d29bc46f086d10534572a27e83f5eba59f84
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131664"
---
# <a name="text-visualizations"></a>Visualizaciones de texto

Los libros permiten a los autores incluir bloques de texto en sus libros. El texto puede ser un análisis humano de la telemetría, información para ayudar a los usuarios a interpretar los datos, los encabezados de sección, etc.

[![Captura de pantalla de la tabla de texto Apdex](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

El texto se agrega a través de un control Markdown que proporciona el control de formato completo. Aquí se incluyen diferentes estilos de encabezado y fuente, hipervínculos, tablas, etc.

Modo de edición:

![Captura de pantalla de un paso de texto en modo de edición.](./media/workbooks-text-visualizations/text-edit-mode.png)

Modo de vista previa:

![Captura de pantalla de un paso de texto en modo de edición en la pestaña de vista previa.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Agregar un control de texto

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas **Editar**.
2. Use el vínculo **Agregar texto** para agregar un control de texto al libro.
3. Agregue Markdown en el campo del editor.
4. Use la opción *Estilo de texto* para cambiar entre Markdown sin formato y Markdown encapsulado con el estilo estándar de información, advertencia, correcto o error de Azure Portal.
5. Use la pestaña **Vista previa** para ver el aspecto que tendrá el contenido. Durante la edición, la vista previa mostrará el contenido dentro de un área de la barra de desplazamiento para limitar su tamaño; sin embargo, en tiempo de ejecución, el contenido de Markdown se expandirá para rellenar el espacio necesario, sin barras de desplazamiento.
6. Seleccione el botón **Edición finalizada** para finalizar la edición del paso.

> [!TIP]
> Use esta [hoja de referencia rápida de Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) para obtener información acerca de las diferentes opciones de formato.

## <a name="text-styles"></a>Estilos de texto

Los siguientes estilos de texto están disponibles para el paso de texto:

| Estilo     | Explicación                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | No se aplica ningún formato adicional.                                                      |
| `info`    | Estilo de "información" del portal, con un icono `ℹ` o similar y, por lo general, un fondo azul.      |
| `error`   | Estilo de "error" del portal, con un icono `❌` o similar y, por lo general, un fondo rojo.     |
| `success` | Estilo de "correcto" del portal, con un icono `✔` o similar y, por lo general, un fondo verde.  |
| `upsell`  | Estilo de "incremento de ventas" del portal, con un icono `🚀` o similar y, por lo general, un fondo púrpura. |
| `warning` | Estilo de "advertencia" del portal, con un icono `⚠` o similar y, por lo general, un fondo azul.   |

En lugar de seleccionar un estilo específico, también puede elegir un parámetro de texto como origen del estilo. El valor del parámetro debe ser uno de los valores de texto anteriores. Los valores ausentes o no reconocidos se tratarán como estilo `plain`.

Ejemplo de estilo de información:

![Captura de pantalla de la apariencia del estilo de información.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Ejemplo de estilo de advertencia:

![Captura de pantalla de la apariencia del estilo de advertencia.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a crear un [gráfico en los libros](workbooks-chart-visualizations.md).
* Aprenda a crear una [cuadrícula en los libros](workbooks-grid-visualizations.md).
