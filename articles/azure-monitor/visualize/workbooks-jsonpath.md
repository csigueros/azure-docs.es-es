---
title: 'Libros de Azure Monitor: transformación de datos JSON con JSONPath'
description: Cómo usar JSONPath en los libros de Azure Monitor para transformar los resultados de los datos JSON que ha devuelto un punto de conexión consultado al formato deseado.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: f43fde52d8b85483a5d10548053fb7ca421f93c1
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132386"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Uso de JSONPath para transformar datos JSON en libros

Los libros pueden consultar datos de muchos orígenes. Algunos puntos de conexión, como [Azure Resource Manager](../../azure-resource-manager/management/overview.md) o un punto de conexión personalizado, pueden devolver resultados en JSON. Si los datos JSON devueltos por el punto de conexión consultado no se han configurado en el formato deseado, se puede usar JSONPath para transformar los resultados.

JSONPath es un lenguaje de consulta para JSON similar a XPath para XML. Al igual que XPath, JSONPath permite la extracción y el filtrado de datos a partir de una estructura JSON.

Mediante la transformación JSONPath, los creadores de los libros pueden convertir JSON en una estructura de tabla. A continuación, la tabla se puede usar para trazar [visualizaciones de libros](./workbooks-overview.md#visualizations).

## <a name="using-jsonpath"></a>Uso de JSONPath

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas *Editar*.
2. Use el vínculo *Agregar* > *Agregar consulta* para agregar un control de consulta al libro.
3. Seleccione el origen de datos como *JSON*.
4. Use el editor de JSON para introducir el siguiente fragmento de código JSON.
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Supongamos que se proporciona el objeto JSON anterior como una representación del inventario de un almacén. Nuestra tarea consiste en crear una tabla de los libros disponibles en el almacén mediante una lista de los títulos, autores y precios.

1. Seleccione la pestaña *Configuración del resultado* y cambie el formato del resultado a *Ruta de acceso JSON*.
2. Aplique la siguiente configuración de ruta de acceso JSON:

    Tabla de rutas de acceso JSON: `$.store.books`. Este campo representa la ruta de acceso de la raíz de la tabla. En este caso, nos interesa el inventario de libros de la tienda. La ruta de acceso de la tabla filtra el archivo JSON para obtener la información de los libros.

   | Id. de columna | Ruta de acceso JSON de la columna |
   |:-----------|:-----------------|
   | Título      | `$.title`        |
   | Autor     | `$.author`       |
   | Price      | `$.price`        |

    Los id. de columna serán los encabezados de las columnas. Los campos de rutas de acceso JSON de columna representan la ruta de acceso de la raíz de la tabla al valor de la columna.

1. Haga clic en *Ejecutar consulta* para aplicar la configuración anterior.

![ Edición del elemento de consulta con origen de datos JSON y el formato de resultado de la ruta de acceso JSON](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Pasos siguientes
- [Información general sobre libros](./workbooks-overview.md)
- [Grupos en los libros de Azure Monitor](workbooks-groups.md)