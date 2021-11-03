---
title: 'Inicio rápido: API REST y bibliotecas cliente del SDK de Language Understanding (LUIS)'
description: Cree y consulte una aplicación de LUIS con la API REST y las bibliotecas cliente del SDK de LUIS.
ms.topic: quickstart
ms.date: 10/20/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, LUIS, azure luis, natural language understanding, ai chatbot, chatbot maker,  understanding natural language
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020, ignite-fall-2021
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: c98bbd885bb2646ff32c856aecc219cae326d01c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043877"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Inicio rápido: API REST y bibliotecas cliente de Language Understanding (LUIS)

> [!NOTE]
> Hay disponible una versión más reciente de las funcionalidades de Language Understanding como parte de Azure Cognitive Service for Language. Para más información, consulte [Documentación de Azure Cognitive Service for Language](../language-service/index.yml). Para información sobre las funcionalidades de reconocimiento del lenguaje del servicio de lenguaje, consulte [Reconocimiento de lenguaje conversacional](../language-service/conversational-language-understanding/overview.md), [Reconocimiento de entidades con nombre personalizado](../language-service/custom-named-entity-recognition/overview.md) y [Clasificación personalizada](../language-service/custom-classification/overview.md).

Con este inicio rápido, creará una aplicación de inteligencia artificial de Azure LUIS, y realizará consultas en ella, con las bibliotecas cliente del SDK de LUIS mediante C#, Python o JavaScript. También puede usar cURL para enviar solicitudes mediante la API REST.

Language Understanding (LUIS) permite aplicar procesamiento del lenguaje natural a una conversación o un texto de lenguaje natural de un usuario para predecir el significado global y extraer información pertinente y detallada.

* La API REST y la biblioteca cliente de **creación** le permiten crear, editar, entrenar y publicar aplicaciones de LUIS.
* La API REST y la biblioteca cliente del **runtime de predicción** permiten consultar la aplicación publicada.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

La aplicación se puede eliminar desde el [portal de LUIS](https://www.luis.ai) y los recursos de Azure desde [Azure Portal](https://portal.azure.com/).

Si usa la API REST, elimine el archivo `ExampleUtterances.JSON` del sistema de archivos cuando haya completado el inicio rápido.

## <a name="troubleshooting"></a>Solución de problemas

* Autenticación en la biblioteca cliente: los errores de autenticación indican normalmente que se han usado el punto de conexión y la clave incorrectos. Por comodidad, en este inicio rápido se usan la clave y el punto de conexión de creación del runtime de predicción, pero solo funcionarán si aún no ha usado la cuota mensual. Si no puede usarlos, debe utilizar la clave y el punto de conexión del entorno de ejecución de predicción al acceder a la biblioteca cliente del SDK del entorno de ejecución de predicción.
* Creación de entidades: si recibe un error al crear la entidad de aprendizaje automático anidada que se usa en este tutorial, asegúrese de que ha copiado el código y que no lo ha modificado para crear una entidad diferente.
* Creación de expresiones de ejemplo: si recibe un error al crear la expresión del ejemplo con etiquetas usado en este tutorial, asegúrese de que ha copiado el código y de que no lo ha modificado para crear otro ejemplo con etiquetas.
* Entrenamiento: si recibe un error de entrenamiento, normalmente significa que hay una aplicación vacía (sin intenciones con expresiones de ejemplo) o una aplicación con intenciones o entidades con un formato incorrecto.
* Errores varios: dado que el código llama a las bibliotecas cliente con objetos de texto y JSON, asegúrese de que no ha cambiado el código.

Otros errores: si recibe un error que no aparece en la lista anterior, háganoslo saber; para ello, puede enviarnos sus comentarios en la parte inferior de esta página. Incluya el lenguaje de programación y la versión de las bibliotecas cliente que ha instalado.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Desarrollo iterativo de aplicaciones para LUIS](./luis-concept-app-iteration.md)
