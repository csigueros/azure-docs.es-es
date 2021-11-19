---
title: Mapa de datos en Azure Synapse Analytics | Microsoft Docs
description: Aprenda a usar la herramienta Mapa de datos en Azure Synapse Analytics
author: joshuha-msft
ms.author: joowen
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: 2b8a06fae81cf4c9a032d25699e3d907b498f516
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404291"
---
# <a name="map-data-in-azure-synapse-analytics"></a>Mapa de datos en Azure Synapse Analytics


## <a name="what-is-the-map-data-tool"></a>¿Qué es la herramienta Mapa de datos?


La herramienta Mapa de datos es un proceso guiado para ayudar a los usuarios a crear asignaciones de extracción, transformación y carga de datos y flujos de datos de asignación desde sus datos de origen a tablas de base de datos de lago de Synapse sin escribir código. El inicio de este proceso es que el usuario elige las tablas de destino en las bases de datos de lago de Synapse y, después, asigna sus datos de origen a estas tablas. 

Para más información sobre las bases de datos de lago de Synapse, consulte la [introducción a las plantillas de base de datos de Azure Synapse: Azure Synapse Analytics | Microsoft Docs](overview-database-templates.md)

Mapa de datos proporciona una experiencia guiada en la que el usuario puede generar un flujo de datos de asignación sin tener que empezar con un lienzo en blanco y generar rápidamente un flujo de datos de asignación escalable que se puede ejecutar en las canalizaciones de Synapse.


> [!NOTE] 
> La característica Asignar datos de las canalizaciones de Synapse Analytics está actualmente en versión preliminar pública

## <a name="getting-started"></a>Introducción

La herramienta Mapa de datos se inicia desde la experiencia de base de datos de lago de Synapse. Ahí puede seleccionar la herramienta Mapa de datos para comenzar el proceso. 

![Captura de pantalla que muestra cómo abrir Mapa de datos](./media/overview-map-data/open-map-data.png)


Mapa de datos necesita proceso disponible para ayudar a los usuarios a obtener una vista previa de los datos y a leer el esquema de sus archivos de origen. Después de usar Mapa de datos por primera vez en una sesión, tendrá que preparar un clúster.
![Captura de pantalla que muestra los clústeres de depuración](./media/overview-map-data/debug-map-data.png)

Para empezar, elija el origen de datos que desea asignar a las tablas de la base de datos de lago. Los orígenes de datos admitidos actualmente son las bases de datos de lago de Azure Data Lake Storage Gen 2 y Synapse.
![Captura de pantalla que muestra los orígenes](./media/overview-map-data/sources-map-data.png)

### <a name="file-type-options"></a>Opciones de tipo de archivo
Al elegir un almacén de archivos como Azure Data Lake Storage Gen 2, se admiten los siguientes tipos de archivo:

* Common Data Model
* Texto delimitado
* Parquet


## <a name="create-data-mapping"></a>Creación de una asignación de datos
Asigne un nombre a la asignación de datos y seleccione el destino de la base de datos de lago de Synapse.
![Captura de pantalla que muestra la nomenclatura y el destino](./media/overview-map-data/destination-map-data.png)

## <a name="source-to-target-mapping"></a>Asignación de origen a destino
Elija una tabla de origen principal para asignarla a la tabla de destino de la base de datos de lago de Synapse.
![Captura de pantalla que muestra las reglas de Mapa de datos](./media/overview-map-data/rules-map-data.png)

### <a name="new-mapping"></a>Asignación nueva
Use el botón Nueva asignación para agregar un método de asignación para crear una asignación o transformación.

### <a name="additional-source"></a>Origen adicional
Use el botón Origen adicional para unirse y agregar otro origen a la asignación.

### <a name="preview-data"></a>Vista previa de los datos
La pestaña **Vista previa de los datos** proporciona una instantánea interactiva de los datos de cada transformación. Para más información, consulte [Vista previa de los datos en modo de depuración](../../data-factory/concepts-data-flow-debug-mode.md#data-preview).

### <a name="mapping-methods"></a>Métodos de asignación

Estos son los métodos de asignación que se admiten:

* [Directo](../../data-factory/data-flow-select.md)
* [Clave suplente](../../data-factory/data-flow-surrogate-key.md)
* [Búsqueda](../../data-factory/data-flow-lookup.md)
* [Anulación de dinamización](../../data-factory/data-flow-unpivot.md)
* [Agregada](../../data-factory/data-flow-aggregate.md)
    * Sum
    * Mínima
    * Máxima
    * Primero
    * Último
    * Desviación estándar
    * Media
    * Media
* [Columna derivada](../../data-factory/data-flow-derived-column.md)
    * Trim
    * Upper
    * Inferior
    * Avanzado 


## <a name="create-pipeline"></a>Creación de una canalización

Una vez que haya terminado con las transformaciones de Mapa de datos, seleccione el botón Crear canalización para generar un flujo de datos de asignación y una canalización para depurar y ejecutar la transformación.