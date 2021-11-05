---
title: Biblioteca de acciones y errores de Chaos Studio
description: Vea una descripción de las acciones disponibles que puede usar con Chaos Studio, incluidos los requisitos previos y los parámetros.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 80d399ecb2b3d73b8014e3eebf8dbb8ea1de0436
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090992"
---
# <a name="chaos-studio-fault-and-action-library"></a>Biblioteca de acciones y errores de Chaos Studio

Los siguientes errores están disponibles para su uso hoy en día. Visite la página de [Proveedores de errores](./chaos-studio-fault-providers.md) para saber qué tipos de recursos se admiten.

## <a name="time-delay"></a>Retraso de tiempo

| Propiedad | Valor |
|-|-|
| Proveedor de errores | N/D |
| Tipos de sistemas operativos compatibles | N/D |
| Descripción | Agrega un retraso de tiempo antes, entre o después de otras acciones. Resulta útil para esperar a que el impacto de un error aparezca en un servicio o para esperar a que se complete una actividad fuera del experimento (por ejemplo, esperar a que se produzca la recuperación automática antes de insertar otro error). |
| Prerrequisitos | N/D |
| Urn | urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed |
| duration | Duración del retraso en formato ISO 8601 (ejemplo: PT10M) |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [ 
    {
      "type": "delay",
      "name": "urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed",
      "duration": "PT10M"
    }
  ] 
}
```

## <a name="cpu-pressure"></a>Presión de CPU

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | CPUPressure-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows, Linux |
| Descripción | Agregue presión de CPU hasta el valor especificado en la máquina virtual donde se inserta este error mientras dure la acción de error. La presión artificial de la CPU se elimina al final de la duración o si se cancela el experimento. |
| Prerrequisitos | **Linux:** la ejecución del error en una máquina virtual Linux requiere la instalación de la utilidad **stress-ng**. Puede realizar su instalación mediante el administrador de paquetes para su distribución de Linux, </br> Comando APT para instalar stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Comando YUM para instalar stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows:** ninguno. |
| Urn | urn:csci:microsoft:agent:cpuPressure/1.0 |
| Parámetros (clave, valor)  |
| pressureLevel | Entero entre 1 y 99 que indica cuánta presión de CPU (%) se aplicará a la máquina virtual. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON
```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="notes"></a>Notas
Problemas conocidos en Linux:
1. Es posible que el efecto de esfuerzo no finalice correctamente si AzureChaosAgent se termina inesperadamente.
2. El error de CPU de Linux solo se prueba en Ubuntu 16.04-LTS y Ubuntu 18.04-LTS.

