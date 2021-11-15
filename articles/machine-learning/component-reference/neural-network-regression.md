---
title: 'Neural Network Regression: Referencia del componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Neural Network Regression (Regresión de red neuronal) de Azure Machine Learning para crear un modelo de regresión mediante un algoritmo de red neuronal personalizable.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a3db08281653032fee660d262cae2e77c143078a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565887"
---
# <a name="neural-network-regression-component"></a>Componente Neural Network Regression

*Crea un modelo de regresión usando un algoritmo de red neuronal*  
  
 Categoría: Machine Learning/Inicializar modelo/Regresión
  
## <a name="component-overview"></a>Información general sobre los componentes  

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Utilice este componente para crear un modelo de regresión usando un algoritmo de red neuronal personalizable.
  
 Aunque las redes neuronales son ampliamente conocidas por su uso en problemas complejos de aprendizaje profundo y modelado, como el reconocimiento de imágenes, se adaptan fácilmente a los problemas de regresión. Cualquier clase de modelo estadístico puede denominarse red neuronal si utiliza pesos adaptables y puede aproximar funciones no lineales de sus entradas. Por lo tanto, la regresión de red neuronal es adecuada en los problemas para los que un modelo de regresión más tradicional no puede encontrar una solución.
  
 La regresión de red neuronal es un método de aprendizaje supervisado y, por lo tanto, requiere un *conjunto de datos etiquetado*, que incluya una columna de etiqueta. Dado que un modelo de regresión predice un valor numérico, la columna de etiqueta debe ser de un tipo de datos numérico.  
  
 Puede entrenar el modelo proporcionando el modelo y el conjunto de datos etiquetado como entrada para [Entrenar modelo](./train-model.md). Después, el modelo entrenado puede utilizarse para predecir valores para los nuevos ejemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar la regresión de red neuronal 

Las redes neuronales se pueden personalizar ampliamente. En esta sección se describe cómo crear un modelo mediante dos métodos:
  
+ [Crear un modelo de red neuronal con la arquitectura predeterminada](#bkmk_DefaultArchitecture)  
  
    Si acepta la arquitectura predeterminada de red neuronal, use el panel **Propiedades** para establecer los parámetros que controlan el comportamiento de la red neuronal, como el número de nodos de la capa oculta, la velocidad de aprendizaje y la normalización.

    Empiece aquí si no está familiarizado con las redes neuronales. El componente admite muchas personalizaciones, así como el ajuste de modelos, sin un conocimiento profundo de las redes neuronales. 

+ Definir una arquitectura personalizada para una red neuronal 

    Use esta opción si desea agregar capas ocultas adicionales o personalizar completamente la arquitectura de red, sus conexiones y las funciones de activación.
    
    Esta opción es mejor si ya está un poco familiarizado con las redes neuronales. Use el lenguaje Net# para definir la arquitectura de red.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Crear un modelo de red neuronal con la arquitectura predeterminada

1.  Agregue el componente **Neural Network Regression** (Regresión de red neuronal) a la canalización del diseñador. Puede encontrar este componente en **Machine Learning**, **Inicializar**, en la categoría **Regresión**. 
  
2. Para indicar cómo quiere que se entrene el modelo, establezca la opción **Create trainer mode** (Crear modo entrenador).  
  
    -   **Single Parameter** (Parámetro único): Elija esta opción si ya sabe cómo desea configurar el modelo.

    -   **Parameter Range** (Intervalo de parámetros): seleccione esta opción si no está seguro de los mejores parámetros y quiere ejecutar un barrido de parámetros. Seleccione un rango de valores que iterar y el módulo [Optimización de hiperparámetros de un modelo](tune-model-hyperparameters.md) itera en todas las combinaciones posibles de los valores proporcionados para determinar los hiperparámetros que generan los resultados óptimos.   

3.  En **Hidden layer specification** (Especificación de capa oculta), seleccione **Fully connected case** (Caso completamente conectado). Esta opción crea un modelo mediante la arquitectura predeterminada de red neuronal, lo que, para un modelo de regresión de red neuronal, tiene estos atributos:  
  
    + La red tiene una sola capa oculta.
    + La capa de salida está completamente conectada a la capa oculta, y esta a la capa de entrada.
    + El usuario puede establecer el número de nodos de la capa oculta (el valor predeterminado es 100).  
  
    Dado que el número de nodos de la capa de entrada viene determinado por el número de características de los datos de entrenamiento, en un modelo de regresión solo puede haber un nodo en la capa de salida.  
  
4. Para **Número de nodos ocultos**, escriba el número de nodos ocultos. El valor predeterminado es una capa oculta con 100 nodos. (Esta opción no está disponible si se define una arquitectura personalizada con Net#).
  
5.  Para **Velocidad de aprendizaje**, escriba un valor que defina el paso llevado a cabo en cada iteración, antes de la corrección. Un valor mayor para la velocidad de aprendizaje puede hacer que el modelo converja con mayor rapidez, pero puede superar los mínimos locales.

6.  Para **Number of learning iterations** (Número de iteraciones de aprendizaje), especifique el número máximo de veces que el algoritmo procesa los casos de entrenamiento.


8.  Para **The momentum** (El momentum), escriba el valor que se debe aplicar durante el aprendizaje como peso en los nodos de iteraciones anteriores.

10. Seleccione la opción **Shuffle examples** (Ejemplos de orden aleatorio), para cambiar el orden de los casos entre iteraciones. Si anula la selección de esta opción, los casos se procesan exactamente en el mismo orden cada vez que se ejecuta la canalización.
  
11. Para **Random number seed** (Inicialización de número aleatorio), puede escribir opcionalmente un valor que se usará como inicialización. Especificar un valor de inicialización es útil cuando desea asegurar la repetibilidad entre ejecuciones de la misma canalización.
  
13. Conecte un conjunto de datos de entrenamiento y entrene el modelo:

    + Si establece **Create trainer mode** (Crear modo entrenador) en **Single Parameter** (Parámetro único), conecte un conjunto de datos etiquetado y el componente [Entrenar modelo](train-model.md).  
  
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

- Para guardar una instantánea del modelo entrenado, seleccione la pestaña **Resultados** en el panel derecho del componente **Entrenar modelo**. Seleccione el icono **Registrar conjunto de datos** para guardar el modelo como componente reutilizable.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 