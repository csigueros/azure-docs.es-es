---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 8122a9c001ce56edefaa0215fc20fe183d2cd00c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367579"
---
Si observa alguno de estos problemas, use estas sugerencias.

|Problema  |Resolución  |
|---------|---------|
|Solo se pueden usar los conjuntos de datos creados en almacenes de datos de blobs.     |  Limitación conocida de la versión actual.       |
|Después de la creación, el proyecto muestra el mensaje "Initializing" (Inicializando) durante mucho tiempo.     | Actualice manualmente la página. La inicialización debería completarse aproximadamente en 20 puntos de datos por segundo. La falta de actualización automática es un problema conocido.         |
|Elementos etiquetados recientemente no visibles en la revisión de datos.     |   Para cargar todas las imágenes etiquetadas, elija el botón **Primera**. El botón **Primera** le llevará al principio de la lista, pero carga todos los datos etiquetados.      |
|No se puede asignar un conjunto de tareas a un etiquetador concreto.     |   Limitación conocida de la versión actual.  |