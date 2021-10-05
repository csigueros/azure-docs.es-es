---
title: Inventario de blobs de Azure Storage
description: El inventario de Azure Storage es una herramienta que ayuda a obtener información general de todos los datos de blobs dentro de una cuenta de almacenamiento.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 9cf53cdf35435030b9aa16b8336d80d887e6efbd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584220"
---
# <a name="azure-storage-blob-inventory"></a>Inventario de blobs de Azure Storage

La característica de inventario de blobs de Azure Storage proporciona información general de sus contenedores, blobs, instantáneas y versiones de blob dentro de una cuenta de almacenamiento. Use el informe de inventario para conocer diversos atributos de blobs y contenedores, como el tamaño total de los datos, la antigüedad, el estado de cifrado, la directiva de inmutabilidad y la suspensión legal, entre otros. El informe proporciona información general de los datos para los requisitos empresariales y de cumplimiento.

## <a name="inventory-features"></a>Características de inventario

En la siguiente lista se describen las características y funcionalidades disponibles en la versión actual del inventario de blobs de Azure Storage.

- **Informes de inventario para blobs y contenedores**

  Puede generar informes de inventario para blobs y contenedores. Un informe para blobs puede contener blobs base, instantáneas, versiones de blob y sus propiedades asociadas, como la hora de creación o la hora de la última modificación. En un informe para contenedores se describen los contenedores y sus propiedades asociadas, como el estado de la directiva de inmutabilidad o el estado de suspensión legal.

- **Esquema personalizado**

  Puede elegir qué campos aparecen en los informes. Elija entre una lista de campos admitidos. La lista aparece más adelante en este artículo.

- **Formato de salida CSV y Apache Parquet**

  Puede generar un informe de inventario con formato de salida CSV o Apache Parquet.

- **Archivo de manifiesto y evento de Azure Event Grid por informe de inventario**

  Por cada informe de inventario se generan un archivo de manifiesto y un evento de Azure Event Grid. Estos se describen más adelante en este artículo.

## <a name="enabling-inventory-reports"></a>Habilitación de los informes de inventario

Para habilitar los informes de inventario de blobs, agregue una directiva con una o más reglas a su cuenta de almacenamiento. Para obtener instrucciones, consulte [Habilitación de los informes de inventario de blobs de Azure Storage](blob-inventory-how-to.md).

## <a name="upgrading-an-inventory-policy"></a>Actualización de una directiva de inventario

Si ya es usuario de inventarios de blobs de Azure Storage y ha configurado el inventario antes de junio de 2021, puede empezar a usar las nuevas características cargando la directiva y, luego, guardándola de nuevo después de realizar cambios. Al volver a cargar la directiva, los nuevos campos de esta se rellenarán con los valores predeterminados. Si lo desea, puede cambiar estos valores. Además, estarán disponibles las dos características siguientes:

- Ahora se admite un contenedor de destino para todas las reglas, en lugar de ser solo compatible con la directiva.

- Ahora se generan un archivo de manifiesto y un evento de Azure Event Grid por regla, en lugar de por directiva.

## <a name="inventory-policy"></a>Directiva de inventario

Una directiva de inventario es una colección de reglas en un documento JSON.

```json
{
  "enabled": true,
  "rules": [
  {
    "enabled": true,
    "name": "inventoryrule1",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  },
  {
    "enabled": true,
    "name": "inventoryrule2",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  }]
}
```

Para ver el documento JSON para una directiva de inventario, seleccione la pestaña **Vista Código** en la sección **Blob inventory** (Inventario de blobs) de Azure Portal.

| Nombre de parámetro | Tipo de parámetro | Notas | ¿Necesario? |
|--|--|--|--|
| enabled | boolean | Se usa para deshabilitar toda la directiva. Cuando se establece en **true**, el campo habilitado de nivel de regla invalida este parámetro. Cuando se deshabilita, se deshabilitará el inventario para todas las reglas. | Sí |
| reglas | Matriz de objetos de regla | Se requiere al menos una regla en una directiva. Se admiten hasta 100 reglas por directiva. | Sí |

## <a name="inventory-rules"></a>Reglas de inventario

Una regla captura las condiciones de filtrado y los parámetros de salida para generar un informe de inventario. Cada regla crea un informe de inventario. Las reglas pueden tener prefijos superpuestos. Un blob puede aparecer en más de un inventario en función de las definiciones de la regla.

Cada regla de la directiva tiene varios parámetros:

| Nombre de parámetro | Tipo de parámetro | Notas | ¿Necesario? |
|--|--|--|--|
| name | string | Un nombre de regla puede incluir hasta 256 caracteres alfanuméricos que distinguen mayúsculas de minúsculas. El nombre debe ser único dentro de un proyecto. | Sí |
| enabled | boolean | Marca que permite habilitar o deshabilitar una regla. El valor predeterminado es **true**. | Sí |
| definición | Definición de regla de inventario JSON | Cada definición se compone de un conjunto de filtros de regla. | Sí |
| destination | string | Contenedor de destino en el que se generarán todos los archivos de inventario. El contenedor de destino ya debe existir. |

