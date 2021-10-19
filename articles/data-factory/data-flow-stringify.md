---
title: Conversión a cadena JSON los análisis de datos en el flujo de datos de asignación
description: Convertir a cadena JSON los tipos de datos complejos
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: e2d0a5993dddccc65109eb623d8f04f11715ac47
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660046"
---
# <a name="stringify-transformation-in-mapping-data-flow"></a>Transformación para convertir a cadena JSON en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use la transformación para convertir a cadena JSON y así convertir los tipos de datos complejos en cadenas. Esto puede ser muy útil cuando necesita almacenar o enviar datos de columna como una entidad de cadena única que puede originarse como un tipo de estructura, asignación o matriz.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMTs9]

## <a name="configuration"></a>Configuración

En el panel de configuración de la transformación para convertir a cadena JSON, primero se selecciona el tipo de datos contenidos en las columnas que quiere analizar en línea. La transformación de análisis también contiene los siguientes valores de configuración.

:::image type="content" source="media/data-flow/stringify.png" alt-text="Configuración de la opción para convertir a cadena JSON":::

### <a name="column"></a>Columna

De forma similar a las columnas derivadas y los agregados, aquí es donde modificará una columna de salida, para lo cual deberá seleccionarla en el selector desplegable. También puede escribir aquí el nombre de una nueva columna. ADF almacenará los datos de origen que se convertirán en cadenas JSON en esta columna. En la mayoría de los casos, es posible que quiera definir una nueva columna que convierta en cadenas JSON el tipo de campo complejo entrante.

### <a name="expression"></a>Expression

Use el generador de expresiones para establecer el campo complejo de origen que se va a convertir en cadenas JSON. Esto puede ser tan sencillo como seleccionar la columna de origen con los datos independientes que quiera convertir en cadenas JSON, o bien puede crear expresiones complejas para analizarlas.

:::image type="content" source="media/data-flow/stringify-2.png" alt-text="Expresiones para convertir a cadena JSON":::

#### <a name="example-expression"></a>Expresión de ejemplo

En este ejemplo, ```body.properties.periods``` es una matriz dentro de una estructura devuelta desde un origen REST.

```
body.properties.periods
```

## <a name="data-flow-script"></a>Script de flujo de datos

```
stringify(mydata = body.properties.periods ? string,
    format: 'json') ~> Stringify1
```

## <a name="next-steps"></a>Pasos siguientes

* Use la [transformación Aplanar](data-flow-flatten.md) para dinamizar las filas en columnas.
* Utilice la [Transformación de análisis](data-flow-parse.md) para convertir tipos incrustados complejos en columnas independientes.
* Use la [transformación de columna derivada](data-flow-derived-column.md) para dinamizar columnas en filas.
