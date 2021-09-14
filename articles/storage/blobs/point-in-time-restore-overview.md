---
title: Restauración a un momento dado para blobs en bloques
titleSuffix: Azure Storage
description: La restauración a un momento dado para blobs en bloques proporciona protección contra eliminaciones accidentales o daños, ya que le permite restaurar una cuenta de almacenamiento a su estado anterior en un momento dado en el tiempo.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 06f5c1192d5216f753d77fb842359835c4552197
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471055"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Restauración a un momento dado para blobs en bloques

La restauración a un momento dado proporciona protección contra eliminaciones accidentales o daños, ya que le permite restaurar los datos de blobs en bloques a un estado anterior. Asimismo, la restauración a un momento dado resulta útil en escenarios en los que un usuario o una aplicación eliminan datos accidentalmente o donde un error de la aplicación daña los datos. La restauración a un momento dado también permite habilitar escenarios de prueba que requieran revertir un conjunto de datos a un estado conocido antes de ejecutar otras pruebas.

La restauración a un momento dado solo se admite para las cuentas de almacenamiento de uso general v2 en el nivel de rendimiento estándar. Solo se pueden restaurar los datos de los niveles de almacenamiento de acceso frecuente y esporádico con la restauración a un momento dado.

Para aprender a habilitar la restauración a un momento dado para una cuenta de almacenamiento, consulte el artículo sobre la [realización de una restauración a un momento dado en los datos de un blob en bloques](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Cómo funciona la restauración a un momento dado

Para habilitar la restauración a un momento dado, cree una directiva de administración para la cuenta de almacenamiento y especifique un período de retención. Durante el período de retención, puede restaurar los blobs en bloques desde el estado actual a un estado de un momento dado anterior.

Para iniciar una restauración a un momento dado, llame a la operación [Restore Blob Ranges](/rest/api/storagerp/storageaccounts/restoreblobranges) y especifique un punto de restauración en una hora UTC. Puede especificar rangos lexicográficos de nombres de contenedor y blob para restaurar u omitir el rango para restaurar todos los contenedores de la cuenta de almacenamiento. Se admiten hasta 10 rangos lexicográficos por operación de restauración.

Igualmente, Azure Storage analiza todos los cambios que se han realizado en los blobs especificados entre el punto de restauración solicitado,la hora UTC especificada y el momento presente. La operación de restauración es atómica, por lo que o todos los cambios de la restauración se realizan correctamente, o se produce un error. Si hay blobs que no se pueden restaurar, se produce un error en la operación y se reanudan las operaciones de lectura y escritura en los contenedores afectados.

En el siguiente diagrama se muestra cómo funciona la restauración a un momento dado. Uno o varios contenedores o intervalos de blobs se restauran a su estado de hace *n* días, donde *n* es menor o igual que el período de retención definido para la restauración a un momento dado. El efecto consiste en revertir las operaciones de escritura y eliminación que se produjeron durante el período de retención.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Diagrama que muestra cómo se realiza la restauración a un momento dado de los contenedores a un estado anterior":::

Recuerde que solo se puede ejecutar una operación de restauración en una cuenta de almacenamiento a la vez. Una operación de restauración no se puede cancelar una vez está en curso, pero se puede realizar una segunda operación de restauración para deshacer la primera operación.

La operación **Restore Blob Ranges** devuelve un id. de restauración que identifica de forma única la operación. Para comprobar el estado de una restauración a un momento dado, llame a la operación **Get Restore Status** con el id. de restauración que se devolvió de la operación **Restore Blob Ranges**.

> [!IMPORTANT]
> Cuando se realiza una operación de restauración, Azure Storage bloquea las operaciones de datos en los blobs del intervalo que se está restaurando mientras dure la operación. Las operaciones de lectura, escritura y eliminación se bloquean en la ubicación principal. Por este motivo, es posible que las operaciones como la enumeración de contenedores en Azure Portal no funcionen según lo esperado mientras se realiza la operación de restauración.
>
> Asimismo, las operaciones de lectura de la ubicación secundaria pueden continuar durante la operación de restauración si la cuenta de almacenamiento tiene replicación geográfica.

> [!CAUTION]
> La restauración a un momento dado solo admite operaciones de restauración de blobs en bloques. No se permiten operaciones de restauración de contenedores. Por ejemplo, si elimina un contenedor de la cuenta de almacenamiento llamando a la operación [Eliminar contenedor](/rest/api/storageservices/delete-container), dicho contenedor no se puede restaurar con una operación de restauración a un momento dado. En lugar de eliminar un contenedor completo, elimine blobs individuales, por si desea restaurarlos más adelante.

### <a name="prerequisites-for-point-in-time-restore"></a>Requisitos previos para la restauración a un momento dado

La restauración a un momento dado requiere que las siguientes características de Azure Storage se habiliten antes de poder habilitar la restauración a un momento dado:

- [Eliminación temporal](soft-delete-blob-overview.md)
- [Fuente de cambios](storage-blob-change-feed.md)
- [Control de versiones de blobs](versioning-overview.md)

Si habilita estas características, pueden producirse cargos adicionales. Asegúrese de que comprende las implicaciones de facturación antes de habilitar la restauración a un momento dado y las características de los requisitos previos.

### <a name="retention-period-for-point-in-time-restore"></a>Período de retención para la restauración a un momento dado

Al habilitar la restauración a un momento dado de una cuenta de almacenamiento, se especifica un período de retención. Los blobs en bloques de la cuenta de almacenamiento se pueden restaurar durante el período de retención.

El período de retención comienza unos minutos después de habilitar la restauración a un momento dado. Tenga en cuenta que no se pueden restaurar blobs a un estado anterior al inicio del período de retención. Por ejemplo, si habilitó la restauración a un momento dado el 1 de mayo con una retención de 30 días, el 15 de mayo podrá restaurar hasta un máximo de 15 días. Así pues, el 1 de junio podrá restaurar los datos de entre 1 y 30 días.

El período de retención de la restauración a un momento dado debe ser al menos un día inferior que el período de retención especificado en la eliminación temporal. Por ejemplo, si el período de retención de eliminación temporal se establece en 7 días, el período de retención de restauración a un momento dado puede estar comprendido entre 1 y 6 días.

> [!IMPORTANT]
> El tiempo que se tarda en restaurar un conjunto de datos se basa en el número de operaciones de escritura y eliminación realizadas durante el período de restauración. Por ejemplo, una cuenta con un millón de objetos con 3000 objetos agregados al día y 1000 objetos eliminados al día requerirá aproximadamente dos horas para restaurar a un período de 30 días determinado del pasado. No se recomendarían un período de retención ni una restauración superiores a 90 días en el pasado para una cuenta con esta tasa de cambio.

### <a name="permissions-for-point-in-time-restore"></a>Permisos para la restauración a un momento dado

Para iniciar una operación de restauración, un cliente debe tener permisos de escritura en todos los contenedores de la cuenta de almacenamiento. Para conceder permisos para autorizar una operación de restauración con Azure Active Directory (Azure AD), asigne el rol de **Colaborador de la cuenta de almacenamiento** a la entidad de seguridad en el nivel de la cuenta de almacenamiento, el grupo de recursos o la suscripción.

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

La restauración a un momento dado para los blobs en bloques tiene las siguientes limitaciones y problemas conocidos:

- Solo los blobs en bloques de una cuenta de almacenamiento estándar de uso general v2 pueden restaurarse como parte de una operación de restauración a un momento dado. Los blobs en anexos, blobs en páginas y blobs en bloques Premium no se restauran.
- Si ha eliminado un contenedor durante el período de retención, dicho contenedor no se restaurará con la operación de restauración a un momento dado. Si intenta restaurar un intervalo de blobs que incluye blobs en un contenedor eliminado, se producirá un error en la operación de restauración a un momento dado. Para aprender a proteger los contenedores contra la eliminación, consulte [Eliminación temporal de contenedores](soft-delete-container-overview.md).
- Si un blob se ha desplazado entre los niveles de acceso frecuente y esporádico en el período comprendido entre el momento actual y el punto de restauración, el blob se restaura a su nivel anterior. No se admite la restauración de blobs en blob en bloques en el nivel de archivo. Por ejemplo, si un blob en el nivel de acceso frecuente se movió al nivel de archivo hace dos días y se realiza una operación de restauración a un momento de hace tres días, el blob no se restaura en el nivel de acceso frecuente. Para restaurar un blob archivado, sáquelo antes del nivel de archivo. Para más información, consulte [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).
- Si se configura una directiva de inmutabilidad, se puede iniciar una operación de restauración, pero no se modificarán los blobs protegidos por la directiva de inmutabilidad. En este caso, una operación de restauración no dará como resultado la restauración de un estado coherente a la fecha y hora especificadas.
- Un bloque que se ha cargado mediante la operación [Put Block](/rest/api/storageservices/put-block) o [Put Block from URL](/rest/api/storageservices/put-block-from-url), pero que no se ha confirmado a través de [Put Block List](/rest/api/storageservices/put-block-list), no es parte de un blob y, por tanto, no se restaura como parte de una operación de restauración.
- No se puede restaurar un blob con una concesión activa. Si un blob con una concesión activa se incluye en el rango de blobs que se van a restaurar, la operación de restauración producirá un error de forma atómica. Interrumpa las concesiones activas antes de iniciar la operación de restauración.
- Las instantáneas no se crean ni se eliminan como parte de una operación de restauración. Solo el blob base se restaura a su estado anterior.
- No se admite la restauración de los espacios de nombres planos y jerárquicos de Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Si restaura blobs en bloques a una fecha anterior al 22 de septiembre de 2020, surtirán efecto las limitaciones en versión preliminar de la restauración a un momento dado. Microsoft recomienda elegir como punto de restauración el 22 de septiembre de 2020, o cualquier fecha posterior, con el fin de aprovechar las ventajas de la característica de restauración a un momento dado disponible con carácter general.

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades. 

| Tipo de cuenta de almacenamiento                | Blob Storage (compatibilidad predeterminada)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) |![No](../media/icons/no-icon.png)              | ![No](../media/icons/no-icon.png) | 
| Blobs en bloques Premium          | ![No](../media/icons/no-icon.png)|![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 y el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

## <a name="pricing-and-billing"></a>Precios y facturación

No se aplica ningún cargo por habilitar la restauración a un momento dado. Sin embargo, la habilitación de la restauración a un momento dado también permite el control de versiones de blobs, la eliminación temporal y la fuente de cambios, cada uno de los cuales puede dar lugar a cargos adicionales.

La facturación de la restauración a un momento dado depende de la cantidad de datos procesados para realizar la operación de restauración. La cantidad de datos procesados se basa en el número de cambios que se produjeron entre el punto de restauración y el momento actual. Por ejemplo, suponiendo una tasa de cambio relativamente constante para los datos de blobs en bloques de una cuenta de almacenamiento, una operación de restauración para revertir los datos 1 día tendría un costo de 1/10 de una restauración que pueda retroceder 10 días.

Para calcular el costo de una operación de restauración, revise el registro de la fuente de cambios para calcular la cantidad de datos que se modificaron durante el período de restauración. Por ejemplo, si el período de retención de la fuente de cambios es de 30 días y el tamaño de la fuente de cambios es de 10 MB, la restauración a un punto anterior de 10 días costaría aproximadamente un tercio del precio de una cuenta de LRS en esa región. La restauración a un punto que sea de 27 días costaría aproximadamente nueve décimos del precio de la lista.

Para obtener más información sobre los precios de las restauraciones a un momento dado, consulte [Precios de los blobs en bloques](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación y administración de la restauración a un momento dado para blobs en bloques (versión preliminar)](point-in-time-restore-manage.md)
- [Compatibilidad con la fuente de cambios en Azure Blob Storage (versión preliminar)](storage-blob-change-feed.md)
- [Habilitación de la eliminación temporal para blobs](./soft-delete-blob-enable.md)
- [Habilitación y administración de las versiones de blob](versioning-enable.md)