La marca global **Blob inventory enabled** (Inventario de blobs habilitado) tiene prioridad sobre el parámetro *enabled* en una regla.

### <a name="rule-definition"></a>Definición de reglas

| Nombre de parámetro | Tipo de parámetro | Notas | Obligatorio |
|--|--|--|--|
| filters | json | Los filtros deciden si un blob o contenedor forma parte o no del inventario. | Sí |
| format | string | Determina la salida del archivo de inventario. Los valores válidos son `csv` (para el formato CSV) y `parquet` (para el formato Apache Parquet).| Sí |
| objectType | string | Indica si se trata de una regla de inventario para blobs o para contenedores. Los valores válidos son `blob` y `container`. |Sí |
| schedule | string | Programación según la cual se va a ejecutar esta regla. Los valores válidos son `daily` y `weekly`. | Sí |
| schemaFields | matriz JSON | Lista de campos de esquema que formarán parte del inventario. | Sí |

### <a name="rule-filters"></a>Filtros de reglas

Hay varios filtros disponibles para personalizar un informe de inventario de blobs:

| Nombre de filtro | Tipo de filtro | Notas | ¿Necesario? |
|--|--|--|--|
| blobTypes | Matriz de valores de enumeración predefinidos | Los valores válidos son `blockBlob` y `appendBlob` para cuentas habilitadas para el espacio de nombres jerárquico, y `blockBlob`, `appendBlob` y `pageBlob` para otras cuentas. Este campo no es aplicable al inventario en un contenedor (objectType: `container`). | Sí |
| prefixMatch | Matriz de hasta 10 cadenas de prefijos con los que debe hacer coincidencias. | Si no define *prefixMatch* o proporciona un prefijo vacío, la regla se aplica a todos los blobs de la cuenta de almacenamiento. Los prefijos deben ser prefijos de nombre de contenedor o nombres de contenedor. Por ejemplo: `container`, `container1/foo`. | No |
| includeSnapshots | boolean | Especifica si el inventario debe incluir instantáneas. El valor predeterminado es `false`. Este campo no es aplicable al inventario en un contenedor (objectType: `container`). | No |
| includeBlobVersions | boolean | Especifica si el inventario debe incluir versiones de blob. El valor predeterminado es `false`. Este campo no es aplicable al inventario en un contenedor (objectType: `container`). | No |

Para ver el documento JSON para reglas de inventario, seleccione la pestaña **Vista Código** en la sección **Blob inventory** (Inventario de blobs) de Azure Portal. Los filtros se especifican dentro de una definición de regla.

```json
{
  "destination": "inventory-destination-container",
  "enabled": true,
  "rules": [
  {
    "definition": {
      "filters": {
        "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"],
        "includeSnapshots": false,
        "includeBlobVersions": true,
      },
      "format": "csv",
      "objectType": "blob",
      "schedule": "daily",
      "schemaFields": ["Name", "Creation-Time"]
    },
    "enabled": true,
    "name": "blobinventorytest",
    "destination": "inventorydestinationContainer"
  },
  {
    "definition": {
      "filters": {
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"]
      },
      "format": "csv",
      "objectType": "container",
      "schedule": "weekly",
      "schemaFields": ["Name", "HasImmutabilityPolicy", "HasLegalHold"]
    },
    "enabled": true,
    "name": "containerinventorytest",
    "destination": "inventorydestinationContainer"
    }
  ]
}
```

### <a name="custom-schema-fields-supported-for-blob-inventory"></a>Campos de esquema personalizados admitidos para un inventario de blobs

- Name (obligatorio)
- Creation-Time
- Last-Modified
- Content-Length
- Content-MD5
- BlobType
- AccessTier
- AccessTierChangeTime
- Expiry-Time
- hdi_isfolder
- Propietario
- Grupo
- Permisos
- Acl
- Snapshot (disponible y obligatorio si opta por incluir instantáneas en el informe)
- VersionId (disponible y obligatorio si opta por incluir versiones de blob en el informe)
- IsCurrentVersion (disponible y obligatorio si opta por incluir versiones de blob en el informe)
- Metadatos
- LastAccessTime

### <a name="custom-schema-fields-supported-for-container-inventory"></a>Campos de esquema personalizados admitidos para un inventario de contenedores

- Name (obligatorio)
- Last-Modified
- LeaseStatus
- LeaseState
- LeaseDuration
- PublicAccess
- HasImmutabilityPolicy
- HasLegalHold
- Metadatos

