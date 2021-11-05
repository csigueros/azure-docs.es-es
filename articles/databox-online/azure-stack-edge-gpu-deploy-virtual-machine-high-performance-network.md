---
title: Implementación de máquinas virtuales de red de alto rendimiento en el GPU de Azure Stack Edge Pro
description: Obtenga información sobre cómo implementar máquinas virtuales de red de alto rendimiento en el GPU de Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/29/2021
ms.author: alkohli
ms.openlocfilehash: 4b3f2a2b232bec60edbce204008e245bcabee09c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093576"
---
# <a name="deploy-high-performance-network-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Implementación de máquinas virtuales de red de alto rendimiento en el dispositivo de GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Puede crear y administrar máquinas virtuales (VM) en un dispositivo de Azure Stack Edge con GPU mediante Azure Portal, plantillas y cmdlets de Azure PowerShell y a través de scripts de Python o de la CLI de Azure. En este artículo se describe cómo crear y administrar una máquina virtual de red de alto rendimiento (HPN) en su dispositivo de Azure Stack Edge Pro con GPU. 

## <a name="about-hpn-vms"></a>Acerca de las máquinas virtuales de HPN

Se usa una arquitectura de acceso a memoria no uniforme (NUMA) para aumentar la velocidad del procesador. En un sistema NUMA, las CPU se organizan en sistemas más pequeños denominados nodos. Cada nodo tiene sus propios procesadores y memoria. Normalmente, los procesadores asignan memoria a la que están cerca para que el acceso sea más rápido. Para obtener más información, consulte [Soporte técnico de NUMA](/windows/win32/procthread/numa-support).  

En el dispositivo de Azure Stack Edge, los procesadores lógicos se distribuyen en nodos NUMA y las interfaces de red de alta velocidad se pueden conectar a estos nodos. Una máquina virtual de HPN tiene un conjunto dedicado de procesadores lógicos. Estos procesadores se seleccionan primero del nodo NUMA que tiene conectada una interfaz de red de alta velocidad y, a continuación, se seleccionan de otros nodos. Una máquina virtual de HPN solo puede usar la memoria del nodo NUMA asignado a sus procesadores.  

Para ejecutar aplicaciones de red de baja latencia y alto rendimiento en las máquinas virtuales de HPN implementadas en el dispositivo, asegúrese de reservar las vCPU que residen en el nodo 0 de NUMA. Este nodo tiene conectadas interfaces de red Mellanox de alta velocidad, puerto 5 y puerto 6.   

        
## <a name="hpn-vm-deployment-workflow"></a>Flujo de trabajo de implementación de una VM de HPN

El resumen de alto nivel del flujo de trabajo de implementación del HPN es el siguiente:

1. Habilite una interfaz de red para el proceso en el dispositivo Azure Stack Edge. Este paso crea un conmutador virtual en la interfaz de red especificada.
1. Habilite la administración en la nube de máquinas virtuales desde Azure Portal.
1. Cargue un disco duro virtual en una cuenta de Azure Storage mediante el Explorador de Azure Storage. 
1. Use el disco duro virtual cargado para descargarlo en el dispositivo y crear una imagen de máquina virtual a partir de este. 
1. Reserve vCPU en el dispositivo para máquinas virtuales de HPN.
1. Use los recursos creados en los pasos anteriores:
    1. Imagen de máquina virtual que creó.
    1. Conmutador virtual asociado con la interfaz de red en la que habilitó el proceso.
    1. Subred asociada al conmutador virtual.

    Y cree o especifique los recursos insertados siguientes:
    1. Nombre de la máquina virtual, elija un tamaño de VM de HPN compatible y credenciales de inicio de sesión para la misma. 
    1. Cree discos de datos nuevos o conecte discos de datos existentes.
    1. Configure una dirección IP estática o dinámica para la VM. Si proporciona una dirección IP estática, elija una dirección IP libre en el intervalo de la subred de la interfaz de red habilitada para el proceso.

    Use los recursos anteriores para crear una máquina virtual de HPN.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a crear y administrar máquinas virtuales en el dispositivo a través de Azure Portal, debe asegurarse de lo siguiente:

