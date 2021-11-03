---
title: Actualización parcial de documentos en Azure Cosmos DB
description: Obtenga información sobre la actualización parcial de documentos en Azure Cosmos DB.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2fd4b082d5c09cb9329451e9f4f165a1699c85a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093590"
---
# <a name="partial-document-update-in-azure-cosmos-db"></a>Actualización parcial de documentos en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La característica de actualización parcial de documentos de Azure Cosmos DB (también conocida como Patch API) proporciona una manera cómoda de modificar un documento en un contenedor. Actualmente, para actualizar un documento, el cliente debe leerlo, ejecutar comprobaciones de control de simultaneidad optimista (si es necesario), actualizar el documento localmente y, a continuación, enviarlo a través de la conexión como una llamada API de reemplazo del documento completo. 

La característica de actualización parcial de documentos mejora considerablemente esta experiencia. El cliente puede enviar solo las propiedades o campos modificados de un documento sin realizar una operación de reemplazo completa del documento. Entre las principales ventajas de esta característica están las siguientes:

- **Mejora de la productividad de los desarrolladores**: proporciona una cómoda API para facilitar el uso y la capacidad de actualizar condicionalmente el documento. 
- **Mejoras de rendimiento**: evita ciclos de CPU adicionales en el lado del cliente y reduce la latencia de un extremo a otro y el ancho de banda de la red.
- **Escrituras en varias regiones**: admite la resolución automática y transparente de conflictos con actualizaciones parciales en rutas de acceso discretas dentro del mismo documento.
 
## <a name="supported-operations"></a>Operaciones compatibles

En la tabla siguiente se resumen las operaciones admitidas por esta característica.

> [!NOTE]
> *Ruta de acceso de destino* hace referencia a una ubicación dentro del documento JSON.

| **Tipo de operación** | **Descripción** |
| ------------ | -------- |
| **Add (Agregar)**      | `Add` realiza una de las siguientes acciones, en función de la ruta de acceso de destino: <br/><ul><li>Si la ruta de acceso de destino especifica un elemento que no existe, se agrega.</li><li>Si la ruta de acceso de destino especifica un elemento que ya existe, su valor se reemplaza.</li><li>Si la ruta de acceso de destino es un índice de matriz válido, se insertará un nuevo elemento en la matriz en el índice especificado. Desplaza los elementos existentes a la derecha.</li><li>Si el índice especificado es igual a la longitud de la matriz, anexará un elemento a la matriz. En lugar de especificar un índice, también puede usar el carácter `-`. También hará que el elemento se anexe a la matriz.</li></ul> <br/> **Nota:** Si se especifica un índice mayor que la longitud de la matriz, se producirá un error.|
| **Establecimiento**      | La operación `Set` es similar a `Add` excepto en el caso del tipo de datos de matriz: si la ruta de acceso de destino es un índice de matriz válido, se actualiza el elemento existente en ese índice.| 
| **Sustituya**      | La operación `Replace` es similar a `Set` excepto que sigue la semántica _estricta_ de solo sustitución. Si la ruta de acceso de destino especifica un elemento o una matriz que no existe, se produce un error.  | 
| **Remove**     | `Remove` realiza una de las siguientes acciones, en función de la ruta de acceso de destino: <br/><ul><li>Si la ruta de acceso de destino especifica un elemento que no existe, se produce un error. </li><li> Si la ruta de acceso de destino especifica un elemento que ya existe, se quita. </li><li> Si la ruta de acceso de destino es un índice de matriz, se eliminará y los elementos situados por encima del índice especificado se desplazarán una posición a la izquierda.</li></ul> <br/> **Nota:** Si se especifica un índice igual o mayor que la longitud de la matriz, se producirá un error.  |
| **Incremento**     | Este operador incrementa un campo por el valor especificado. Puede aceptar valores positivos y negativos. Si el campo no existe, lo crea y lo establece en el valor especificado. |

## <a name="supported-modes"></a>Modos admitidos

La característica de actualización parcial de documentos admite los siguientes modos de funcionamiento. Consulte el documento [Introducción](partial-document-update-getting-started.md) para obtener ejemplos de código.

* **Revisión de un documento único**: Puede aplicar revisiones a un único documento en función de su identificador y la clave de partición. Es posible ejecutar varias operaciones de revisión en un único documento. El [límite máximo es de 10 operaciones.](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)

* **Revisión de varios documentos:** se pueden aplicar revisiones a varios documentos dentro de la misma clave de partición como [parte de una transacción](transactional-batch.md). Esta transacción solo se confirma si todas las operaciones se realizan correctamente en el orden en que se describen. Si se produce un error en alguna de las operaciones, se revierte toda la transacción.

