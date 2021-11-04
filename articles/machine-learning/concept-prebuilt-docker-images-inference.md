---
title: Imágenes de Docker precompiladas
titleSuffix: Azure Machine Learning
description: Imágenes de Docker precompiladas para inferencia (puntuación) en Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, curated environments
ms.openlocfilehash: 0f7fc034ee6df962ecfe6e00b62b8899e223505c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563708"
---
# <a name="prebuilt-docker-images-for-inference"></a>Imágenes de Docker precompiladas para la inferencia

Se usan imágenes de contenedor de Docker precompiladas para la inferencia al implementar un modelo con Azure Machine Learning.  Las imágenes se precompilan con conocidos marcos de aprendizaje automático y paquetes de Python. También puede ampliar los paquetes para agregar otros paquetes mediante uno de los métodos siguientes:

* [Agregar paquetes de Python](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Usar una imagen de inferencia precompilada como base para un nuevo Dockerfile](how-to-extend-prebuilt-docker-image-inference.md). Con este método, puede instalar **paquetes de Python y paquetes apt**.

## <a name="why-should-i-use-prebuilt-images"></a>¿Por qué debo usar imágenes precompiladas?

* Reduce la latencia de la implementación de modelo.
* Mejora la tasa de éxito de la implementación de modelo.
* Evite la compilación innecesaria de imágenes durante la implementación de modelo.
* Disponga solo de las dependencias y el derecho de acceso necesarios en la imagen o contenedor. 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>Lista de imágenes de Docker precompiladas para la inferencia 

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Agregue paquetes de Python a imágenes precompiladas](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Use un paquete precompilado como base para un nuevo Dockerfile](how-to-extend-prebuilt-docker-image-inference.md).