## <a name="physical-memory-pressure"></a>Presión de memoria física

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | PhysicalMemoryPressure-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows, Linux |
| Descripción | Agregue presión de memoria física hasta el valor especificado en la máquina virtual donde se inserta este error mientras dure la acción de error. La presión de memoria física artificial se elimina al final de la duración o si se cancela el experimento. |
| Prerrequisitos | **Linux:** la ejecución del error en una máquina virtual Linux requiere la instalación de la utilidad **stress-ng**. Puede realizar su instalación mediante el administrador de paquetes para su distribución de Linux, </br> Comando APT para instalar stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Comando YUM para instalar stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows:** ninguno. |
| Urn | urn:csci:microsoft:agent:physicalMemoryPressure/1.0 |
| Parámetros (clave, valor) |  |
| pressureLevel | Entero entre 1 y 99 que indica cuánta presión de memoria física (%) se aplicará a la máquina virtual. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:physicalMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="virtual-memory-pressure"></a>Presión de memoria virtual

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | VirtualMemoryPressure-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Agregue presión de memoria virtual hasta el valor especificado en la máquina virtual donde se inserta este error mientras dure la acción de error. La presión de memoria virtual artificial se elimina al final de la duración o si se cancela el experimento. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:agent:virtualMemoryPressure/1.0 |
| Parámetros (clave, valor) |  |
| pressureLevel | Entero entre 1 y 99 que indica cuánta presión de memoria física (%) se aplicará a la máquina virtual. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:virtualMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-windows"></a>Presión de E/S de disco (Windows)

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | DiskIOPressure-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Usa la [utilidad diskspd](https://github.com/Microsoft/diskspd/wiki) para agregar presión de disco al almacenamiento principal de la máquina virtual donde se inserta mientras dure la acción de error. Este error tiene cinco modos de ejecución diferentes. La presión del disco artificial se elimina al final de la duración o si se cancela el experimento. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| Parámetros (clave, valor) |  |
| pressureMode | Modo preestablecido de presión de disco que se agrega al almacenamiento principal de la máquina virtual. Debe ser uno de los PressureMode de la tabla siguiente. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="pressure-modes"></a>Modos de presión

| PressureMode | Descripción |
| -- | -- |
| PremiumStorageP10IOPS | numberOfThreads = 1<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 2<br/>percentOfWriteActions = 50 |
| PremiumStorageP10Throttling |<br/>numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50IOPS | numberOfThreads = 32<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 32<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50Throttling | numberOfThreads = 2<br/>randomBlockSizeInKB = 1024<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 1024<br/>sizeOfWriteBufferInKB = 1024<br/>fileSizeInGB = 20<br/>percentOfWriteActions = 50|
| Valor predeterminado | numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:diskIOPressure/1.0",
      "parameters": [
        {
          "key": "pressureMode",
          "value": "PremiumStorageP10IOPS"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-linux"></a>Presión de E/S de disco (Linux)

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | LinuxDiskIOPressure-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Linux |
| Descripción | Usa stress-ng para aplicar presión al disco. Se generan uno o varios procesos de trabajo que realizan procesos de E/S con archivos temporales. Para obtener más información sobre cómo se aplica la presión, vea https://wiki.ubuntu.com/Kernel/Reference/stress-ng. |
| Prerrequisitos | La ejecución del error en una máquina virtual Linux requiere la instalación de la utilidad **stress-ng**. Puede realizar su instalación mediante el administrador de paquetes para su distribución de Linux, </br> Comando APT para instalar stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Comando YUM para instalar stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:linuxDiskIOPressure/1.0 |
| Parámetros (clave, valor) |  |
| workerCount | Número de procesos de trabajo que se ejecutarán. Si se establece en 0, se generarán tantos procesos de trabajo como número de procesadores existan. |
| fileSizePerWorker | Tamaño del archivo temporal en el que un trabajo realizará operaciones de E/S. Entero más una unidad en bytes (b), kilobytes (k), megabytes (m) o gigabytes (g) (por ejemplo, 4 m para 4 megabytes, 256 g para 256 gigabytes) |
| blockSize | Tamaño de bloque que se usará para las operaciones de E/S de disco, con un límite de 4 megabytes. Entero más una unidad en bytes (b), kilobytes (k) o megabytes (m) (por ejemplo, 512 000 para 512 kilobytes) |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:linuxDiskIOPressure/1.0",
      "parameters": [
        {
          "key": "workerCount",
          "value": "0"
        },
        {
          "key": "fileSizePerWorker",
          "value": "512m"
        },
        {
          "key": "blockSize",
          "value": "256k"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arbitrary-stress-ng-stress"></a>Esfuerzo de stress-ng arbitrario

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | StressNg-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Linux |
| Descripción | Ejecute cualquier comando stress-ng pasando argumentos directamente a stress-ng. Resulta útil cuando uno de los errores predefinidos para stress-ng no satisface las necesidades. |
| Prerrequisitos | La ejecución del error en una máquina virtual Linux requiere la instalación de la utilidad **stress-ng**. Puede realizar su instalación mediante el administrador de paquetes para su distribución de Linux, </br> Comando APT para instalar stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Comando YUM para instalar stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:stressNg/1.0 |
| Parámetros (clave, valor) |  |
| stressNgArguments | Uno o varios argumentos que se pasarán al proceso stress-ng. Para más información sobre los posibles argumentos de stress-ng, consulte https://wiki.ubuntu.com/Kernel/Reference/stress-ng |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stressNg/1.0",
      "parameters": [
        {
          "key": "stressNgArguments",
          "value": "--random 64"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="stop-windows-service"></a>Detener un servicio de Windows

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | StopService-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Usa las API de Windows Service Controller para detener un servicio de Windows mientras dure el error, reiniciando al final de la duración o si se cancela el experimento. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:agent:stopService/1.0 |
| Parámetros (clave, valor) |  |
| serviceName | Nombre del servicio de Windows que quiere detener. Puede ejecutar `sc.exe query` en el símbolo del sistema para explorar los nombres de servicio, no se admiten los nombres descriptivos del servicio de Windows. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stopService/1.0",
      "parameters": [
        {
          "key": "serviceName",
          "value": "nvagent"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="time-change"></a>Cambio de hora

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | TimeChange-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Cambia la hora del sistema de la máquina virtual donde se inserta y la restablece al final de la duración o si se cancela el experimento. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:agent:timeChange/1.0 |
| Parámetros (clave, valor) |  |
| dateTime | Una cadena DateTime en [formato ISO8601](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html). Si faltan valores AAAA-MM-DD, el valor predeterminado es el día actual cuando se ejecuta el experimento. Si faltan valores hh:mm:ss, el valor predeterminado es 12:00:00 AM. Si se proporciona un año de 2 dígitos (AA), se convierte en un año de 4 dígitos (AAAA) basado en el siglo actual. Si \<Z\> falta, el valor predeterminado es el desplazamiento de la zona horaria local. \<Z\> debe incluir siempre un símbolo de signo (negativo o positivo). |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:timeChange/1.0",
      "parameters": [
        {
          "key": "dateTime",
          "value": "2038-01-01T03:14:07"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="kill-process"></a>Proceso de terminación

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | KillProcess-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows, Linux |
| Descripción | Termina todas las instancias en ejecución de un proceso que coincide con el nombre del proceso enviado en los parámetros de error. Dentro de la duración establecida para la acción de error, un proceso se termina de forma repetitiva en función del valor del intervalo de eliminación especificado. Este es un error destructivo donde el administrador del sistema tendrá que recuperar manualmente el proceso si la recuperación automática está configurada para el proceso. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:agent:killProcess/1.0 |
| Parámetros (clave, valor) |  |
| processName | Nombre de un proceso que se ejecuta en una máquina virtual (sin el archivo .exe) |
| killIntervalInMilliseconds | Cantidad de tiempo que el error esperará entre intentos sucesivos de eliminación en milisegundos. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:killProcess/1.0",
      "parameters": [
        {
          "key": "processName",
          "value": "myapp"
        },
        {
          "key": "killIntervalInMilliseconds",
          "value": "1000"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="dns-failure"></a>Error de DNS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | DnsFailure-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Sustituye la respuesta de una solicitud de búsqueda DNS por un código de error especificado. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:agent:dnsFailure/1.0 |
| Parámetros (clave, valor) |  |
| hosts | Matriz JSON delimitada de nombres de host para los que se producirá un error en la solicitud de búsqueda DNS. |
| dnsFailureReturnCode | Código de error DNS que se va a devolver al cliente para el error de búsqueda (FormErr, ServFail, NXDomain, NotImp, Refused, XDomain, YXRRSet, NXRRSet, NotAuth, NotZone). Para más información sobre los códigos de retorno DNS, visite [el sitio web de IANA](https://www.iana.org/assignments/dns-parameters/dns-parameters.xml#dns-parameters-6) |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:dnsFailure/1.0",
      "parameters": [
        {
          "key": "hosts",
          "value": "[ \"www.bing.com\", \"msdn.microsoft.com\" ]"
        },
        {
          "key": "dnsFailureReturnCode",
          "value": "ServFail"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>Limitaciones

* El error de DNS requiere Windows 2019 RS5 o posterior.
* La caché DNS se omitirá durante la duración del error para los nombres de host definidos en el error.

## <a name="network-latency"></a>Latencia de red

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | NetworkLatency-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Aumenta la latencia de red para un intervalo de puertos y un bloque de red especificados. |
| Prerrequisitos | El agente debe ejecutarse como administrador. Si el agente se instala como una extensión de máquina virtual, se ejecuta como administrador de forma predeterminada. |
| Urn | urn:csci:microsoft:agent:networkLatency/1.0 |
| Parámetros (clave, valor) |  |
| latencyInMilliseconds | Cantidad de latencia que se va a aplicar en milisegundos. |
| destinationFilters | Matriz JSON delimitada de filtros de paquetes que define los paquetes salientes que se deben dirigir para la inyección de errores. Máximo de 3. |
| address | Dirección IP que indica el inicio del intervalo IP. |
| subnetMask | Máscara de subred para el intervalo de direcciones IP. |
| portLow | (Opcional) Número de puerto del inicio del intervalo de puertos. |
| portHigh | (Opcional) Número de puerto del final del intervalo de puertos. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkLatency/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "latencyInMilliseconds",
          "value": "100",
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-disconnect"></a>Desconexión de red

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | NetworkDisconnect-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Bloquea el tráfico de red saliente para el intervalo de puertos y el bloque de red especificados. |
| Prerrequisitos | El agente debe ejecutarse como administrador. Si el agente se instala como una extensión de máquina virtual, se ejecuta como administrador de forma predeterminada. |
| Urn | urn:csci:microsoft:agent:networkDisconnect/1.0 |
| Parámetros (clave, valor) |  |
| destinationFilters | Matriz JSON delimitada de filtros de paquetes que define los paquetes salientes que se deben dirigir para la inyección de errores. Máximo de 3. |
| address | Dirección IP que indica el inicio del intervalo IP. |
| subnetMask | Máscara de subred para el intervalo de direcciones IP. |
| portLow | (Opcional) Número de puerto del inicio del intervalo de puertos. |
| portHigh | (Opcional) Número de puerto del final del intervalo de puertos. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnect/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

> [!WARNING]
> El error de desconexión de red solo afecta a las nuevas conexiones. Las conexiones **activas** existentes siguen persistiendo. Puede reiniciar el servicio o el proceso para forzar la interrupción de las conexiones.

## <a name="network-disconnect-with-firewall-rule"></a>Desconexión de red con regla de firewall

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | NetworkDisconnectViaFirewall-1.0 |
| Tipo de destino | Microsoft-Agent |
| Tipos de sistemas operativos compatibles | Windows |
| Descripción | Aplica una regla de firewall de Windows para bloquear el tráfico saliente para el intervalo de puertos y el bloque de red especificados. |
| Prerrequisitos | El agente debe ejecutarse como administrador. Si el agente se instala como una extensión de máquina virtual, se ejecuta como administrador de forma predeterminada. |
| Urn | urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0 |
| Parámetros (clave, valor) |  |
| destinationFilters | Matriz JSON delimitada de filtros de paquetes que define los paquetes salientes que se deben dirigir para la inyección de errores. Máximo de 3. |
| address | Dirección IP que indica el inicio del intervalo IP. |
| subnetMask | Máscara de subred para el intervalo de direcciones IP. |
| portLow | (Opcional) Número de puerto del inicio del intervalo de puertos. |
| portHigh | (Opcional) Número de puerto del final del intervalo de puertos. |
| virtualMachineScaleSetInstances | (Opcional) Matriz de ID de instancia al aplicar este error a un conjunto de escalado de máquinas virtuales |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"Address\": \"23.45.229.97\", \"SubnetMask\": \"255.255.255.224\", \"PortLow\": \"5000\", \"PortHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-shutdown"></a>Apagado de la máquina virtual ARM
| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | Shutdown-1.0 |
| Tipo de destino | Microsoft-VirtualMachine |
| Tipos de sistemas operativos compatibles | Windows, Linux |
| Descripción | Apaga una máquina virtual mientras dura el error y, opcionalmente, la reinicia al final de la duración del error o si se cancela el experimento. solo se admiten VM de Azure Resource Manager. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:virtualMachine:shutdown/1.0 |
| Parámetros (clave, valor) |  |
| abruptShutdown | (Opcional) Valor booleano que indica si la máquina virtual debe apagarse correcta o repentinamente (de forma destructiva). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "false"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-scale-set-instance-shutdown"></a>Apagado de la instancia del conjunto de escalado de la máquina virtual ARM

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | Shutdown-1.0 |
| Tipo de destino | Microsoft-VirtualMachineScaleSet |
| Tipos de sistemas operativos compatibles | Windows, Linux |
| Descripción | Apaga o elimina una instancia del conjunto de escalado virtual mientras dura el error y, opcionalmente, reinicia la máquina virtual al final de la duración del error o si se cancela el experimento. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0 |
| Parámetros (clave, valor) |  |
| abruptShutdown | (Opcional) Valor booleano que indica si la instancia del conjunto de escalado de máquinas virtuales debe apagarse correcta o repentinamente (destructiva). |
| instances | Cadena que es una matriz delimitada de identificadores de instancia del conjunto de escalado de máquina virtual a los que se aplicará el error. |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "true"
        },
        {
          "key": "instances",
          "value": "[\"1\",\"3\"]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="cosmos-db-failover"></a>Conmutación por error de Cosmos DB

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | Failover-1.0 |
| Tipo de destino | Microsoft-CosmosDB |
| Descripción | Hace que una cuenta de Cosmos DB con una sola región de escritura conmute por error a una región de lectura especificada para simular una [interrupción de la región de escritura](../cosmos-db/high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage) |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:cosmosDB:failover/1.0 |
| Parámetros (clave, valor) |  |
| readRegion | La región de lectura que se debe promover a la región de escritura durante la conmutación por error, por ejemplo, "Este de EE. UU. 2". |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:cosmosDB:failover/1.0",
      "parameters": [
        {
          "key": "readRegion",
          "value": "West US 2"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-network-faults"></a>Errores de red de Chaos Mesh de AKS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | NetworkChaos-1.0 |
| Tipo de destino | Microsoft-AzureKubernetesServiceChaosMesh |
| Descripción | Provoca un error de red disponible a través de [Chaos Mesh](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/networkchaos_experiment) para ejecutarse en el clúster de AKS. Resulta útil para volver a crear incidentes de AKS resultantes de interrupciones de red, retrasos, duplicaciones, pérdidas y daños. |
| Prerrequisitos | El clúster de AKS debe [tener implementada la versión 1.2.3 o anterior de Chaos Mesh y tener la versión de AAD administrada por AKS deshabilitada](chaos-studio-tutorial-aks.md). |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/1.0 |
| Parámetros (clave, valor) |  |
| jsonSpec | [Especificación de Chaos Mesh](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) con formato JSON y con escape que usa el [tipo NetworkChaos](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/networkchaos_experiment). Puede usar un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON, reducirlo y usar una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para escapar la especificación JSON. Incluya solo EL YAML en la propiedad "jsonSpec" (no incluya metadatos, tipo, etc.). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"delay\",\"delay\":{\"latency\":\"30s\"},\"duration\":\"30s\",\"mode\":\"one\",\"scheduler\":{\"cron\":\"@every 60s\"},\"selector\":{\"labelSelectors\":{\"app\":\"web-show\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-pod-faults"></a>Errores de pod de Chaos Mesh de AKS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | PodChaos-1.0 |
| Tipo de destino | Microsoft-AzureKubernetesServiceChaosMesh |
| Descripción | Provoca un error de pod disponible a través de [Chaos Mesh](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/podchaos_experiment) para ejecutarse en el clúster de AKS. Resulta útil para volver a crear incidentes de AKS que son el resultado de errores de pod o problemas de contenedor. |
| Prerrequisitos | El clúster de AKS debe [tener implementada la versión 1.2.3 o anterior de Chaos Mesh y tener la versión de AAD administrada por AKS deshabilitada](chaos-studio-tutorial-aks.md). |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/1.0 |
| Parámetros (clave, valor) |  |
| jsonSpec | [Especificación de Chaos Mesh](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) con formato JSON y con escape que usa el [tipo PodChaos](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/podchaos_experiment#pod-failure-configuration-file). Puede usar un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON, reducirlo y usar una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para escapar la especificación JSON. Incluya solo EL YAML en la propiedad "jsonSpec" (no incluya metadatos, tipo, etc.). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"pod-failure\",\"mode\":\"one\",\"value\":\"\",\"duration\":\"30s\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"tikv\"}},\"scheduler\":{\"cron\":\"@every 2m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-stress-faults"></a>Errores de esfuerzo de Chaos Mesh de AKS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | StressChaos-1.0 |
| Tipo de destino | Microsoft-AzureKubernetesServiceChaosMesh |
| Descripción | Provoca un error de esfuerzo disponible a través de [Chaos Mesh](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/stresschaos_experiment) para ejecutarse en el clúster de AKS. Resulta útil para volver a crear incidentes de AKS debido a las sobrecargas en una colección de pods, por ejemplo, debido a un consumo elevado de CPU o memoria. |
| Prerrequisitos | El clúster de AKS debe [tener implementada la versión 1.2.3 o anterior de Chaos Mesh y tener la versión de AAD administrada por AKS deshabilitada](chaos-studio-tutorial-aks.md). |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/1.0 |
| Parámetros (clave, valor) |  |
| jsonSpec | [Especificación de Chaos Mesh](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) con formato JSON y con escape que usa el [tipo StressChaos](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/stresschaos_experiment#configuration). Puede usar un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON, reducirlo y usar una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para escapar la especificación JSON. Incluya solo EL YAML en la propiedad "jsonSpec" (no incluya metadatos, tipo, etc.). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"tidb-cluster-demo\"]},\"stressors\":{\"cpu\":{\"workers\":1}},\"duration\":\"30s\",\"scheduler\":{\"cron\":\"@every 2m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-io-faults"></a>Errores de E/S de Chaos Mesh de AKS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | IOChaos-1.0 |
| Tipo de destino | Microsoft-AzureKubernetesServiceChaosMesh |
| Descripción | Provoca un error de E/S disponible a través de [Chaos Mesh](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/iochaos_experiment) para ejecutarse en el clúster de AKS. Resulta útil para volver a crear incidentes de AKS debidos a retrasos de E/S y errores de lectura y escritura al usar llamadas del sistema de E/S como `open`, `read` y `write`. |
| Prerrequisitos | El clúster de AKS debe [tener implementada la versión 1.2.3 o anterior de Chaos Mesh y tener la versión de AAD administrada por AKS deshabilitada](chaos-studio-tutorial-aks.md). |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/1.0 |
| Parámetros (clave, valor) |  |
| jsonSpec | [Especificación de Chaos Mesh](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) con formato JSON y con escape que usa el [tipo IOChaos](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/iochaos_experiment#configuration-file). Puede usar un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON, reducirlo y usar una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para escapar la especificación JSON. Incluya solo EL YAML en la propiedad "jsonSpec" (no incluya metadatos, tipo, etc.). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"latency\",\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"etcd\"}},\"volumePath\":\"\/var\/run\/etcd\",\"path\":\"\/var\/run\/etcd\/**\/*\",\"delay\":\"100ms\",\"percent\":50,\"duration\":\"400s\",\"scheduler\":{\"cron\":\"@every 10m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-time-faults"></a>Errores de tiempo de Chaos Mesh de AKS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | TimeChaos-1.0 |
| Tipo de destino | Microsoft-AzureKubernetesServiceChaosMesh |
| Descripción | Provoca un cambio en el reloj del sistema en el clúster de AKS mediante [Chaos Mesh](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/timechaos_experiment). Resulta útil para volver a crear incidentes de AKS que se deban a sistemas distribuidos que no están sincronizados, lógica de año bisiesto o segundo intercalar ausente o incorrecta, etc. |
| Prerrequisitos | El clúster de AKS debe [tener implementada la versión 1.2.3 o anterior de Chaos Mesh y tener la versión de AAD administrada por AKS deshabilitada](chaos-studio-tutorial-aks.md). |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/1.0 |
| Parámetros (clave, valor) |  |
| jsonSpec | [Especificación de Chaos Mesh](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) con formato JSON y con escape que usa el [tipo TimeChaos](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/timechaos_experiment#configuration-file). Puede usar un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON, reducirlo y usar una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para escapar la especificación JSON. Incluya solo EL YAML en la propiedad "jsonSpec" (no incluya metadatos, tipo, etc.). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"pd\"}},\"timeOffset\":\"-10m100ns\",\"clockIds\":[\"CLOCK_REALTIME\"],\"containerNames\":[\"pd\"],\"duration\":\"10s\",\"scheduler\":{\"cron\":\"@every 15s\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-kernel-faults"></a>Errores del kernel de Chaos Mesh de AKS

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | KernelChaos-1.0 |
| Tipo de destino | Microsoft-AzureKubernetesServiceChaosMesh |
| Descripción | Provoca un error de kernel disponible a través de [Chaos Mesh](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/kernelchaos_experiment) para ejecutarse en el clúster de AKS. Resulta útil para volver a crear incidentes de AKS debidos a errores de nivel de kernel de Linux, como un error de montaje o falta de asignación de memoria. |
| Prerrequisitos | El clúster de AKS debe [tener implementada la versión 1.2.3 o anterior de Chaos Mesh y tener la versión de AAD administrada por AKS deshabilitada](chaos-studio-tutorial-aks.md). |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/1.0 |
| Parámetros (clave, valor) |  |
| jsonSpec | [Especificación de Chaos Mesh](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) con formato JSON y con escape que usa el [tipo KernelChaos](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/kernelchaos_experiment#configuration-file). Puede usar un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON, reducirlo y usar una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para escapar la especificación JSON. Incluya solo EL YAML en la propiedad "jsonSpec" (no incluya metadatos, tipo, etc.). |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"chaos-mount\"]},\"failKernRequest\":{\"callchain\":[{\"funcname\":\"__x64_sys_mount\"}],\"failtype\":0}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-security-group-set-rules"></a>Grupo de seguridad de red (establecer reglas)

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | SecurityRule-1.0 |
| Tipo de destino | Microsoft-NetworkSecurityGroup |
| Descripción | Habilita la manipulación o creación de una regla en un grupo de seguridad de red de Azure existente o un conjunto de grupos de seguridad de red de Azure (suponiendo que la definición de la regla sea aplicable entre grupos de seguridad). Resulta útil para simular una interrupción de una dependencia o no dependencia de nivel inferior o entre regiones, simular un evento que se espera que desencadene una lógica para forzar una conmutación por error del servicio, simular un evento que se espera que desencadene una acción desde un servicio de supervisión o administración de estado, o como alternativa para bloquear o permitir el tráfico de red donde no se puede implementar el agente de Chaos. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:networkSecurityGroup:securityRule/1.0 |
| Parámetros (clave, valor) |  |
| name | Un nombre único para la regla de seguridad que se creará. Se producirá un error si ya existe otra regla en NSG con el mismo nombre. Debe empezar con una letra o un número y acabar con una letra, un número o un guion bajo, y solo debe contener letras, números, guiones bajos, puntos o guiones. |
| protocol | Protocolo para la regla de seguridad. Debe ser Cualquiera, TCP, UDP o ICMP. |
| sourceAddresses | Cadena que representa una matriz delimitada por JSON de direcciones IP con formato CIDR. También puede ser un nombre de etiqueta de servicio para una regla de entrada, por ejemplo, "AppService". También se puede utilizar un asterisco (*) para establecer correspondencia entre todas las IP de origen. |
| destinationAddresses | Cadena que representa una matriz delimitada por JSON de direcciones IP con formato CIDR. También puede ser un nombre de etiqueta de servicio para una regla de salida, por ejemplo, "AppService". También se puede utilizar un asterisco (*) para establecer correspondencia entre todas las IP de destino. |
| action | Tipo de acceso del grupo de seguridad. Debe ser Permitir o Denegar |
| destinationPortRanges | Cadena que representa una matriz delimitada por JSON de puertos únicos o intervalos de puertos, como 80 o 1024-65535. |
| sourcePortRanges | Cadena que representa una matriz delimitada por JSON de puertos únicos o intervalos de puertos, como 80 o 1024-65535. |
| priority | Escriba un valor de 100 a 4096 que sea único para todas las reglas de seguridad del grupo de seguridad de red. Se producirá un error si ya existe otra regla en NSG con la misma prioridad. |
| direction | Dirección del tráfico afectado por la regla de seguridad. Debe ser Entrante o Saliente. |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{ 
  "name": "branchOne", 
  "actions": [ 
    { 
      "type": "continuous", 
      "name": "urn:csci:microsoft:networkSecurityGroup:securityRule/1.0", 
      "parameters": [ 
          { 
              "key": "name", 
              "value": "Block_SingleHost_to_Networks" 

          }, 
          { 
              "key": "protocol", 
              "value": "Any" 
          }, 
          { 
              "key": "sourceAddresses", 
              "value": "[\"10.1.1.128/32\"]"
          }, 
          { 
              "key": "destinationAddresses", 
              "value": "[\"10.20.0.0/16\",\"10.30.0.0/16\"]"
          }, 
          { 
              "key": "access", 
              "value": "Deny" 
          }, 
          { 
              "key": "destinationPortRanges", 
              "value": "[\"80-8080\"]"
          }, 
          { 
              "key": "sourcePortRanges", 
              "value": "[\"*\"]"
          }, 
          { 
              "key": "priority", 
              "value": "100" 
          }, 
          { 
              "key": "direction", 
              "value": "Outbound" 
          } 
      ], 
      "duration": "PT10M", 
      "selectorid": "myResources" 
    } 
  ] 
} 
```

### <a name="limitations"></a>Limitaciones

* El error solo se puede aplicar a un grupo de seguridad de red existente.
* Cuando se aplica una regla de NSG destinada a denegar el tráfico, las conexiones existentes no se dividirán hasta que hayan estado **inactivas** durante 4 minutos. Una solución alternativa consiste en agregar otra rama en el mismo paso que usa un error que hará que las conexiones existentes se interrumpan cuando se aplica el error de NSG. Por ejemplo, eliminar el proceso, detener temporalmente el servicio o reiniciar la máquina virtual provocará el restablecimiento de las conexiones.
* Las reglas se aplican al principio de la acción. Los cambios externos en la regla durante la duración de la acción provocarán un error en el experimento.
* No se admite la creación o modificación de reglas del grupo de seguridad de aplicaciones.
* Los valores de prioridad deben ser únicos en cada NSG de destino. Si se intenta crear una nueva regla que tenga el mismo valor de prioridad que otra, se producirá un error en el experimento.

## <a name="azure-cache-for-redis-reboot"></a>Reinicio de Azure Cache for Redis

| Propiedad | Valor |
|-|-|
| Nombre de la funcionalidad | Reboot-1.0 |
| Tipo de destino | Microsoft-AzureClusteredCacheForRedis |
| Descripción | Hace que se produzca una operación de reinicio forzado en el destino para simular una breve interrupción. |
| Prerrequisitos | Ninguno. |
| Urn | urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0 |
| Tipo de error | Discrete |
| Parámetros (clave, valor) |  |
| rebootType | Los tipos de nodo donde se va a realizar la acción de reinicio que se pueden especificar como PrimaryNode, SecondaryNode o AllNodes.  |
| shardId | Identificador de la partición que se va a reiniciar.  |

### <a name="sample-json"></a>Ejemplo de JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "discrete",
      "name": "urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0",
      "parameters": [
        {
          "key": "RebootType",
          "value": "AllNodes"
        },
        {
          "key": "ShardId",
          "value": "0"
        }
      ],
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>Limitaciones

* El error de reinicio hace que un reinicio forzado simule mejor un evento de interrupción, lo cual significa que existe la posibilidad de que se produzca una pérdida de datos.
* El error de reinicio es un tipo de error **discreto**. A diferencia de los errores continuos, es una acción única y, por tanto, no tiene ninguna duración.
