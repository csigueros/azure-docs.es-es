---
title: Introducción a la administración de dispositivos con Microsoft Azure IoT Hub
description: 'Información general sobre la administración de dispositivos con IoT Hub de Azure: ciclo de vida del dispositivo empresarial y patrones de administración de dispositivos como, reinicio, restablecimiento de fábrica, actualización de firmware, configuración, dispositivos gemelos, consultas, trabajos y detección de amenazas.'
author: anastasia-ms
ms.service: iot-hub
services: iot-hub
ms.author: v-stharr
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: f76996f721287679982f92345ecc075c3db55aba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612935"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Información general sobre la administración de dispositivos con IoT Hub

Azure IoT Hub proporciona características y un modelo de extensibilidad que permiten a los desarrolladores de back-end y de dispositivos generar soluciones sólidas de administración de dispositivos. Los dispositivos incluyen sensores restringidos y microcontroladores para un solo fin, así como puertas de enlace eficaces que enrutan las comunicaciones para grupos de dispositivos.  Además, las condiciones de uso y los requisitos de los operadores de IoT varían considerablemente de un sector a otro.  A pesar de esta variación, la administración de dispositivos con Azure IoT Hub proporciona las funcionalidades, las bibliotecas de código y los patrones adecuados para un conjunto diverso de dispositivos y usuarios finales.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Una parte fundamental de la creación de una solución de IoT empresarial adecuada es contar con una estrategia relativa al modo en que los operadores controlan la administración continua de su conjunto de dispositivos. Los operadores de IoT requieren herramientas y aplicaciones sencillas y confiables que les permitan centrarse en los aspectos más estratégicos de sus trabajos. En este artículo se proporciona:

* Descripción general del método de administración de dispositivos con Azure IoT Hub.
* Descripción de los principios de administración de dispositivos comunes.
* Descripción del ciclo de vida de los dispositivos.
* Información general sobre los patrones comunes de administración de dispositivos.

## <a name="device-lifecycle"></a>Ciclo de vida del dispositivo

Las fases generales de administración de dispositivos son comunes en la mayoría de los proyectos de IoT empresariales. En IoT de Azure, hay cinco fases en el ciclo de vida del dispositivo:

![Las cinco fases del ciclo de vida del dispositivo de Azure IoT son: planear, aprovisionar, configurar, supervisar, retirar.](./media/iot-hub-device-management-overview/image5.png)

Dentro de cada una de estas cinco fases, hay que cumplir varios requisitos de operador de dispositivo para proporcionar una solución completa:

* **Planeamiento**: permitir a los operadores que creen un esquema de metadatos de los dispositivos que les permita consultar y seleccionar, de forma fácil y rápida, un grupo de dispositivos para operaciones de administración masiva. Puede usar al dispositivo gemelo para almacenar los metadatos de este dispositivo en forma de etiquetas y propiedades.
  
    *Lecturas adicionales*: 
  * [Introducción a los dispositivos gemelos](iot-hub-node-node-twin-getstarted.md)
  * [Descripción de dispositivo gemelos](iot-hub-devguide-device-twins.md)
  * [Uso de propiedades de dispositivos gemelos](tutorial-device-twins.md)
  * [Procedimientos recomendados para la configuración de dispositivos en una solución de IoT](iot-hub-configuration-best-practices.md)

* **Aprovisionamiento**: aprovisionar de forma segura nuevos dispositivos en IoT Hub y permitir que los operadores puedan detectar de inmediato las funcionalidades de los dispositivos.  Use el registro de identidad de IoT Hub para crear credenciales e identidades de dispositivo flexibles, y realice esta operación de forma masiva mediante un trabajo. Cree dispositivos para informar de sus funcionalidades y condiciones mediante las propiedades de dispositivo en el dispositivo gemelo.
  
    *Lecturas adicionales*: 
    * [Administración de identidades de dispositivo](iot-hub-devguide-identity-registry.md)
    * [Administración de identidades de dispositivos de forma masiva](iot-hub-bulk-identity-mgmt.md)
    * [Uso de propiedades de dispositivos gemelos](tutorial-device-twins.md)
    * [Procedimientos recomendados para la configuración de dispositivos en una solución de IoT](iot-hub-configuration-best-practices.md)
    * [Servicio Azure IoT Hub Device Provisioning](../iot-dps/index.yml)

* **Configuración**: facilitar los cambios de configuración de forma masiva y las actualizaciones de firmware en dispositivos, a la vez que se mantienen el estado y la seguridad. Realice estas operaciones de administración de dispositivos de forma masiva usando las propiedades que desee o con métodos directos y trabajos de difusión.
  
    *Lecturas adicionales*:
    * [Uso de propiedades de dispositivos gemelos](tutorial-device-twins.md)
    * [Configuración y supervisión de dispositivos IoT a escala](./iot-hub-automatic-device-management.md)
    * [Procedimientos recomendados para la configuración de dispositivos en una solución de IoT](iot-hub-configuration-best-practices.md)

