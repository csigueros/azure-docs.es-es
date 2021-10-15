---
title: Procedimientos recomendados de configuración de dispositivos para Azure IoT Hub | Microsoft Docs
description: Conozca los procedimientos recomendados sobre el uso de la administración automática de dispositivos con el fin de minimizar las tareas repetitivas y complejas implicadas en la administración de dispositivos IoT a escala.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 26d81a492b12cd2dd7f1d4de260aa22ac4e73a3e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234459"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Procedimientos recomendados para la configuración de dispositivos en una solución de IoT

La administración automática de dispositivos en Azure IoT Hub automatiza muchas de las tareas repetitivas y complejas de administración de grandes flotas de dispositivos durante su ciclo de vida completo. En este artículo se definen muchos de los procedimientos recomendados para los diversos roles que intervienen en el desarrollo y el funcionamiento de una solución de IoT.

* **Integrador o fabricante de hardware de IoT**: fabricantes de hardware de IoT, integradores que ensamblan hardware de diversos fabricantes o proveedores que proporcionan hardware para una implementación de IoT que fabrican o integran otros proveedores. Intervienen en el desarrollo y la integración de firmware, sistemas operativos integrados y software integrado.

* **Desarrollador de soluciones de IoT**: el desarrollo de soluciones de IoT suelen realizarlo los desarrolladores de soluciones. Este desarrollador puede formar parte de un equipo interno o ejercer como integrador de sistemas especializado en esta actividad. El desarrollador de soluciones de IoT puede desarrollar diversos componentes de la solución de IoT desde cero, o bien integrar varios componentes estándar o de código abierto.

* **Operador de soluciones de IoT**: una vez que la solución de IoT se implementa, requiere operaciones, supervisión, actualizaciones y mantenimiento a largo plazo. De estas tareas puede encargarse un equipo interno formado por especialistas en tecnología de la información, equipos de operaciones de hardware y mantenimiento y especialistas en dominios que supervisan que la infraestructura de IoT funciona de manera correcta globalmente.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Administración automática de dispositivos para configurar dispositivos IoT a escala

La administración automática de dispositivos incluye las muchas ventajas de los [dispositivos gemelos](iot-hub-devguide-device-twins.md) y los [módulos gemelos](iot-hub-devguide-module-twins.md) para sincronizar los estados deseados y notificados entre los dispositivos y la nube. Las [configuraciones automáticas de dispositivos](./iot-hub-automatic-device-management.md) actualizan automáticamente grandes conjuntos de gemelos y resumen el progreso y el cumplimiento. En los siguientes pasos generales se describe cómo se desarrolla y usa la administración automática de dispositivos:

* El **integrador o fabricante de hardware de IoT** implementa las características de administración de dispositivos dentro de una aplicación integrada mediante [dispositivos gemelos](iot-hub-devguide-device-twins.md). Estas características pueden incluir actualizaciones de firmware, instalación y actualizaciones de software y administración de configuraciones.

* El **desarrollador de soluciones de IoT** implementa la capa de administración de las operaciones de administración de dispositivos mediante [dispositivos gemelos](iot-hub-devguide-device-twins.md) y [configuraciones automáticas de dispositivos](./iot-hub-automatic-device-management.md). La solución debe incluir la definición de una interfaz de operador para llevar a cabo las tareas de administración de dispositivos.

* El **operador de soluciones de IoT** usa la solución de IoT para realizar tareas de administración de dispositivos, en concreto, para agrupar los dispositivos, iniciar cambios de configuración (como actualizaciones de firmware), supervisar el progreso y solucionar los problemas que surjan.

## <a name="iot-hardware-manufacturerintegrator"></a>Integrador/fabricante de hardware IoT

Los siguientes procedimientos recomendados van dirigidos a los fabricantes e integrados de hardware que se ocupan del desarrollo de software integrado:

* **Implementar [dispositivos gemelos](iot-hub-devguide-device-twins.md):** los dispositivos gemelos permiten la sincronización de la configuración deseada desde la nube y la notificación de las propiedades actuales de configuración y de dispositivo. La mejor manera de implementar dispositivos gemelos en aplicaciones integradas es mediante los [SDK de Azure IoT](https://github.com/Azure/azure-iot-sdks). Los dispositivos gemelos resultan muy adecuados para la configuración debido a que:

    * Admiten la comunicación bidireccional.
    * Permiten estados de dispositivo conectado y desconectado.
    * Siguen el principio de coherencia final.
    * Se pueden consultar completamente en la nube.

* **Estructurar el dispositivo gemelo para la administración de dispositivos:** el dispositivo gemelo se debe estructurar de tal forma que las propiedades de administración de dispositivos estén agrupadas lógicamente en secciones. Al hacer esto, los cambios en la configuración se pueden aislar sin afectar a otras secciones del dispositivo gemelo. Por ejemplo, puede crear una sección dentro de las propiedades deseadas para firmware, otra sección para software y una tercera sección para configuración de red. 

* **Notificar atributos de dispositivo que son útiles para la administración de dispositivos:** atributos como la marca y el modelo del dispositivo físico, el firmware, el sistema operativo, el número de serie y otros identificadores resultan de utilidad para notificaciones y como parámetros para dirigir los cambios de configuración.

* **Definir los estados principales para notificar el estado y el progreso:** los estados de nivel superior se deben enumerar para que se puedan notificar al operador. Por ejemplo, una actualización de firmware notificaría el estado como Actual, Descargando, Aplicando, En curso y Error. Defina campos adicionales para obtener más información sobre cada estado.

## <a name="iot-solution-developer"></a>Desarrollador de soluciones de IoT

Estos son los procedimientos recomendados para los desarrolladores de soluciones de IoT que crean sistemas basados en Azure:

* **Implementar [dispositivos gemelos](iot-hub-devguide-device-twins.md):** los dispositivos gemelos permiten la sincronización de la configuración deseada desde la nube y la notificación de las propiedades actuales de configuración y de dispositivo. La mejor manera de implementar dispositivos gemelos dentro de las aplicaciones de las soluciones en la nube es usar los [SDK de Azure IoT](https://github.com/Azure/azure-iot-sdks). Los dispositivos gemelos resultan muy adecuados para la configuración debido a que:

    * Admiten la comunicación bidireccional.
    * Permiten estados de dispositivo conectado y desconectado.
    * Siguen el principio de coherencia final.
    * Se pueden consultar completamente en la nube.

* **Organizar los dispositivos mediante etiquetas de dispositivo gemelo:** la solución debe permitir que el operador defina anillos de calidad u otros conjuntos de dispositivos basados en diversas estrategias de implementación, como valores controlados. La organización de los dispositivos se puede implementar dentro de la solución mediante etiquetas de dispositivo gemelo y [consultas](iot-hub-devguide-query-language.md). La organización de los dispositivos es necesaria para permitir implementaciones de configuraciones con precisión y seguridad.

* **Implementar [configuraciones automáticas de dispositivos](./iot-hub-automatic-device-management.md):** las configuraciones automáticas de dispositivos implementan y supervisan cambios de configuración en grandes conjuntos de dispositivos IoT mediante dispositivos gemelos.

   Las configuraciones automáticas de dispositivos están dirigidas a conjuntos de dispositivos gemelos mediante la **condición de destino**, que es una consulta basada en etiquetas de dispositivo gemelo o propiedades notificadas. El **contenido de destino** es el conjunto de propiedades deseadas que se establecerán en los dispositivo gemelos dirigidos. El contenido de destino se debe alinear con la estructura de dispositivo gemelo definida por el fabricante o integrador de hardware de IoT. Las **métricas** son consultas basadas en las propiedades notificadas del dispositivo gemelo, y se deben alinear también con la estructura de dispositivo gemelo definida por el fabricante o integrador de hardware de IoT.

   Las configuraciones automáticas de dispositivos se ejecutan por primera vez poco después de crear la configuración y, a continuación, en intervalos de cinco minutos. También se benefician de que IoT Hub realiza operaciones de dispositivo gemelo a una velocidad que nunca supera los [límites](iot-hub-devguide-quotas-throttling.md) de lecturas y actualizaciones de dispositivos gemelos.

* **Usar el servicio [Device Provisioning](../iot-dps/how-to-manage-enrollments.md):** los desarrolladores de soluciones deben usar el servicio Device Provisioning para asignar etiquetas de dispositivo gemelo a nuevos dispositivos, de forma que se configurarán automáticamente mediante **configuraciones automáticas de dispositivos** dirigidas a dispositivos gemelos con esa etiqueta. 

## <a name="iot-solution-operator"></a>Operador de soluciones de IoT

Los siguientes procedimientos recomendados están destinados a los operadores de soluciones de IoT que usan una solución de IoT creada en Azure:

* **Organizar los dispositivos para la administración:** la solución de IoT debe definir o permitir la creación de anillos de calidad u otros conjuntos de dispositivos basados en diversas estrategias de implementación, como valores controlados. Los conjuntos de dispositivos se usarán para implementar los cambios de configuración y realizar otras operaciones de administración de dispositivos a escala.

* **Realizar cambios de configuración mediante una implementación por fases:** una implementación por fases es un proceso global por el cual un operador implementa cambios en un conjunto cada vez más amplio de dispositivos IoT. El objetivo consiste en realizar cambios gradualmente para reducir el riesgo de realizar cambios importantes a gran escala.  El operador debe usar la interfaz de la solución para crear una [configuración automática de dispositivos](./iot-hub-automatic-device-management.md), y la condición de destino debe tener como destino un conjunto inicial de dispositivos (por ejemplo, un grupo de valores controlados). El operador debe validar entonces el cambio de configuración en el conjunto inicial de dispositivos.

   Una vez completada la validación, el operador actualizará la configuración automática de dispositivos para que incluya un conjunto mayor de dispositivos. El operador también debe establecer la configuración con una prioridad más alta que otras configuraciones dirigidas actualmente a esos dispositivos. La implementación se puede supervisar mediante las métricas notificadas por la configuración automática de dispositivos.

* **Realizar reversiones en caso de errores o configuraciones incorrectas:** una configuración automática de dispositivos que provoca errores o configuraciones incorrectas se puede revertir cambiando la **condición de destino** de forma que los dispositivos ya no la cumplan. Asegúrese de que otra configuración automática de dispositivos de prioridad inferior todavía está dirigida a esos dispositivos. Para comprobar que la reversión se ha realizado correctamente, examine las métricas: la configuración revertida ya no debe mostrar el estado de los dispositivos no dirigidos, y las métricas de la segunda configuración debe incluir ahora recuentos de los dispositivos aún dirigidos.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda sobre la implementación de dispositivos gemelos en [Dispositivos gemelos de IoT Hub](iot-hub-devguide-device-twins.md).

* Siga los pasos para crear, actualizar o eliminar una configuración automática de dispositivos en [Configuración y supervisión de dispositivos IoT a escala](./iot-hub-automatic-device-management.md).

* Implemente un patrón de actualización de firmware mediante dispositivos gemelos y configuraciones automáticas de dispositivos con los pasos del [Tutorial: Implementación de un proceso de actualización de firmware de dispositivos](tutorial-firmware-update.md).