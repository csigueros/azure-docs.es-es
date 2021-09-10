---
title: Migración de Azure Data Lake Analytics a Azure Synapse Analytics
description: En este artículo se explica cómo migrar de Azure Data Lake Analytics a Azure Synapse Analytics.
author: lingluo0531
ms.author: lingluo
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: migrate-azure-data-lake-analytics-to-synapse
ms.date: 08/25/2021
ms.openlocfilehash: 34a199fe8f488add4f1f2cdd9357dd9ac718d4f0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254962"
---
# <a name="migrate-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Migración de Azure Data Lake Analytics a Azure Synapse Analytics

Microsoft ha publicado Azure Synapse Analytics, que aspira a combinar los lagos de datos y el almacenamiento de datos en una experiencia única de análisis de macrodatos. Esto va a ayudar a los clientes a recopilar y analizar toda la variedad de datos, a resolver su ineficacia y a cooperar. Además, la integración de Synapse con Azure Machine Learning y Power BI va a permitir a las organizaciones obtener información a partir de sus datos y ejecutar el aprendizaje automático en todas sus aplicaciones inteligentes. 

En el documento se muestra cómo realizar la migración desde Azure Data Lake Analytics a Azure Synapse Analytics. 

## <a name="recommended-approach"></a>Enfoque recomendado
- Paso 1: Evaluación de la preparación
- Paso 2: Preparación de la migración
- Paso 3: Migración de los datos y las cargas de trabajo de las aplicaciones
- Paso 4: Transición de Azure Data Lake Analytics a Azure Synapse Analytics

### <a name="step-1-assess-readiness"></a>Paso 1: Evaluación de la preparación

1. Vea [Apache Spark en Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-overview.md) y comprenda las diferencias clave entre Azure Data Lake Analytics y Spark en Azure Synapse Analytics. 

    |Artículo | Análisis con Azure Data Lake | Spark en Synapse |
    | --- | --- |--- |
    | Precios  |Por unidad de análisis por hora |Por núcleo virtual por hora|
    |Motor     |Análisis con Azure Data Lake  |Spark de Apache
    |Lenguaje de programación predeterminado    |U-SQL   |T-SQL, Python, Scala, Spark SQL y .NET
    |Orígenes de datos   |Azure Data Lake Storage    |Azure Blob Storage, Azure Data Lake Storage

2. Vea el <a href="#questionnaire">Cuestionario para la evaluación de la migración</a> e indique los posibles riesgos que se deben tener en cuenta. 

### <a name="step-2-prepare-to-migrate"></a>Paso 2: Preparación de la migración

1.  Identifique los trabajos y los datos que va a migrar.
    -   Aproveche esta oportunidad para limpiar los trabajos que ya no usa. A menos que piense migrar todos los trabajos al mismo tiempo, dedique este tiempo a identificar los grupos lógicos de trabajos que puede migrar en fases.
    -   Evalúe el tamaño de los datos y comprenda el formato de datos de Apache Spark. Revise los scripts de U-SQL, evalúe los esfuerzos de reescritura de scripts y comprenda el concepto de código de Apache Spark.

2.  Determine el impacto que una migración tendrá en su empresa. Por ejemplo, si puede permitirse algún tiempo de inactividad mientras se realiza la migración.

3.  Cree un plan de migración.

### <a name="step-3-migrate-data-and-application-workload"></a>Paso 3: Migración de los datos y la carga de trabajo de las aplicaciones

1.  Migre los datos de Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. <br></br>
    La retirada de Azure Data Lake Storage Gen1 se realizará en febrero de 2024, vea el [anuncio oficial](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/). Primero se recomienda migrar los datos a Gen2. Vea [Descripción de las diferencias entre los formatos de datos de Spark y U-SQL](understand-spark-data-formats.md) y mueva tanto el archivo como los datos almacenados en tablas de U-SQL para que Azure Synapse Analytics pueda acceder a ellos.  [Aquí](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) puede obtener más detalles de la guía de migración. 

2.  Transforme los scripts de U-SQL a Spark. 
    Vea [Descripción del código de Apache Spark para los desarrolladores de U-SQL](understand-spark-code-concepts.md) para transformar los scripts de U-SQL a Spark. 

3.  Transforme o vuelva a crear las canalizaciones de orquestación de trabajos en el nuevo programa de Spark.

### <a name="step-4-cut-over-from-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Paso 4: Transición de Azure Data Lake Analytics a Azure Synapse Analytics

Una vez que esté seguro de que las aplicaciones y las cargas de trabajo son estables, puede empezar a usar Azure Synapse Analytics para satisfacer los escenarios empresariales. Desactive las canalizaciones restantes que se ejecuten en Azure Data Lake Analytics y retire las cuentas de Azure Data Lake Analytics.

<a name="questionnaire"></a>
## <a name="questionnaire-for-migration-assessment"></a>Cuestionario para la evaluación de la migración 

|Category   |Preguntas  |Referencia|
| --- | --- |--- |
|Evaluación del tamaño de la migración |¿Cuántas cuentas de Azure Data Lake Analytics tiene? ¿Cuántas canalizaciones en uso hay? ¿Cuántos scripts de U-SQL en uso hay?| Cuantos más datos y scripts se van a migrar y más UDO/UDF se usan en los scripts, más difícil es migrar. El tiempo y los recursos necesarios para la migración deben planearse correctamente conforme a la escala del proyecto.|
|Origen de datos |¿Cuál es el tamaño del origen de datos? ¿Qué tipos de formatos de datos hay para procesar? |[Descripción de las diferencias entre los formatos de datos de Spark y U-SQL](understand-spark-data-formats.md)|
|Salida de datos |¿Va a mantener los datos de salida para su uso más adelante? Si los datos de salida se guardan en tablas de U-SQL, ¿cómo se controlan? | Si los datos de salida se van a usar con frecuencia y se van a guardar en tablas de U-SQL, debe cambiar los scripts y los datos de salida al formato de datos admitido por Spark.|
|Migración de datos |¿Ha realizado el plan de migración de almacenamiento? |[Migración de Azure Data Lake Storage de Gen1 a Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) |
|Transformación de scripts de U-SQL|¿Usa UDO/UDF (.NET, Python, etc.)? Si la respuesta anterior es sí, ¿qué lenguaje usa en UDO/UDF y los problemas de transformación durante la transformación? ¿Se usa la consulta federada en U-SQL?|[Descripción del código de Apache Spark para los desarrolladores de U-SQL](understand-spark-code-concepts.md)|

## <a name="next-steps"></a>Pasos siguientes

- [Azure Synapse Analytics](../synapse-analytics/get-started.md)
