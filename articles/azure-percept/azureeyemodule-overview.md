---
title: Módulo de inteligencia artificial de Azure Percept Vision
description: Información general de azureeyemodule, que es el módulo responsable de ejecutar la carga de trabajo de visión IA en el Azure Percept DK.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: overview
ms.date: 08/09/2021
ms.custom: template-overview
ms.openlocfilehash: b09c5eb0dc2fa977c544d7da8178408dadcd08a6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222381"
---
# <a name="azure-percept-vision-ai-module"></a>Módulo de inteligencia artificial de Azure Percept Vision

Azureeyemodule es el nombre del módulo perimetral responsable de ejecutar la carga de trabajo de visión IA en el Azure Percept DK. Forma parte del conjunto de módulos perimetrales de Azure IoT y se implementa en el Azure Percept DK durante la [experiencia de configuración](./quickstart-percept-dk-set-up.md). En este artículo se proporciona información general sobre el módulo y su arquitectura.

## <a name="architecture"></a>Architecture

:::image type="content" source="media/azureeyemodule-overview/azureeyemodule-architecture.png" alt-text="Diagrama que muestra la arquitectura de azureeyemodule.":::

La carga de trabajo de Azure Percept en Azure Percept DK es una aplicación de C++ que se ejecuta dentro del contenedor de Docker azureeyemodule. Usa G-API de OpenCV para el procesamiento de imágenes y la ejecución del modelo. Azureeyemodule se ejecuta en el sistema operativo Mariner como parte del conjunto de módulos de Azure IoT que se ejecutan en el Azure Percept DK.

La carga de trabajo de Azure Percept está pensada para recibir imágenes y generar imágenes y mensajes. Las imágenes generadas pueden estar marcadas con dibujos como rectángulos de selección, máscaras de segmentación, uniones, etiquetas, etc. Los mensajes generados son un flujo JSON de resultados de inferencia que las tareas de nivel inferior pueden ingerir y usar.
Los resultados se sirven como una secuencia RTSP que está disponible en el puerto 8554 del dispositivo. Los resultados también se envían a otro módulo que se ejecuta en el dispositivo, que sirve la secuencia RTSP encapsulada en un servidor HTTP, que se ejecuta en el puerto 3000. En cualquier caso, solo se podrán ver en la red local.

> [!CAUTION]
> *No hay* cifrado ni autenticación con respecto a las fuentes RTSP. Cualquier usuario de la red local puede ver exactamente lo que el Azure Percept Vision está viendo si escribe la dirección correcta en un explorador web o en el reproductor multimedia RTSP.

La carga de trabajo de Azure Percept habilita varias características que los usuarios finales pueden aprovechar:
- Una solución sin código para casos de uso comunes de Computer Vision, como la clasificación de objetos y la detección de objetos comunes.
- Una solución avanzada, donde un desarrollador puede traer su propio modelo entrenado (potencialmente en cascada) al dispositivo y ejecutarlo, pasando posiblemente los resultados a otro módulo de IoT de su propia creación que se ejecuta en el dispositivo.
- Bucle de reentrenamiento para capturar imágenes del dispositivo de forma periódica, volver a entrenar el modelo en la nube y, a continuación, volver a insertar el modelo recién entrenado en el dispositivo. Uso de la capacidad del dispositivo para actualizar e intercambiar modelos sobre la marcha.

## <a name="ai-workload-details"></a>Detalles de la carga de trabajo de IA
La aplicación de carga de trabajo es de código abierto en el [repositorio de GitHub](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app) de desarrollo avanzado de Azure Percept y se integra en muchos módulos pequeños de C++, siendo algunos de los más importantes:
- [main.cpp:](https://github.com/microsoft/azure-percept-advanced-development/blob/main/azureeyemodule/app/main.cpp) Lo configura todo y, a continuación, ejecuta el bucle principal.
- [iot:](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/iot) esta carpeta contiene módulos que controlan los mensajes entrantes y salientes desde Azure IoT Edge Hub y el método de actualización gemela.
- [model](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/model): esta carpeta contiene módulos para una jerarquía de clases de modelos de Computer Vision.
- [kernels](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/kernels): esta carpeta contiene módulos para kernels de G-API, operaciones y funciones contenedoras de C++.

Los desarrolladores pueden compilar módulos personalizados o personalizar el módulo azureeyemodule actual mediante esta aplicación de carga de trabajo. 

## <a name="next-steps"></a>Pasos siguientes

- Ahora que tiene más información sobre azureeyemodule y la carga de trabajo de Azure Percept, pruebe a usar su propio modelo o canalización siguiendo uno de [estos tutoriales](https://github.com/microsoft/azure-percept-advanced-development/blob/main/tutorials/README.md)
- O bien, pruebe **aprendizaje por transferencia** mediante uno de nuestros [cuadernos de aprendizaje automático](https://github.com/microsoft/azure-percept-advanced-development/tree/main/machine-learning-notebooks) listos para usar

