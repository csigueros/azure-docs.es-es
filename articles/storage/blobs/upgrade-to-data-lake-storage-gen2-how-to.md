---
title: Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Muestra cómo usar plantillas de Resource Manager para actualizar de Azure Blob Storage a Data Lake Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: b1f612ee189d47529033fdd9e6c7e7df0f523df9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459029"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2

En este artículo podrá desbloquear funcionalidades como la seguridad de nivel de archivo y directorio y operaciones más rápidas. Estas funcionalidades se usan ampliamente en las cargas de trabajo de análisis de macrodatos y se conocen colectivamente como Azure Data Lake Storage Gen2. 

Para más información sobre estas funcionalidades y para evaluar el impacto de esta actualización en cargas de trabajo, aplicaciones, costos, integraciones de servicios, herramientas, características y documentación, vea [Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2.md).

> [!IMPORTANT]
> Tenga en cuenta que una actualización es unidireccional. No hay manera de revertir la cuenta una vez que haya realizado la actualización. Se recomienda validar la actualización en un entorno que no sea de producción.

## <a name="perform-the-upgrade"></a>Realización de la actualización

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) para empezar a trabajar.

2. Busque la cuenta de almacenamiento y muestre la información general de la cuenta.

3. Seleccione **Migración de Data Lake Gen2**.

   Aparece la página de configuración **Actualizar a una cuenta de almacenamiento con las funcionalidades de Azure Data Lake Gen2**.

   > [!div class="mx-imgBorder"]
   > ![Página Configuración](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. Expanda la sección **Paso 1: Revisar los cambios de la cuenta antes de actualizar** y haga clic en **Revisar y aceptar los cambios**.

5. En la página **Revisar los cambios de la cuenta**, seleccione la casilla y después haga clic en **Agree to changes** (Aceptar los cambios).

6. Expanda la sección **Paso 2: Validar la cuenta antes de actualizar** y, a continuación, haga clic en **Start validation** (Iniciar validación).

   Si se produce un error en la validación, seleccione el vínculo **Ver errores**.

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

## <a name="migrate-data-workloads-and-applications"></a>Migración de datos, cargas de trabajo y aplicaciones 

1. Configure los [servicios en las cargas de trabajo](data-lake-storage-integrate-with-azure-services.md) para apuntar al punto de conexión de **Blob service** o al punto de conexión de **Data Lake Storage**.

   > [!div class="mx-imgBorder"]
   > ![Puntos de conexión de cuenta](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Para las cargas de trabajo de Hadoop que usan el controlador de Azure Storage Blob de Windows o [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html), asegúrese de modificarlas para usar el controlador de [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html). A diferencia del controlador WASB que realiza solicitudes al punto de conexión de **Blob service**, el controlador ABFS realizará solicitudes al punto de conexión de **Data Lake Storage** de la cuenta.

2. Pruebe las aplicaciones personalizadas para asegurarse de que funcionan según lo previsto con la cuenta actualizada. 

   El [acceso multiprotocolo en Data Lake Storage](data-lake-storage-multi-protocol-access.md) permite a la mayoría de las aplicaciones seguir usando las API de blob sin modificaciones. Si tiene problemas o desea usar las API para trabajar con las operaciones de directorio y las ACL, considere la posibilidad de mover parte del código para usar las API de Data Lake Storage Gen2. Vea las guías de [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md) y [REST](/rest/api/storageservices/data-lake-storage-gen2). 

3. Pruebe todos los scripts personalizados para asegurarse de que funcionan según lo previsto con la cuenta actualizada. 

   Como es el caso de las API de blobs, es probable que muchos de los scripts funcionen sin necesidad de modificarlos. No obstante, si es necesario, puede actualizar los scripts para usar los [cmdlets de PowerShell](data-lake-storage-directory-file-acl-powershell.md) y los [comandos de la CLI de Azure](data-lake-storage-directory-file-acl-cli.md) de Data Lake Storage Gen2.
 

## <a name="see-also"></a>Vea también

[Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)