---
title: Modificación de los módulos de IoT Edge en el dispositivo de FPGA para la ejecución en el dispositivo de GPU de Azure Stack Edge Pro
description: Describe las modificaciones necesarias para que los módulos de IoT Edge existentes en los dispositivos de FPGA existentes se ejecuten en el dispositivo de GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.custom: ignite-fall-2021
ms.openlocfilehash: ab3cb4360b9bd0c65ac8567109890fc364411367
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073061"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Ejecución de módulos de IoT Edge existentes desde dispositivos de FPGA de Azure Stack Edge Pro en el dispositivo de GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

En este artículo se detallan los cambios necesarios para un módulo de IoT Edge basado en Docker que se ejecuta en el dispositivo de FPGA de Azure Stack FPGA Edge Pro, de modo que se pueda ejecutar en una plataforma de IoT Edge basada en Kubernetes en el dispositivo de GPU de Azure Stack Edge Pro. 

## <a name="about-iot-edge-implementation"></a>Acerca de la implementación de IoT Edge 

La implementación de IoT Edge es diferente en los dispositivos de FPGA de Azure Stack Edge Pro y los dispositivos de GPU de Azure Stack Edge Pro. En el caso de los dispositivos de GPU, Kubernetes se usa como plataforma de hospedaje para IoT Edge. IoT Edge en dispositivos de FPGA usa una plataforma basada en Docker. El modelo de aplicación basado en Docker de IoT Edge se convierte automáticamente al modelo de aplicación nativa de Kubernetes. Sin embargo, es posible que se sigan necesitando algunos cambios ya que solo se admite un pequeño subconjunto del modelo de aplicación de Kubernetes.

Si va a migrar las cargas de trabajo de un dispositivo de FPGA a un dispositivo de GPU, deberá realizar cambios en los módulos de IoT Edge existentes para que se ejecuten correctamente en la plataforma de Kubernetes. Es posible que tenga que especificar los requisitos de almacenamiento, redes, uso de recursos y proxy web de manera diferente. 

## <a name="storage"></a>Storage

Tenga en cuenta la siguiente información al especificar el almacenamiento para los módulos de IoT Edge.

- El almacenamiento de contenedores en Kubernetes se especifica mediante montajes de volumen.
- La implementación en Kubernetes no puede tener enlaces para asociar almacenamiento persistente o rutas de acceso de host.
    - Para el almacenamiento persistente, use `Mounts` con el tipo `volume`.
    - En el caso de las rutas de acceso de host, use `Mounts` con el tipo `bind`.
- Para IoT Edge en Kubernetes, el enlace a través de `Mounts` solo funciona para el directorio y no para el archivo.

#### <a name="example---storage-via-volume-mounts"></a>Ejemplo: almacenamiento a través de montajes de volúmenes 

En el caso de IoT Edge en Docker, se usan enlaces ruta de acceso de host para asignar los recursos compartidos en el dispositivo a las rutas de acceso dentro del contenedor. Estas son las opciones de creación de contenedores usadas en dispositivos de FPGA:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
En el caso de las rutas de acceso de host para IoT Edge en Kubernetes, aquí se muestra un ejemplo de uso de `Mounts` con el tipo `bind`:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

En el caso de los dispositivos de GPU que ejecutan IoT Edge en Kubernetes, se usan montajes de volúmenes para especificar el almacenamiento. Para aprovisionar el almacenamiento mediante recursos compartidos, el valor de `Mounts.Source` sería el nombre del recurso compartido de SMB o NFS que se aprovisionó en el dispositivo de GPU. El elemento `/home/input` es la ruta de acceso en la que se puede acceder al volumen del contenedor. Estas son las opciones de creación de contenedores usadas en los dispositivos de GPU:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Red

Tenga en cuenta la siguiente información al especificar las redes para los módulos de IoT Edge. 

- La especificación `HostPort` es necesaria para exponer un servicio tanto dentro como fuera del clúster.
    - Opciones de K8sExperimental para limitar la exposición del servicio a un solo clúster.
- La comunicación entre módulos requiere la especificación `HostPort` y la conexión mediante el puerto asignado (y no el puerto expuesto del contenedor).
- Las redes de host funcionan con `dnsPolicy = ClusterFirstWithHostNet`, objeto con el que todos los contenedores (especialmente `edgeHub`) no tendrán que estar también en la red del host. <!--Need further clarifications on this one-->
- La adición de asignaciones de puerto para TCP, UDP en la misma solicitud no funciona.

#### <a name="example---external-access-to-modules"></a>Ejemplo: acceso externo a los módulos 

