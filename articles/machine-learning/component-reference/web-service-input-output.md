---
title: 'Entrada de servicio web y Salida de servicio web: referencia de componentes'
description: Aprenda a usar los componentes de servicio web del diseñador de Azure Machine Learning para administrar entradas y salidas.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 67741a786f60b45e2e0cc7f728aa136397220899
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565952"
---
# <a name="web-service-input-and-web-service-output-components"></a>Componentes Entrada de servicio web y Salida de servicio web

En este artículo se describen los componentes Entrada de servicio web y Salida de servicio web del diseñador de Azure Machine Learning.

El componente Entrada de servicio web solo puede conectarse a un puerto del entrada de tipo **DataFrameDirectory**. El componente Salida de servicio web solo puede conectarse desde un puerto de salida de tipo **DataFrameDirectory**. Puede encontrar los dos componentes en el árbol de componentes, en la categoría **Servicio web**. 

El componente Entrada de servicio web indica en dónde entran los datos de usuario en la canalización. El componente Salida de servicio web indica a dónde se devuelven los datos de usuario en una canalización de inferencia en tiempo real.

## <a name="how-to-use-web-service-input-and-output"></a>Procedimientos para el uso de los módulos de entrada y salida del servicio web

Al [crear una canalización de inferencia en tiempo real](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) a partir de la canalización de entrenamiento, los componentes Entrada de servicio web y Salida de servicio web se agregan automáticamente para mostrar el lugar en donde los datos de usuario entran en la canalización y aquel a donde se devuelven. 

> [!NOTE]
> La generación automática de una canalización de inferencia en tiempo real es un proceso de la mejor opción basado en reglas. No hay ninguna garantía de corrección. 

Puede agregar o quitar manualmente los componentes Entrada de servicio web y Salida de servicio web para satisfacer sus requisitos. Asegúrese de que la canalización de inferencia en tiempo real tenga por lo menos un componente Entrada de servicio web y un componente Salida de servicio web. Si tiene varios componentes Entrada de servicio web y Salida de servicio web, asegúrese de que tengan nombres únicos. Puede escribir el nombre en el panel derecho del componente.

También puede crear manualmente una canalización de inferencia en tiempo real si agrega los componentes Entrada de servicio web y Salida de servicio web a la canalización sin enviar.

> [!NOTE]
> El tipo de canalización se establecerá la primera vez que la envíe. Asegúrese de agregar los componentes Entrada de servicio web y Salida de servicio web antes de enviar por primera vez.

En el ejemplo siguiente se muestra cómo crear manualmente una canalización de inferencia en tiempo real a partir del componente Ejecución de script de Python. 

![Ejemplo](media/module/web-service-input-output-example.png)
   
Después de enviar la canalización y de que la ejecución se complete correctamente, puede implementar el punto de conexión en tiempo real.
   
> [!NOTE]
>  En el ejemplo anterior, el módulo **Enter Data Manually** (Introducción manual de datos) proporciona el esquema de datos para la entrada del servicio web y es necesario para implementar el punto de conexión en tiempo real. En general, siempre debe conectar un componente o un conjunto de datos al puerto al que está conectado **Entrada de servicio web** para proporcionar el esquema de datos.
   
## <a name="next-steps"></a>Pasos siguientes
Más información sobre la [implementación del punto de conexión en tiempo real](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.