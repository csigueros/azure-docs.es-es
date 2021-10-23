---
title: Representador de barra compuesta de libro de Azure Monitor
description: Obtenga información sobre todas las visualizaciones del representador de barra compuesta de libro de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.openlocfilehash: 1b2946d4def2004d9d620eb253c733fdf31c39cf
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132538"
---
# <a name="composite-bar-renderer"></a>Representador de barra compuesta

El libro permite representar datos mediante una barra compuesta, una barra formada por varias barras.

En la imagen siguiente se muestra la barra compuesta del estado de la base de datos que representa el número de servidores en línea, sin conexión y en estado de recuperación.

![Captura de pantalla de la barra compuesta del estado de la base de datos.](./media/workbooks-composite-bar/database-status.png)

El representador de barra compuesta es compatible con las visualizaciones de cuadrículas, iconos y grafos.

## <a name="adding-composite-bar-renderer"></a>Incorporación de un representador de barra compuesta

1. Para cambiar el libro al modo de edición, seleccione el elemento de la barra de herramientas *Editar*.
2. Seleccione *Agregar* y, luego, *Agregar consulta*.
3. Establezca *Origen de datos* en "JSON" y *Visualización* en "Cuadrícula".
4. Agregue los siguientes datos JSON:

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Ejecute una consulta.
6. Seleccione **Configuración de columnas** para abrir el panel de configuración.
7. Seleccione "total" en *Columnas* y elija "Barra compuesta" para *Representador de columnas*.
8. Establezca la siguiente configuración en *Configuración de barra compuesta*.

| Nombre de columna | Color        |
|-------------|--------------|
| online (en línea)      | Verde        |
| recuperar  | Amarillo       |
| sin conexión     | Rojo (brillante) |

9. Agregue la etiqueta `["online"] of ["total"] are healthy`.
10. En la columna configuración para en línea, sin conexión y recuperar, puede establecer el representador de columnas en "Oculto" (opcional).
11. Seleccione *Etiquetas* en la parte superior y la etiqueta de actualización para la columna total como "Estado de base de datos" (opcional).
12. Seleccione **Aplicar**.

La configuración de la barra compuesta se parecerá a la siguiente captura de pantalla:

![Captura de pantalla de la configuración de columnas de la barra compuesta con la configuración descrita anteriormente.](./media/workbooks-composite-bar/composite-bar-settings.png)

La barra compuesta con la configuración anterior:

![Captura de pantalla de la barra compuesta.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Configuración de la barra compuesta

Seleccione el nombre de columna y el color correspondiente para representar esa columna en ese color como parte de la barra compuesta. Puede insertar, eliminar, subir y bajar filas.

### <a name="label"></a>Etiqueta

La etiqueta de barra compuesta se muestra en la parte superior de la barra compuesta. Puede utilizar una combinación de texto estático, columnas y parámetros.  Si el campo Etiqueta está vacío, se muestra el valor de las columnas actuales como la etiqueta. En el ejemplo anterior, si se deja el campo Etiqueta en blanco, se mostraría el valor de las columnas totales.

Consulte las columnas con `["columnName"]`.

Consulte los parámetros con `{paramName}`.

Tanto el nombre de columna como el nombre de parámetro distinguen mayúsculas de minúsculas. También puede hacer que las etiquetas sean un vínculo seleccionando "Convertir este elemento en vínculo" y, a continuación, agregar la configuración de vínculo.

### <a name="aggregation"></a>Agregación

Las agregaciones son útiles para las visualizaciones Árbol y Agrupar por. Los datos de una columna de la fila de grupo se deciden mediante el conjunto de agregaciones para esa columna. Hay tres tipos de agregaciones aplicables para las barras compuestas: Ninguno, Suma y Heredar.

Para agregar la configuración Agrupar por:

1. En configuración de columnas, vaya a la columna a la que desea agregar la configuración.
2. En *Árbol/Agrupar por valores*, en *Tipo de árbol*, seleccione **Agrupar por**.
3. Seleccione el campo que desea agrupar por.

![Captura de pantalla de la configuración Agrupar por.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>None

Ninguna agregación significa no mostrar resultados para esa columna para las filas de grupo.

![Captura de pantalla de la barra compuesta sin agregación.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

Si la agregación se establece como Suma, la columna de la fila de grupo mostrará la barra compuesta mediante la suma de las columnas que se usan para representarla. La etiqueta también usará la suma de las columnas referidas en ella.

En el ejemplo siguiente, las columnas en línea, sin conexión y recuperar tienen la agregación máxima establecida en ellas y la agregación para la columna total es la suma.

![Captura de pantalla de la barra compuesta con agregación de suma.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Heredar

Si la agregación se establece como Heredar, la columna de la fila de grupo mostrará la barra compuesta mediante la agregación establecida por los usuarios para las columnas que se usan para representarla. Las columnas usadas en la etiqueta también usan la agregación establecida por el usuario. Si el representador de la columna actual es una barra compuesta y se hace referencia a ella en la etiqueta (como "total" en el ejemplo anterior), se utiliza la suma como agregación para esa columna.

En el ejemplo siguiente, las columnas en línea, sin conexión y recuperar tienen la agregación máxima establecida en ellas y la agregación para la columna total es heredar.

![Captura de pantalla de la barra compuesta con el tipo de agregación Heredar.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Ordenar

En las visualizaciones de cuadrícula, la ordenación de las filas de la columna con el representador de barras compuestas funciona según el valor que es la suma de las columnas usadas para representar el equipo de la barra compuesta dinámicamente. En los ejemplos anteriores, el valor usado para la ordenación es la suma de las columnas en línea, recuperar y sin conexión para esa fila en particular.

## <a name="tiles-visualization"></a>Visualización de iconos

1. Seleccione **Agregar** y luego *Agregar consulta*.
2. Cambie el origen de datos a JSON y escriba los datos del [ejemplo anterior](#adding-composite-bar-renderer).
3. Cambie la visualización a *Iconos*.
4. Ejecute una consulta.
5. Seleccione **Configuración de icono**.
6. Seleccione *Izquierda* en Campos de icono.
7. Escriba la siguiente configuración en *Configuración de campo*.
    1. Usar columna: "servidor".
    2. Representador de columnas: "Texto".
8. Seleccione *Inferior* en Campos de icono.
9. Escriba la siguiente configuración en *Configuración de campo*.
    1. Usar columna: "total".
    2. Representador de columnas: "Barra compuesta".
    3. Establezca la siguiente configuración en "Configuración de barra compuesta".

    | Nombre de columna | Color        |
    |-------------|--------------|
    | online (en línea)      | Verde        |
    | recuperar  | Amarillo       |
    | sin conexión     | Rojo (brillante) |

    4. Agregar etiqueta:`["online"] of ["total"] are healthy`.
10. Seleccione **Aplicar**.

Configuración de iconos de la barra compuesta:

![Captura de pantalla de la configuración de iconos de la barra compuesta con la configuración descrita anteriormente.](./media/workbooks-composite-bar/tiles-settings.png)

La vista de barra compuesta para los iconos con la configuración anterior tendrá el siguiente aspecto:

![Captura de mosaicos de la barra compuesta.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Visualización de grafos

Para crear un representador de barra compuesta para la visualización de grafos (tipo Clústeres de Hive), siga las instrucciones que se indican a continuación.

1. Seleccione **Agregar** y luego *Agregar consulta*.
2. Cambie el origen de datos a JSON y escriba los datos del [ejemplo anterior](#adding-composite-bar-renderer).
3. Cambie la visualización a *Grafos*.
4. Ejecute una consulta.
5. Seleccione **Configuración del gráfico**.
6. Seleccione *Contenido en el centro* en Configuración de formato del nodo.
7. Escriba la siguiente configuración en *Configuración de campo*.
    1. Usar columna: "total".
    2. Representador de columnas: "Barra compuesta".
    3. Establezca la siguiente configuración en *Configuración de barra compuesta*.

    |Nombre de columna  |     Color    |
    |-------------|--------------|
    | online (en línea)      | Verde        |
    | recuperar  | Amarillo       |
    | sin conexión     | Rojo (brillante) |

   4. Agregar etiqueta:`["online"] of ["total"] are healthy`.
9. Escriba la siguiente configuración en *Configuración de diseño*.
    1. Tipo de gráfico: **Clústeres de Hive**.
    2. Selección para Id. de nodo: "servidor".
    3. Campo Agrupar por: "Ninguno".
    4. Tamaño del nodo: 100.
    5. Margen entre hexágonos: 5.
    6. Tipo de coloreado: **No**.
1. Seleccione **Aplicar**.
    
Configuración de la barra compuesta para grafos:

![Captura de pantalla de la configuración de grafos de la barra compuesta con la configuración descrita anteriormente.](./media/workbooks-composite-bar/graphs-settings.png)

La vista de barra compuesta para los grafos con la configuración anterior tendrá el siguiente aspecto:

![Captura de pantalla de grafos de barra compuesta con clústeres de Hive.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Pasos siguientes

* [Implemente](../visualize/workbooks-automate.md) los libros con Azure Resource Manager.
* [Controle](./workbooks-access-control.md) y comparta el acceso a los recursos del libro.