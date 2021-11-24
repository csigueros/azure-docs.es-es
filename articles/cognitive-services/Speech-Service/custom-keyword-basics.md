---
title: 'Inicio rápido: Creación de palabras clave en el servicio Speech'
titleSuffix: Azure Cognitive Services
description: El dispositivo siempre espera una palabra clave (o frase). Cuando un usuario dice la palabra clave, el dispositivo envía su dictado a la nube, hasta que el usuario deja de hablar. La personalización de la palabra clave es una forma eficaz de diferenciar el dispositivo y de reforzar la marca.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 59756ec624bfc4d716bcf222195f53029be05420
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493216"
---
# <a name="get-started-with-custom-keyword"></a>Introducción a Palabra clave personalizada

En este inicio rápido va a aprender los aspectos básicos del trabajo con palabras clave personalizadas. Una palabra clave es una palabra o frase corta que permite activar el producto mediante la voz. Cree modelos de palabras clave en Speech Studio. Luego exporte un archivo de modelos para usarlo con el SDK de Voz en las aplicaciones.

## <a name="prerequisites"></a>Requisitos previos

Los pasos indicados en este artículo requieren una suscripción a Voz y el SDK de Voz. Si no dispone ya de una suscripción, [pruebe el servicio de voz de forma gratuita](overview.md#try-the-speech-service-for-free). Para obtener el SDK, consulte la [guía de instalación](quickstarts/setup-platform.md) para su plataforma.

## <a name="create-a-keyword-in-speech-studio"></a>Creación de una palabra clave en Speech Studio

Antes de que pueda usar una palabra clave personalizada, deberá crear una palabra clave con la página [Custom Keyword](https://aka.ms/sdsdk-wakewordportal) (Palabra clave personalizada) en [Speech Studio](https://aka.ms/sdsdk-speechportal). Después de proporcionar una palabra clave, se genera un archivo `.table` que puede usar con el SDK de Voz.

> [!IMPORTANT]
> Los modelos de palabra clave personalizada y los archivos `.table` resultantes **solo** pueden crearse en Speech Studio.
> No se pueden crear palabras clave personalizadas a partir del SDK o mediante llamadas REST.

1. Vaya a [Speech Studio](https://aka.ms/sdsdk-speechportal) e **inicie sesión**. Si no tiene una suscripción de Voz, vaya a [**Create Speech Services**](https://go.microsoft.com/fwlink/?linkid=2086754) (Crear servicios de Voz).

1. En la página [Palabra clave personalizada](https://aka.ms/sdsdk-wakewordportal), seleccione **Crear un proyecto**. 

1. Escriba un **Nombre**, una **Descripción** y un **Idioma** para el proyecto de palabra clave personalizada. Solo puede elegir un idioma por proyecto. Además, la compatibilidad está limitada actualmente a inglés (Estados Unidos) y chino (mandarín, simplificado). 

    ![Descripción del proyecto de palabra clave](media/custom-keyword/custom-kw-portal-new-project.png)

1. Seleccione el nombre del proyecto en la lista. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-project-list.png" alt-text="Selección del proyecto de palabra clave.":::

1. Para crear una palabra clave personalizada para el asistente virtual, seleccione **Crear un nuevo modelo**.

1. Escriba un **Nombre** para el modelo, una **Descripción** y la **Palabra clave** que prefiera y después seleccione **Siguiente**. Consulte las [directrices](keyword-recognition-guidelines.md#choosing-an-effective-keyword) sobre cómo elegir una palabra clave eficaz.

    ![Escriba su palabra clave](media/custom-keyword/custom-kw-portal-new-model.png)

1. El portal creará pronunciaciones candidatas para la palabra clave. Para escuchar cada candidata, seleccione los botones de reproducción y desactive las casillas situadas junto a las pronunciaciones incorrectas. Seleccione todas las pronunciaciones que correspondan a como espera que los usuarios digan la palabra clave y luego seleccione **Siguiente** para empezar a generar el modelo de palabras clave. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-choose-prons.png" alt-text="Captura de pantalla que muestra dónde elegir las pronunciaciones correctas":::

1. Seleccione un tipo de modelo y luego **Crear**. Puede ver una lista de regiones que admiten el tipo de modelo **Avanzado** en la documentación [Compatibilidad con la región de reconocimiento de palabras clave](keyword-recognition-region-support.md). 

1. El modelo puede tardar hasta 30 minutos en generarse. La lista de palabras clave cambiará de **Procesando** a **Correcto** cuando el modelo esté completo. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-review-keyword.png" alt-text="Revisión de la palabra clave.":::

1. En el menú contraíble de la izquierda, seleccione **Optimizar** para obtener opciones para optimizar y descargar el modelo. El archivo descargado es un archivo `.zip`. Extraiga el archivo y verá un archivo con la extensión `.table`. El archivo `.table` se usa con el SDK, así que asegúrese de anotar su ruta de acceso.

    :::image type="content" source="media/custom-keyword/custom-kw-portal-download-model.png" alt-text="Descarga de la tabla de modelos.":::


## <a name="use-a-keyword-model-with-the-speech-sdk"></a>Uso de un modelo de palabras clave con el SDK de Voz

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtención del SDK de voz](speech-sdk.md)
