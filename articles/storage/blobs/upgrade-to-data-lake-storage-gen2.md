---
title: Actualización de Azure Blob Storage a Azure Data Lake Storage Gen2 | Microsoft Docs
description: La descripción va aquí.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: 7f5c2397e2b8d6e19e912dd46eec39f432e27434
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459039"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2

En este artículo podrá desbloquear funcionalidades como la seguridad de nivel de archivo y directorio y operaciones más rápidas. Estas funcionalidades se usan ampliamente en las cargas de trabajo de análisis de macrodatos y se conocen colectivamente como Azure Data Lake Storage Gen2. Las funcionalidades más populares incluyen:

- Mayor rendimiento, operaciones de entrada y salida por segundo (IOPS) y límites de capacidad de almacenamiento.

- Operaciones más rápidas (como las operaciones de cambio de nombre), ya que puede operar en URI de nodo individuales.
 
- Motor de consultas eficaz que transfiere solo los datos necesarios para realizar una operación determinada.

- Seguridad en el nivel de contenedor, directorio y archivo.

Para obtener más información sobre estas funcionalidades, consulte [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

Este artículo le ayuda a evaluar el impacto en las cargas de trabajo, las aplicaciones, los costos, las integraciones de servicios, las herramientas, las características y la documentación. Asegúrese de revisar estos impactos cuidadosamente. Cuando esté listo para actualizar una cuenta, consulte esta guía paso a paso: [Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

> [!IMPORTANT]
> Tenga en cuenta que una actualización es unidireccional. No hay manera de revertir la cuenta una vez que haya realizado la actualización. Se recomienda validar la actualización en un entorno que no sea de producción.

## <a name="impact-on-availability"></a>Impacto en la disponibilidad

Asegúrese de planear algún tiempo de inactividad en la cuenta mientras se completa el proceso de actualización. Las operaciones de escritura estarán deshabilitadas mientras se actualiza la cuenta. Las operaciones de lectura no están deshabilitadas, pero se recomienda encarecidamente suspender las operaciones de lectura, ya que estas podrían desestabilizar el proceso de actualización.

## <a name="impact-on-workloads-and-applications"></a>Impacto en las cargas de trabajo y aplicaciones

Las API de blobs funcionan con cuentas que tienen un espacio de nombres jerárquico, por lo que la mayoría de las aplicaciones que interactúan con su cuenta mediante estas API siguen funcionando sin modificaciones.

Para obtener una lista completa de los problemas y las soluciones alternativas, consulte [Problemas conocidos con las API de Blob Storage](data-lake-storage-known-issues.md#blob-storage-apis).

Las cargas de trabajo de Hadoop que usan el controlador de Azure Storage Blob de Windows o [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) deben modificarse para usar el controlador de [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html). A diferencia del controlador WASB que realiza solicitudes al punto de conexión de **Blob service**, el controlador ABFS realizará solicitudes al punto de conexión de **Data Lake Storage** de la cuenta.

### <a name="data-lake-storage-endpoint"></a>Punto de conexión de Data Lake Storage

La cuenta actualizada tendrá un punto de conexión de almacenamiento de Data Lake. Puede encontrar la dirección URL de este punto de conexión en Azure Portal si abre la página **Propiedades** de su cuenta.

> [!div class="mx-imgBorder"]
> ![Uso general de la categoría v2](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

No tiene que modificar las aplicaciones y cargas de trabajo existentes para usar ese punto de conexión. El [acceso multiprotocolo en Data Lake Storage](data-lake-storage-multi-protocol-access.md) le permite usar el punto de conexión de Blob service o el de Data Lake Storage para interactuar con los datos. 

Los servicios y herramientas de Azure (como AzCopy) pueden usar el punto de conexión de Data Lake Storage para interactuar con los datos de la cuenta de almacenamiento. Asimismo, deberá usar este nuevo punto de conexión para las operaciones que realice mediante los [SDK de Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md), los [comandos de PowerShell](data-lake-storage-directory-file-acl-powershell.md) o los [comandos de la CLI de Azure](data-lake-storage-directory-file-acl-cli.md). 

### <a name="directories"></a>Directorios

Una cuenta de Blob Storage que no tiene un espacio de nombres jerárquico organiza los archivos en un paradigma plano, en lugar de un paradigma jerárquico. Asimismo, los blobs se pueden organizar en directorios virtuales para imitar una estructura de carpetas. Un directorio virtual forma parte del nombre del blob y se indica mediante el carácter delimitador. Dado que un directorio virtual forma parte del nombre del blob, no existe realmente como un objeto independiente.

La nueva cuenta tiene un espacio de nombres jerárquico. Esto significa que los directorios no son virtuales. Son objetos concretos e independientes sobre los que puede trabajar directamente. Un directorio puede existir sin contener ningún archivo. Al eliminar un directorio, se quitan todos los archivos de ese directorio. Por lo tanto, ya no tiene que eliminar cada blob individual antes de que el directorio desaparezca. 

### <a name="blob-metadata"></a>Metadatos de blob

Antes de realizar la migración, los metadatos del blob se asocian con el nombre del blob junto con toda la ruta de acceso virtual. Después de la migración, los metadatos solo se asocian al blob. La ruta de acceso virtual al blob se convierte en una colección de directorios. Así pues, los metadatos de un blob no se aplican a ninguno de esos directorios. 

### <a name="put-operations"></a>Operaciones PUT

Al cargar un blob y la ruta de acceso que especifique incluye un directorio que no existe, la operación crea ese directorio y, a continuación, le agrega un blob. Este comportamiento es lógico en el contexto de una estructura jerárquica de carpetas. En una cuenta de Blob Storage que no tiene un espacio de nombres jerárquico, la operación no crea un directorio. En su lugar, el nombre del directorio se agrega al espacio de nombres del blob. 

### <a name="list-operations"></a>Mostrar operaciones

Una operación de tipo [Enumerar blobs](/rest/api/storageservices/list-blobs) devuelve directorios y archivos. Cada uno se enumera por separado. Por lo tanto, los directorios aparecen en la lista como blobs de longitud cero. En una cuenta de Blob Storage que no tiene un espacio de nombres jerárquico, una operación [Enumerar blobs](/rest/api/storageservices/list-blobs) devuelve solo blobs y no directorios. Si usa la operación [Ruta de acceso: lista](/rest/api/storageservices/datalakestoragegen2/path/list) de Data Lake Storage Gen2, los directorios aparecerán como entradas de directorio y no como blobs de longitud cero.

El orden de la lista también es diferente. Los directorios y archivos aparecen según el orden de *búsqueda de equilibrio de carga en profundidad*. Una cuenta de Blob Storage que no tiene un espacio de nombres jerárquico enumera los blobs en orden *lexicográfico*. 

### <a name="operations-to-rename-blobs"></a>Operaciones para cambiar el nombre de blobs

Cambiar el nombre de un blob es mucho más eficaz porque las aplicaciones cliente pueden cambiar el nombre de un blob en una sola operación. En las cuentas que *no* tienen un espacio de nombres jerárquico, las herramientas y las aplicaciones tienen que copiar un blob y, a continuación, eliminar el blob de origen.  

> [!NOTE]
> Al cambiar el nombre de un blob, no se actualiza la hora de la última modificación del blob. Esto se debe a que el contenido del blob no ha cambiado. 

## <a name="impact-on-costs"></a>Impacto en los costos

Los costos de almacenamiento no se verán afectados, pero los costos de las transacciones sí se verán afectados. Use estas páginas para evaluar los costos de comparación. 

- [Precios de los blobs en bloques](https://azure.microsoft.com/pricing/details/storage/blobs/).

- [Precios de Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

También puede usar la opción de **Cuentas de almacenamiento** en la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para calcular el impacto de los costos después de una actualización. 

Además de los cambios en los precios, tenga en cuenta el ahorro de costos asociado a las funcionalidades de Data Lake Storage Gen2. Normalmente, el costo total de la propiedad disminuye debido a un mayor rendimiento y a las operaciones optimizadas. Asimismo, un mayor rendimiento permite transferir más datos en menos tiempo. Un espacio de nombres jerárquico mejora la eficacia de las operaciones.  

## <a name="impact-on-service-integrations"></a>Impacto en las integraciones de servicios

Aunque la mayoría de las integraciones de servicios de Azure seguirán funcionando después de habilitar estas funcionalidades, algunas de ellas permanecen en versión preliminar o aún no se admiten. Consulte los [servicios de Azure que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md) para comprender la compatibilidad actual con las integraciones de servicios de Azure con Data Lake Storage Gen2.

## <a name="impact-on-tools-features-and-documentation"></a>Impacto en herramientas, características y documentación

Después de realizar la actualización, cambiará la forma en que estos elementos interactúan con algunas características. En esta sección se describen esos cambios.

### <a name="blob-storage-feature-support"></a>Compatibilidad de las características de Blob Storage

Aunque la mayoría de las características de Blob Storage seguirá funcionando después de habilitar estas funcionalidades, algunas de ellas permanecen en versión preliminar o aún no se admiten. 

Consulte [Características de Blob Storage disponibles en Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) para comprender la compatibilidad actual con las características de Blob Storage con Data Lake Storage Gen2. 

### <a name="diagnostic-logs"></a>Registros de diagnóstico

Si habilita el [registro de análisis de Storage](../common/storage-analytics-logging.md), ahora tiene la opción de usar la versión 2.0 del formato de registro.  

No tiene que usar esta nueva versión. Sin embargo, las operaciones que se aplican al punto de conexión de Data Lake Storage solo se registran en los registros de la versión 2.0. Algunos servicios y herramientas que use (como AzCopy) usarán ese punto de conexión para realizar operaciones en su cuenta. Para asegurarse de capturar la información de registro de todas las actividades, considere la posibilidad de usar la versión 2.0 del formato de registro. 

### <a name="azure-lifecycle-management"></a>Administración del ciclo de vida de Azure

Las directivas que mueven o eliminan todos los blobs de un directorio no eliminarán el directorio que contiene esos blobs hasta el día siguiente. Esto se debe a que el directorio no se puede eliminar hasta que se quitan por primera vez todos los blobs que se encuentran en él. Al día siguiente, se quitará el directorio. 

### <a name="event-grid"></a>Event Grid

La nueva cuenta tiene dos puntos de conexión: el punto de conexión de Data Lake y el punto de conexión de Blob service. Los servicios, las herramientas y las aplicaciones pueden usar cualquier punto de conexión para operar en los datos. Como resultado, una respuesta de evento que devuelve Event Grid puede mostrar cualquiera de estos dos puntos de conexión en el campo **url** que describe el blob afectado. 

El siguiente JSON muestra la dirección URL de un blob que aparece en la respuesta del evento cuando se crea un blob mediante el punto de conexión de Blob service.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

El siguiente JSON muestra la dirección URL de un blob que aparece en la respuesta del evento cuando se crea un blob mediante el punto de conexión de Data Lake Storage.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

Si las aplicaciones usan Event Grid, es posible que tenga que modificar esas aplicaciones para tener esto en cuenta. 

### <a name="storage-explorer"></a>Explorador de Storage

Los botones siguientes aún no aparecen en la barra de herramientas del Explorador de Azure Storage.

|Botón|Motivo|
|--|--|
|Copiar la dirección URL|Todavía no se implementó|
|Administración de instantáneas|Todavía no se implementó|
|Deshacer eliminación|Depende de las características de Blob Storage que aún no se admiten con Data Lake Storage Gen2. |


Los botones siguientes se comportan de forma diferente en la nueva cuenta.

|Botón|Comportamiento de Blob Storage|Comportamiento de Data Lake Storage Gen2|
|---|---|---|
|Carpeta|La carpeta es virtual y desaparece si no se le agregan archivos. |La carpeta existe incluso sin archivos agregados a ella.| 
|Cambiar nombre|Da como resultado una copia y, a continuación, una eliminación del blob de origen|Cambia el nombre del mismo blob. Es mucho más eficaz.|

### <a name="documentation"></a>Documentación

Puede encontrar instrucciones para usar las funcionalidades de Data Lake Storage Gen2 aquí: [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md). 

No ha cambiado nada con respecto a dónde se encuentran las instrucciones para todas las características existentes de Blob Storage. Encontrará la guía aquí: [Introducción a Azure Blob Storage](storage-blobs-introduction.md). 

A medida que se mueve entre conjuntos de contenido, observará algunas pequeñas diferencias de terminología. Por ejemplo, el contenido destacado en el contenido de Data Lake Storage Gen2 podría usar los términos *archivo* y *sistema de archivos* y en lugar de *blob* y *contenedor*. Los términos *archivo* y *sistema de archivos* están profundamente arraigados en el mundo del análisis de macrodatos, donde Data Lake Storage tiene un largo historial. El contenido alberga estos términos para que sigan relacionados con estas audiencias. Estos términos no describen *cosas* independientes.

## <a name="next-steps"></a>Pasos siguientes

Cuando esté listo para actualizar la cuenta de almacenamiento para incluir funcionalidades de Data Lake Storage Gen2, consulte esta guía paso a paso. 
> [!div class="nextstepaction"]
> [Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md)
