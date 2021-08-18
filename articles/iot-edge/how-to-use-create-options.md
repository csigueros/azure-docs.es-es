---
title: 'Escritura de createOptions para módulos: Azure IoT Edge | Microsoft Docs'
description: Cómo usar createOptions en el manifiesto de implementación para configurar módulos en tiempo de ejecución
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a1b77cc3cfdda927c6df8c2e0d4b013647bab45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728828"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Configuración de las opciones de creación de contenedores para módulos de IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

El parámetro **createOptions** del manifiesto de implementación le permite configurar los contenedores de módulos en tiempo de ejecución. Este parámetro expande el control sobre los módulos y permite tareas como permitir o restringir el acceso del módulo a los recursos del dispositivo host o configurar las redes.

Los módulos de IoT Edge se implementan como contenedores compatibles con Docker en el dispositivo IoT Edge. Docker ofrece muchas opciones para crear contenedores y estas opciones se aplican también a los módulos de IoT Edge. Para más información, consulte [Opciones de creación de un contenedor de Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Formato de las opciones de creación

El manifiesto de implementación de IoT Edge acepta las opciones de creación con formato JSON. Por ejemplo, tome las opciones de creación que se incluyen automáticamente para todos los módulos de edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

En este ejemplo de edgeHub se usa el parámetro **HostConfig.PortBindings** para asignar los puertos expuestos en el contenedor a un puerto en el dispositivo host.

Si usa las extensiones de Azure IoT Tools para Visual Studio o Visual Studio Code, puede escribir las opciones de creación en formato JSON en el archivo **deployment.template.json**. A continuación, cuando se usa la extensión para crear la solución IoT Edge o generar el manifiesto de implementación, el archivo JSON se convierte en una cadena en el formato que espera el entorno de ejecución de Azure IoT Edge. Por ejemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Una sugerencia para escribir las opciones de creación es usar el comando `docker inspect`. Como parte del proceso de desarrollo, ejecute el módulo localmente mediante `docker run <container name>`. Una vez que el módulo funcione de la manera que desea, ejecute `docker inspect <container name>`. Este comando genera los detalles del módulo en formato JSON. Busque los parámetros que ha configurado y copie el código JSON. Por ejemplo:

[![Resultados del comando docker inspect edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Escenarios frecuentes

Las opciones de creación de contenedores permiten muchos escenarios, pero aquí se incluyen algunos de los más frecuentes al crear soluciones IoT Edge:

* [Concesión a los módulos de acceso al almacenamiento de host](how-to-access-host-storage-from-module.md)
* [Asignación del puerto del host al puerto del módulo](#map-host-port-to-module-port)
* [Restricción del uso de CPU y memoria del módulo](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Asignación del puerto del host al puerto del módulo

Si el módulo necesita comunicarse con un servicio fuera de la solución IoT Edge y no usa el enrutamiento de mensajes para hacerlo, deberá asignar un puerto del host a un puerto del módulo.

>[!TIP]
>Esta asignación de puertos no es necesaria para la comunicación de módulo a módulo en el mismo dispositivo. Si el módulo A necesita consultar una API hospedada en el módulo B, puede hacerlo sin ninguna asignación de puertos. El módulo B debe exponer un puerto en su archivo dockerfile, por ejemplo: `EXPOSE 8080`. A continuación, el módulo A puede consultar la API con el nombre del módulo B, por ejemplo: `http://ModuleB:8080/api`.

En primer lugar, asegúrese de que se expone un puerto dentro del módulo para escuchar las conexiones. Puede hacerlo mediante una instrucción [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) en el archivo dockerfile. Por ejemplo, `EXPOSE 8080`. La instrucción EXPOSE tiene como valor predeterminado el protocolo TCP si no se especifica o bien se puede especificar UDP.

A continuación, use el valor **PortBindings** del grupo **HostConfig** de las [opciones de creación de un contenedor de Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) para asignar el puerto expuesto en el módulo a un puerto del dispositivo host. Por ejemplo, si expuso el puerto 8080 dentro del módulo y desea asignarlo al puerto 80 del dispositivo host, las opciones de creación del archivo template.json serían como en el ejemplo siguiente:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Una vez convertido en cadena para el manifiesto de implementación, la misma configuración sería similar a la del ejemplo siguiente:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Restricción del uso de CPU y memoria del módulo

Puede declarar la cantidad de recursos del host que puede usar un módulo. Este control es útil para garantizar que un módulo no puede consumir demasiada memoria o uso de CPU y evitar que otros procesos se ejecuten en el dispositivo. Puede administrar esta configuración con las [opciones de creación de contenedores de Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) del grupo **HostConfig**, incluidas:

* **Memoria**: límite de memoria en bytes. Por ejemplo, 268435456 bytes = 256 MB.
* **MemorySwap**: límite de memoria total (memoria + intercambio). Por ejemplo, 536870912 bytes = 512 MB.
* **CpuPeriod**: duración de un período de CPU en microsegundos. El valor predeterminado es 100 000, por lo que, por ejemplo, un valor de 25 000 limita un contenedor al 25 % de los recursos de CPU.

En el formato del archivo template.json, estos valores se verían como en el ejemplo siguiente:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Una vez convertido en cadena para el manifiesto de implementación final, estos valores serían similares a los del ejemplo siguiente:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Pasos siguientes

Para más ejemplos de opciones de creación en acción, consulte los siguientes ejemplos de IoT Edge:

* [Custom Vision y Azure IoT Edge en un Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Ejemplo de Blob Storage y Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
