---
title: Límites del servicio Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los límites de datos y del servicio al usar el Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: d56cec53d4fec33ec6db17667d95f9cc246796a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093279"
---
# <a name="custom-named-entity-recognition-ner-service-limits"></a>Límites del servicio Reconocimiento de entidades con nombre (NER) personalizado

Utilice este artículo para obtener información sobre los límites de datos y del servicio al usar NER personalizado.

## <a name="file-limits"></a>Límites de archivos

* Solo puede usar archivos `.txt` . Si los datos están en otro formato, puede usar el [comando Parse de CLUtils](https://github.com/microsoft/CogSLanguageUtilities/blob/main/CLUtils/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) para abrir el documento y extraer el texto.

* Todos los archivos cargados en el contenedor deben contener datos. No se permiten archivos vacíos para el entrenamiento.

* Todos los archivos deben estar disponibles en la raíz del contenedor.

* La longitud máxima permitida para el archivo es de 128 000 caracteres, que son aproximadamente 28 000 palabras o 56 páginas.

* El [conjunto de datos de entrenamiento](how-to/train-model.md#data-split) debe incluir al menos 10 archivos y no más de 100 000 archivos.


## <a name="apis-limits"></a>Límites de las API

* Al usar la API de creación, hay un máximo de 10 solicitudes POST y 100 solicitudes GET por minuto.

* Al usar la API de análisis, hay un máximo de 20 solicitudes GET o POST por minuto.

* El tamaño máximo de archivo por cada solicitud es de 125 000 caracteres. Puede enviar hasta 25 archivos siempre que no superen colectivamente los 125 000 caracteres.

> [!NOTE]
> Si tiene que enviar documentos con un tamaño superior al límite permitido, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. Puede usar el [comando Chunk de CLUtils](https://github.com/microsoft/CogSLanguageUtilities/tree/main/CLUtils/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand) para este proceso.

## <a name="azure-resource-limits"></a>Límites de recursos de Azure

* Solo puede conectar 1 cuenta de almacenamiento por recurso. Este proceso es irreversible. Si conecta una cuenta de almacenamiento al recurso, no puede desvincularla más adelante.

* Puede tener hasta 500 proyectos por recurso.

* Los nombres de proyecto deben ser únicos dentro del mismo recurso tanto en NER personalizado como en la [clasificación personalizada de texto](../custom-classification/overview.md).

## <a name="regional-availability"></a>Disponibilidad regional 

La clasificación personalizada de texto solo está disponible en algunas regiones de Azure. Al crear un [recurso de Azure](how-to/create-project.md), se debe implementar en una de las siguientes regiones:
* **Oeste de EE. UU. 2**
* **Oeste de Europa**
    
## <a name="project-limits"></a>Límites del proyecto

* Solo puede conectar 1 contenedor de almacenamiento para cada proyecto. Este proceso es irreversible. Si conecta un contenedor al proyecto, no podrá desconectarlo más adelante.

* Solo puede tener un [archivo de etiquetas](how-to/tag-data.md) por proyecto. No puede cambiar a otro archivo de etiquetas más adelante. Solo puede actualizar las etiquetas dentro del proyecto.

* No puede cambiar el nombre del proyecto después de su creación.

* Debe tener un mínimo de 10 archivos etiquetados en el proyecto y un máximo de 100 000 archivos.

* Puede tener hasta 10 modelos entrenados por proyecto.

* Los nombres de modelo deben ser únicos dentro del mismo proyecto.

* No puede cambiar el nombre del modelo después de su creación.

* Solo puede entrenar un modelo a la vez por proyecto.

## <a name="entity-limits"></a>Límites de entidad

* Se recomienda que la entidad etiquetada no supere las 10 palabras, pero el máximo permitido es de 100 caracteres.

* Debe tener al menos 1 tipo de entidad en el proyecto y el máximo es de 200 tipos de entidad.

* Se recomienda tener alrededor de 200 instancias etiquetadas por entidad y debe tener un mínimo de 10 instancias etiquetadas por entidad.

## <a name="naming-limits"></a>Límites de nomenclatura

| Atributo | Límites |
|--|--|
| Nombre de proyecto |  Solo puede usar letras `(a-z, A-Z)` y números `(0-9)` sin espacios. |
| Nombre del modelo |  Solo puede usar letras `(a-z, A-Z)` y números `(0-9)` sin espacios. |
| Nombres de entidad| Solo puede usar letras `(a-z, A-Z)`, números `(0-9)` y los símbolos `@ # _ . , ^ \ [ ]`. |
| Nombres de archivo | Solo puede usar letras `(a-z, A-Z)` y números `(0-9)` sin espacios. |

## <a name="next-steps"></a>Pasos siguientes

[Introducción a NER personalizado](../overview.md)
