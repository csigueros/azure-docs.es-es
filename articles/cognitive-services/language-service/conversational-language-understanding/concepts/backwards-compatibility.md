---
title: Compatibilidad con versiones anteriores del reconocimiento del lenguaje conversacional
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre la compatibilidad con versiones anteriores entre LUIS y el reconocimiento del lenguaje conversacional.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: f4c354780cd5d32c2c801b76bde2b8b6abd2d555
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093201"
---
# <a name="backwards-compatibility-with-luis-applications"></a>Compatibilidad con versiones anteriores con aplicaciones de LUIS

Puede reutilizar parte del contenido de las aplicaciones de LUIS existentes en el reconocimiento del lenguaje conversacional. Al trabajar con proyectos de reconocimiento del lenguaje conversacional, puede hacer lo siguiente:
* Crear proyectos de conversación de CLU a partir de archivos JSON de la aplicación de LUIS.
* Crear aplicaciones de LUIS que se puedan conectar a proyectos de flujos de trabajo de orquestación.  
  
> [!NOTE]
> En esta guía, se da por supuesto que ha creado un recurso de idioma. Si va a empezar a trabajar con el servicio, consulte el [artículo de inicio rápido](../quickstart.md). 

## <a name="import-a-luis-application-json-file-into-conversational-language-understanding"></a>Importación de un archivo JSON de una aplicación de LUIS al reconocimiento del lenguaje conversacional

Para importar un archivo JSON de una aplicación de LUIS, haga clic en el icono situado junto a **Crear un nuevo proyecto** y seleccione **Importar**. A continuación, seleccione el archivo de LUIS. Al importar un nuevo proyecto en el reconocimiento del lenguaje conversacional, puede seleccionar un archivo JSON exportado de una aplicación de LUIS y el servicio creará automáticamente un proyecto con las características disponibles actualmente.

:::image type="content" source="../media/import.png" alt-text="Captura de pantalla que muestra el botón importar para proyectos de conversación." lightbox="../media/import.png":::

### <a name="supported-features"></a>Características admitidas
Al importar la aplicación JSON de LUIS en CLU, se creará un proyecto de **conversaciones** con las siguientes características seleccionadas:

|**Característica**|**Notas**|
| :- | :- |
|Intenciones|Todas las intenciones se transferirán como intenciones de CLU con los mismos nombres.|
|Entidades de ML|Todas las entidades de ML se transferirán como entidades de CLU con los mismos nombres. Las etiquetas de ML se conservarán y usarán para entrenar el componente aprendido de la entidad. Las entidades de ML estructuradas solo se transferirán como entidad de nivel superior. Se omitirán las entidades secundarias individuales.|
|Grabaciones de voz|Todas las expresiones de LUIS se transferirán como expresiones de CLU con su intención y etiquetas de entidad. Las etiquetas de entidad de ML estructurada solo tendrán en cuenta las etiquetas de entidad de nivel superior y se omitirán las etiquetas de las entidades secundarias individuales.|
|culture|El idioma principal del proyecto de conversación será la referencia cultural de la aplicación de LUIS. Si no se admite la referencia cultural, se producirá un error en la importación. |

### <a name="unsupported-features"></a>Características no admitidas

Al importar la aplicación JSON de LUIS en CLU, se omitirán ciertas características, pero no le impedirá importar la aplicación. Se omitirán las siguientes características:

|**Característica**|**Notas**|
| :- | :- |
|Configuración de la aplicación|Las opciones de configuración, como Normalizar puntuación, Normalizar marcas diacríticas y Usar todos los datos de entrenamiento, estaban pensadas para mejorar las predicciones de intenciones y entidades. Los nuevos modelos de CLU no son sensibles a pequeños cambios como la puntuación y, por lo tanto, no están disponibles como opción de configuración.|
|Características|Se omitirán todas las características de la lista de frases y las características para las intenciones. Las características estaban pensadas para introducir una comprensión semántica para LUIS que CLU puede proporcionar de forma integrada con sus nuevos modelos.|
|Patrones|Los patrones se utilizaban para cubrir la falta de calidad en la clasificación de intenciones. Se espera que los nuevos modelos de CLU tengan un mejor funcionamiento sin necesidad de patrones.|
|Entidades Pattern.Any|Las entidades Pattern.Any se utilizaban para cubrir la falta de calidad en la extracción de entidades de ML. Se espera que los nuevos modelos de CLU tengan un mejor funcionamiento sin necesidad de entidades Pattern.Any.|
|Entidades de expresión regular| No se admite actualmente. |
|Entidades de ML estructuradas| No se admite actualmente. |
|Enumeración de entidades | No se admite actualmente. |
|Entidades precompiladas | No se admite actualmente. |
|Características de entidad necesarias en las entidades de ML | No se admite actualmente. |
|Características de entidad no necesarias en las entidades de ML | No se admite actualmente. |
|Roles | No se admite actualmente. |

## <a name="use-a-published-luis-application-in-conversational-language-understanding-orchestration-projects"></a>Uso de una aplicación de LUIS publicada en proyectos de orquestación de reconocimiento del lenguaje conversacional

Solo puede conectarse a aplicaciones de LUIS publicadas que pertenecen al mismo recurso de idioma que se usa para el reconocimiento del lenguaje conversacional. Puede cambiar el recurso de creación a un recurso de idioma **S** en las aplicaciones de **Oeste de Europa**. Consulte la [documentación de LUIS](../../../luis/luis-how-to-azure-subscription.md#assign-luis-resources) para ver los pasos para asignar otro recurso a la aplicación de LUIS. También puede exportar y, a continuación, importar las aplicaciones de LUIS en el recurso de idioma. Debe entrenar y publicar las aplicaciones de LUIS para que aparezcan en el reconocimiento del lenguaje conversacional cuando desee conectarlas a proyectos de orquestación.


## <a name="next-steps"></a>Pasos siguientes

[Introducción al reconocimiento del lenguaje conversacional](../overview.md)
