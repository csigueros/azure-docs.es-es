---
title: Descripción de las características de búsqueda de Azure Purview
description: En este artículo se explica cómo Azure Purview permite la detección de datos mediante características de búsqueda.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 6bc9029a4b75206b5de99622729415b1aa36d4a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207533"
---
# <a name="understand-search-features-in-azure-purview"></a>Descripción de las características de búsqueda de Azure Purview

En este artículo se proporciona información general sobre la experiencia de búsqueda en Azure Purview. La búsqueda es una funcionalidad básica de la plataforma de Purview, que permite las experiencias de gobernanza y detección de datos de una organización.

## <a name="search"></a>Search

La experiencia de búsqueda de Purview se basa en un índice de búsqueda administrado. Después de que se registra un origen de datos con Purview, el servicio de búsqueda indexa sus metadatos para permitir que se detecten fácilmente. El índice proporciona funcionalidades de relevancia de búsqueda y consulta millones de recursos de metadatos para completar las solicitudes de búsqueda. La búsqueda le ayuda a detectar, comprender y usar los datos para aprovecharlos al máximo.

La experiencia de búsqueda en Purview es un proceso de tres fases:

1. En el cuadro de búsqueda se muestra el historial que contiene las palabras clave y los recursos usados recientemente.
1. Al empezar a pulsar las teclas, la búsqueda sugiere las palabras clave y los recursos que coinciden. 
1. Aparece la página de resultados de la búsqueda con los recursos que coinciden con la palabra clave especificada.

## <a name="reduce-the-time-to-discover-data"></a>Reducción del tiempo de detección de datos

Para los consumidores de datos, la detección de datos es el primer paso en una carga de trabajo de análisis o gobernanza de datos. Esta operación puede llevar mucho tiempo, ya que es posible que no sepa dónde encontrar los datos que desea. Incluso después de encontrarlos, puede que tenga dudas sobre si puede confiar en ellos o aceptar sus dependencias. 

El objetivo de la búsqueda en Azure Purview es acelerar el proceso de detección de datos mediante gestos, a fin de encontrar rápidamente los datos que realmente importan.

## <a name="recent-search-and-suggestions"></a>Búsqueda y sugerencias recientes

Puede que muchas veces esté trabajando en varios proyectos a la vez. Para que sea más fácil reanudar los proyectos anteriores, la búsqueda de Purview ofrece la posibilidad de ver las palabras clave y sugerencias de búsqueda recientes. Además, puede administrar el historial de búsqueda reciente seleccionando **View all** (Ver todo) en la lista desplegable del cuadro de búsqueda.

## <a name="filters"></a>Filtros

Los filtros (también conocidos como *facetas*) están diseñados para complementar la búsqueda y se muestran en la página de resultados de la búsqueda. Estos filtros incluyen clasificación, etiqueta de confidencialidad, origen de datos y propietarios. Los usuarios pueden seleccionar valores específicos de un filtro para ver solo los recursos de datos coincidentes y restringir a estos el resultado de la búsqueda.

## <a name="hit-highlighting"></a>Resaltado de referencias

Las palabras clave coincidentes de la página de resultado de la búsqueda se resaltan para que sea más fácil identificar el motivo por el que la búsqueda devolvió un recurso de datos. La coincidencia de palabras clave puede producirse en varios campos, como el nombre, la descripción y el propietario del recurso de datos.

Puede que no resulte claro el motivo de que un recurso de datos se incluya en la búsqueda, incluso con el resaltado de coincidencias habilitado. De forma predeterminada, se realizan búsquedas en todas las propiedades. Por lo tanto, es posible que se devuelva un recurso de datos debido a una coincidencia en una propiedad de nivel de columna. Y, puesto que distintos usuarios pueden anotar los recursos de datos con sus propias clasificaciones y descripciones, no todos los metadatos se muestran en la lista de resultados de la búsqueda.

## <a name="sort"></a>Sort

Para ordenar los resultados de la búsqueda, los usuarios tienen dos opciones. Pueden ordenar por el nombre del recurso o por la relevancia de búsqueda predeterminada.

## <a name="search-relevance"></a>Relevancia de búsqueda

La relevancia es el criterio de ordenación predeterminado en la página de resultados de la búsqueda. La relevancia de búsqueda encuentra los documentos que incluyen la palabra clave de búsqueda (algunas o todas). Los recursos que contienen muchas instancias de la palabra clave de búsqueda se clasifican en los primeros puestos. Además, continuamente se optimizan mecanismos de puntuación personalizados para mejorar la relevancia de la búsqueda.

## <a name="next-steps"></a>Pasos siguientes

* [Inicio rápido: creación de una cuenta de almacenamiento de Azure Purview en Azure Portal](create-catalog-portal.md).
* [Inicio rápido: creación de una cuenta de Azure Purview mediante Azure PowerShell o la CLI de Azure](create-catalog-powershell.md).
* [Uso de Purview Studio](use-purview-studio.md)
