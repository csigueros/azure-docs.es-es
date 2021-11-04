---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 912aa02fadef0f950f5d89535bfe38133b61137b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093360"
---
## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services).

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Creación de un recurso de Azure y una cuenta de Azure Blob Storage

Para poder usar la clasificación de texto personalizada, deberá crear un recurso de idioma de Azure, que le proporcionará las credenciales necesarias para crear un proyecto y empezar a entrenar un modelo. También necesitará una cuenta de almacenamiento de Azure, donde pueda cargar el conjunto de datos que se usará para compilar el modelo.

> [!IMPORTANT]
> Para empezar a trabajar rápidamente, se recomienda crear un recurso de idioma de Azure mediante los pasos que se indican a continuación, que le permitirán crear el recurso y configurar una cuenta de almacenamiento al mismo tiempo, lo que resulta más fácil que hacerlo luego.
>
> Si ya tiene un recurso que le gustaría usar, deberá configurar dicho recurso y la cuenta de almacenamiento por separado. Para más información, consulte [**Requisitos de proyecto**](../../how-to/create-project.md#using-a-pre-existing-azure-resource).

1. Vaya a [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) y cree un recurso de idioma de Azure. Si se le pide que seleccione características adicionales, seleccione **Custom text classification & custom NER** (Clasificación de texto personalizada y NER personalizado). Al crear el recurso, asegúrese de que tiene los parámetros siguientes.

    |Requisito del recurso de Azure  |Valor obligatorio  |
    |---------|---------|
    |Location | Oeste de EE. UU. 2 u Oeste de Europa         |
    |Plan de tarifa     | Plan de tarifa estándar (**S**)        |

2. En la sección **Custom Named Entity Recognition (NER) & Custom Classification (Preview)** (Reconocimiento de entidades con nombre (NER) personalizado y clasificación personalizada [versión preliminar]), seleccione una cuenta de almacenamiento existente o elija **Create a new storage account** (Crear una cuenta de almacenamiento). Tenga en cuenta que estos valores son para este inicio rápido y no necesariamente los [valores de la cuenta de almacenamiento](/azure/storage/common/storage-account-overview) que querrá usar en entornos de producción.

    |Valor de la cuenta de almacenamiento  |Valor recomendado  |
    |---------|---------|
    | Nombre | Cualquier nombre |
    | Rendimiento | Estándar |
    | Tipo de cuenta| Storage (de uso general v1) |
    | Replicación | Almacenamiento con redundancia local (LRS)
    |Location | Cualquier ubicación más cercana a usted, para obtener la mejor latencia.        |


## <a name="upload-sample-data-to-blob-container"></a>Carga de los datos de ejemplo en el contenedor de blobs

Después de crear una cuenta de almacenamiento de Azure y vincularla al recurso de idioma, deberá cargar los archivos de ejemplo de este inicio rápido. Estos archivos se usarán más adelante para entrenar el modelo.

1. [Descargue los datos de ejemplo](https://go.microsoft.com/fwlink/?linkid=2175083) de este inicio rápido desde GitHub.

2. Vaya a la cuenta de almacenamiento de Azure en [Azure Portal](https://ms.portal.azure.com). Vaya a su cuenta y cargue los datos de ejemplo en ella.

El conjunto de datos de ejemplo proporcionado contiene alrededor de 200 resúmenes de películas que pertenecen a una o varias de las clases siguientes: "Misterio", "Teatro", "Suspense", "Comedia", "Acción".

## <a name="create-a-custom-classification-project"></a>Creación de un proyecto de clasificación personalizado

[!INCLUDE [Language Studio project creation](../create-project.md)]

    
## <a name="train-your-model"></a>Entrenamiento de un modelo

Normalmente, después de crear un proyecto, importaría los datos y comenzaría a [etiquetar las entidades](../../how-to/tag-data.md) que contiene para entrenar el modelo de clasificación. En este inicio rápido, usará el archivo de datos etiquetado de ejemplo que descargó anteriormente y que está almacenado su cuenta de almacenamiento de Azure.

Un modelo es el objeto de aprendizaje automático que se entrenará para clasificar el texto. El modelo aprenderá de los datos de ejemplo y podrá clasificar los vales de soporte técnico más adelante.

Para empezar a entrenar el modelo:

1. En el menú de la izquierda, seleccione **Train** (Entrenar).

2. Seleccione **Train a new model** (Entrenar un nuevo modelo) y escriba el nombre del modelo en el cuadro de texto siguiente.

    :::image type="content" source="../../media/train-model.png" alt-text="Captura de pantalla que muestra la página de selección del modelo para el entrenamiento" lightbox="../../media/train-model.png":::

3. Haga clic en el botón **Train** (Entrenar) en la parte inferior de la página.

    > [!NOTE]
    > * Durante el entrenamiento, los datos se dividirán en dos conjuntos: el 80 % para entrenamiento y el 20 % para prueba. Puede encontrar más información sobre la división de datos [aquí](../../how-to/train-model.md#data-splits).
    > * El entrenamiento puede tardar hasta varias horas.

## <a name="deploy-your-model"></a>Implementación del modelo


Por lo general, después de entrenar un modelo, revisaría sus [detalles de evaluación](../../how-to/view-model-evaluation.md) y [realizaría mejoras](../../how-to/improve-model.md) si fuera necesario. En este inicio rápido, simplemente implementará el modelo y hará que esté disponible para probarlo.

Una vez entrenado el modelo, puede implementarlo. La implementación del modelo le permite empezar a usarlo para clasificar texto mediante [Analyze API](https://aka.ms/ct-runtime-swagger).

1. Seleccione **Deploy model** (Implementar modelo) en el menú de la izquierda.

2. Seleccione el modelo que quiere implementar y elija **Deploy model** (Implementar modelo).

## <a name="test-your-model"></a>Comprobación del modelo

Una vez implementado el modelo, puede empezar a usarlo para la clasificación de texto. Realice los pasos siguientes para enviar la primera solicitud de clasificación de texto. 

1. Seleccione **Test model** (Probar modelo) en el menú de la izquierda.

2. Seleccione el modelo que quiera probar.

3. Agregue el texto al cuadro de texto; también puede cargar un archivo `.txt`. 

4. Haga clic en **Run the test** (Ejecutar la prueba).

5. En la pestaña **Resultado**, puede ver las clases de predicción para el texto. También puede ver la respuesta JSON en la pestaña **JSON**. 

    :::image type="content" source="../../media/test-model-results.png" alt-text="Captura de pantalla que muestra los resultados de la prueba del modelo. El ejemplo es de CMU Movie Summary, CC BY-SA 3.0, modificado por Microsoft" lightbox="../../media/test-model-results.png":::

## <a name="clean-up-projects"></a>Limpieza de proyectos

Cuando ya no necesite el proyecto, puede eliminarlo mediante desde su [página de proyectos en Language Studio](https://aka.ms/custom-classification
). Seleccione el proyecto que desea eliminar y haga clic en **Eliminar**.
