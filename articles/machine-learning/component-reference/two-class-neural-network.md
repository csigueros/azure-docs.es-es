---
title: 'Red neuronal de dos clases: referencia del componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente de red neuronal de dos clases en Azure Machine Learning para crear un clasificador binario.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: ec463fb42bbab9c97e9e1950c894764e27980e38
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565956"
---
# <a name="two-class-neural-network-component"></a>Componente Red neuronal de dos clases

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para crear un modelo de red neuronal que se pueda usar para predecir un destino con dos valores únicamente.

La clasificación mediante redes neuronales es un método de aprendizaje supervisado y, por lo tanto, requiere un *conjunto de datos etiquetado*, que incluya una columna de etiqueta. Por ejemplo, puede usar este modelo de red neuronal para predecir resultados binarios, como si un paciente tiene una determinada enfermedad o no, o bien si una máquina es propensa a errores dentro de una ventana de tiempo especificada.  

Después de definir el modelo, debe entrenarlo proporcionando un conjunto de datos etiquetado y el modelo como entrada para [Entrenar modelo](./train-model.md). Después, el modelo entrenado puede utilizarse para predecir valores para las nuevas entradas.

### <a name="more-about-neural-networks"></a>Más información sobre redes neuronales

Una red neuronal es un conjunto de capas interconectadas. Las entradas son la primera capa y se conectan a una capa de salida mediante un grafo acíclico que consta de nodos y aristas ponderadas.

Entre las capas de entrada y salida puede insertar varias capas ocultas. La mayoría de tareas predictivas pueden realizarse fácilmente con solo una o varias capas ocultas. Sin embargo, las investigaciones recientes han demostrado que las redes neuronales profundas (DNN) con muchas capas pueden ser eficaces en tareas complejas, como en el reconocimiento de imágenes o de voz. Las capas sucesivas se usan para modelar los crecientes niveles de profundidad semántica.

La relación entre entradas y salidas se aprende mediante el entrenamiento de la red neuronal con los datos de entrada. La dirección del grafo procede desde las entradas a través de la capa oculta y hacia la capa de salida. Todos los nodos de una capa se conectan mediante las aristas ponderadas a los nodos de la capa siguiente.

Para calcular la salida de la red para una entrada determinada, se calcula un valor en cada nodo en las capas ocultas y en la capa de salida. El valor se establece calculando la suma ponderada de los valores de los nodos de la capa anterior. A continuación, se aplica una función de activación a esa suma ponderada.
  
## <a name="how-to-configure"></a>Cómo se configura

1.  Agregue el componente **Red neuronal de dos clases** a la canalización. Puede encontrar este módulo en **Machine Learning**, **Inicializar**, en la categoría **Clasificación**.  
  
2.  Especifique cómo quiere que se entrene el modelo, estableciendo la opción **Create trainer mode** (Crear modo entrenador).  
  
    -   **Single Parameter** (Parámetro único): Elija esta opción si ya sabe cómo desea configurar el modelo.

    -   **Intervalo de parámetros**: si no está seguro de los mejores parámetros, puede encontrar los óptimos mediante el componente [Optimización de hiperparámetros de un modelo](tune-model-hyperparameters.md). Proporcionará un intervalo de valores, y el instructor recorrerá en iteración varias combinaciones de la configuración para determinar la combinación de valores que genera el mejor resultado.  

3.  En **Hidden layer specification** (Especificación de capa oculta), seleccione el tipo de arquitectura de red que se va a crear.  
  
    -   **Fully connected case** (Caso completamente conectado): Usa la arquitectura de red neuronal predeterminada, que se define para las redes neuronales de dos clases como sigue:
  
        -   Tiene una capa oculta.
  
        -   La capa de salida está completamente conectada a la capa oculta, y esta a la capa de entrada.
  
        -   El número de nodos de la capa de entrada es igual al número de características de los datos de entrenamiento.
  
        -   El usuario establece el número de nodos de la capa oculta. El valor predeterminado es 100.
  
        -   El número de nodos es igual al número de clases. Para una red neuronal de dos clases, significa que todas las entradas deben asignarse a uno de los dos nodos en el nivel de salida.

5.  Para **Velocidad de aprendizaje**, defina el tamaño del paso llevado a cabo en cada iteración, antes de la corrección. Un valor mayor para la velocidad de aprendizaje puede hacer que el modelo converja con mayor rapidez, pero puede superar los mínimos locales.

6.  Para **Number of learning iterations** (Número de iteraciones de aprendizaje), especifique el número máximo de veces que el algoritmo debe procesar los casos de entrenamiento.

7.  Para **The initial learning weights diameter** (El diámetro de pesos de aprendizaje inicial), escriba un valor que determine los pesos de nodo en el inicio del proceso de aprendizaje.

8.  Para **The momentum** (El momentum), especifique un peso que se debe aplicar durante el aprendizaje a los nodos de iteraciones anteriores.  

10. Seleccione la opción **Shuffle examples** (Ejemplos de orden aleatorio) para ordenar de forma aleatoria los casos entre iteraciones. Si anula la selección de esta opción, los casos se procesan exactamente en el mismo orden cada vez que se ejecuta la canalización.
  
11. Para **Random number seed** (Inicialización de número aleatorio), escriba un valor que se usará como inicialización.
  
     Especificar un valor de inicialización es útil cuando desea asegurar la repetibilidad entre ejecuciones de la misma canalización.  En caso contrario, se utiliza un valor de reloj del sistema como la inicialización, lo que puede producir resultados ligeramente diferentes cada vez que ejecute la canalización.
  
13. Agregue un conjunto de datos etiquetado a la canalización y entrene el modelo:

    + Si establece **Crear el modo de entrenador** en **Parámetro único**, conecte un conjunto de datos etiquetado y el componente [Entrenar modelo](train-model.md).  
  
    + Si establece **Create trainer mode** (Crear el modo de entrenador) en **Parameter Range** (Intervalo de parámetros), conecte un conjunto de datos etiquetado y entrene el modelo mediante [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Optimizar los hiperparámetros del modelo).  
  
    > [!NOTE]
    > 
    > Si pasa un intervalo de parámetros a [Train Model](train-model.md) (Entrenar modelo), solo utiliza el valor predeterminado en la lista de parámetros única.  
    > 
    > Si pasa un único conjunto de valores de parámetro al componente [Optimizar los hiperparámetros del modelo](tune-model-hyperparameters.md), cuando espera un intervalo de valores para cada parámetro, omite los valores y usa los valores predeterminados para el aprendiz.  
    > 
    > Si selecciona la opción **Parameter Range** (Intervalo de parámetros) y especifica un valor único para algún parámetro, ese valor único que haya especificado se utilizará en todo el barrido, incluso si otros parámetros cambian en un intervalo de valores.  
  
14. Envíe la canalización.

## <a name="results"></a>Results

Una vez completado el entrenamiento:

+ Para guardar una instantánea del modelo entrenado, seleccione la pestaña **Salidas** en el panel derecho del componente **Entrenar modelo**. Seleccione el icono **Registrar conjunto de datos** para guardar el modelo como componente reutilizable.

+ Para usar el modelo de puntuación, agregue el componente **Puntuar modelo** a una canalización.


## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