- Ha completado la configuración de red en el dispositivo de Azure Stack Edge Pro con GPU como se describe en el [Paso 1: Configuración de un dispositivo de Azure Stack Edge Pro con GPU](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device).

    1. Ha habilitado una interfaz de red para el proceso. Esta dirección IP de la interfaz de red se utiliza para crear un conmutador virtual para la implementación de la máquina virtual. En la interfaz de usuario local del dispositivo, vaya a **Proceso**. Seleccione la interfaz de red que va a usar para crear un conmutador virtual.

        > [!IMPORTANT] 
        > Solo puede configurar un puerto por proceso.

    1. Habilite Proceso en la interfaz de red. Azure Stack Edge Pro GPU crea y administra un conmutador virtual correspondiente a esa interfaz de red.

-  Tiene acceso a un disco duro virtual de Windows o Linux que usará para crear la imagen de la máquina virtual que quiere crear.

Además de los requisitos previos anteriores que se usan para la creación de máquinas virtuales, también deberá configurar el siguiente requisito previo específicamente para las máquinas virtuales de HPN:

- Reserve vCPU para máquinas virtuales HPN en la interfaz Mellanox. Siga estos pasos:

    1. [Conexión a la interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

    1. Identifique todas las máquinas virtuales que se ejecutan en el dispositivo. Esto incluye las máquinas virtuales de Kubernetes o las cargas de trabajo de máquina virtual que haya implementado.

        ```powershell
        get-vm -force
        ```
    1. Detenga todas las máquinas virtuales en ejecución.
    
        ```powershell
        stop-vm -force
        ```
    1. Obtenga el `hostname` para el dispositivo. Esto debe devolver una cadena correspondiente al nombre de host del dispositivo.

        ```powershell
        hostname
        ```
    1. Obtenga los índices de procesador lógico que se reservan para las máquinas virtuales de HPN.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName <Output of hostname command>
        ```
        A continuación, se incluye una salida de ejemplo:
    
        ```powershell    
        [dbe-1csphq2.microsoftdatabox.com]: PS>hostname
        1CSPHQ2
        [dbe-1csphq2.microsoftdatabox.com]: P> Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName 1CSPHQ2
         { Numa Node #0 : CPUs [4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19] }
         { Numa Node #1 : CPUs [24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39] }
        
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. Reserve vCPU para máquinas virtuales HPN. El número de vCPU reservadas aquí determina las vCPU disponibles que se podrían asignar a las máquinas virtuales de HPN. Para ver el número de núcleos que usa cada tamaño de máquina virtual de HPN, consulte [Tamaños de máquina virtual de HPN admitidos](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes). En el dispositivo, los puertos 5 y 6 de Mellanox están en el nodo 0 de NUMA.
    
        ```powershell
        Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated <Logical indexes from the Get-HcsNumaLpMapping cmdlet> -AssignAllCpusToRoot $false
        ```
    
        Después de ejecutar este comando, el dispositivo se reinicia automáticamente.

        A continuación, se incluye una salida de ejemplo: 
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated "4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39" -AssignAllCpusToRoot $false
        Requested Configuration requires a reboot...
        Machine will reboot in some time. Please be patient.
        [dbe-1csphq2.microsoftdatabox.com]: PS>    
        ```
    
        > [!NOTE]
        > - Puede optar por reservar todos los índices lógicos de ambos nodos NUMA que se muestran en el ejemplo o un subconjunto de los índices. Si decide reservar un subconjunto de índices, elija los índices del nodo de dispositivo que tenga asociada una interfaz de red Mellanox, para obtener el mejor rendimiento. Para el GPU de Azure Stack Edge Pro, el nodo NUMA con la interfaz de red Mellanox es el n.º 0.  
        > - La lista de índices lógicos debe contener una secuencia emparejada de un número par y uno impar. Por ejemplo, ((4,5)(6,7)(10,11)). No funcionará el intento de establecer una lista de números como `5,6,7` o pares como `4,6`.
        > - El uso de dos comandos `Set-HcsNuma` consecutivos para asignar vCPU restablecerá la configuración. Además, no libera las CPU mediante el cmdlet Set-HcsNuma si ha implementado una máquina virtual de HPN.
  
    1. Espere a que el dispositivo termine de reiniciarse. Una vez que el dispositivo se esté ejecutando, abra una nueva sesión de PowerShell. [Conexión a la interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
    
    1. Valide la reserva de vCPU.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType MinRootAware -NodeName <Output of hostname command> 
        ```
        La salida no debe mostrar los índices establecidos. Si ve los índices que ha establecido en la salida, el comando `Set` no se ha completado correctamente. Vuelva a intentar el comando y si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. 
    
        Esta es una salida de ejemplo.
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS> Get-HcsNumaLpMapping -MapType MinRootAware -NodeName 1CSPHQ2
        { Numa Node #0 : CPUs [0, 1, 2, 3] }
        { Numa Node #1 : CPUs [20, 21, 22, 23] }
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. Reinicie las máquinas virtuales que se habían detenido en el paso anterior.
    
        ```powershell
        start-vm 
        ```
    <!-- Start-vm doesn't seem to work alone. Get-vm alone doesn't seem to return my running VM"VmId"--> 



## <a name="deploy-a-vm"></a>Implementación de una máquina virtual

Siga estos pasos para crear una máquina virtual HPN en el dispositivo.

1. En la instancia de Azure Portal del recurso de Azure Stack Edge, vaya a [Agregar una imagen de la máquina virtual](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm-image). Usará esta imagen de la máquina virtual para crear una máquina virtual en el paso siguiente.

1. Siga todos los pasos descritos en [Agregar una máquina virtual](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm) con este requisito de configuración. 

    En la pestaña Aspectos básicos, seleccione un tamaño de máquina virtual de las [series DSv2 o F compatibles con HPN](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

    ![Captura de pantalla que muestra la pestaña Aspectos básicos del Asistente para agregar máquina virtual para Azure Stack Edge. La pestaña Aspectos básicos y el botón Siguiente: Discos están resaltados.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-1.png)

1. Finalice los pasos restantes en la creación de la máquina virtual. La máquina virtual tarda aproximadamente 30 minutos en crearse. 

    ![Captura de pantalla que muestra la pestaña Revisar + Crear en el Asistente para agregar máquina virtual para Azure Stack Edge. El botón Crear está resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-2.png)

1. Una vez que la VM se crea correctamente, verá la nueva VM en el panel **Información general**. Seleccione la VM recién creada para ir a **Máquinas virtuales**.

    ![Captura de pantalla que muestra el panel Máquinas virtuales de un dispositivo de Azure Stack Edge. La etiqueta Máquinas virtuales y una entrada de máquina virtual están resaltadas.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-3.png)

    Seleccione la máquina virtual para ver los detalles.

    ![Captura de pantalla que muestra la pestaña Detalles en el panel Información general de una máquina virtual en Azure Stack Edge. El tamaño de la máquina virtual y la dirección IP en Redes están resaltadas.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-4.png)

    Usará la dirección IP de la interfaz de red para conectarse a la máquina virtual.

    > [!NOTE]
    > Si las vCPU no están reservadas para las máquinas virtuales de HPN antes de la implementación, se producirá un error `FabricVmPlacementErrorInsufficientNumaNodeCapacity` en la implementación.
                                                                                                                                                                         
## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de implementación de máquinas virtuales](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Supervisión de la actividad de VM en el dispositivo](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Supervisión de CPU y memoria en una VM](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

