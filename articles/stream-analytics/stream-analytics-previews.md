---
title: Características en vista previa (GB) de Azure Stream Analytics
description: En este artículo se enumeran las características de Azure Stream Analytics que están actualmente en versión preliminar.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 3f6b46425954d8befaef396c66b023565310ec36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741845"
---
# <a name="azure-stream-analytics-preview-features"></a>Características en vista previa (GB) de Azure Stream Analytics

En este artículo se resumen todas las características actualmente en versión preliminar de Azure Stream Analytics. No se recomienda el uso de características en vista previa en un entorno de producción.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Autenticación en la salida de SQL Database con identidades administradas (versión preliminar)

Azure Stream Analytics admite la [autenticación de identidades administradas](../active-directory/managed-identities-azure-resources/overview.md) para los receptores de salida de Azure SQL Database. Las identidades administradas eliminan las limitaciones de los métodos de autenticación basada en el usuario, como la necesidad de volver a realizar la autenticación debido a los cambios de contraseña. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Puntuación de alto rendimiento en tiempo real con modelos de ML personalizados y administrados por Azure Machine Learning

Azure Stream Analytics admite puntuaciones en tiempo real y de alto rendimiento aprovechando modelos de Machine Learning entrenados previamente personalizados administrados por Azure Machine Learning y hospedados en Azure Kubernetes Service (AKS) o Azure Container Instances (ACI), mediante un flujo de trabajo que no requiere que escriba código. [Registrarse](https://aka.ms/asapreview1) para la versión preliminar

## <a name="c-custom-de-serializers"></a>Deserializador personalizado de C#
Los desarrolladores pueden aprovechar la eficacia de Azure Stream Analytics para procesar los datos en Protobuf, XML o cualquier formato personalizado. Puede implementar [deserializadores personalizados](custom-deserializer-examples.md) en C# para deserializar los eventos recibidos por Azure Stream Analytics.

## <a name="extensibility-with-c-custom-code"></a>Extensibilidad con código personalizado de C#

Los desarrolladores que crean módulos de Stream Analytics en la nube o en IoT Edge pueden escribir o volver a usar funciones personalizadas de C# e invocarlas directamente en la consulta a través de las [funciones definidas por el usuario](stream-analytics-edge-csharp-udf-methods.md).

## <a name="debug-queries-locally-using-job-diagram-in-visual-studio-code"></a>Depuración local de consultas mediante un diagrama de trabajos de Visual Studio Code

Puede usar el diagrama de trabajos mientras prueba la consulta localmente para examinar el conjunto de resultados intermedio y las métricas de cada paso.

## <a name="explore-jobs-in-visual-studio-code"></a>Exploración de trabajos en Visual Studio Code

El Explorador de Stream Analytics en la extensión de Visual Studio Code ofrece a los desarrolladores una experiencia ligera para administrar los trabajos de Stream Analytics. En el Explorador de Stream Analytics, puede administrar fácilmente los trabajos, ver el diagrama de trabajos y realizar la depuración en el Monitor de trabajos.

## <a name="debug-query-steps-in-visual-studio"></a>Depuración de pasos de consulta en Visual Studio

Puede obtener fácilmente una vista previa del conjunto de filas intermedio en un diagrama de datos al realizar pruebas locales en las herramientas de Azure Stream Analytics para Visual Studio. 


## <a name="live-data-testing-in-visual-studio"></a>Prueba de datos activos en Visual Studio

Las herramientas de Visual Studio para Azure Stream Analytics mejoran la función de pruebas locales que le permite realizar pruebas en las consultas en comparación con los flujos de datos de eventos activos desde fuentes en la nube como un centro de eventos o un centro de IoT. Aprenda cómo realizar una [Prueba local de datos activos mediante herramientas de Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md).


