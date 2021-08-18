---
title: Implementación automática para grupos de dispositivos en Azure IoT Edge | Microsoft Docs
description: Uso de implementaciones automáticas en Azure IoT Edge para administrar grupos de dispositivos basándose en etiquetas compartidas
author: kgremban
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0461fffa62f46491d5bbb3c2c9f66670b3bd9cba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732877"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Descripción de las implementaciones automáticas de IoT Edge en un único dispositivo o a escala

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Las implementaciones automáticas y la implementación superpuesta le ayudan a administrar y configurar módulos en un gran número de dispositivos IoT Edge.

Azure IoT Edge proporciona dos maneras de configurar los módulos para que se ejecuten en dispositivos IoT Edge. El primer método consiste en implementar módulos en cada dispositivo. Cree un manifiesto de implementación y aplíquelo a un dispositivo determinado por nombre. El segundo método consiste en implementar módulos automáticamente en cualquier dispositivo registrado que cumpla un conjunto de condiciones definidas. Cree un manifiesto de implementación y defina a qué dispositivos se aplica en función de las [etiquetas](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) en el dispositivo gemelo.

Este artículo se centra en la configuración y la supervisión de flotas de dispositivos, lo que se conoce en conjunto como *implementaciones automáticas de IoT Edge*.  Los pasos de implementación básicos son los siguientes:

1. Un operador define una implementación que describe un conjunto de módulos y los dispositivos de destino.  Cada implementación tiene un manifiesto de implementación que refleja esta información.
2. El servicio IoT Hub se comunica con todos los dispositivos de destino para configurarlos con los módulos deseados.
3. El servicio IoT Hub recupera el estado de los dispositivos IoT Edge y hace que estén disponibles para el operador.    Por ejemplo, un operador puede ver cuándo un dispositivo Edge no está configurado correctamente o si se produce un error en un módulo en tiempo de ejecución.
4. En cualquier momento, los nuevos dispositivos IoT Edge que satisfacen las condiciones de destino se configuran para la implementación.

Este artículo describe cada uno de los componentes que intervienen en la configuración y la supervisión de una implementación. Para ver un tutorial sobre la creación y la actualización de una implementación, consulte [Deploy and monitor IoT Edge modules at scale](how-to-deploy-at-scale.md) (Implementación y supervisión de módulos de IoT Edge a escala).

## <a name="deployment"></a>Implementación

Una implementación automática de IoT Edge asigna imágenes de módulos de IoT Edge para ejecutarse como instancias en un conjunto de dispositivos IoT Edge de destino. Se configura un manifiesto de implementación de IoT Edge para incluir una lista de módulos con los parámetros de inicialización correspondientes.  Una implementación se puede asignar a un único dispositivo (basándose en su identificador) o a un grupo de dispositivos (basándose en las etiquetas).  Una vez que el dispositivo IoT Edge recibe un manifiesto de implementación, descarga e instala las imágenes de contenedor de los repositorios de contenedores respectivos, y los configura como corresponde.  Después de que se crea una implementación, un operador puede supervisar el estado de implementación para ver si los dispositivos de destino están configurados correctamente.

Solo los dispositivos IoT Edge se pueden configurar con una implementación. El dispositivo debe cumplir los siguientes requisitos previos para poder recibir la implementación:

* El sistema operativo base
* Un sistema de administración de contenedores, como Moby o Docker
* Aprovisionamiento del entorno de tiempo de ejecución de IoT Edge

### <a name="deployment-manifest"></a>Manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe los módulos que se van a configurar en los dispositivos IoT Edge de destino. Contiene los metadatos de configuración de todos los módulos, incluidos los módulos del sistema necesarios (en concreto, el agente de IoT Edge y el centro de IoT Edge).  

Los metadatos de configuración de cada módulo incluyen:

* Versión
* Tipo
* Estado (por ejemplo, en ejecución o detenido)
* Directiva de reinicio
* Registro de contenedor e imagen
* Rutas de entrada y salida de datos