* **Actualización condicional**: Para los modos mencionados anteriormente, también es posible agregar un predicado de filtro similar a SQL (por ejemplo, *from c where c.taskNum = 3*) de modo que se produzca un error en la operación si no se cumple la condición previa especificada en el predicado. 

* También puede usar las API masivas de los SDK admitidos para ejecutar una o varias operaciones de revisión en varios documentos.


## <a name="similarities-and-differences"></a>Similitudes y diferencias

### <a name="add-vs-set"></a>Comparación entre Add y Set

La operación `Set` es similar a `Add` para todos los tipos de datos excepto `Array`. Una operación `Add` en cualquier índice (válido) da como resultado la adición de un elemento en el índice especificado y los elementos existentes en la matriz terminan desplazándose a la derecha. Esto contrasta con la operación `Set`, que actualiza el elemento existente en el índice especificado. 

### <a name="add-vs-replace"></a>Comparación entre Add y Replace

La operación `Add` agrega una propiedad si aún no existe (incluido el tipo de datos `Array`). Se producirá un error en la operación `Replace` si la propiedad no existe (también se aplica al tipo de datos `Array`).

### <a name="set-vs-replace"></a>Comparación entre Set y Replace

La operación `Set` agrega una propiedad si aún no existe (excepto si había un valor de `Array`). Se producirá un error en la operación `Replace` si la propiedad no existe (también se aplica al tipo de datos `Array`).

> [!NOTE]
> `Replace` es un buen candidato cuando el usuario espera que algunas de las propiedades estén siempre presentes, y le permite su aserción o aplicación.

## <a name="partial-document-update-specification"></a>Especificación de actualización parcial de documentos

El componente orientado al cliente de la funcionalidad de actualización parcial de documentos se implementa como una API REST de nivel superior. Este es un ejemplo de cómo Azure Cosmos DB modela una operación `Conditional Add`.

```bash
PATCH /dbs/{db}/colls/{coll}/documents/{doc}
HTTP/1.1
Content-Type:application/json-patch+json

{
   "condition":"from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))", 
   "operations":[ 
      { 
         "op":"add", 
         "path":"amount", 
         "value":80000 
      }
   ]
} 
```

> [!NOTE]
> En este caso, el valor en la ruta de acceso `amount` se establecerá en `80000` (la operación) *solo* si el valor de `TotalDue` es 0 o no está presente (la condición).

## <a name="document-level-vs-path-level-conflict-resolution"></a>Resolución de conflictos de nivel de documento frente a nivel de ruta de acceso  

Si la cuenta de Azure Cosmos DB está configurada con varias regiones de escritura, [los conflictos y las directivas de resolución de conflictos](conflict-resolution-policies.md) son aplicables a nivel de documento, siendo la directiva donde la última escritura prevalece (`LWW`) la que se establece de manera predeterminada para la resolución de conflictos. En el caso de las actualizaciones parciales de documentos, las operaciones de revisión en varias regiones detectan y resuelven conflictos en un nivel de ruta de acceso más granular.

Esto se puede entender mejor con un ejemplo.

Supongamos que tiene el siguiente documento en Azure Cosmos DB:

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345",
      "67890"
   ], 
   "level":"gold",
} 
```

Las siguientes operaciones de revisión las emiten simultáneamente distintos clientes en regiones diferentes:

- atributo `Set` `/level` a platinum  
- `Remove` 67890 desde `/phone`

:::image type="content" source="./media/partial-document-update/patch-multi-region-conflict-resolution.png" alt-text="Imagen que muestra la resolución de conflictos en operaciones simultáneas de actualización parcial de varias regiones." border="false" lightbox="./media/partial-document-update/patch-multi-region-conflict-resolution.png":::

Puesto que las solicitudes de revisión se realizaron en rutas de acceso que no entran en conflicto dentro del documento, estos conflictos se resolverán de forma automática y transparente (a diferencia de la directiva donde la última escritura prevalece a nivel de documento).    

El cliente verá el siguiente documento después de la resolución de conflictos:

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345"
   ], 
   "level":"platinum",
} 
```

> [!NOTE]
> En caso de que se aplique una revisión simultánea a la misma propiedad de un documento en varias regiones, se aplican las [directivas de resolución de conflictos](conflict-resolution-policies.md) normales.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [cómo empezar a trabajar](partial-document-update-getting-started.md) con la actualización parcial de documentos mediante .NET, Java y Node.
- [Preguntas más frecuentes](partial-document-update-faq.yml) sobre la actualización parcial de documentos
