---
title: Desfase de esquema en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Creación de flujos de datos resistentes en Azure Data Factory y canalizaciones de Synapse Analytics con desfase de esquema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: f82d28ba819e03e1e4c01b6fda11eeab7ede6e0e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815394"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Desfase de esquema en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

El desfase de esquema es el caso en que los orígenes suelen cambiar los metadatos. Los campos, columnas y tipos pueden agregarse, quitarse o cambiarse sobre la marcha. Si no se controla el desfase de esquema, el flujo de datos se vuelve vulnerable a los cambios del origen de datos de nivel superior. Cuando se cambian los campos y las columnas de entrada, los patrones ETL típicos generan un error porque tienden a estar vinculados a los nombres de esos orígenes.

Con el fin de protegerse contra el desfase de esquema, es importante disponer de las funciones en una herramienta de flujo de datos que le permite, como ingeniero de datos, hacer lo siguiente:

* Definir orígenes que tienen nombres de campo, tipos de datos, valores y tamaños mutables
* Definir parámetros de transformación que pueden funcionar con modelos de datos en lugar de con campos y valores codificados de forma rígida
* Definir expresiones que comprenden los patrones para que coincidan con los campos de entrada, en lugar de utilizar campos con nombre

Azure Data Factory admite de forma nativa esquemas flexibles que cambian de una ejecución a otra, lo que le permite compilar lógica de transformación de datos genéricos sin necesidad de volver a compilar los flujos de datos.

Deberá tomar una decisión de arquitectura en el flujo de datos para aceptar el desfase de esquema en todo el flujo. Al hacerlo, puede protegerse de los cambios de esquema en los orígenes. Sin embargo, se perderá el enlace temprano de las columnas y los tipos a lo largo del flujo de datos. Azure Data Factory trata los flujos de desfase de esquema como flujos de enlace tardío, por lo que, al compilar las transformaciones, los nombres de columnas desfasadas no estarán disponibles en las vistas de esquema en todo el flujo.

En este vídeo se proporciona una introducción a algunas de las soluciones complejas que puede compilar con facilidad en canalizaciones de Azure Data Factory o Synapse Analytics con la característica de **desfase de esquema** del flujo de datos. En este ejemplo, se compilan patrones reutilizables basados en esquemas de base de datos flexibles:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Desfase de esquema en origen

Las columnas que entran en el flujo de datos de la definición de origen se definen como "desplazadas" cuando no están presentes en la proyección de origen. Puede ver la proyección de origen desde la pestaña Proyección en la transformación de origen. Cuando se selecciona un conjunto de datos para el origen, el servicio toma automáticamente el esquema del conjunto de datos y crea una proyección a partir de esa definición de esquema de conjunto de datos.

En una transformación de origen, el desfase de esquema se define como columnas de lectura que no están definidas en el esquema del conjunto de los mismos. Para habilitar el desfase de esquema, elija **Allow Schema Drift** (Permitir desfase de esquema) en la transformación de origen.

:::image type="content" source="media/data-flow/schemadrift001.png" alt-text="Origen de desfase de esquema":::

Cuando se habilita el desfase de esquema, todos los campos de entrada se leen desde el origen durante la ejecución y se pasan a través del flujo completo al receptor. De forma predeterminada, todas las columnas que se acaban de detectar (llamadas *columnas desfasadas*) llegan como un tipo de datos String. Si desea que el flujo de datos infiera automáticamente los tipos de datos de las columnas desfasadas, active **Infer drifted column type** (Inferir tipos de columnas desfasadas) en la configuración de origen.

## <a name="schema-drift-in-sink"></a>Desfase de esquema en el receptor

En una transformación del receptor, el desfase de esquema se produce cuando escribe columnas adicionales sobre lo que se define en el esquema de datos del receptor. Para habilitar el desfase de esquema, active **Allow Schema Drift** (Permitir desfase de esquema) en la transformación del receptor.

:::image type="content" source="media/data-flow/schemadrift002.png" alt-text="Receptor de desfase de esquema":::

Si está habilitado el desfase de esquema, asegúrese de que el control deslizante **Auto-mapping** (Asignación automática) de la pestaña Asignación está activado. Con este control deslizante activado, todas las columnas de entrada se escriben en el destino. De lo contrario, debe usar la asignación basada en reglas para escribir columnas desfasadas.

:::image type="content" source="media/data-flow/automap.png" alt-text="Asignación automática del receptor":::

## <a name="transforming-drifted-columns"></a>Transformación de columnas desfasadas

Cuando el flujo de datos tiene columnas desfasadas, puede acceder a ellas en las transformaciones con los métodos siguientes:

* Utilice las expresiones `byPosition` y `byName` para hacer referencia explícita a una columna por nombre o número de posición.
* Agregue un patrón de columna en una transformación Agregar o Columna derivada para que coincida con cualquier combinación de nombre, flujo, posición, origen o tipo.
* Agregue una asignación basada en reglas en una transformación Selección o Receptor para asociar columnas desfasadas con alias de columnas mediante un patrón.

Para más información sobre cómo implementar patrones de columna, consulte [Patrones de columna en flujos de datos de asignación](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Acción rápida de asignación de columnas desfasadas

Para hacer referencia explícita a columnas desfasadas, puede generar rápidamente asignaciones para estas columnas mediante una acción rápida de vista previa de datos. Una vez activado el [modo de depuración](concepts-data-flow-debug-mode.md), vaya a la pestaña Vista previa de datos y haga clic en **Actualizar** para obtener una vista previa de los datos. Si Data Factory detecta que existen columnas desfasadas, puede hacer clic en **Map Drifted** (Asignar desfasadas) y generar una columna derivada que le permita hacer referencia a todas las columnas desfasadas en las vistas de esquema de nivel inferior.

:::image type="content" source="media/data-flow/mapdrifted1.png" alt-text="Captura de pantalla que muestra la pestaña Vista previa de datos con la opción Asignar desfasadas resaltada.":::

En la transformación Columna derivada generada, cada columna desfasada se asigna a su nombre y tipo de datos detectados. En la vista previa de datos anterior, la columna "movieId" se detecta como un entero. Tras hacer clic en **Map Drifted** (Asignar desfasadas), movieId se define en la columna derivada como `toInteger(byName('movieId'))` y se incluye en las vistas de esquema en las transformaciones de nivel inferior.

:::image type="content" source="media/data-flow/mapdrifted2.png" alt-text="Captura de pantalla que muestra la pestaña Configuración de Columna derivada.":::

## <a name="next-steps"></a>Pasos siguientes
En el [lenguaje de expresiones de Data Flow](data-flow-expression-functions.md) encontrará características adicionales para patrones de columnas y desfase de esquema, incluidas las funciones "byName" y "byPosition".
