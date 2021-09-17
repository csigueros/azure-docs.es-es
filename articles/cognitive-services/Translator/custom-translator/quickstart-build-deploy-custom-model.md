---
title: 'Guía de inicio rápido: Compilación, implementación y uso de un modelo personalizado con Custom Translator'
titleSuffix: Azure Cognitive Services
description: En esta guía de inicio rápido recorrerá el proceso paso a paso para compilar un sistema de traducción mediante Custom Translator.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: quickstart
ms.openlocfilehash: e77196572a0ea3b18f0b28a6ad26349e6e7c668e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744788"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Guía de inicio rápido: Compilación, implementación y uso de un modelo personalizado para la traducción

En este artículo se proporcionan instrucciones detalladas para compilar un sistema de traducción con Custom Translator.

## <a name="prerequisites"></a>Requisitos previos

1. Para usar el portal de [Custom Translator](https://portal.customtranslator.azure.ai), necesitará una [cuenta Microsoft](https://signup.live.com) o [cuenta de Azure AD](../../../active-directory/fundamentals/active-directory-whatis.md) (cuenta de organización hospedada en Azure) para iniciar sesión.

2. Una suscripción a Translator Text API a través de Azure Portal. Necesitará la clave de suscripción de Translator Text API para asociarla con el área de trabajo de Custom Translator. Consulte [Cómo suscribirse a Translator Text API](../translator-how-to-signup.md).

3. Cuando tenga los dos anteriores, inicie sesión in en el portal [Custom Translator](https://portal.customtranslator.azure.ai) para crear áreas de trabajo y proyectos, así como cargar archivos y crear e implementar modelos.

También puede ver un tutorial de vídeo completo de Traductor personalizado en [YouTube](https://www.youtube.com/watch?v=TykB6WDTkRc&t=3s).

>[!Note]
>Traductor personalizado no admite la creación de un área de trabajo para el recurso de Translator Text API que se creó en la [red virtual habilitada](../../../api-management/api-management-using-with-vnet.md).

## <a name="create-a-workspace"></a>Crear un área de trabajo

Si es la primera vez que lo usa, se le solicitará que acepte las Condiciones del servicio para crear un área de trabajo y asociarla a la suscripción de Microsoft Translator Text API.

![Crear área de trabajo](media/quickstart/terms-of-service.png)
![Crear área de trabajo imagen 1](media/quickstart/create-workspace-1.png)
![Crear área de trabajo imagen 2](media/quickstart/create-workspace-2.png)
![Crear área de trabajo imagen 3](media/quickstart/create-workspace-3.png)
![Crear área de trabajo imagen 4](media/quickstart/create-workspace-4.png)
![Crear área de trabajo imagen 5](media/quickstart/create-workspace-5.png)
![Crear área de trabajo imagen 6](media/quickstart/create-workspace-6.png)

En las posteriores visitas al portal de Custom Translator, vaya a la página de configuración, donde puede administrar el área de trabajo, crear más áreas de trabajo, asociar la clave de suscripción de Microsoft Translator Text API a sus áreas de trabajo, agregar copropietarios y cambiar una clave de suscripción.

## <a name="create-a-project"></a>Crear un proyecto

En la página de aterrizaje del portal de Custom Translator, haga clic en Nuevo proyecto. En el cuadro de diálogo puede especificar el nombre de proyecto deseado, el par de idiomas y la categoría, así como otros campos importantes. Después, guarde el proyecto. Para más información, visite [Creación de proyectos](how-to-create-project.md).

![Crear proyecto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Cargar documentos

A continuación, cargue los conjuntos de documentos de [aprendizaje](training-and-model.md#training-document-type-for-custom-translator), [optimización](training-and-model.md#tuning-document-type-for-custom-translator) y [pruebas](training-and-model.md#testing-dataset-for-custom-translator). Puede cargar documentos [paralelos](what-are-parallel-documents.md) y documentos de cuadro combinado. También puede cargar un [diccionario](what-is-dictionary.md).

Puede cargar documentos desde la pestaña de documentos o desde la página de un proyecto concreto.

![Cargar documentos](media/quickstart/ct-how-to-upload.png)

Al cargar los documentos, elija el tipo de documento (aprendizaje, optimización o pruebas) y el par de idiomas. Si carga documentos paralelos tendrá que especificar además un nombre de documento. Para más información, visite [Carga de documentos](how-to-upload-document.md).

## <a name="create-a-model"></a>Crear un modelo

Una vez cargados todos los documentos necesarios el siguiente paso es crear el modelo.

Seleccione el proyecto que ha creado. Verá todos los documentos que ha cargado que compartan el par de idiomas con este proyecto. Seleccione los documentos que desea incluir en el modelo. Puede seleccionar datos de [aprendizaje](training-and-model.md#training-document-type-for-custom-translator), [optimización](training-and-model.md#tuning-document-type-for-custom-translator) y [prueba](training-and-model.md#testing-dataset-for-custom-translator) o solo seleccionar datos de aprendizaje y dejar que Custom Translator compile automáticamente los conjuntos de datos de optimización y prueba del modelo.

![Crear un modelo](media/quickstart/ct-how-to-train.png)

Cuando haya acabado de seleccionar los documentos que desee, haga clic en el botón Crear modelo para crear el modelo e iniciar el aprendizaje. Puede ver el estado del aprendizaje y los detalles de todos los modelos entrenados, en la pestaña Modelos.

Para más información, visite [Creación de un modelo](how-to-train-model.md).

## <a name="analyze-your-model"></a>Análisis del modelo

Una vez que el aprendizaje se ha completado correctamente, inspeccione los resultados. La puntuación BLEU es una métrica que indica la calidad de la traducción. También puede comparar manualmente las traducciones realizadas con el modelo personalizado con las traducciones proporcionadas en el conjunto de pruebas. Para ello, vaya a la pestaña "Prueba" y haga clic en "Resultados del sistema". Si inspecciona manualmente varias de estas traducciones podrá hacerse una buena idea de la calidad de la traducción que su sistema ha producido. Para más información, visite la página de [resultados de pruebas del sistema](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Implementación de un modelo entrenado

Cuando esté listo para implementar el modelo entrenado, haga clic en el botón "Implementar". Puede tener un modelo implementado por proyecto y puede ver el estado de la implementación en la columna Estado. Para más información, visite [Model Deployment](how-to-view-system-test-results.md#deploy-a-model) (Implementación de un modelo)

![Implementación de un modelo entrenado](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>Intercambio de un modelo implementado

Para intercambiar un modelo implementado por otro dentro de un proyecto, haga clic en el botón "Intercambiar" que se muestra junto al modelo deseado. Durante el proceso de intercambio, el modelo implementado seguirá estando disponible para atender las solicitudes de traducción. 

![Intercambio de un modelo implementado](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>Uso de un modelo implementado

Se puede acceder a los modelos implementados a través de Microsoft Translator [Text API V3 especificando CategoryID](../reference/v3-0-translate.md?tabs=curl). Puede encontrar más información sobre Translator Text API en la página web de [referencia de API](../reference/v3-0-reference.md).

## <a name="next-steps"></a>Pasos siguientes

- Vaya a [Custom Translator workspace and manage your projects](workspace-and-project.md) (Área de trabajo de Custom Translator y administración de proyectos).
