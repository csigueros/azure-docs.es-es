---
title: Uso de una imagen administrada para crear un grupo de imágenes personalizadas
description: Cree un grupo de imágenes personalizadas de Batch a partir de una imagen administrada para aprovisionar nodos de ejecución con el software y los datos para su aplicación.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 9e70d3391ca9c8d4854c4cd587ffb6e840ef4254
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476896"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Uso de una imagen administrada para crear un grupo de imágenes personalizadas

Para crear un grupo de imágenes personalizadas para las máquinas virtuales (VM) del grupo de Batch, puede usar una imagen administrada para crear una [imagen de Azure Compute Gallery](batch-sig-images.md). También se admite el uso de una imagen administrada, pero solo para las versiones de API hasta el 1 de agosto de 2019 inclusive.

> [!IMPORTANT]
> En la mayoría de los casos, debe crear imágenes personalizadas mediante Azure Compute Gallery. Mediante el uso de Azure Compute Gallery, puede aprovisionar grupos más rápido, escalar grandes cantidades de máquinas virtuales y mejorar la confiabilidad al aprovisionar máquinas virtuales. Para más información, consulte [Uso de Azure Compute Gallery para crear un grupo personalizado](batch-sig-images.md).

En este tema se explica cómo crear un grupo de imágenes personalizadas a partir de una sola imagen administrada.

## <a name="prerequisites"></a>Requisitos previos

- **Un recurso de imagen administrada**. Para crear un grupo de máquinas virtuales con una imagen personalizada, tiene que tener o crear un recurso de imagen administrada en la misma suscripción y región de Azure que la cuenta de Batch. La imagen debe crearse desde instantáneas del disco del sistema operativo de la máquina virtual y, opcionalmente, de sus discos de datos conectados.
  - Use una imagen personalizada única para cada grupo que cree.
  - Para crear un grupo con la imagen mediante las API de Batch, especifique el **identificador de recurso** de la imagen, cuyo formato es `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`.
  - El recurso de imagen administrada debe existir durante la vigencia del grupo para permitir su escalado vertical y puede quitarse después de eliminar el grupo.

- **Autenticación de Azure Active Directory (Azure AD)** . La API de cliente de Batch debe utilizar la autenticación de Azure AD. La compatibilidad de Azure Batch con Azure AD se documenta en [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md).

## <a name="prepare-a-managed-image"></a>Preparación de una imagen administrada

En Azure, puede preparar una imagen administrada a partir de:

- Instantáneas del sistema operativo y los discos de datos de una máquina virtual de Azure
- Una máquina virtual de Azure generalizada con discos administrados
- Un disco duro virtual local generalizado cargado en la nube

Para escalar grupos de Batch de forma confiable con una imagen administrada se recomienda crear la imagen administrada *solo* con el primer método: instantáneas de los discos de la máquina virtual. Consulte los siguientes pasos para preparar la máquina virtual, tomar una instantánea y crear una imagen administrada a partir de ella.

### <a name="prepare-a-vm"></a>Preparación de la máquina virtual

