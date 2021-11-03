---
title: Definiciones que se usan en la clasificación de texto personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre las definiciones que se usan en la clasificación de texto personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 0699f4c32a62be3b07fd795357302f71ffcce8c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091504"
---
# <a name="terms-and-definitions-used-in-custom-text-classification"></a>Términos y definiciones que se usan en la clasificación de texto personalizado 

Obtenga información sobre las definiciones que se usan en la clasificación de texto personalizado. 

## <a name="project"></a>Proyecto

Un proyecto es un área de trabajo para crear modelos de inteligencia artificial personalizados basados en datos. A su proyecto solo puede acceder usted y otros usuarios que tengan acceso de colaborador al recurso de Azure que se usa.
Como requisito previo para crear un proyecto de clasificación de texto personalizado, es preciso [conectar un recurso a una cuenta de almacenamiento](how-to/create-project.md).
Como parte del flujo de creación del proyecto, debe conectarlo a un contenedor de blobs donde haya cargado el conjunto de datos. El proyecto incluye automáticamente todos los archivos `.txt` disponibles en el contenedor. En el proyecto puede tener varios modelos, todos ellos basados en el mismo conjunto de datos. Para más información, consulte el artículo sobre [límites del servicio](service-limits.md).

Dentro del proyecto puede realizar las siguientes operaciones:

* [Etiquetar los datos](./how-to/tag-data.md): el proceso de etiquetado de cada archivo del conjunto de datos con las clases o clases respectivas para que, al entrenar el modelo, aprenda a clasificar los archivos.
* [Compilar y entrenar el modelo](./how-to/train-model.md): el paso principal del proyecto. En este paso, el modelo empieza a aprender de los datos etiquetados. 
* [Ver los detalles de evaluación del modelo](./how-to/view-model-evaluation.md): examine el rendimiento del modelo para decidir si es posible mejorar o si está satisfecho con los resultados.
* [Mejorar el modelo (opcional)](./how-to/improve-model.md): determine qué ha ido mal con el modelo y mejore el rendimiento.
* [Implementar el modelo](quickstart.md?pivots=language-studio#deploy-your-model): haga que el modelo esté disponible para su uso. 
* [Probar el modelo](quickstart.md?pivots=language-studio#test-your-model): pruebe el modelo y vea cómo funciona.

### <a name="project-types"></a>Tipos de proyecto

La clasificación de texto personalizado admite dos tipos de proyectos:

* **Clasificación mediante etiqueta única**: solo puede asignar una clase a cada archivo del conjunto de datos. Por ejemplo, si es un script de película, el archivo solo puede ser `Action`, `Thriller` o `Romance`.

* **Clasificación mediante varias etiquetas**: puede asignar **varias** clases a cada archivo del conjunto de datos. Por ejemplo, si es un guión de una película, el archivo solo ser `Action` o `Action` y `Thriller` o `Romance`.

## <a name="model"></a>Modelo

Un modelo es un objeto entrenado para realizar una determinada tarea, en nuestro caso la clasificación de texto personalizado. Para entrenar los modelos se proporcionan datos etiquetados de los que aprenden para que posteriormente se puedan usar para clasificar texto. Una vez que esté satisfecho con el rendimiento del modelo, se puede implementar, lo que hace que esté [disponible para su consumo](https://aka.ms/ct-runtime-swagger).

## <a name="class"></a>Clase

Una clase es una categoría definida por el usuario que se usa para indicar la clasificación general del texto. Los datos se etiquetarán con las clases asignadas antes de pasarlos al modelo para el entrenamiento. 
