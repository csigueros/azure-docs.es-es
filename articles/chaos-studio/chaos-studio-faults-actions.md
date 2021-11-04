---
title: Errores y acciones en Azure Chaos Studio
description: Comprenda qué errores y acciones se producen en Azure Chaos Studio. ¿Cuál es la diferencia entre un error y una acción? ¿Cómo se define un error?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c4db4525c3535b0dda7bd8a21c46509f1203b748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092879"
---
# <a name="faults-and-actions-in-azure-chaos-studio"></a>Errores y acciones en Azure Chaos Studio

En Chaos Studio, cada actividad que tiene lugar como parte de un experimento se denomina **acción**, y el tipo de acción más común es un **error**. En este artículo se describen las acciones y los errores, así como las propiedades de cada uno.

## <a name="experiment-actions"></a>Acciones de un experimento

Una acción es cualquier actividad que se orquesta como parte de un experimento de caos. Las acciones se organizan en pasos y ramas, lo que permite ejecutar las acciones secuencialmente o en paralelo. Cada acción tiene las propiedades siguientes:
* **Nombre**: La acción específica que tiene lugar. Normalmente, un nombre toma la forma de un URN para la acción, por ejemplo, "urn:
* **Tipo**: La forma en que se ejecuta la acción. Las acciones pueden ser *continuas*, es decir, la acción se ejecuta sin parar durante un período (por ejemplo, aplicar presión de CPU durante 10 minutos); o *discretas*, es decir, la acción se produce solo una vez (por ejemplo, reiniciar una instancia de Redis Cache).

## <a name="types-of-actions"></a>Tipos de acciones

En Chaos Studio hay dos variedades de acciones:
- **Errores**: Esta acción provoca una interrupción en uno o varios recursos.
- **Retrasos de tiempo**: Esta acción "espera" sin afectar a ningún recurso. Es útil para pausar entre errores para esperar a que un sistema se vea afectado por el error anterior.

## <a name="faults"></a>Errores

Los errores son la acción más común en Chaos Studio. Los errores provocan una interrupción en un sistema, lo que le permite comprobar que el sistema controla eficazmente esa interrupción sin afectar a la disponibilidad. Los errores pueden ser destructivos (por ejemplo, terminar un proceso), aplicar presión (por ejemplo, agregar presión de memoria virtual), agregar latencia o provocar un cambio de configuración. Además de un nombre y un tipo, los errores también pueden tener una *duración*, si es continua, y *parámetros*. Los parámetros describen cómo se debe aplicar el error y son específicos del nombre del error. Por ejemplo, un parámetro para el error de conmutación por error de Cosmos DB es la región de lectura que se promoverá a la región de escritura durante el error de la región de escritura. Algunos parámetros son obligatorios, mientras que otros son opcionales.

Los errores se *basan en agentes* o son *directos del servicio* en función del tipo de destino. Un error basado en agente requiere que el agente de Chaos Studio esté instalado en una máquina virtual o en un conjunto de escalado de máquinas virtuales. El agente está disponible tanto para Windows como para Linux, pero no todos los errores están disponibles en ambos sistemas operativos. Para obtener información sobre los errores que se admiten en cada sistema operativo, consulte la [biblioteca de errores](chaos-studio-fault-library.md). Los errores directos del servicio no requieren ningún agente: se ejecutan directamente en un recurso de Azure.

Los errores también incluyen el nombre del selector que describe los recursos en los que se ejecutará el error. Puede obtener más información sobre los selectores en el [artículo sobre experimentos de caos](chaos-studio-chaos-experiments.md). Un error solo puede afectar a un recurso si este se ha incorporado como destino y tiene habilitada la funcionalidad de error correspondiente en el recurso.

## <a name="next-steps"></a>Pasos siguientes
Ahora que comprende las acciones y los errores, está listo para:
- [Crear y ejecutar tu primer experimento](chaos-studio-tutorial-service-direct.md)
