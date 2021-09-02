---
title: 'Administración de la configuración de modelos y del aprendizaje: Personalizer'
description: La configuración del aprendizaje y los modelos con aprendizaje automático se puede exportar para realizar copias de seguridad en su propio sistema de control de código fuente.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1e1e5dd9c21b89aba4c905656dd79c7934f8b061
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831607"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Administración de la configuración de modelos y del aprendizaje

La configuración del aprendizaje y los modelos con aprendizaje automático se puede exportar para realizar copias de seguridad en su propio sistema de control de código fuente.

## <a name="export-the-personalizer-model"></a>Exportación del modelo de Personalizer

En la sección de administración de recursos para **Configuración de aprendizaje y modelo**, revise las fechas de creación y de última actualización del modelo y exporte el modelo actual. Puede usar Azure Portal o las API de Personalizer para exportar un archivo de modelo para el archivado.

![Exportación del modelo de Personalizer actual](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Limpieza de datos para el bucle de aprendizaje

1. En Azure Portal, en el recurso de Personalizer, en la página **Configuración de aprendizaje y modelo**, seleccione **Borrar datos**.
1. Para borrar todos los datos y restablecer el bucle de aprendizaje al estado original, active las tres casillas.

    ![En Azure Portal, desactive los datos del recurso Personalizer.](./media/settings/clear-data-from-personalizer-resource.png)

    |Value|Propósito|
    |--|--|
    |Datos de personalización y recompensa registrados.|Estos datos de registro se usan en las evaluaciones sin conexión. Borre los datos si va a restablecer el recurso.|
    |Restablezca el modelo Personalizer.|Este modelo cambia en cada reentrenamiento. Esta frecuencia de entrenamiento se especifica en la **frecuencia del modelo de carga** en la página **Configuración**. |
    |Establezca la directiva de aprendizaje como predeterminada.|Si ha cambiado la directiva de aprendizaje como parte de una evaluación sin conexión, se restablece a la original.|

1. Seleccione **Borrar datos seleccionados** para comenzar el proceso de limpieza. El estado se comunica en las notificaciones de Azure, en el panel de navegación superior derecha.

## <a name="import-a-new-learning-policy"></a>Importación de una nueva directiva de aprendizaje

La configuración de una [directiva de aprendizaje](concept-active-learning.md#understand-learning-policy-settings) determina los _hiperparámetros_ del entrenamiento de modelos. Realice una [evaluación sin conexión](how-to-offline-evaluation.md) para buscar una nueva directiva de aprendizaje.

1. Abra [Azure Portal](https://portal.azure.com) y seleccione su recurso de Personalizer.
1. Seleccione **Configuración de modelo y aprendizaje** en la sección **Administración de recursos**.
1. En **Importar configuración de aprendizaje**, seleccione el archivo que creó con el formato JSON especificado anteriormente y, después, haga clic en el botón **Cargar**.

    Espere a recibir la notificación de que la directiva de aprendizaje se ha cargado correctamente.

## <a name="export-a-learning-policy"></a>Exportación de una directiva de aprendizaje

1. Abra [Azure Portal](https://portal.azure.com) y seleccione su recurso de Personalizer.
1. Seleccione **Configuración de modelo y aprendizaje** en la sección **Administración de recursos**.
1. En **Importar configuración de aprendizaje**, haga clic en el botón **Exportar configuración de aprendizaje**. De este modo, se guarda el archivo `json` en su equipo local.

## <a name="next-steps"></a>Pasos siguientes

[Análisis del bucle de aprendizaje con una evaluación sin conexión](how-to-offline-evaluation.md)