Si la imagen del módulo se almacena en un registro de contenedor privado, el agente de IoT Edge almacena las credenciales del registro.

### <a name="target-condition"></a>Condición de destino

La condición de destino se evalúa continuamente durante la vigencia de la implementación. Se incluyen todos los nuevos dispositivos que cumplen los requisitos y se quitan todos los dispositivos existentes que ya no lo hacen. La implementación se volverá a activar si el servicio detecta cualquier cambio en la condición de destino.

Por ejemplo, tiene una implementación con una condición de destino tags.environment = "prod". Al comenzar la implementación, hay diez dispositivos de producción. Los módulos se instalarán correctamente en estos 10 dispositivos. El estado del agente de IoT Edge se muestra con 10 dispositivos en total, 10 respuestas correctas, 0 respuestas erróneas y 0 respuestas pendientes. Ahora agregue cinco dispositivos más con tags.environment = 'prod'. El servicio detecta el cambio y el estado del agente de IoT Edge pasará a incluir 15 dispositivos en total, 10 respuestas correctas, 0 respuestas erróneas y 5 respuestas pendientes al intentar implementar los cinco nuevos dispositivos.

Use cualquier condición booleana en las etiquetas de dispositivos gemelos, propiedades notificadas de dispositivos gemelos o valores de deviceId para seleccionar los dispositivos de destino. Si desea usar una condición con etiquetas, debe agregar la sección "etiquetas":{} en el dispositivo gemelo en el mismo nivel que las propiedades. [Más información acerca de las etiquetas en dispositivos gemelos](../iot-hub/iot-hub-devguide-device-twins.md)

Ejemplos de condiciones de destino:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

Tenga en cuenta estas restricciones cuando construya una condición de destino:

* En dispositivos gemelos, solo puede crear una condición de destino mediante etiquetas, propiedades notificadas o valores de deviceId.
* No se permiten las comillas dobles en ninguna porción de la condición de destino. Use comillas simples.
* Las comillas simples representan los valores de la condición de destino. Por lo tanto, deberá agregar una comilla simple a otra comilla simple si esta ya forma parte del nombre del dispositivo. Por ejemplo, para dirigirse a un dispositivo llamado `operator'sDevice`, escriba `deviceId='operator''sDevice'`.
* Se permiten números, letras y los siguientes caracteres en los valores de la condición de destino: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priority

Una prioridad define si se debe aplicar una implementación a un dispositivo de destino en relación con otras implementaciones. Una prioridad de implementación es un entero positivo, con un número más grande que indica la prioridad más alta. Si un dispositivo IoT Edge se destina a más de una implementación, se aplica aquella con una prioridad más alta.    Las implementaciones con prioridades inferiores no se aplican, ni se combinan.    Si un dispositivo se destina a dos o más implementaciones con la misma prioridad, se aplica la implementación creada más recientemente (determinada por la marca de tiempo de creación).

### <a name="labels"></a>Etiquetas

Las etiquetas son pares de clave/valor de cadena que se pueden usar para filtrar y agrupar las implementaciones.  Una implementación puede tener varias etiquetas. Las etiquetas son opcionales y no afectan a la configuración real de los dispositivos IoT Edge.

### <a name="metrics"></a>Métricas

De forma predeterminada, todas las implementaciones informan sobre cuatro métricas:

* **Destino** muestra los dispositivos IoT Edge que coinciden con la condición de destino de implementación.
* **Aplicado** muestra los dispositivos IoT Edge de destino que no están destinados a otra implementación de mayor prioridad.
* **Informe correcto** muestra los dispositivos IoT Edge que han informado de que los módulos se han implementado correctamente.
* **Error en el informe** muestra los dispositivos IoT Edge que han informado de que uno o más módulos no se han implementado correctamente. Para investigar más el error, conéctese de forma remota a esos dispositivos para examinar los archivos de registros.

Además, puede definir sus propias métricas personalizadas para ayudar a supervisar y administrar la implementación.