En el caso de los módulos de IoT Edge que especifican enlaces de puerto, se asigna una dirección IP mediante el intervalo de direcciones IP del servicio externo de Kubernetes que se especificó en la interfaz de usuario local del dispositivo. No hay ningún cambio en las opciones de creación del contenedor de IoT Edge en Docker frente a IoT Edge en Kubernetes, tal y como se muestra en el ejemplo siguiente.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Sin embargo, para consultar la dirección IP asignada al módulo, puede usar el panel de Kubernetes como se describe en [Obtención de la dirección IP para los servicios o módulos](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Como alternativa, siga los pasos de [Conectarse a la interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) y use el comando de lista `iotedge` para enumerar todos los módulos que se ejecutan en el dispositivo. La [salida del comando](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) también indicará las direcciones IP externas asociadas al módulo.


## <a name="resource-usage"></a>Uso de recursos 

Con las configuraciones de IoT Edge basadas en Kubernetes en los dispositivos de GPU, los recursos como la aceleración de hardware, la memoria y los requisitos de CPU se especifican de forma diferente que en los dispositivos de FPGA. 

#### <a name="compute-acceleration-usage"></a>Uso de la aceleración de proceso

Para implementar módulos en FPGA, use las opciones de creación del contenedor <!--with Device Bindings--> como se muestra en la configuración siguiente: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Para la GPU, use especificaciones de solicitud de recursos en lugar de enlaces de dispositivo, tal como se muestra en la configuración mínima siguiente. Debe solicitar los recursos de NVIDIA en lugar de catapulta y no tiene que especificar el elemento `wireserver`. 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Uso de memoria y CPU
 
Para establecer el uso de memoria y CPU, utilice los límites de procesador para los módulos de la sección `k8s-experimental`. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
La especificación de memoria y CPU no es necesaria pero generalmente se recomienda. Si no se especifica `requests`, se usan los valores establecidos en los límites como mínimo requerido. 

El uso de memoria compartida para los módulos también requiere un método diferente. Por ejemplo, puede usar el modo de IPC de host para el acceso a la memoria compartida entre las soluciones Live Video Analytics e Inferencia, como se describe en [Implementación de Live Video Analytics en Azure Stack Edge](../azure-video-analyzer/video-analyzer-docs/overview.md).


## <a name="web-proxy"></a>Proxy web 

Tenga en cuenta la siguiente información al configurar el proxy web:

Si tiene el proxy web configurado en la red, configure las siguientes variables de entorno para la implementación de `edgeHub` en la configuración de IoT Edge basada en Docker en dispositivos de FPGA:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (a menos que el proxy web permita el tráfico de `Amqp`)

En el caso de las configuraciones de IoT Edge basadas en Kubernetes en dispositivos de GPU, tendrá que configurar esta variable adicional durante la implementación:

- `no_proxy`: localhost

- El proxy de IoT Edge en la plataforma Kubernetes usa los puertos 35000 y 35001. Asegúrese de que el módulo no se ejecuta en estos puertos o podría causar conflictos de puerto. 

## <a name="other-differences"></a>Otras diferencias

- **Estrategia de implementación**: es posible que deba cambiar el comportamiento de la implementación de las actualizaciones del módulo. El comportamiento predeterminado de los módulos de IoT Edge es la actualización gradual. Este comportamiento impide que el módulo actualizado se reinicie si el módulo utiliza recursos como la aceleración de hardware o los puertos de red. Este comportamiento puede tener efectos inesperados, especialmente cuando se trabaja con volúmenes persistentes en la plataforma Kubernetes para los dispositivos de GPU. Para invalidar este comportamiento predeterminado, puede especificar un `Recreate` en la sección `k8s-experimental` del módulo.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Nombres de módulos**: los nombres de módulo deben seguir las convenciones de nomenclatura de Kubernetes. Es posible que tenga que cambiar el nombre de los módulos que se ejecutan en IoT Edge con Docker cuando mueve esos módulos a IoT Edge con Kubernetes. Para obtener más información sobre la nomenclatura, vea [Convenciones de nomenclatura de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Otras opciones**: 
    - Ciertas opciones de creación de Docker que funcionaban en dispositivos FPGA no funcionarán en el entorno de Kubernetes en los dispositivos GPU. Por ejemplo: , como – EntryPoint.<!--can we confirm what exactly is required here-->
    - Las variables de entorno como `:` deben reemplazarse por `__`.
    - El estado de **Creando contenedor** de un pod de Kubernetes conduce al estado **Retroceso** para un módulo en el recurso de IoT Hub. Aunque hay una serie de motivos por los que el Pod está en este estado, una razón habitual es cuando se extrae una imagen de contenedor grande a través de una conexión de ancho de banda de red baja. Cuando el pod está en este estado, el estado del módulo aparece como **Retroceso** en IoT Hub, a pesar de que el módulo se está iniciando.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [configurar la GPU para usar un módulo](./azure-stack-edge-gpu-configure-gpu-modules.md).
