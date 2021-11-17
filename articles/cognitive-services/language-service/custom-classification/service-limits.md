---
title: Límites de clasificación de texto personalizada
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los límites de datos y velocidad al usar la clasificación de texto personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, references_regions, ignite-fall-2021
ms.openlocfilehash: 06db585531cb3a73b291f2c8d45de18a3d298fb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484093"
---
# <a name="custom-text-classification-limits"></a>Límites de clasificación de texto personalizada

Use este artículo para obtener información sobre los límites de datos y velocidad al usar la clasificación de texto personalizado.

## <a name="file-limits"></a>Límites de archivos

* Solo se pueden utilizar archivos `.txt` para la clasificación de textos personalizados. Si los datos están en otro formato, puede usar el [comando de análisis CLUtils](https://aka.ms/CognitiveServicesLanguageUtilities) para abrir el documento y extraer el texto.

* Todos los archivos cargados en el contenedor deben contener datos, no se permite el entrenamiento de archivos vacíos.

* Todos los archivos deben estar disponibles en la raíz del contenedor.

* El [conjunto de datos de entrenamiento](how-to/train-model.md#data-splits) debe incluir al menos 10 archivos y no más de 1 000 000 archivos.

## <a name="api-limits"></a>Límites de API

**API de creación**

* Puede enviar un máximo de 10 solicitudes POST y 100 solicitudes GET por minuto.

**API de análisis**

* Puede enviar un máximo de 20 solicitudes GET o POST por minuto.

* El tamaño máximo de los archivos por solicitud es de 125 000 caracteres. Puede enviar hasta 25 archivos, siempre y cuando su tamaño colectivo no supere los 125 000 caracteres.

> [!NOTE]
> Si tiene que enviar documentos con un tamaño superior al límite permitido, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. Puede usar el [comando de fragmento de CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md) para este proceso.

## <a name="azure-resource-limits"></a>Límites de recursos de Azure

* Solo puede conectar 1 cuenta de almacenamiento por recurso. Este proceso es irreversible. Si conecta una cuenta de almacenamiento al recurso, no puede desconectarla más adelante.

* Puede tener hasta 500 proyectos por recurso.

* Los nombres de los proyectos tienen que ser únicos dentro del mismo recurso, tanto en las funciones personalizadas de reconocimiento de entidades con nombre (NER) como en las de clasificación de texto.

## <a name="regional-availability"></a>Disponibilidad regional 

La clasificación personalizada de texto solo está disponible en algunas regiones de Azure. Al crear un [recurso de Azure](how-to/create-project.md), se debe implementar en una de las siguientes regiones:
* **Oeste de EE. UU. 2**
* **Oeste de Europa**

## <a name="project-limits"></a>Límites del proyecto

* Solo puede conectar un contenedor de almacenamiento para cada proyecto. Este proceso es irreversible, si conecta un contenedor al proyecto, no podrá desconectarlo más adelante.

* Solo puede tener un archivo de etiquetas por proyecto. No puede cambiar el archivo de etiquetas más adelante, solo puede actualizar las etiquetas dentro del proyecto.

* No puede cambiar el nombre del proyecto después de su creación.

* Debe tener un mínimo de 10 archivos etiquetados en el proyecto y un máximo de 1 000 000 archivos.

* Puede tener hasta 10 modelos entrenados por proyecto.

* Los nombres de modelo deben ser únicos dentro del mismo proyecto.

* No puede cambiar el nombre del modelo después de su creación.

* Solo puede entrenar un modelo a la vez por proyecto.

## <a name="classes-limits"></a>Límites de clases

* Debe tener al menos 2 clases en el proyecto. <!-- The maximum is 200 classes. -->

* Se recomienda tener alrededor de 200 instancias etiquetadas por clase y debe tener un mínimo de 10 instancias etiquetadas por clase.

## <a name="naming-limits"></a>Límites de nomenclatura

| Atributo | Límites |
|--|--|
| Nombre de proyecto |  Solo puede usar letras `(a-z, A-Z)` y números `(0-9)` sin espacios. |
| Nombre del modelo |  Solo puede usar letras `(a-z, A-Z)`, números `(0-9)` y los símbolos `@ # _ . , ^ \ [ ]` |
| nombres de entidad| Solo puede usar letras `(a-z, A-Z)`, números `(0-9)` y los símbolos `@ # _ . , ^ \ [ ]` |
| Nombres de archivo | Solo puede usar letras `(a-z, A-Z)` y números `(0-9)` sin espacios. |
