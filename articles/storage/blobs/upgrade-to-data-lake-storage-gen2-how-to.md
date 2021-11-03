---
title: Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Muestra cómo usar plantillas de Resource Manager para actualizar de Azure Blob Storage a Data Lake Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: ecf0988069bf023225354b4ad864537737999094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008715"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2

En este artículo podrá desbloquear funcionalidades como la seguridad de nivel de archivo y directorio y operaciones más rápidas. Estas funcionalidades se usan ampliamente en las cargas de trabajo de análisis de macrodatos y se conocen colectivamente como Azure Data Lake Storage Gen2. 

Para más información sobre estas funcionalidades y para evaluar el impacto de esta actualización en cargas de trabajo, aplicaciones, costos, integraciones de servicios, herramientas, características y documentación, vea [Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2.md).

> [!IMPORTANT]
> Tenga en cuenta que una actualización es unidireccional. No hay manera de revertir la cuenta una vez que haya realizado la actualización. Se recomienda validar la actualización en un entorno que no sea de producción.

## <a name="review-feature-support"></a>Revisión de la compatibilidad con las características

Es posible que su cuenta esté configurada para usar características que aún no se admiten en las cuentas habilitadas para Data Lake Storage Gen2. Si la cuenta usa una característica que aún no se admite, la actualización no superará el paso de validación. 

Revise el artículo [Compatibilidad con la característica Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md) para identificar las características no admitidas. Si usa cualquiera de esas características no admitidas en su cuenta, asegúrese de deshabilitarlas antes de comenzar la actualización.

## <a name="perform-the-upgrade"></a>Realización de la actualización

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) para empezar a trabajar.

2. Busque la cuenta de almacenamiento y muestre la información general de la cuenta.

