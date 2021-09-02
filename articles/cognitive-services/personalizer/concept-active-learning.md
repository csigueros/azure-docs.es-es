---
title: 'Directiva de aprendizaje: Personalizer'
description: La configuración del aprendizaje determina los *hiperparámetros* del entrenamiento de modelo. Dos modelos de los mismos datos que se entrenan con configuraciones de aprendizaje diferentes se comportarán de manera distinta.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aad256981001d3b115570ddac0c3cf4993bc2da1
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830743"
---
# <a name="learning-policy-and-settings"></a>Directiva de aprendizaje y configuración

La configuración del aprendizaje determina los *hiperparámetros* del entrenamiento de modelo. Dos modelos de los mismos datos que se entrenan con configuraciones de aprendizaje diferentes se comportarán de manera distinta.

[La directiva de aprendizaje y la configuración](how-to-settings.md#configure-rewards-for-the-feedback-loop) se establecen en el recurso de Personalizer en Azure Portal.

## <a name="import-and-export-learning-policies"></a>Importación y exportación de directivas de aprendizaje

Puede importar y exportar archivos de directivas de aprendizaje desde Azure Portal. Use este método para guardar las directivas existentes, probarlas, reemplazarlas y archivarlas en el control de código fuente como artefactos para futuras referencias y auditorías.

Aprenda a [importar y exportar](how-to-manage-model.md#import-a-new-learning-policy) una directiva de aprendizaje en Azure Portal para el recurso de Personalizer.

## <a name="understand-learning-policy-settings"></a>Comprensión de la configuración de directivas de aprendizaje

La configuración de la directiva de aprendizaje no está pensada para modificarse. Cambie la configuración solo si comprende cómo afecta a Personalizer. Sin este conocimiento, podría causar problemas, como la invalidación de los modelos de Personalizer.

Personalizer usa [vowpalwabbit](https://github.com/VowpalWabbit) para entrenar y puntuar los eventos. Consulte la [documentación de vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sobre cómo editar la configuración de aprendizaje con vowpalwabbit. Una vez que tenga la propiedad arguments correcta para la línea de comandos, guarde el comando en un archivo con el siguiente formato (reemplace el valor de la propiedad arguments por el comando deseado) y cargue el archivo para importar la configuración de aprendizaje en el panel **Configuración de aprendizaje y modelos** de Azure Portal para el recurso de Personalizer.

El siguiente `.json` es un ejemplo de directiva de aprendizaje.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Comparación de directivas de aprendizaje

Puede comparar el rendimiento de las diferentes directivas de aprendizaje con datos anteriores en los registros de Personalizer mediante [evaluaciones sin conexión](concepts-offline-evaluation.md).

[Cargue sus propias directivas de aprendizaje](how-to-manage-model.md) para compararlas con la directiva actual de aprendizaje.

## <a name="optimize-learning-policies"></a>Optimización de directivas de aprendizaje

Personalizer puede crear una directiva de aprendizaje optimizada en una [evaluación sin conexión](how-to-offline-evaluation.md). Una directiva de aprendizaje optimizada que tiene mejores recompensas en una evaluación sin conexión, tendrá mejores resultados cuando se utilice en línea en Personalizer.

Después de optimizar una directiva de aprendizaje, puede aplicarla directamente en Personalizer para que reemplace inmediatamente a la directiva actual. También puede guardar la directiva optimizada para su evaluación adicional y, posteriormente, decidir si descartarla, guardarla o aplicarla.

## <a name="next-steps"></a>Pasos siguientes

* Información sobre [eventos activos e inactivos](concept-active-inactive-events.md).
