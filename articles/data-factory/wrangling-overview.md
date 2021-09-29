---
title: Limpieza y transformación de datos en Azure Data Factory
description: Información general de la limpieza y transformación de datos en Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: d49b587262bfceb8072761cec6981ab908abb21e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732944"
---
# <a name="what-is-data-wrangling"></a>¿Qué es la limpieza y transformación de datos?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Las organizaciones deben poder explorar sus datos empresariales críticos para limpiarlos y transformarlos con el fin de analizar de forma precisa los datos complejos que aumentan con el paso de los días. La preparación de datos es necesaria para que las organizaciones puedan usar los datos en diversos procesos empresariales y reducir el plazo de amortización.

Data Factory permite preparar datos sin código a escala de la nube de forma iterativa mediante Power Query. Data Factory se integra con [Power Query Online](/power-query/) y permite que las funciones de M de Power Query estén disponibles como actividades de canalización.

Además, Data Factory traduce el código M generado por el editor de mashup de Power Query Online en código de Spark para la ejecución a escala de la nube. Para ello, traduce M en flujos de datos de Azure Data Factory. La tarea de limpieza y transformación de datos es especialmente útil para los ingenieros de datos o "integradores de datos de ciudadanos".

> [!NOTE]
> La actividad de Power Query de Azure Data Factory está disponible actualmente en versión preliminar pública.

## <a name="use-cases"></a>Casos de uso

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Exploración y preparación rápidas de datos interactivos

Varios ingenieros de datos e integradores de datos de ciudadanos pueden explorar y preparar interactivamente conjuntos de datos a escala de nube. Con el aumento del volumen, la variedad y la velocidad de los datos en los lagos de datos, los usuarios necesitan una manera eficaz de explorar y preparar los conjuntos de datos. Por ejemplo, puede que necesite crear un conjunto de datos que "tenga toda la información demográfica de los clientes para los nuevos clientes desde 2017". No está asignando a un destino conocido. Está explorando, limpiando y transformando, y preparando los conjuntos de datos para que cumplan un requisito antes de publicarlos en el lago. La tarea de limpieza y transformación se usa a menudo para escenarios de análisis menos formales. Los conjuntos de datos preparados se pueden usar para realizar transformaciones y operaciones de aprendizaje automático de nivel inferior.

### <a name="code-free-agile-data-preparation"></a>Preparación de datos ágil sin código

Los integradores de datos de los ciudadanos invierten más del 60 % de su tiempo en buscar y preparar los datos. Buscan hacerlo sin código para mejorar la productividad operativa. Permitir a los integradores de datos de ciudadanos enriquecer, dar forma y publicar datos mediante herramientas conocidas, como Power Query Online, de forma escalable mejora drásticamente su productividad. La tarea de limpieza y transformación en Azure Data Factory permite que el conocido editor de mashup de Power Query Online consiga que los integradores de datos de ciudadanos corrijan errores rápidamente, estandaricen datos y generen datos de alta calidad para respaldar las decisiones empresariales.

### <a name="data-validation-and-exploration"></a>Exploración y validación de datos

Analice visualmente los datos sin código para quitar los valores atípicos y las anomalías, y hacer que cumplan con una forma para el análisis rápido.

## <a name="supported-sources"></a>Orígenes compatibles

| Conector | Formato de datos | Tipo de autenticación |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Clave de cuenta |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entidad de servicio |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Clave de cuenta, Entidad de servicio |
| [Azure SQL Database](connector-azure-sql-database.md) | - | Autenticación SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticación SQL |

## <a name="the-mashup-editor"></a>Editor de mashup

Cuando se crea una actividad de Power Query, todos los conjuntos de datos de origen se convierten en consultas de conjuntos de datos y se colocan en la carpeta **ADFResource**. De forma predeterminada, UserQuery apuntará a la primera consulta del conjunto de datos. Todas las transformaciones deben realizarse en UserQuery, ya que no se admiten ni se conservan los cambios en las consultas del conjunto de datos. Actualmente no se admiten las opciones para cambiar el nombre o agregar y eliminar consultas.

:::image type="content" source="media/wrangling-data-flow/editor.png" alt-text="Limpieza y transformación":::

Actualmente no se admiten todas las funciones de Power Query M para la limpieza y transformación de datos, a pesar de estar disponibles durante la creación. Al compilar las actividades de Power Query, aparecerá el siguiente mensaje de error si no se admite una función:

`The Power Query Spark Runtime does not support the function`

Para obtener más información sobre las transformaciones admitidas, vea [Funciones de transformación de datos de Power Query](wrangling-functions.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [mashup de Power Query de limpieza y transformación de datos](wrangling-tutorial.md).
