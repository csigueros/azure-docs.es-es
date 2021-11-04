---
title: Enriquecimiento de un índice de Cognitive Search con entidades personalizadas
titleSuffix: Azure Cognitive Services
description: Mejore los índices de Cognitive Search mediante el Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: cc7a7c478ada34aeea5815ec937b8442aa2bd98c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093078"
---
# <a name="tutorial-enrich-a-cognitive-search-index-with-custom-entities-from-your-data"></a>Tutorial: Enriquecimiento de un índice de Cognitive Search con entidades personalizadas a partir de los datos

En las empresas, tener una gran cantidad de documentos electrónicos puede suponer que la búsqueda en ellos sea una tarea larga y costosa. [Azure Cognitive Search](/azure/search/search-create-service-portal) puede ayudar a buscar en los archivos, en función de sus índices. El NER personalizado puede facilitar esta tarea, ya que extrae las entidades pertinentes de los archivos y enriquece el proceso de indexación de estos archivos.

En este tutorial aprenderá a:

* Crear un proyecto de Reconocimiento de entidades con nombre personalizado.
* Publicar una función de Azure.
* Agregar un índice a Azure Cognitive Search.

## <a name="prerequisites"></a>Prerrequisitos

* [Un recurso de idioma de Azure conectado a una cuenta de Azure Blob Storage](../how-to/create-project.md).
    * Para facilitar la instalación, se recomienda seguir las instrucciones para crear un recurso mediante Azure Portal. 
* [Un servicio de Azure Cognitive Search](/azure/search/search-create-service-portal) en la suscripción actual.
    * Puede usar cualquier nivel y cualquier región para este servicio.
