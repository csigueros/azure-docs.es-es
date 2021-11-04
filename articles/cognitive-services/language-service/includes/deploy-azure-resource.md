---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: adbcef246e8b027230ec436de9d9a1da838e523f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091789"
---
Para usar Language Studio, necesitará un recurso de Azure para el idioma para la autenticación. También puede usar este recurso para llamar a las API REST de características y a las bibliotecas cliente. Para comenzar, siga estos pasos. 

> [!IMPORTANT] 
> El proceso de instalación y los requisitos de las características personalizadas son diferentes. Si usa una de las características personalizadas, se recomienda seguir uno de los vínculos siguientes a artículos de inicio rápido para empezar a trabajar más fácilmente.  
> * [Reconocimiento del lenguaje conversacional](../conversational-language-understanding/quickstart.md)
> * [Clasificación de texto personalizada](../custom-classification/quickstart.md)
> * [Reconocimiento de entidades con nombre (NER) personalizado](../custom-named-entity-recognition/quickstart.md) 

1. Cree una suscripción a Azure. Puede [crear una de forma gratuita](https://azure.microsoft.com/free/ai/). 

2. [Inicie sesión en Language Studio](https://aka.ms/languageStudio). Si es la primera vez que inicia sesión, verá una ventana que le permite elegir un recurso de idioma. 

   :::image type="content" source="../media/language-resource-small.png" alt-text="Captura de pantalla que muestra la pantalla de selección de recursos en Language Studio." lightbox="../media/language-resource.png":::

3. Seleccione **Create a new language resource** (Crear un recurso de idioma). Después, escriba información para el nuevo recurso, como un nombre, una ubicación y un grupo de recursos.

    
    > [!TIP]
    > * Al seleccionar una ubicación para el recurso de Azure, elija una que esté más cerca para reducir la latencia.
    > * Se recomienda **activar** la opción **Identidad administrada** para autenticar las solicitudes en Azure.
    > * Si usa el plan de tarifa gratuito, puede seguir usando el servicio Language incluso después de que expire la evaluación gratuita de Azure o el crédito del servicio. 

    :::image type="content" source="../media/create-new-resource-small.png" alt-text="Captura de pantalla que muestra la pantalla de creación de recursos en Language Studio." lightbox="../media/create-new-resource.png":::

4. Seleccione **Listo**. Se creará el recurso y podrá probar las distintas características que ofrece el servicio Language. Por ejemplo, seleccione **Find linked entities** (Buscar entidades vinculadas).

    :::image type="content" source="../media/language-studio-main-screen.png" alt-text="Captura de pantalla en la que se muestra la pantalla principal de Language Studio." lightbox="../media/language-studio-main-screen.png":::


5. Esta característica tiene una sección para escribir texto, cargar un archivo o elegir un ejemplo de texto para mostrar cómo funciona la característica. Para probar la demostración, tendrá que elegir un recurso y confirmar que incurrirá en uso según el [plan de tarifa](https://aka.ms/unifiedLanguagePricing).

    :::image type="content" source="../media/language-studio-feature.png" alt-text="Captura de pantalla en la que se muestra una característica en Language Studio" lightbox="../media/language-studio-feature.png":::

6. Después de enviar texto, podrá ver una visualización, junto con la respuesta JSON. En la parte inferior de la página, verá los pasos siguientes y el comando cURL para la solicitud de API que acaba de enviar.

    :::image type="content" source="../media/language-studio-feature-result.png" alt-text="Captura de pantalla en la que se muestra el resultado de una característica en Language Studio" lightbox="../media/language-studio-feature-result.png":::
