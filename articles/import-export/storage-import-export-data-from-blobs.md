---
title: Tutorial para exportar datos de Azure Blob Storage con Azure Import/Export | Microsoft Docs
description: Aprenda a crear trabajos de exportación en Azure Portal para transferir datos de los blobs de Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: ccda14f4046efe32370b206577807dca558d9f25
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709641"
---
# <a name="tutorial-export-data-from-azure-blob-storage-with-azure-importexport"></a>Tutorial: Exportación de datos de Azure Blob Storage con Azure Import/Export

Este artículo proporciona instrucciones paso a paso sobre cómo usar el servicio Azure Import/Export para exportar de forma segura grandes cantidades de datos desde Azure Blob Storage. El servicio requiere que envíe unidades de disco vacías al centro de datos de Azure. El servicio exporta datos de la cuenta de almacenamiento a las unidades de disco y luego envía de vuelta las unidades de disco.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Requisitos previos para exportar datos de Azure Blob Storage con Azure Import/Export
> * Paso 1: Crear un trabajo de exportación
> * Paso 2: Envío de las unidades de disco
> * Paso 3: Actualización del trabajo con información de seguimiento
> * Paso 4: Recepción de los discos
> * Paso 5: Desbloqueo de los discos

## <a name="prerequisites"></a>Requisitos previos

Antes de crear un trabajo de exportación para transferir datos fuera de Azure Blob Storage, revise con cuidado y complete la siguiente lista de requisitos previos para este servicio.
Debe:

- Tener una suscripción activa de Azure que pueda usarse para el servicio Import/Export.
- Tener al menos una cuenta de Azure Storage. Consulte la lista de [las cuenta de almacenamiento y los tipos de almacenamiento admitidos para el servicio Import/Export](storage-import-export-requirements.md). Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md).
- Tener un número suficiente de discos de los [tipos admitidos](storage-import-export-requirements.md#supported-disks). Puede usar la herramienta Azure Import/Export para determinar cuántos discos se proporcionan. Para conocer los pasos, consulte [Determinación de las unidades para usar](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need).
- Tener una cuenta de FedEx o DHL. Si quiere usar alguna empresa de mensajería que no sea FedEx o DHL, póngase en contacto con el equipo de operaciones de Azure Data Box en `adbops@microsoft.com`.
  - La cuenta debe ser válida, debe tener saldo positivo y debe tener capacidades de devolución de envíos.
  - Generar un número de seguimiento del trabajo de exportación.
  - Cada trabajo debe tener un número de seguimiento independiente. No se admiten varios trabajos con el mismo número de seguimiento.
  - Si no tiene una cuenta de transportista, vaya a:
    - [Crear una cuenta de FedEx](https://www.fedex.com/en-us/create-account.html), o
    - [Crear una cuenta de DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Paso 1: Crear un trabajo de exportación

### <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estos pasos para crear un trabajo de exportación en Azure Portal.

1. Inicie sesión en <https://portal.azure.com/>.
2. Busque los **trabajos de importación y exportación**.

    ![Búsqueda de los trabajos de importación y exportación](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Seleccione **+ Nuevo**.

    ![Selección de + Nuevo para crear uno ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. En **Aspectos básicos**:

   1. Seleccione una suscripción.
   1. Seleccione un grupo de recursos o elija **Crear nuevo** y cree uno.
   1. Escriba un nombre descriptivo para el trabajo de importación. Utilice el nombre para realizar un seguimiento del progreso de los trabajos.
       * El nombre solo pueden contener letras minúsculas, números y guiones.
       * El nombre tiene que empezar por una letra y no puede contener espacios.

   1. Seleccione **Export from Azure** (Exportar desde Azure).

    ![Opciones básicas de un pedido de exportación](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Seleccione **Siguiente: Detalles del trabajo >** para continuar.

5. En **Detalles del trabajo**:

   1. Seleccione la región de Azure en la que estén los datos en este momento.
   1. Seleccione la cuenta de Azure Storage desde la que quiere exportar los datos. Use una cuenta de almacenamiento cercana a su ubicación.

      La ubicación de la entrega se rellena automáticamente según la región de la cuenta de almacenamiento seleccionada.

   1. Especifique los datos de blobs que quiere exportar desde su cuenta de almacenamiento a una o varias unidades vacías. Elija uno de los tres métodos siguientes:

      - Elija **Exportar todos** los datos de blob de la cuenta de almacenamiento.

        ![Exportar todo](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Elijas **Seleccionar contenedores y blobs** y especifique los contenedores y los blobs que se exportarán. Puede usar más de uno de los métodos de selección. Al seleccionar una opción **Agregar**, se abre un panel a la derecha donde puede agregar las cadenas de selección.

        |Opción|Descripción|
        |------|-----------|      
        |**Agregar contenedores**|Exporte todos los blobs de un contenedor.<br>Seleccione **Agregar contenedores** y escriba cada nombre de contenedor.|
        |**Agregar blobs**|Especifique cada uno de los blobs que se van a exportar.<br>Seleccione **Agregar blobs**. Luego, especifique la ruta de acceso relativa al blob, empezando con el nombre del contenedor. Utilice *$root* para especificar el contenedor raíz.<br>Tiene que proporcionar las rutas de acceso del blob en formato válido para evitar errores durante el procesamiento, tal y como se muestra en esta captura de pantalla. Para más información, consulte [Ejemplos de rutas de acceso del blob válidas](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).|
        |**Agregar prefijos**|Use un prefijo para seleccionar un conjunto de contenedores con nombres parecidos o blobs con nombres parecidos en un contenedor. El prefijo puede ser el prefijo del nombre del contenedor, el nombre del contenedor completo o el nombre del contendor completo seguido del prefijo del nombre del blob. |

        ![Exportación de blobs y contenedores seleccionados](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Elija **Exportar desde archivo de lista de blobs (formato XML)** y seleccione un archivo XML que contenga una lista de rutas de acceso y prefijos para los blobs que se van a exportar desde la cuenta de almacenamiento. Debe crear el archivo XML y almacenarlo en un contenedor de la cuenta de almacenamiento. El archivo no puede estar vacío.

      > [!IMPORTANT]
      > Si utiliza un archivo XML para seleccionar los blobs que se van a exportar, asegúrese de que el XML contenga rutas de acceso o prefijos válidos. Si el archivo no es válido o no hay ningún dato que coincida con las rutas de acceso especificadas, el pedido finaliza con una exportación de datos parcial o sin datos exportados.

       Para ver cómo agregar un archivo XML a un contenedor, consulte [Pedido de exportación mediante un archivo XML](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Exportación desde el archivo de lista de blobs](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Si el blob que se va a exportar está en uso en el transcurso de la copia de datos, el servicio Azure Import/Export tomará una instantánea del blob y la copiará.

   Seleccione **Siguiente: Envío >** para continuar.

6. En **Envío**:

    - Seleccione el transportista en la lista desplegable. Si desea usar una empresa de mensajería que no sea FedEx o DHL, elija una de las opciones de la lista desplegable. Póngase en contacto con el equipo de operaciones de Azure Data Box en `adbops@microsoft.com` con la información relacionada con el transportista que quiere usar.
    - Escriba un número válido de cuenta de transportista que haya creado con ese transportista. Microsoft usa esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de exportación.
    - Proporcione la siguiente información completa y válida: nombre de contacto, teléfono, correo electrónico, dirección postal, ciudad, código postal, estado o provincia y país o región.

        > [!TIP]
        > En lugar de especificar una dirección de correo electrónico para un solo usuario, proporcione un correo electrónico de grupo. Esto garantiza que recibirá notificaciones incluso si sale un administrador.

    Seleccione **Revisar y crear** para continuar.

7. En **Revisar y crear**:

   1. Revise los detalles del trabajo.
   1. Anote el nombre del trabajo y proporcione la dirección de envío del centro de datos de Azure para el envío de discos a Azure.

      > [!NOTE]
      > Envíe siempre los discos al centro de datos que se ha indicado en Azure Portal. Si los discos se envían al centro de datos incorrecto, no se procesará el trabajo.

   1. Revise los **Términos** del pedido relativos a la privacidad y la eliminación de los datos de origen. Si acepta los términos, active la casilla que aparece debajo. Comienza la validación del pedido.

   ![Revisión y creación del pedido de exportación](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Una vez pasada la validación, seleccione **Crear**.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Siga los pasos a continuación para crear un trabajo de exportación en Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Creación de un trabajo

1. Use el comando [az extension add](/cli/azure/extension#az_extension_add) para agregar la extensión [az import-export](/cli/azure/import-export):

    ```azurecli
    az extension add --name import-export
    ```

1. Para obtener una lista de las ubicaciones desde las que puede recibir discos, use el comando [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list):

    ```azurecli
    az import-export location list
    ```

1. Ejecute el comando [az import-export create](/cli/azure/import-export#az_import_export_create) siguiente para crear un trabajo de exportación que use su cuenta de almacenamiento existente:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > En lugar de especificar una dirección de correo electrónico para un solo usuario, proporcione un correo electrónico de grupo. Esto garantiza que recibirá notificaciones incluso si sale un administrador.

   Este trabajo exporta todos los blobs de la cuenta de almacenamiento. Puede especificar un blob para la exportación si reemplaza este valor por **--export**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Este valor de parámetro exporta el blob denominado *logo.bmp* en el contenedor raíz.

   También tiene la opción de seleccionar todos los blobs de un contenedor mediante un prefijo. Reemplace este valor por **--export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Para más información, consulte [Ejemplos de rutas de acceso del blob válidas](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).

   > [!NOTE]
   > Si el blob que se va a exportar está en uso en el transcurso de la copia de datos, el servicio Azure Import/Export tomará una instantánea del blob y la copiará.

1. Use el comando [az import-export list](/cli/azure/import-export#az_import_export_list) para ver todos los trabajos del grupo de recursos myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Para actualizar o cancelar el trabajo, ejecute el comando [az import-export update](/cli/azure/import-export#az_import_export_update):

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Siga los pasos a continuación para crear un trabajo de exportación en Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Aunque el módulo **Az.ImportExport** de PowerShell está en versión preliminar, se debe instalar por separado mediante el cmdlet `Install-Module`. Una vez que este módulo de PowerShell esté disponible con carácter general, formará parte de las futuras versiones del módulo Az de PowerShell y estará disponible de forma predeterminada en Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Creación de un trabajo

1. Para obtener una lista de las ubicaciones desde las que puede recibir discos, use el cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation):

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Ejecute el ejemplo [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) siguiente para crear un trabajo de exportación que use su cuenta de almacenamiento existente:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > En lugar de especificar una dirección de correo electrónico para un solo usuario, proporcione un correo electrónico de grupo. Esto garantiza que recibirá notificaciones incluso si sale un administrador.

   Este trabajo exporta todos los blobs de la cuenta de almacenamiento. Puede especificar un blob para la exportación si reemplaza este valor por **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Este valor de parámetro exporta el blob denominado *logo.bmp* en el contenedor raíz.

   También tiene la opción de seleccionar todos los blobs de un contenedor mediante un prefijo. Reemplace este valor por **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Para más información, consulte [Ejemplos de rutas de acceso del blob válidas](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).

   > [!NOTE]
   > Si el blob que se va a exportar está en uso en el transcurso de la copia de datos, el servicio Azure Import/Export tomará una instantánea del blob y la copiará.

1. Use el cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) para ver todos los trabajos del grupo de recursos de myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Para actualizar el trabajo o cancelarlo, ejecute el cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport):

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-2-ship-the-drives"></a>Paso 2: Envío de las unidades de disco

Si no conoce el número de unidades que necesita, consulte [Determinación del número de unidades que necesita](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need). Si conoce el número de unidades, continúe con el envío de las unidades.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Paso 3: Actualización del trabajo con información de seguimiento

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Paso 4: Recepción de los discos

Cuando el panel notifica que el trabajo está completo, se le envían los discos y el número de seguimiento para el envío está disponible en el portal.

1. Después de que haya recibido las unidades de disco con los datos exportados, tiene que obtener las claves de BitLocker para desbloquear las unidades de disco. Vaya al trabajo de exportación en Azure Portal. Haga clic en la pestaña **Import/Export**.
2. Seleccione el trabajo de exportación de la lista y haga clic en él. Vaya a **Cifrado** y copie las claves.

   ![Visualización de claves de BitLocker de un trabajo de exportación](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Use las claves de BitLocker para desbloquear los discos.

La exportación se ha terminado.

## <a name="step-5-unlock-the-disks"></a>Paso 5: Desbloqueo de los discos

Escriba el siguiente comando para desbloquear la unidad:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Este es un ejemplo de la entrada.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

En este momento, puede eliminar el trabajo o dejarlo. Los trabajos se eliminan automáticamente después de 90 días.

## <a name="next-steps"></a>Pasos siguientes

* [Visualización del estado del trabajo y de la unidad de disco](storage-import-export-view-drive-status.md)
* [Revisión de los registros de copia de Import/Export](storage-import-export-tool-reviewing-job-status-v1.md)