* Una [aplicación de funciones de Azure](/azure/azure-functions/functions-create-function-app-portal).
* Descargue estos [datos de ejemplo](https://go.microsoft.com/fwlink/?linkid=2175226).

## <a name="create-a-custom-ner-project-through-language-studio"></a>Creación de un proyecto de NER personalizado mediante Language Studio

1. Inicie sesión en el [portal de Language Studio](https://aka.ms/LanguageStudio) y seleccione **Custom entity extraction** (Extracción de entidades personalizadas).

2. Seleccione el recurso de idioma. Asegúrese de que ha [habilitado la administración de identidades](../how-to/create-project.md#enable-identity-management-for-your-resource) y los roles para el recurso y la cuenta de almacenamiento.

3. En la parte superior de la pantalla de proyectos, seleccione **Create new project** (Crear nuevo proyecto). Si se solicita, elija la cuenta de almacenamiento en el menú que aparece.

    :::image type="content" source="../media/create-project.png" alt-text="Captura de pantalla de la página de creación del proyecto." lightbox="../media/create-project.png":::

4. Escriba la información del proyecto:

    | Clave | Descripción |
    |--|--|
    | Nombre | Nombre del proyecto. No podrá cambiar el nombre del proyecto después de crearlo. |
    | Descripción | Descripción del proyecto. |
    | Idioma | Idioma de los archivos del proyecto.|

    > [!NOTE]
    > Si los documentos van a estar en varios idiomas, seleccione la opción de **varios idiomas** en la creación del proyecto y establezca la opción de **idioma** en el idioma de la mayoría de los documentos.

5. Para los fines de este tutorial, use un **archivo de etiquetas existente** y seleccione el archivo de etiquetas que descargó de los datos de ejemplo.

## <a name="train-your-model"></a>Entrenamiento de un modelo

1. En el menú de la izquierda, seleccione **Train** (Entrenar).

2. Para entrenar un nuevo modelo, seleccione **Train a new model** (Entrenar un nuevo modelo) y escriba el nombre del modelo en el cuadro de texto siguiente.

    :::image type="content" source="../media/train-model.png" alt-text="Creación de un modelo" lightbox="../media/train-model.png":::

3. Seleccione el botón **Train** (Entrenar) en la parte inferior de la página.

4. Una vez finalizado el entrenamiento, puede [ver los detalles de la evaluación del modelo](../how-to/view-model-evaluation.md) y [mejorar el modelo](../how-to/improve-model.md).

## <a name="deploy-your-model"></a>Implementación del modelo

1. Seleccione **Deploy model** (Implementar modelo) en el menú de la izquierda.

2. Seleccione el modelo que quiere implementar y, en el menú superior, haga clic en **Deploy model** (Implementar modelo). Solo puede ver los modelos que hayan completado el entrenamiento satisfactoriamente.

## <a name="prepare-your-secrets-for-the-azure-function"></a>Preparación de los secretos para la función de Azure

A continuación, deberá preparar los secretos para la función de Azure. Los secretos del proyecto son los siguientes: 
* Punto de conexión
* Clave del recurso
* Id. de modelo

### <a name="get-your-custom-ner-project-secrets"></a>Obtención de los secretos del proyecto de NER personalizado

* Necesitará el **nombre del proyecto**. Los nombres de proyecto distinguen mayúsculas de minúsculas.

* También necesitará la ranura de implementación. 
   * Si ha implementado el modelo mediante Language Studio, la ranura de implementación será `prod` de forma predeterminada. 
   * Si ha implementado el modelo mediante programación, con la API, este es el nombre de la implementación que asignó en la solicitud.

### <a name="get-your-resource-keys-endpoint"></a>Obtención del punto de conexión de las claves de recurso

1. Vaya al recurso en [Azure Portal](https://ms.portal.azure.com/#home).

2. En el menú de la izquierda, seleccione **Keys and Endpoint** (Claves y punto de conexión). Necesitará el punto de conexión y una de las claves para las solicitudes de API.

    :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="Captura de pantalla que muestra la pantalla de clave y punto de conexión en Azure Portal" lightbox="../../media/azure-portal-resource-credentials.png":::
   
## <a name="edit-and-deploy-your-azure-function"></a>Edición e implementación de la función de Azure

1. Descargue y use la [función de ejemplo proporcionada](https://aka.ms/ct-cognitive-search-integration-tool).

2. Después de descargar la función de ejemplo, abra el archivo *program.cs* y escriba los secretos de la aplicación.

3. [Publique la función en Azure](/azure/azure-functions/functions-develop-vs?tabs=in-process#publish-to-azure).

## <a name="use-the-integration-tool"></a>Uso de la herramienta de integración

En las secciones siguientes, usará la [herramienta de integración de Cognitive Search](https://aka.ms/ct-cognitive-search-integration-tool) para integrar el proyecto con Azure Cognitive Search. Descargue este repositorio ahora. 

### <a name="prepare-configuration-file"></a>Preparación del archivo de configuración

1. En la carpeta que acaba de descargar, busque el [archivo de configuración de ejemplo](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Docs/Assets/configs.json). Ábralo en un editor de texto. 

2. Obtenga la cadena de conexión de la cuenta de almacenamiento siguiendo estos pasos:
    1. Vaya a la página de información general de su cuenta de almacenamiento en [Azure Portal](https://ms.portal.azure.com/#home).
    2. En la sección superior de la pantalla, copie el nombre del contenedor en el campo `containerName` del archivo de configuración, en `blobStorage`.  
    3. En la sección **Claves de acceso** del menú de la izquierda de la pantalla, copie la **cadena de conexión** en el campo `connectionString` del archivo de configuración, en `blobStorage`.

1. Obtenga el punto de conexión y las claves de Cognitive Search siguiendo estos pasos:
    1. Vaya a la página de información general del recurso en [Azure Portal](https://ms.portal.azure.com/#home).
    2. Copie la dirección **URL** de la sección superior derecha de la página en el campo `endpointUrl` dentro de `cognitiveSearch`.
    3. Vaya a la sección **Claves** del menú situado a la izquierda de la pantalla. Copie la **clave de administración principal** en el campo `apiKey` dentro de `cognitiveSearch`.

3. Obtención del punto de conexión y las claves de la función de Azure
    
    1. Para obtener el punto de conexión y las claves de la función de Azure, vaya a la página de información general de la función en [Azure Portal](https://ms.portal.azure.com/#home).
    2. Vaya al menú **Funciones** de la izquierda de la pantalla y haga clic en la función que ha creado.
    3. En el menú superior, haga clic en **Obtener la dirección URL de la función**. La dirección URL tendrá el formato siguiente: `YOUR-ENDPOINT-URL?code=YOUR-API-KEY`. 
    4. Copie `YOUR-ENDPOINT-URL` en el campo `endpointUrl` del archivo de configuración, en `azureFunction`. 
    5. Copie `YOUR-API-KEY` en el campo `apiKey` del archivo de configuración, en `azureFunction`. 

### <a name="prepare-schema-file"></a>Preparación del archivo de esquema

En la carpeta que descargó anteriormente, busque el [archivo de esquema de ejemplo](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Docs/Assets/app-schema.json). Ábralo en un editor de texto. 

Las entradas de la matriz `entityNames` serán los nombres de entidad que haya asignado al crear el proyecto. Puede copiarlas y pegarlas desde el proyecto en [Language Studio](https://aka.ms/custom-extraction), o 

### <a name="run-the-index-command"></a>Ejecute el comando `Index`.

Una vez que haya completado la configuración y el archivo de esquema, puede indexar el proyecto. Coloque el archivo de configuración en la misma ruta de acceso de la herramienta CLI y ejecute el siguiente comando:

```cli
    indexer index --schema <path/to/your/schema> --index-name <name-your-index-here>
```

Reemplace `name-your-index-here` por el nombre del índice que aparece en la instancia de Cognitive Search.

## <a name="next-steps"></a>Pasos siguientes

* [Búsqueda de la aplicación con el SDK de Cognitive Search](/azure/search/search-howto-dotnet-sdk#run-queries)
