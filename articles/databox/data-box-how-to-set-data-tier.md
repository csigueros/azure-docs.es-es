---
title: Envío de datos al nivel de blobs de acceso frecuente, acceso esporádico y de archivo a través de Azure Data Box/Azure Data Box Heavy
description: Describe cómo usar Azure Data Box o Azure Data Box Heavy para enviar datos a un nivel de almacenamiento de blobs en bloques apropiado, como de acceso frecuente, poco frecuente o de archivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 82ac4e9c4a2f573295790d5d7fa3b02c2e690bbc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819851"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Uso de Azure Data Box o Azure Data Box Heavy para enviar datos al nivel del blob de Azure Storage adecuado

Azure Data Box mueve grandes cantidades de datos a Azure mediante el envío de un dispositivo de almacenamiento propio. Debe llenar el dispositivo de datos y devolverlo. Los datos de Data Box se cargan en un nivel predeterminado asociado con la cuenta de almacenamiento. A continuación, puede mover los datos a otro nivel de almacenamiento.

En este artículo se describe cómo se pueden mover los datos que haya cargado Data Box a un nivel de blob de acceso frecuente, poco frecuente o de archivo. La información de este artículo se aplica tanto a Azure Data Box como a Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Selección del nivel de almacenamiento correcto para sus datos

Azure Storage ofrece tres niveles diferentes para almacenar datos de la manera más rentable: frecuente, poco frecuente o archivo. El nivel de almacenamiento de acceso frecuente está optimizada para almacenar datos que se consultan con frecuencia. El almacenamiento frecuente tiene mayores costos que el esporádico o el de archivo, pero menores costos de acceso.

El nivel de almacenamiento de acceso esporádico es para datos que se consultan con poca frecuencia y que deben almacenarse durante un mínimo de 30 días. El costo de almacenamiento del nivel poco frecuente es inferior al del nivel de acceso frecuente, pero los gastos de acceso a los datos son superiores en comparación con el nivel frecuente.

El nivel de almacenamiento de archivo de Azure se realiza sin conexión y ofrece los menores costos de almacenamiento, pero los mayores costos de acceso. Este nivel está pensado para datos que permanecen en el almacenamiento de archivo durante un período mínimo de 180 días. Para obtener detalles de cada uno de estos niveles y el modelo de precios, consulte [Comparación de los niveles de almacenamiento](../storage/blobs/storage-blob-storage-tiers.md).

Los datos de Data Box o de Data Box Heavy se cargan en un nivel de almacenamiento asociado a la cuenta de almacenamiento. Al crear una cuenta de almacenamiento, puede establecer el nivel de acceso como frecuente o poco frecuente. Según el patrón de acceso de la carga de trabajo y el costo, puede cambiar estos datos del nivel predeterminado a otro nivel de almacenamiento.

Solo puede disponer los datos de almacenamiento de objetos en niveles de acceso en cuentas de Blob Storage o de uso general v2 (GPv2). Las cuentas de General Purpose v1 (GPv1) no admiten niveles. Para elegir el nivel de almacenamiento correcto para sus datos, revise las consideraciones que se detallan en [Azure Blob Storage: Niveles de almacenamiento de acceso frecuente, acceso esporádico y acceso de archivo](../storage/blobs/storage-blob-storage-tiers.md).

## <a name="set-a-default-blob-tier"></a>Establecimiento de un nivel de blob predeterminado

El nivel de blob predeterminado se especifica cuando se crea la cuenta de almacenamiento en Azure Portal. Una vez que se selecciona un tipo de almacenamiento como Blob Storage o GPv2, se puede especificar el atributo de nivel de acceso. De forma predeterminada, se selecciona el nivel de acceso frecuente.

Los niveles no se pueden especificar si intenta crear una nueva cuenta al realizar un pedido de Data Box o Data Box Heavy. Una vez creada la cuenta, puede modificar la cuenta en el portal para establecer el nivel de acceso predeterminado.

También puede crear primero una cuenta de almacenamiento con el atributo de nivel de acceso especificado. Al crear el pedido de Data Box o Data Box Heavy, seleccione la cuenta de almacenamiento existente. Para más información sobre cómo establecer el nivel de blob predeterminado durante la creación de la cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento en Azure Portal](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Transferencia de datos a un nivel distinto al predeterminado

Una vez que se carguen los datos del dispositivo de Data Box en el nivel predeterminado, podrá mover los datos a un nivel no predeterminado. Existen dos maneras de mover los datos a un nivel distinto al predeterminado.

- **Administración del ciclo de vida de Azure Blob Storage**: puede usar un enfoque basado en directivas para asignar automáticamente los datos a un nivel o para que expiren al final de su ciclo de vida. Para más información, consulte [Administración del ciclo de vida de Azure Blob Storage](../storage/blobs/lifecycle-management-overview.md).
- **Scripts**: puede usar un enfoque con scripts mediante Azure PowerShell para habilitar niveles de blob. Puede llamar a la operación `SetBlobTier` para establecer el nivel en el blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Uso de Azure PowerShell para establecer el nivel de blob

Los pasos siguientes describen cómo establecer el nivel de blob de archivo mediante un script de Azure PowerShell.

1. Abra una sesión de Windows PowerShell con privilegios elevados. Asegúrese de utilizar PowerShell 5.0 o superior. Escriba:

   `$PSVersionTable.PSVersion`     

2. Inicie sesión en Azure PowerShell. 

   `Login-AzAccount`  

3. Defina las variables para la cuenta de almacenamiento, la clave de acceso, el contenedor y el contexto de almacenamiento.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Obtenga todos los blobs del contenedor.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Establezca el nivel de todos los blobs del contenedor en el nivel de archivo.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    A continuación se muestra una salida de ejemplo:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Si desea que los datos se archiven al ingerirse, establezca el nivel de cuenta predeterminado en frecuente. Si el nivel predeterminado es esporádico, habrá una penalización de eliminación temprana de 30 días si los datos se cambian al nivel de archivo inmediatamente.

## <a name="next-steps"></a>Pasos siguientes

-  Obtenga información acerca de cómo tratar [escenarios de niveles de datos comunes con reglas de directivas de ciclo de vida](../storage/blobs/lifecycle-management-overview.md#examples-of-lifecycle-policies).