Las métricas proporcionan el número de resúmenes de los distintos estados de los que los dispositivos pueden informar como resultado de la aplicación una configuración de implementación. Las métricas pueden consultar [propiedades notificadas del módulo gemelo edgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), como *lastDesiredStatus* o *lastConnectTime*. Por ejemplo:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Agregar sus propias métricas es opcional y no afecta a la configuración real de los dispositivos IoT Edge.

## <a name="layered-deployment"></a>Implementación superpuesta

Las implementaciones superpuestas son implementaciones automáticas que se pueden combinar para reducir el número de implementaciones únicas que se deben crear. Las implementaciones superpuestas son útiles en escenarios en los que se reutilizan los mismos módulos en distintas combinaciones en muchas implementaciones automáticas.

Las implementaciones superpuestas tienen los mismos componentes básicos que cualquier implementación automática. Su objetivo son dispositivos en función de las etiquetas de los dispositivos gemelos y proporcionan la misma función en torno a las etiquetas, las métricas y los informes de estado. Las implementaciones superpuestas también tienen prioridades asignadas, pero en lugar de usar la prioridad para determinar qué implementación se aplica a un dispositivo, la prioridad determina cómo se clasifican varias implementaciones en un dispositivo. Por ejemplo, si dos implementaciones superpuestas tienen un módulo o una ruta con el mismo nombre, se aplicará la implementación superpuesta con la prioridad más alta mientras se sobrescribe la prioridad inferior.

Los módulos en tiempo de ejecución del sistema, edgeAgent y edgeHub no se configuran como parte de una implementación superpuesta. Cualquier dispositivo IoT Edge que tenga como destino una implementación superpuesta necesita tener aplicada primero una implementación automática estándar. La implementación automática proporciona la base sobre la que se pueden agregar implementaciones superpuestas.

Un dispositivo IoT Edge puede aplicar solo una implementación automática estándar, pero puede aplicar varias implementaciones automáticas superpuestas. Cualquier implementación superpuesta que tenga como destino un dispositivo debe tener una prioridad superior a la implementación automática de ese dispositivo.

Por ejemplo, considere el siguiente escenario de una empresa que administra edificios. Desarrollaron módulos IoT Edge para recopilar datos de cámaras de seguridad, sensores de movimiento y ascensores. Sin embargo, no todos los edificios pueden usar los tres módulos. Con las implementaciones automáticas estándar, la empresa debe crear implementaciones individuales para todas las combinaciones de módulos que sus edificios necesitan.

![Las implementaciones automáticas estándar deben adaptarse a cada combinación de módulos](./media/module-deployment-monitoring/standard-deployment.png)

Sin embargo, una vez que la empresa cambia a las implementaciones automáticas superpuestas, observa que pueden crear las mismas combinaciones de módulos para sus edificios con menos implementaciones que administrar. Cada módulo tiene su propia implementación superpuesta y las etiquetas de dispositivo identifican los módulos que se agregan a cada edificio.