Si va a crear una máquina virtual para la imagen, use una imagen propia de Azure Marketplace admitida por Batch como imagen base para la imagen administrada. Solo pueden usarse imágenes propias como imagen base. Para ver una lista completa de las referencias de imagen de Azure Marketplace compatibles con Azure Batch, consulte la operación [List node agent SKUs](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> No se puede usar una imagen de terceros que tenga licencias adicionales y términos de compra como imagen base. Para más información acerca de estas imágenes de Azure Marketplace, consulte las instrucciones para las máquinas virtuales [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties).

- Asegúrese de que la máquina virtual se crea con un disco administrado. Se trata de la configuración de almacenamiento predeterminada cuando se crea una máquina virtual.
- No instale extensiones de Azure, como la extensión de script personalizado, en la máquina virtual. Si la imagen contiene una extensión preinstalada, Azure podría experimentar problemas al implementar el grupo de Batch.
- Cuando use discos de datos conectados, debe montar y dar formato a los discos desde una máquina virtual para usarlos.
- Asegúrese de que la imagen del sistema operativo base que proporcione usa la unidad temporal predeterminada. El agente de nodo de Batch actualmente espera la unidad temporal predeterminada.
- Asegúrese de que el disco del sistema operativo no esté cifrado.
- Una vez que la máquina virtual está en ejecución, conéctese a ella a través de RDP (para Windows) o SSH (para Linux). Instale el software necesario o copie los datos deseados.  

### <a name="create-a-vm-snapshot"></a>Creación de una instantánea de máquina virtual

Una instantánea es una copia completa de solo lectura de un disco duro virtual. Para crear una instantánea de un sistema operativo de máquinas virtuales o discos de datos, puede usar las herramientas de Azure Portal o la línea de comandos. En cuanto a pasos y opciones de creación de una instantánea, consulte la guía para [máquinas virtuales](../virtual-machines/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creación de una imagen a partir de una o varias instantáneas

Para crear una imagen administrada a partir de una instantánea, use las herramientas de la línea de comandos de Azure, como el comando [az image create](/cli/azure/image). Puede crear una imagen mediante la especificación de una instantánea del disco del sistema operativo y, opcionalmente, una o varias instantáneas de disco de datos.

## <a name="create-a-pool-from-a-managed-image"></a>Creación de un grupo a partir de una imagen administrada

Una vez que haya encontrado el identificador de recurso de la imagen administrada, cree un grupo de imágenes personalizado a partir de esa imagen. En los pasos siguientes se muestra cómo crear un grupo de imágenes personalizadas mediante el servicio Batch o la administración de Batch.

> [!NOTE]
> Asegúrese de que la identidad que usa para la autenticación de Azure AD tenga permisos en el recurso de imagen. Consulte [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md).
>
> El recurso para la imagen administrada debe existir durante la vigencia del grupo. Si se elimina el recurso subyacente, el grupo no se puede escalar.

### <a name="batch-service-net-sdk"></a>SDK de .NET del servicio Batch

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
```

### <a name="batch-management-rest-api"></a>API de REST de administración de Batch

URI DE LA API REST

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Cuerpo de la solicitud

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Consideraciones sobre grupos grandes

Si va a crear un grupo con cientos de máquinas virtuales o más con una imagen personalizada, es importante seguir las instrucciones anteriores para usar una imagen creada a partir de una instantánea de máquina virtual.

Además, tenga en cuenta las siguientes consideraciones:

- **Límites de tamaño**: Batch limita el tamaño de grupo a 2500 nodos de proceso dedicados o 1000 nodos de prioridad baja con las imágenes personalizadas.

  Si usa la misma imagen (o varias imágenes basadas en la misma instantánea subyacente) para crear varios grupos, el número total de nodos de proceso de los grupos no puede superar los límites anteriores. No se recomienda usar una imagen o su instantánea subyacente para más de un grupo.

  Si configura el grupo con [grupos NAT de entrada](pool-endpoint-configuration.md) es posible que los límites se reduzcan.

- **Tiempo de espera del cambio de tamaño**: si el grupo contiene un número fijo de nodos (no se escala automáticamente), aumente la propiedad resizeTimeout del grupo a un valor de 20 a 30 minutos. Si el grupo no alcanza su tamaño de destino en el tiempo de espera, realice otra [operación de cambio de tamaño](/rest/api/batchservice/pool/resize).

  Si tiene previsto un grupo de más de 300 nodos de proceso, es posible que necesite cambiar el tamaño del grupo varias veces para alcanzar el tamaño de destino.
  
Con [Azure Compute Gallery](batch-sig-images.md), puede crear grupos más grandes con las imágenes personalizadas junto con más réplicas de imágenes compartidas. Mediante el uso de imágenes de Shared Image, el tiempo que tarda el grupo en alcanzar el estado estable es hasta un 25 % más rápido y la latencia de inactividad de la VM es hasta un 30 % más breve.

## <a name="considerations-for-using-packer"></a>Consideraciones a la hora de usar Packer

Solo se puede crear un recurso de imagen administrada directamente con Packer mediante cuentas de Batch en modo de suscripción de usuario. Para las cuentas de modo de servicio de Batch, deberá crear primero un disco duro virtual y, después, importar el disco duro virtual a un recurso de imagen administrada. Según el modo de asignación de grupos (suscripción de usuario o servicio de Batch), los pasos para crear un recurso de imagen administrada variarán.

Asegúrese de que el recurso utilizado para crear la imagen administrada exista durante la vigencia de los grupos que hacen referencia a la imagen personalizada. Si no lo hace, pueden ocurrir errores de asignación de grupo o errores de cambio de tamaño.

Si se quita la imagen o el recurso subyacente, puede obtener un error similar a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Si recibe este error, asegúrese de que no se haya quitado el recurso subyacente.

Para obtener más información sobre el uso de Packer para crear una máquina virtual, consulte [Creación de una imagen de Linux con Packer](../virtual-machines/linux/build-image-with-packer.md) o [Creación de una imagen de Windows con Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a usar [Azure Compute Gallery](batch-sig-images.md) para crear un grupo personalizado.
- Para obtener información general detallada sobre Batch, consulte [Flujo de trabajo y recursos del servicio Batch](batch-service-workflow-features.md).
