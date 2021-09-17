---
title: Configuración de la directiva de almacenamiento
description: Aprenda a configurar la directiva de almacenamiento para las máquinas virtuales de Azure VMware Solution.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: df8f6931dbae377833d3f380e3fd5fcad8bd73ba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431339"
---
# <a name="configure-storage-policy"></a>Configuración de la directiva de almacenamiento

Las directivas de almacenamiento de vSAN definen los requisitos de almacenamiento de las máquinas virtuales. Estas directivas garantizan el nivel de servicio requerido para las máquinas virtuales, ya que determinan cómo se asigna el almacenamiento a la máquina virtual. A cada máquina virtual implementada en un almacén de datos vSAN se le asigna al menos una directiva de almacenamiento de máquina virtual.

Puede asignar una directiva de almacenamiento de máquina virtual en una implementación inicial de una máquina virtual o al realizar otras operaciones de máquina virtual, como clonación o migración. Los usuarios cloudadmin posteriores a la implementación o roles equivalentes no pueden cambiar la directiva de almacenamiento predeterminada de una máquina virtual. Sin embargo, se permiten cambios en la **directiva de almacenamiento de máquina virtual** por disco. 

El comando Ejecutar permite a los usuarios autorizados cambiar la directiva de almacenamiento de máquina virtual predeterminada o existente por una directiva disponible para una máquina virtual posterior a la implementación. No se realizan cambios en la directiva de almacenamiento de máquina virtual de nivel de disco. Siempre puede cambiar la directiva de almacenamiento de máquina virtual de nivel de disco según sus requisitos.


>[!NOTE]
>Los comandos de ejecución se ejecutan de uno en uno en el orden enviado.


En este procedimiento, aprenderá a:

> [!div class="checklist"]
> * Enumerar todas las directivas de almacenamiento.
> * Establecer la directiva de almacenamiento de una máquina virtual.
> * Especificar la directiva de almacenamiento de un clúster.



## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que se [cumple el nivel mínimo de hosts](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-EDBB551B-51B0-421B-9C44-6ECB66ED660B.html).

|  **Configuración de RAID** | **Errores tolerables (FTT)** | **Hosts mínimos requeridos** |
| --- | :---: | :---: |
| RAID-1 (Creación de reflejo) <br />Configuración predeterminada.  | 1  | 3  |
| RAID-5 (codificación de borrado)  | 1  | 4  |
| RAID-1 (Creación de reflejo)  | 2  | 5  |
| RAID-6 (codificación de borrado)  | 2  | 6  |
| RAID-1 (Creación de reflejo)  | 3  | 7  |


 

## <a name="list-storage-policies"></a>Enumeración de directivas de almacenamiento

Ejecutará el cmdlet `Get-StoragePolicy` para enumerar las directivas de almacenamiento basadas en vSAN disponibles para establecer en una máquina virtual.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Ejecutar comando** > **Paquetes** > **Get-StoragePolicies**.

   :::image type="content" source="media/run-command/run-command-overview-storage-policy.png" alt-text="Captura de pantalla que muestra cómo acceder a los comandos de ejecución de la directiva de almacenamiento disponibles." lightbox="media/run-command/run-command-overview-storage-policy.png":::

1. Proporcione los valores necesarios o cambie los valores predeterminados y, luego, seleccione **Ejecutar**.

   :::image type="content" source="media/run-command/run-command-get-storage-policy.png" alt-text="Captura de pantalla que muestra cómo enumerar las directivas de almacenamiento disponibles.":::
   
   | **Campo** | **Valor** |
   | --- | --- |
   | **Retain up to** (Conservar hasta)  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Specify name for execution** (Especificar el nombre de la ejecución)  | Nombre alfanumérico, por ejemplo, **Get-StoragePolicies-Exec1**. |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.




## <a name="set-storage-policy-on-vm"></a>Establecimiento de la directiva de almacenamiento en la máquina virtual

Ejecutará el cmdlet `Set-AvsVMStoragePolicy` para modificar las directivas de almacenamiento basadas en vSAN en una máquina virtual individual. 

>[!NOTE]
>No puede usar el cliente de vSphere para cambiar la directiva de almacenamiento predeterminada ni ninguna directiva de almacenamiento existente para una máquina virtual. 

1. Seleccione **Ejecutar comando** > **Paaquetes** > **Set-AvsVMStoragePolicy**.

1. Proporcione los valores necesarios o cambie los valores predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **VMName** | Nombre de la máquina virtual de destino. |
   | **StoragePolicyName** | Nombre de la directiva de almacenamiento que se establecerá. Por ejemplo, **RAID-FTT-1**. |
   | **Retain up to** (Conservar hasta)  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Specify name for execution** (Especificar el nombre de la ejecución)  | Nombre alfanumérico, por ejemplo, **changeVMStoragePolicy**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.


## <a name="specify-storage-policy-for-a-cluster"></a>Especificar la directiva de almacenamiento de un clúster.

Ejecutará el cmdlet `Set-ClusterDefaultStoragePolicy` para especificar la directiva de almacenamiento predeterminada de un clúster.

>[!NOTE]
>No se permite cambiar la directiva de almacenamiento del clúster de administración predeterminado.

1. Seleccione **Ejecutar comando** > **Paaquetes** > **Set-ClusterDefaultStoragePolicy**.

1. Proporcione los valores necesarios o cambie los valores predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **ClusterName** | Nombre del clúster. |
   | **StoragePolicyName** | Nombre de la directiva de almacenamiento que se establecerá. Por ejemplo, **RAID-FTT-1**. |
   | **Retain up to** (Conservar hasta)  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Specify name for execution** (Especificar el nombre de la ejecución)  | Nombre alfanumérico, por ejemplo, **Set-ClusterDefaultStoragePolicy-Exec1**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.



## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a configurar directivas de almacenamiento de vSAN, puede aprender más sobre:

- [Conexión de grupos de discos a hosts de Azure VMware Solution (versión preliminar)](attach-disk-pools-to-azure-vmware-solution-hosts.md): puede usar discos como almacenamiento persistente para Azure VMware Solution a fin de optimizar los costos y el rendimiento.

- [Configuración de la identidad externa de vCenter](configure-identity-source-vcenter.md): vCenter tiene un usuario local integrado llamado cloudadmin que tiene asignado el rol CloudAdmin. El usuario cloudadmin local se usa para configurar usuarios en Active Directory (AD). Con la característica Ejecutar comando, puede configurar Active Directory a través de LDAP o LDAPS para vCenter como origen de identidad externo.
