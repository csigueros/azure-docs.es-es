---
title: Servicios de IoT de Azure Alemania | Microsoft Docs
description: En este artículo se proporciona un punto de partida para Azure IoT Suite para Azure Alemania.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 16259a816413bcf7f57475b12f46c9410cb3ca44
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029238"
---
# <a name="azure-germany-iot-services"></a>Servicios de IoT de Azure Alemania

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="iot-solution-accelerators"></a>Aceleradores de soluciones de IoT
Todos los servicios necesarios para Azure IoT Suite están disponibles en Azure Alemania. 

### <a name="variations"></a>Variaciones
La página principal de Azure IoT Suite en Azure Alemania no es la misma que la de Azure global.

## <a name="solution-accelerators"></a>Aceleradores de soluciones
Es posible que desee comenzar con uno de los aceleradores de soluciones siguientes. 

### <a name="remote-monitoring"></a>Supervisión remota
El acelerador de soluciones Supervisión remota es una implementación de una solución de supervisión de un extremo a otro para varias máquinas que se ejecutan en ubicaciones remotas. La solución combina servicios clave de Azure para proporcionar una implementación genérica del escenario de negocio. Puede usar la solución como punto de partida para su propia implementación, así como personalizarla para cumplir sus requisitos empresariales concretos.

### <a name="predictive-maintenance"></a>Mantenimiento predictivo
El acelerador de soluciones de mantenimiento predictivo es una solución completa para escenarios empresariales que predice el punto en el que es probable que se produzca un error. Esta solución se puede utilizar de forma proactiva para actividades como la optimización del mantenimiento. La solución combina los principales servicios de Azure IoT Suite, como Azure IoT Hub, Stream Analytics y un área de trabajo de Machine Learning. Esta área de trabajo contiene un modelo, basado en un conjunto público de datos de ejemplo para predecir la vida útil restante (RUL) de un motor de avión. La solución implementa completamente el escenario empresarial de IoT como punto de partida para planear e implementar una solución que satisfaga sus requisitos empresariales concretos.


## <a name="deploying-the-solution-accelerator"></a>Implementación del acelerador de soluciones

Ambas soluciones se pueden implementar de dos maneras, mediante sitio web o mediante PowerShell.

### <a name="deploy-via-website"></a>Implementación mediante sitio web

Siga las instrucciones del [tutorial para las soluciones preconfiguradas](/previous-versions/azure/iot-accelerators/about-iot-accelerators) mediante el uso de la página principal mencionada con anterioridad.

### <a name="deploy-via-powershell"></a>Implementación mediante PowerShell

Hay una versión completa (mediante Visual Studio y las plantillas de Azure Resource Manager) para la solución de *supervisión remota*. Descárguela del [repositorio Azure-IoT-Remote-Monitoring en GitHub](https://github.com/Azure/azure-iot-remote-monitoring). La implementación de PowerShell está lista para otros entornos, como Azure Alemania. Especifique el parámetro de *Environment* parámetro "AzureGermanCloud", de forma que sea similar a lo siguiente:

```powershell
build.cmd cloud debug AzureGermanCloud
```

Mapas de Bing no está disponible actualmente en Azure Alemania, lo por que no es posible suscribirse a él automáticamente. Para resolver este problema, suscríbase al servicio en Azure global y use el servicio ahí. 

> [!NOTE]
> Cuando se usa Mapas de Bing de la manera que se describe aquí, se sale del entorno de Azure Alemania.

Así es como se hace:

1. Cree una API de Mapas de Bing en el portal de Azure global, para lo que debe hacer clic en **+ Nuevo**, buscar **API de Mapas de Bing para empresas** y siga los avisos.
2. Obtenga la clave de API de Mapas de Bing para empresas en el portal de Azure global: 
    1. Vaya al grupo de recursos donde está API de Mapas de Bing para empresas en el portal de Azure global.
    2. Haga clic en **Toda la configuración** > **Administración de claves**. 
    3. Verá dos claves: MasterKey y QueryKey. Copie el valor de QueryKey.
3. Descargue el código más reciente del [repositorio Azure-IoT-Remote-Monitoring en GitHub](https://github.com/Azure/azure-iot-remote-monitoring).
4. Ejecute una implementación en la nube en su entorno, para lo que debe seguir las instrucciones de implementación de la línea de comandos en la carpeta `/docs/` del repositorio. 
5. Cuando haya ejecutado la implementación, busque en la carpeta raíz el archivo **.user.config** creado durante la implementación. Abra este archivo en un editor de texto. 
6. Cambie la línea siguiente para incluir el valor que ha copiado para QueryKey: `<setting name="MapApiQueryKey" value="" />`
7. Vuelva a implementar la solución. Para ello, repita el paso 4.
 


## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al [blog de Azure Alemania](/archive/blogs/azuregermany/).