3. Seleccione **Migración de Data Lake Gen2**.

   Aparece la página de configuración **Actualizar a una cuenta de almacenamiento con las funcionalidades de Azure Data Lake Gen2**.

   > [!div class="mx-imgBorder"]
   > ![Página Configuración](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. Expanda la sección **Paso 1: Revisar los cambios de la cuenta antes de actualizar** y haga clic en **Revisar y aceptar los cambios**.

5. En la página **Revisar los cambios de la cuenta**, seleccione la casilla y después haga clic en **Agree to changes** (Aceptar los cambios).

6. Expanda la sección **Paso 2: Validar la cuenta antes de actualizar** y, a continuación, haga clic en **Start validation** (Iniciar validación).

   Si se produce un error en la validación, aparece un error en la página. En algunos casos, aparece un vínculo **Ver errores**. Si aparece ese vínculo, selecciónelo. 

   > [!div class="mx-imgBorder"]
   > ![Vínculo Ver errores](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   Después, en el menú contextual del archivo **error.json**, seleccione **Descargar**.

   > [!div class="mx-imgBorder"]
   > ![Página json del error](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   Abra el archivo descargado para determinar por qué la cuenta no ha superado el paso de validación. 

   El siguiente código JSON indica que una característica incompatible está habilitada en la cuenta. En este caso, deshabilitaría la característica y, a continuación, iniciaría de nuevo el proceso de validación.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. Una vez validada la cuenta correctamente, expanda la sección **Paso 3: Actualizar cuenta** y, después, haga clic en **Iniciar actualización**.

   > [!IMPORTANT]
   > Las operaciones de escritura estarán deshabilitadas mientras se actualiza la cuenta. Las operaciones de lectura no están deshabilitadas, pero se recomienda encarecidamente suspender las operaciones de lectura, ya que podrían desestabilizar el proceso de actualización.

   Cuando la migración se haya completado correctamente, aparecerá un mensaje similar al siguiente. 

   > [!div class="mx-imgBorder"]
   > ![Página de migración completada](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra una ventana de comando de Windows PowerShell.

2. Asegúrese de que tiene el módulo más reciente de Azure PowerShell. Consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps).

3. Inicie sesión en la suscripción a Azure con el comando `Connect-AzAccount` y siga las instrucciones de la pantalla.

   ```powershell
   Connect-AzAccount
   ```

4. Si su identidad está asociada a más de una suscripción, establezca la suscripción activa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

6. Valide la cuenta de almacenamiento con el siguiente comando:

   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Validation -AsJob
   ```

   - Reemplace el marcador de posición `<resource-group-name>` por el nombre del grupo de recursos.

   - Reemplace el valor de marcador de posición `<storage-account-name>` por el nombre de la cuenta de almacenamiento.

   Según el tamaño de la cuenta, este proceso puede tardar algún tiempo. Puede usar el modificador `asJob` para ejecutar el comando en un trabajo en segundo plano y así el cliente no esté bloqueado. El comando se ejecuta de forma remota, pero el trabajo existe en la máquina local o en la máquina virtual desde la que se ejecuta el comando. Los resultados se transmiten a la máquina local o a la máquina virtual.

7. Para comprobar el estado del trabajo y mostrar todas sus propiedades en una lista, canalice la variable de devolución al cmdlet `Format-List`. 

   ```powershell
   $result | Format-List -Property *
   ```

   Si la validación se realiza correctamente, la propiedad **State** se establecerá en **Completed**.

   Si la validación produce un error, la propiedad **State** se establecerá en **Failed** y la propiedad **Error** mostrará errores de validación. 

   La siguiente salida indica que una característica incompatible está habilitada en la cuenta. En este caso, deshabilitaría la característica y, a continuación, iniciaría de nuevo el proceso de validación.

   > [!div class="mx-imgBorder"]
   > ![Error de validación](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-error-powershell.png)

   En algunos casos, la propiedad **Error** proporciona una ruta de acceso a un archivo denominado **error.json**. Puede abrir el archivo para determinar por qué la cuenta no superó el paso de validación. 

   El siguiente código JSON indica que una característica incompatible está habilitada en la cuenta. En este caso, deshabilitaría la característica y, a continuación, iniciaría de nuevo el proceso de validación.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

8. Una vez que la cuenta se haya validado correctamente, ejecute el siguiente comando para iniciar la actualización.
   
   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Upgrade -AsJob -Force
   ```

   Al igual que en el ejemplo de validación anterior, en este ejemplo se usa el modificador `asJob` para ejecutar el comando en un trabajo en segundo plano. El modificador `Force` invalida los avisos para confirmar la actualización.  Si no usa el modificador `AsJob`, no tiene que usar el modificador `Force` porque solo puede responder a los avisos.

   > [!IMPORTANT]
   > Las operaciones de escritura estarán deshabilitadas mientras se actualiza la cuenta. Las operaciones de lectura no están deshabilitadas, pero se recomienda encarecidamente suspender las operaciones de lectura, ya que podrían desestabilizar el proceso de actualización.

   Para comprobar el estado del trabajo, use las mismas técnicas que se describen en los pasos anteriores. Cuando se ejecute el proceso, la propiedad **State** se establecerá en **Running**.

   Cuando la migración se haya completado correctamente, la propiedad **State** se establecerá en **Completed** y la propiedad **Error** no mostrará ningún error.
   
   > [!div class="mx-imgBorder"]
   > ![Salida de finalización correcta](./media/upgrade-to-data-lake-storage-gen2-how-to/success-message-powershell.png)


### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. En primer lugar, abra [Azure Cloud Shell](../../cloud-shell/overview.md) o, si ha [instalado](/cli/azure/install-azure-cli) la CLI de Azure localmente, abra una aplicación de consola de comandos como Windows PowerShell.

2. Use el comando siguiente para verificar que la versión de la CLI de Azure que ha instalado sea `2.29.0` o posterior.

   ```azurecli
    az --version
   ```

   Si la versión de la CLI de Azure es anterior a `2.29.0`, instale la versión más reciente. Para más información, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

2. Si su identidad está asociada a más de una suscripción, establezca la suscripción activa.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

3. Valide la cuenta de almacenamiento con el siguiente comando:

   ```azurecli
   az storage account hns-migration start --type validation -n <storage-account-name> -g <resource-group-name>
   ```

   - Reemplace el marcador de posición `<resource-group-name>` por el nombre del grupo de recursos.

   - Reemplace el valor de marcador de posición `<storage-account-name>` por el nombre de la cuenta de almacenamiento.

   Si se pasa la validación, el proceso finaliza y no aparece ningún error.
   
   En caso contrario, aparecerá un error de validación en la consola. Por ejemplo, el error `(IncompatibleValuesForAccountProperties) Values for account properties are incompatible: Versioning Enabled` indica que una característica incompatible (control de versiones) está habilitada en la cuenta. En este caso, deshabilitaría la característica y, a continuación, iniciaría de nuevo el proceso de validación.

   En algunos casos, aparece en la consola la ruta de acceso a un archivo llamado **error.json**. Puede abrir el archivo para determinar por qué la cuenta no superó el paso de validación. 

   El siguiente código JSON indica que una característica incompatible está habilitada en la cuenta. En este caso, deshabilitaría la característica y, a continuación, iniciaría de nuevo el proceso de validación.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

5. Una vez que la cuenta se haya validado correctamente, ejecute el siguiente comando para iniciar la actualización.
   
   ```azurecli
   az storage account hns-migration start --type upgrade -n storage-account-name -g <resource-group-name>
   ```

   > [!IMPORTANT]
   > Las operaciones de escritura estarán deshabilitadas mientras se actualiza la cuenta. Las operaciones de lectura no están deshabilitadas, pero se recomienda encarecidamente suspender las operaciones de lectura, ya que podrían desestabilizar el proceso de actualización.

   Si la migración se realiza correctamente, el proceso finaliza y no aparece ningún error.

---

## <a name="stop-the-upgrade"></a>Detención de la actualización

Puede detener la migración antes de que finalice. Estas son algunas cosas que suceden cuando se detiene.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para detener la actualización antes de que finalice, seleccione **Cancelar actualización** mientras la actualización está en curso.

> [!div class="mx-imgBorder"]
> ![Cancelar actualización](./media/upgrade-to-data-lake-storage-gen2-how-to/cancel-the-upgrade.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para detener la actualización antes de que finalice, use el comando `Stop-AzStorageAccountHierarchicalNamespaceUpgrade`.

```powershell
Stop-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName <resource-group-name> -Name <storage-account-name>
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para detener la actualización antes de que finalice, use el comando `az storage account hns-migration stop`.

```azurecli
az storage account hns-migration stop -n <storage-account-name> -g <resource-group-name>
```

---


## <a name="migrate-data-workloads-and-applications"></a>Migración de datos, cargas de trabajo y aplicaciones 

1. Configure los [servicios en las cargas de trabajo](./data-lake-storage-supported-azure-services.md) para apuntar al punto de conexión de **Blob service** o al punto de conexión de **Data Lake Storage**.

   > [!div class="mx-imgBorder"]
   > ![Puntos de conexión de cuenta](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Para las cargas de trabajo de Hadoop que usan el controlador de Azure Storage Blob de Windows o [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html), asegúrese de modificarlas para usar el controlador de [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html). A diferencia del controlador WASB que realiza solicitudes al punto de conexión de **Blob service**, el controlador ABFS realizará solicitudes al punto de conexión de **Data Lake Storage** de la cuenta.

2. Pruebe las aplicaciones personalizadas para asegurarse de que funcionan según lo previsto con la cuenta actualizada. 

   El [acceso multiprotocolo en Data Lake Storage](data-lake-storage-multi-protocol-access.md) permite a la mayoría de las aplicaciones seguir usando las API de blob sin modificaciones. Si tiene problemas o desea usar las API para trabajar con las operaciones de directorio y las ACL, considere la posibilidad de mover parte del código para usar las API de Data Lake Storage Gen2. Vea las guías de [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md) y [REST](/rest/api/storageservices/data-lake-storage-gen2). 

3. Pruebe todos los scripts personalizados para asegurarse de que funcionan según lo previsto con la cuenta actualizada. 

   Como es el caso de las API de blobs, es probable que muchos de los scripts funcionen sin necesidad de modificarlos. No obstante, si es necesario, puede actualizar los scripts para usar los [cmdlets de PowerShell](data-lake-storage-directory-file-acl-powershell.md) y los [comandos de la CLI de Azure](data-lake-storage-directory-file-acl-cli.md) de Data Lake Storage Gen2.
 

## <a name="see-also"></a>Consulte también

[Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)