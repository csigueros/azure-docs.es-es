---
title: Conceptos sobre almacenamiento
description: Conozca la capacidad de almacenamiento, las directivas de almacenamiento, la tolerancia a errores y la integración del almacenamiento en las nubes privadas de Azure VMware Solution.
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 08/31/2021
ms.openlocfilehash: fb6397752893640bed426e668e833126537d028a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255400"
---
# <a name="azure-vmware-solution-storage-concepts"></a>Conceptos de almacenamiento de Azure VMware Solution

Las nubes privadas de Azure VMware Solution proporcionan almacenamiento nativo en todo el clúster con vSAN de VMware. El almacenamiento local de cada host de un clúster se usa en un almacén de datos de vSAN, y el cifrado de datos en reposo está disponible y habilitado de forma predeterminada. Puede usar recursos de Azure Storage para ampliar las funcionalidades de almacenamiento de las nubes privadas.

## <a name="vsan-clusters"></a>Clústeres de vSAN

El almacenamiento local en cada host de un clúster se usa como parte de un almacén de datos de vSAN. Todos los grupos de discos usan un nivel de caché de NVMe de 1,6 TB con la capacidad sin procesar, por host y basada en SSD de 15,4 TB. El tamaño del nivel de capacidad sin procesar de un clúster es la capacidad por host multiplicada por la cantidad de hosts. Por ejemplo, un clúster de cuatro hosts proporciona una capacidad sin procesar de 61,6 TB en el nivel de capacidad de vSAN.

El almacenamiento local en los hosts de un clúster se usa en el almacén de datos de vSAN en todo el clúster. Todos los almacenes de datos se crean como parte de una implementación de nube privada y están disponibles para su uso inmediato. El usuario **cloudadmin** y todos los usuarios con el rol CloudAdmin asignado pueden administrar almacenes de datos con estos privilegios de vSAN:

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>No se puede cambiar el nombre de los almacenes de datos o de los clústeres. Puede seleccionar un nombre de clúster distinto de "Cluster-n", donde n > 1, al aprovisionar desde otro lugar que no sea el portal (AzureCLI o PowerShell).

## <a name="storage-policies-and-fault-tolerance"></a>Directivas de almacenamiento y tolerancia a errores

Esa directiva de almacenamiento predeterminada se establece en RAID-1 (creación de reflejo), FTT-1 y aprovisionamiento grueso. A menos que ajuste la directiva de almacenamiento o aplique una nueva directiva, el clúster aumentará con esta configuración. Para establecer la directiva de almacenamiento, consulte [Configuración de la directiva de almacenamiento](configure-storage-policy.md).

En un clúster de tres hosts, FTT-1 admite el error de un solo host. Microsoft regula los errores periódicamente y reemplaza el hardware cuando se detectan eventos desde una perspectiva de la arquitectura.

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="Captura de pantalla que muestra las directivas de almacenamiento de máquinas virtuales cliente de vSphere.":::


|Tipo de aprovisionamiento  |Descripción  |
|---------|---------|
|**Grueso**      | Espacio de almacenamiento reservado o asignado previamente. Protege los sistemas al permitirles funcionar incluso si el almacén de datos de vSAN está lleno porque el espacio ya está reservado. Por ejemplo, si crea un disco virtual de 10 GB con aprovisionamiento grueso. En ese caso, la capacidad total de almacenamiento del disco virtual se asigna previamente en el almacenamiento físico del disco virtual y consume todo el espacio asignado a él en el almacén de datos. No se permitirá que otras máquinas virtuales compartan el espacio del almacén de datos.         |
|**Fino**      | Consume el espacio que necesita inicialmente y aumenta hasta la demanda del espacio de datos que se usa en el almacén de datos. Fuera del valor predeterminado (aprovisionamiento grueso), puede crear máquinas virtuales con aprovisionamiento fino de FTT-1. Para la configuración de desduplicación, use el aprovisionamiento fino en la plantilla de máquina virtual.         |

>[!TIP]
>Si no está seguro de si el clúster crecerá hasta cuatro o más hosts, realice la implementación con la directiva predeterminada.  Si está seguro de que el clúster crecerá, en lugar de expandirlo después de la implementación inicial, se recomienda implementar los hosts adicionales durante la implementación. A medida que las máquinas virtuales se implementan en el clúster, cambie la directiva de almacenamiento del disco en la configuración de la máquina virtual a RAID-5 FTT-1 o RAID-6 FTT-2. 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="Captura de pantalla en la que se muestran las opciones RAID-5 FTT-1 y RAID-6 Ftt-2 resaltadas.":::


## <a name="data-at-rest-encryption"></a>Cifrado de datos en reposo

Los almacenes de datos de vSAN usan el cifrado de datos en reposo de forma predeterminada mediante claves almacenadas en Azure Key Vault. La solución de cifrado se basa en KMS y admite operaciones de vCenter para la administración de claves.  Cuando se quita un host de un clúster, los datos de los discos SSD se invalidan de inmediato.

## <a name="azure-storage-integration"></a>Integración del almacenamiento de Azure

Puede usar los servicios de almacenamiento de Azure en las cargas de trabajo que se ejecutan en su nube privada. Entre los servicios de almacenamiento de Azure se incluyen cuenta de almacenamiento, Table Storage y Blob Storage. La conexión de las cargas de trabajo a los servicios de almacenamiento de Azure no atraviesa Internet. Esta conectividad ofrece más seguridad y le permite usar los servicios de almacenamiento de Azure basados en SLA en las cargas de trabajo de su nube privada.

## <a name="alerts-and-monitoring"></a>Supervisión y alertas

Microsoft proporciona alertas cuando el consumo de capacidad supera el 75 %. Además, puede supervisar las métricas de consumo de capacidad integradas en Azure Monitor. Para más información, consulte [Configuración de alertas de Azure en Azure VMware Solution](configure-alerts-for-azure-vmware-solution.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha visto los conceptos de Azure VMware Solution, puede que quiera obtener información sobre:

- [Conexión de grupos de discos a hosts de Azure VMware Solution (versión preliminar)](attach-disk-pools-to-azure-vmware-solution-hosts.md): puede usar discos como almacenamiento persistente para Azure VMware Solution a fin de optimizar los costos y el rendimiento.

- [Configurar la directiva de almacenamiento](configure-storage-policy.md): a cada máquina virtual implementada en un almacén de datos vSAN se le asigna al menos una directiva de almacenamiento de máquina virtual. Puede asignar una directiva de almacenamiento de máquina virtual en una implementación inicial de una máquina virtual o al realizar otras operaciones de máquina virtual, como la clonación o la migración.

- [Escala de clústeres y hosts en la nube privada][tutorial-scale-private-cloud]: puede escalar los clústeres y los hosts de una nube privada según sea necesario para la carga de trabajo de la aplicación. Las limitaciones de rendimiento y disponibilidad de los servicios específicos deberán abordarse cada por caso.

- [Azure NetApp Files con Azure VMware Solution](netapp-files-with-azure-vmware-solution.md): puede usar Azure NetApp para la migración y ejecución de las cargas de trabajo de archivos empresariales más exigentes en la nube: bases de datos, SAP y aplicaciones informáticas de alto rendimiento, sin cambios de código. 

- [Control de acceso basado en rol de vSphere para Azure VMware Solution](concepts-identity.md): se usa vCenter para administrar cargas de trabajo de máquina virtual y NSX-T Manager para administrar y ampliar la nube privada. En la administración de identidades y acceso se usa el rol CloudAdmin para vCenter y derechos de administrador restringidos para NSX-T Manager.


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