* **Supervisión**: supervisar la situación general del conjunto de dispositivos y el estado de las operaciones en curso, y alertar a los operadores sobre los problemas que puedan necesitar su atención.  Aplique dispositivos gemelos para que los dispositivos puedan notificar en tiempo real las condiciones de funcionamiento y el estado de las operaciones de actualización. Genere informes de panel eficaces que expongan los problemas más inmediatos mediante el uso de dispositivos gemelos. Proteja el entorno de IoT frente a amenazas, con varias opciones de implementación, entre las que se incluyen entornso totalmente locales, conectados a la nube o híbridos.
  
    *Lecturas adicionales*: 
    * [Uso de propiedades de dispositivos gemelos](tutorial-device-twins.md)
    * [Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes](iot-hub-devguide-query-language.md)
    * [Configuración y supervisión de dispositivos IoT a escala](./iot-hub-automatic-device-management.md)
    * [Azure Defender para IoT para que las organizaciones proporcionen una detección de amenazas completa](../defender-for-iot/organizations/overview.md)
    * [Procedimientos recomendados para la configuración de dispositivos en una solución de IoT](iot-hub-configuration-best-practices.md)

* **Retirada**: reemplazar o retirar dispositivos después de un error, ciclo de actualización o al final de la duración del servicio.  Use dispositivos gemelos para conservar la información del dispositivo si el dispositivo físico se va a reemplazar, o para archivarla si se va a retirar. Use el registro de identidades de IoT Hub para revocar de forma segura las credenciales y las identidades de los dispositivos.
  
    *Lecturas adicionales*: 
    * [Uso de propiedades de dispositivos gemelos](tutorial-device-twins.md)
    * [Administración de identidades de dispositivo](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Patrones de administración de dispositivos

IoT Hub habilita el siguiente conjunto de patrones de administración de dispositivos. Los [tutoriales de administración de dispositivos](iot-hub-node-node-device-management-get-started.md) muestran con más detalle cómo ampliar estos patrones para que se adapten a su escenario exacto y cómo diseñar nuevos patrones basados en estas plantillas centrales.

* **Reinicio**: la aplicación back-end usa un método directo para informar al dispositivo de que se ha iniciado un reinicio.  El dispositivo usa las propiedades notificadas para actualizar el estado de reinicio del dispositivo.
  
    ![Gráfico de los patrones de reinicio de la administración de dispositivos](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Restablecimiento de fábrica**: la aplicación back-end usa un método directo para informar al dispositivo de que se ha iniciado un restablecimiento de fábrica. El dispositivo usa las propiedades notificadas para actualizar el estado de restablecimiento de fábrica del dispositivo.
  
    ![Gráfico de los patrones de restablecimiento de fábrica de la administración de dispositivos](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configuración**: la aplicación back-end usa las propiedades deseadas para configurar el software que se ejecuta en el dispositivo. El dispositivo usa las propiedades notificadas para actualizar el estado de configuración del dispositivo.
  
    ![Gráfico de los patrones de configuración de la administración de dispositivos](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Informes de progreso y estado**: la solución back-end ejecuta consultas de dispositivos gemelos, en un conjunto de dispositivos, para informar sobre el estado y el progreso de las acciones que se ejecutan en el dispositivo.
  
    ![Gráfico del patrón del estado y progreso de la administración de dispositivos](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="device-updates"></a>Actualizaciones de dispositivos

[Device Update for IoT Hub](../iot-hub-device-update/understand-device-update.md) es una completa plataforma que los clientes pueden usar para publicar, distribuir y administrar actualizaciones de forma inalámbrica para todos los elementos, desde minúsculos sensores hasta dispositivos de nivel de puerta de enlace. Device Update for IoT Hub permite a los clientes responder rápidamente a las amenazas de seguridad e implementar características para obtener objetivos empresariales sin incurrir en los costos adicionales de desarrollo y mantenimiento que suponen la creación de sus propias plataformas de actualización.

Device Update for IoT Hub ofrece no solo una implementación de actualizaciones optimizada, sino también operaciones simplificadas mediante la integración con Azure IoT Hub. Gracias a un alcance extendido mediante Azure IoT Edge, proporciona una solución hospedada en la nube que conecta prácticamente cualquier dispositivo. Además, es compatible con una amplio conjunto de sistemas operativos de IoT, entre los que se incluyen Linux y Azure RTOS (sistema operativo en tiempo real), y se puede ampliar a través de código abierto. Algunas características incluyen:

* Compatibilidad con la actualización de dispositivos perimetrales, incluidos los componentes de nivel de host de Azure IoT Edge
* Experiencia de usuario de administración de actualizaciones integrada con Azure IoT Hub.
* Implementación gradual de actualizaciones a través de la agrupación de dispositivos y los controles de programación de actualizaciones.
* API de programación que habiliten la automatización y experiencias del portal personalizadas.
* Vistas rápidas del estado y del cumplimiento de las actualizaciones en conjuntos de dispositivos heterogéneos
* Compatibilidad con actualizaciones de dispositivos resistentes (A/B) para ofrecer una reversión sin problemas
* Almacenamiento en caché de contenido y compatibilidad con dispositivos desconectados, incluidos los dispositivos que están en configuraciones anidadas, mediante la característica Caché conectada de Microsoft integrada, e integración con Azure IoT Edge
* Suscripción y controles de acceso basado en roles disponibles a través del portal de Azure.com
* Características de seguridad y controles de privacidad completos de la nube al perímetro

Para obtener más información, consulte [Device Update for IoT Hub](../iot-hub-device-update/index.yml).

## <a name="next-steps"></a>Pasos siguientes

Las funcionalidades, los patrones y las bibliotecas de código que IoT Hub proporciona para la administración de dispositivos le permiten crear aplicaciones de IoT que cumplan los requisitos del operador de IoT empresarial dentro de cada fase del ciclo de vida de dispositivo.

Para más información acerca de las características de administración de dispositivos en IoT Hub, consulte el tutorial [Introducción a la administración de dispositivos](iot-hub-node-node-device-management-get-started.md).