## <a name="inventory-run"></a>Ejecución del inventario

Cada día se programa automáticamente una ejecución de inventario de blobs. Puede tardar hasta 24 horas en completarse una ejecución de inventario. Un informe de inventario se configura mediante la adición de una directiva de inventario con una o más reglas.

Las directivas de inventario se leen o escriben en su totalidad. No se admiten las actualizaciones parciales.

> [!IMPORTANT]
> Si habilita reglas de firewall para su cuenta de almacenamiento, puede que se bloqueen las solicitudes de inventario. Puede desbloquear estas solicitudes proporcionando excepciones para los servicios de confianza de Microsoft. Para más información, consulte la sección Excepciones en [Configuración de firewalls y redes virtuales](../common/storage-network-security.md#exceptions).

## <a name="inventory-completed-event"></a>Evento de inventario completado

El evento `BlobInventoryPolicyCompleted` se genera cuando se completa la ejecución del inventario con relación a una regla. Este evento también tiene lugar si se produce un error de usuario en la ejecución del inventario antes de que se inicie el proceso. Por ejemplo, el evento se desencadenará en caso de que la directiva no sea válida o si se produce un error debido a la ausencia de un contenedor de destino. En el siguiente fragmento de código JSON se muestra un evento `BlobInventoryPolicyCompleted` de ejemplo:

```json
{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "data": {
    "scheduleDateTime": "2021-05-28T03:50:27Z",
    "accountName": "testaccount",
    "ruleName": "Rule_1",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "manifestBlobUrl": "https://testaccount.blob.core.windows.net/inventory-destination-container/2021/05/26/13-25-36/Rule_1/Rule_1.csv"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-05-28T15:03:18Z"
}
```

En la siguiente tabla se describe el esquema del evento `BlobInventoryPolicyCompleted`:

|Campo|Tipo|Descripción|
|---|---|
|scheduleDateTime|string|Hora a la que se programó la directiva de inventario.|
|accountName|string|El nombre de la cuenta de almacenamiento.|
|ruleName|string|Nombre de la regla.|
|policyRunStatus|string|Estado de la ejecución del inventario. Los valores posibles son `Succeeded`, `PartiallySucceeded` y `Failed`.|
|policyRunStatusMessage|string|Mensaje de estado de la ejecución del inventario.|
|policyRunId|string|Id. de ejecución de la directiva para la ejecución del inventario.|
|manifestBlobUrl|string|Dirección URL del blob del archivo de manifiesto para la ejecución del inventario.|

## <a name="inventory-output"></a>Salida de inventario

Cada regla de ejecución genera un conjunto de archivos en el contenedor de destino de inventario especificado. La salida de inventario se genera en la siguiente ruta de acceso: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/<ruleName` donde:

- *accountName* es el nombre de su cuenta de Azure Blob Storage.
- *inventory-destination-container* es el contenedor de destino que ha especificado en la regla de inventario.
- *YYYY/MM/DD/HH-MM-SS* es el momento en el que el inventario comenzó a ejecutarse.
- *ruleName* es el nombre de la regla de inventario.

### <a name="inventory-files"></a>Archivos de inventario

Cada ejecución de inventario con relación a una regla genera los siguientes archivos:

- **Archivo de inventario:** la ejecución de un inventario de una regla genera uno o más archivos con formato CSV o Apache Parquet. Si hay un gran número de objetos coincidentes, se generan varios archivos en lugar de uno solo. Cada archivo contiene los objetos coincidentes y sus metadatos. En el caso de los archivos con formato CSV, la primera fila siempre es la fila de esquema. En la siguiente imagen se muestra un archivo CSV de inventario abierto en Microsoft Excel.

  :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Captura de pantalla de un archivo CSV de inventario abierto en Microsoft Excel":::

  > [!NOTE]
  > Los informes con formato Apache Parquet presentan fechas con el siguiente formato: `timestamp_millis [number of milliseconds since 1970-01-01 00:00:00 UTC`.

- **Archivo de suma de comprobación:** contiene la suma de comprobación MD5 del contenido del archivo manifest.json. El nombre del archivo de suma de comprobación es `<ruleName>-manifest.checksum`. La generación del archivo de suma de comprobación marca la finalización de la ejecución de una regla de inventario.

- **Archivo de manifiesto:** un archivo manifest.json contiene los detalles de los archivos de inventario generados para esa regla. El nombre del archivo es `<ruleName>-manifest.json`. Este archivo también captura la definición de la regla proporcionada por el usuario y la ruta de acceso al inventario para dicha regla. En el siguiente fragmento de código JSON se muestra el contenido de un archivo manifest.json de ejemplo:

  ```json
  {
  "destinationContainer" : "inventory-destination-container",
  "endpoint" : "https://testaccount.blob.core.windows.net",
  "files" : [
    {
      "blob" : "2021/05/26/13-25-36/Rule_1/Rule_1.csv",
      "size" : 12710092
    }
  ],
  "inventoryCompletionTime" : "2021-05-26T13:35:56Z",
  "inventoryStartTime" : "2021-05-26T13:25:36Z",
  "ruleDefinition" : {
    "filters" : {
      "blobTypes" : [ "blockBlob" ],
      "includeBlobVersions" : false,
      "includeSnapshots" : false,
      "prefixMatch" : [ "penner-test-container-100003" ]
    },
    "format" : "csv",
    "objectType" : "blob",
    "schedule" : "daily",
    "schemaFields" : [
      "Name",
      "Creation-Time",
      "BlobType",
      "Content-Length",
      "LastAccessTime",
      "Last-Modified",
      "Metadata",
      "AccessTier"
    ]
  },
  "ruleName" : "Rule_1",
  "status" : "Succeeded",
  "summary" : {
    "objectCount" : 110000,
    "totalObjectSize" : 23789775
  },
  "version" : "1.0"
  }
  ```

## <a name="pricing-and-billing"></a>Precios y facturación

Los precios del inventario se basan en el número de blobs y contenedores que se analizan durante el período de facturación. Por ejemplo, imagine que una cuenta contiene un millón de blobs y que se establece que el inventario de blobs se ejecute una vez por semana. Después de cuatro semanas, se habrán analizado cuatro millones de entradas de blob.

La facturación del inventario de blobs comienza el 1 de octubre de 2021. Los precios regionales se publicarán en ese momento. El precio base de referencia sin ajuste regional es de aproximadamente 0,0025 USD por millón de entradas examinadas para Blob Storage y 0,0035 USD si está habilitado Data Lake Storage Gen2. Una vez creados los archivos de inventario, se incurrirá en cargos estándar adicionales de operaciones y almacenamiento de datos por almacenar, leer y escribir los archivos generados por el inventario en la cuenta.

Una vez completado un informe de inventario, se incurrirá en cargos estándar adicionales de operaciones y almacenamiento de datos por almacenar, leer y escribir el informe de inventario en la cuenta de almacenamiento.

Si una regla contiene un prefijo que se superpone con otro de cualquier otra regla, el mismo blob puede aparecer en más de un informe de inventario. En este caso, se le facturará por ambas instancias. Por ejemplo, imagine que el elemento `prefixMatch` de una regla se establece `["inventory-blob-1", "inventory-blob-2"]` y que el elemento `prefixMatch` de otra regla se establece en `["inventory-blob-10", "inventory-blob-20"]`. En este caso, aparecerá un objeto de nombre `inventory-blob-200` en ambos informes de inventario.

Las instantáneas y versiones de un blob también se tienen en cuenta en la facturación aunque haya establecido los filtros `includeSnapshots` y `includeVersions` en `false`. Dichos valores de filtro no afectan a la facturación. Solo se pueden usar para filtrar lo que aparece en el informe.

Para obtener más información sobre los precios de los inventarios de blobs de Azure Storage, consulte la página [Precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades.

| Tipo de cuenta de almacenamiento | Blob Storage (compatibilidad predeterminada) | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup>              | ![Sí](../media/icons/yes-icon.png) <sup>2</sup> |
| Blobs en bloques Premium | ![Sí](../media/icons/yes-icon.png)| ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Tanto Data Lake Storage Gen2 como el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

<sup>2</sup> La característica se admite en el nivel de versión preliminar.

## <a name="known-issues"></a>Problemas conocidos

En esta sección se describen las limitaciones y los problemas conocidos de la característica de inventario de blobs de Azure Storage.

### <a name="inventory-job-fails-to-complete-for-hierarchical-namespace-enabled-accounts"></a>Error del trabajo de inventario en el caso de cuentas con un espacio de nombres jerárquico habilitado

Es posible que el trabajo de inventario no pueda completarse en un plazo de 24 horas en el caso de cuentas con cientos de millones de blobs y un espacio de nombres jerárquico habilitado. Si esto ocurre, no se creará ningún archivo de inventario.

### <a name="inventory-job-cannot-write-inventory-reports"></a>El trabajo de inventario no puede escribir informes de inventario

Una directiva de replicación de objetos puede impedir que un trabajo de inventario escriba informes de inventario en el contenedor de destino. Algunos otros escenarios pueden archivar los informes o provocar que sean inmutables cuando se completen parcialmente. Estas situaciones pueden provocar un error del trabajo de inventario.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de los informes de inventario de blobs de Azure Storage](blob-inventory-how-to.md)
- [Cálculo del recuento y el tamaño total de los blobs por contenedor](calculate-blob-count-size.md)
- [Administración del ciclo de vida de Azure Blob Storage](./lifecycle-management-overview.md)