![Las implementaciones automáticas superpuestas simplifican escenarios en los que los mismos módulos se combinan de maneras diferentes](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Configuración de módulos gemelos

Al trabajar con implementaciones superpuestas, puede tener, de forma intencionada o accidental, dos implementaciones con el mismo módulo con un dispositivo como destino. En estos casos, puede decidir si la implementación de prioridad más alta debe sobrescribir el módulo gemelo o anexarlo. Por ejemplo, puede tener una implementación que aplique el mismo módulo a 100 dispositivos distintos. Sin embargo, 10 de esos dispositivos están en instalaciones seguras y necesitan una configuración adicional para comunicarse a través de servidores proxy. Puede usar una implementación superpuesta para agregar propiedades de módulos gemelos que permitan a esos 10 dispositivos comunicarse de forma segura sin sobrescribir la información del módulo gemelo existente de la implementación base.

Puede anexar las propiedades deseadas del módulo gemelo en el manifiesto de implementación. Cuando en una implementación estándar agregaría propiedades en la sección **properties.desired** del módulo gemelo, en una implementación superpuesta puede declarar un nuevo subconjunto de propiedades deseadas.

Por ejemplo, en una implementación estándar puede agregar el módulo de sensor de temperatura simulado con las siguientes propiedades deseadas que le indican que envíe datos en intervalos de 5 segundos:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

En una implementación superpuesta dirigida a algunos o todos los mismos dispositivos, podría agregar una propiedad que indique al sensor simulado que envíe 1000 mensajes y que luego se detenga. No desea sobrescribir las propiedades existentes, por lo que debe crear una nueva sección dentro de las propiedades deseadas llamada `layeredProperties` que contenga la nueva propiedad:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Un dispositivo que tenga ambas implementaciones aplicadas reflejará las siguientes propiedades en el módulo gemelo para el sensor de temperatura simulado:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Si establece el campo `properties.desired` del módulo gemelo en una implementación superpuesta, sobrescribirá las propiedades deseadas para ese módulo en las implementaciones de prioridad inferior.

## <a name="phased-rollout"></a>Lanzamiento por fases

Un lanzamiento por fases es un proceso global en el que un operador implementa cambios en un amplio conjunto de dispositivos IoT Edge. El objetivo consiste en realizar cambios gradualmente para reducir el riesgo de realizar cambios importantes a gran escala. Las implementaciones automáticas ayudan a administrar implementaciones por fases en una flota de dispositivos IoT Edge.

Un lanzamiento por fases se ejecuta en las fases y los pasos siguientes:

1. Establezca un entorno de prueba de los dispositivos IoT Edge; para ello, aprovisiónelos y defina una etiqueta de dispositivo gemelo como `tag.environment='test'`.  El entorno de prueba debe reflejar el entorno de producción en el que finalmente se aplicará la implementación.
2. Cree una implementación que incluya los módulos y las configuraciones deseados. La condición de destino debe tener como objetivo probar el entorno de los dispositivos IoT Edge.
3. Valide la nueva configuración de los módulos en el entorno de prueba.
4. Actualice la implementación para incluir un subconjunto de los dispositivos IoT Edge de producción mediante la adición de una nueva etiqueta a la condición de destino. Además, asegúrese de que la prioridad de la implementación sea mayor que la de otras implementaciones destinadas actualmente a esos dispositivos
5. Compruebe que la implementación se realizó correctamente en los dispositivos IoT de destino examinando el estado de implementación.
6. Actualice la implementación en el resto de los dispositivos IoT Edge de producción de destino.

## <a name="rollback"></a>Reversión

Las implementaciones se pueden revertir si recibe errores o configuraciones incorrectas.  Dado que una implementación define la configuración absoluta del módulo en un dispositivo IoT Edge, las implementaciones adicionales también se deben destinar al mismo dispositivo con una prioridad inferior incluso si el objetivo es quitar todos los módulos.  

Al eliminar una implementación no se quitan los módulos de los dispositivos de destino. Debe haber otra implementación que defina una nueva configuración para los dispositivos, incluso si se trata de una implementación vacía.

Realice las reversiones siguiendo esta secuencia:

1. Confirme que una segunda implementación también tiene como destino el mismo conjunto de dispositivos. Si el objetivo de la reversión es quitar todos los módulos, la segunda implementación no debe incluir ningún módulo.
2. Modifique o quite la expresión de condición de destino de la implementación que quiere revertir para que los dispositivos ya no satisfagan la condición de destino.
3. Examine el estado de implementación para comprobar que la reversión se realizó correctamente.
   * La implementación revertida ya no debe mostrar el estado de los dispositivos que se han revertido.
   * La segunda implementación ahora debe incluir el estado de implementación de los dispositivos que se han revertido.

## <a name="next-steps"></a>Pasos siguientes

* Siga los pasos para crear, actualizar o eliminar una implementación en [Implementación y supervisión de módulos de IoT Edge a escala](how-to-deploy-at-scale.md).
* Aprenda más sobre otros conceptos de IoT Edge como el [entorno de tiempo de ejecución de IoT Edge](iot-edge-runtime.md) y los [módulos de IoT Edge](iot-edge-modules.md).
