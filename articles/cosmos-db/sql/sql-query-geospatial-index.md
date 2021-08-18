---
title: Indexación de datos geoespaciales con Azure Cosmos DB
description: Indexación de datos espaciales con Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 482c35faa2ca7a8cca66820b3f221886b4683f48
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206783"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexación de datos geoespaciales con Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El motor de base de datos de Azure Cosmos DB se ha diseñado para que sea realmente independiente del esquema y proporcione una excelente compatibilidad con JSON. El motor de base de datos optimizado para escritura de Azure Cosmos DB también entiende de forma nativa los datos espaciales representados en el estándar GeoJSON.

En pocas palabras, la geometría se proyecta a partir de coordenadas geodésicas en un plano 2D; después, se divide progresivamente en celdas con un **quadtree**. Estas celdas se asignan a 1D según la ubicación de la celda dentro de una **curva de Hilbert**, que conserva la localidad de los puntos. Además, al indexar los datos de ubicación, estos pasan por un proceso conocido como **teselación**; es decir, todas las celdas que se cruzan en una ubicación se identifican y se almacenan como claves en el índice de Azure Cosmos DB. En el momento de la consulta, los argumentos como puntos y elementos Polygon también se teselan para extraer los intervalos de Id. de celda pertinentes, y luego se usan para recuperar los datos del índice.

Si especifica una directiva de indexación que incluye un índice espacial de `/*` (todas las rutas de acceso), todos los datos encontrados en el contenedor se indexan para que las consultas espaciales resulten eficaces.

> [!NOTE]
> Azure Cosmos DB admite la indexación de elementos Point, LineString, Polygon y MultiPolygon. Si indexa alguno de estos tipos, se indexarán automáticamente todos los demás. En otras palabras, si indexa elementos Polygon, también se indexan los elementos Point, LineString y Multipolygon. La indexación de un nuevo tipo espacial no afecta al tamaño del índice ni al cargo de la RU de escritura, a menos que tenga datos GeoJSON válidos de ese tipo.

## <a name="modifying-geospatial-configuration"></a>Modificación de la configuración geoespacial

En el contenedor, la **Configuración geoespacial** especifica cómo se indexarán los datos geoespaciales. Especifique una **Configuración geoespacial** por contenedor: geografía o geometría.

Puede alternar entre el tipo espacial **geografía** y **geometría** en Azure Portal. Es importante crear una [directiva de indexación de geometría espacial válida con un cuadro de límite](#geometry-data-indexing-examples) antes de cambiar al tipo espacial de geometría.

Aquí se muestra cómo establecer la **Configuración geoespacial** en el **Explorador de datos** en Azure Portal:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Establecimiento de la configuración geoespacial":::

También puede configurar `geospatialConfig` en el SDK de .NET para ajustar la **Configuración geoespacial**:

Si no se especifica, la clase `geospatialConfig` tomará como valor predeterminado el tipo de datos geography. Si modificas la clase `geospatialConfig`, se volverán a indexar todos los datos geoespaciales existentes en el contenedor.

A continuación se muestra un ejemplo de modificación del tipo de datos geoespaciales a `geometry`, mediante la definición de la propiedad `geospatialConfig` y la adición de un objeto **boundingBox**:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Ejemplos de indexación de datos de geografía

El siguiente fragmento JSON muestra una directiva de indexación con la indexación espacial habilitada para el tipo de datos **geography**. Es válido para datos espaciales con el tipo de datos de geografía e indexará cualquier elemento GeoJSON Point, Polygon, MultiPolygon o LineString encontrado en los documentos para las consultas espaciales. Si va a modificar la directiva de indexación mediante Azure Portal, puede especificar el siguiente elemento JSON para que la directiva de indexación permita la indexación espacial en el contenedor:

**JSON de directiva de indexación de contenedores con indexación espacial de geografía**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Si el valor GeoJSON de la ubicación que se encuentra en el documento es incorrecto o no válido, no se indexará para realizar consultas espaciales. Puede validar los valores de ubicación mediante ST_ISVALID y ST_ISVALIDDETAILED.

También puede [modificar la directiva de indexación](../how-to-manage-indexing-policy.md) mediante la CLI de Azure, PowerShell o cualquier SDK.

## <a name="geometry-data-indexing-examples"></a>Ejemplos de indexación de datos de tipo geometry

Con el tipo de datos **geometry**, similar al tipo de datos geography, debe especificar las rutas de acceso y los tipos pertinentes que se van a indexar. Además, también debe especificar un objeto `boundingBox` en la directiva de indexación para indicar el área deseada que se va a indexar para esa ruta de acceso específica. Cada ruta de acceso geoespacial requiere su propio objeto `boundingBox`.

El cuadro de límite está formato por las propiedades siguientes:

- **xmin**: la coordenada x indexada mínima
- **ymin**: la coordenada y indexada mínima
- **xmax**: la coordenada x indexada máxima
- **ymax**: la coordenada y indexada máxima

Se requiere un cuadro de límite porque los datos geométricos ocupan un plano que puede ser infinito. Sin embargo, los índices espaciales requieren un espacio finito. En el caso del tipo de datos **geography**, la Tierra es el límite y no es necesario establecer un cuadro de límite.

Cree un cuadro de límite que contenga todos los datos (o la mayoría). Solo las operaciones calculadas en los objetos que estén completamente dentro del cuadro de límite podrán utilizar el índice espacial. Si el cuadro de límite es más grande de lo necesario, afectará de manera negativa el rendimiento de la consulta.

A continuación se incluye una directiva de indexación de ejemplo que indexa datos de tipo **geometry** con **geospatialConfig** establecido en `geometry`:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

La directiva de indexación anterior tiene un objeto **boundingBox** de (-10, 10) para las coordenadas x y de (-20, 20) para las coordenadas y. El contenedor con la directiva de indexación anterior indexará todos los objetos Point, Polygon, MultiPolygon y LineString que estén totalmente dentro de esta región.

> [!NOTE]
> Si intenta agregar una directiva de indexación con un objeto **boundingBox** a un contenedor con el tipo de datos `geography`, se producirá un error. Debe modificar el objeto **geospatialConfig** del contenedor para que sea `geometry` antes de agregar un objeto **boundingBox**. Puede agregar datos y modificar el resto de la directiva de indexación (como las rutas de acceso y los tipos) antes o después de seleccionar el tipo de datos geoespaciales para el contenedor.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya sabe cómo empezar a trabajar con la compatibilidad geoespacial en Azure Cosmos DB, puede hacer lo siguiente:

* Obtener más información sobre [Consultar Azure Cosmos DB](sql-query-getting-started.md)
* Más información sobre la [consulta de datos espaciales con Azure Cosmos DB](sql-query-geospatial-query.md)
* Más información sobre los [datos de ubicación geoespaciales y GeoJSON en Azure Cosmos DB](sql-query-geospatial-intro.md)
