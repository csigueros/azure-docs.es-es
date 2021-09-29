---
title: Introducción al almacenamiento inmutable para datos de blobs
titleSuffix: Azure Storage
description: Azure Storage ofrece compatibilidad WORM (escribir una vez, leer muchas) para Blob Storage, lo que permite a los usuarios almacenar datos en un estado que no se puede borrar ni modificar. Las directivas de retención con duración definida almacenan los datos de blobs en un estado WORM durante un intervalo especificado, mientras las suspensiones legales permanecen en vigor hasta que se borran explícitamente.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: ca0ec185721b0e35c15dfe99e361ff024cf01a78
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584224"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Almacenamiento de datos de blobs críticos para la empresa con almacenamiento inmutable

El almacenamiento inmutable para Azure Blob Storage permite a los usuarios almacenar los datos críticos para la empresa en un estado WORM (escribir una vez, leer muchas). Mientras los datos se encuentran en estado WORM, no se pueden modificar ni eliminar durante un intervalo especificado por el usuario. Al configurar directivas de inmutabilidad para los datos de blobs, puede proteger los datos frente a sobrescrituras y eliminaciones.

El almacenamiento inmutable para Azure Blob Storage admite dos tipos de directivas de inmutabilidad:

- **Directivas de retención con duración definida**: Con una directiva de retención con duración definida, los usuarios pueden establecer directivas para almacenar datos durante un intervalo especificado. Cuando se establece una directiva de retención con duración definida, se pueden crear y leer objetos, pero no se pueden modificar ni eliminar. Una vez transcurrido el período de retención, los objetos se pueden eliminar, pero no sobrescribir. Para más información sobre las directivas de retención con duración definida, consulte [Directivas de retención con duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md).

- **Directivas de suspensión legal**: Una suspensión legal almacena datos inmutables hasta que la suspensión se borra explícitamente. Cuando se establece una suspensión legal, los objetos se pueden crear y leer, pero no modificar ni eliminar. Para más información sobre las directivas de suspensión legal, consulte [Suspensiones legales para datos de blobs inmutables](immutable-legal-hold-overview.md).

En el diagrama siguiente se muestra cómo las directivas de retención basadas en el tiempo y la conservación por razones legales evitan las operaciones de escritura y eliminación mientras están en vigor.

:::image type="content" source="media/immutable-storage-overview/worm-diagram.png" alt-text="Diagrama que muestra cómo las directivas de retención y la conservación por razones legales evitan las operaciones de escritura y eliminación":::

## <a name="about-immutable-storage-for-blobs"></a>Acerca del almacenamiento inmutable de blobs

El almacenamiento inmutable ayuda a los centros de atención médica, las instituciones financieras y los sectores relacionados &mdash;sobre todo las organizaciones de agente-distribuidor&mdash; a almacenar los datos de forma segura. El almacenamiento inmutable puede usarse en cualquier escenario para proteger datos críticos e impedir que se eliminen o modifiquen.

Las aplicaciones típicas son:

- **Cumplimiento normativo**: el almacenamiento inmutable para Azure Blob Storage ayuda a las organizaciones a cumplir SEC 17a-4(f), CFTC 1.31(d), FINRA y otras regulaciones.

- **Protección de la retención de documentos**: El almacenamiento inmutable de blobs garantiza que ningún usuario pueda modificar ni eliminar datos, ni siquiera los usuarios que tienen privilegios administrativos en la cuenta.

- **Suspensión legal**: El almacenamiento inmutable de blobs permite a los usuarios almacenar información confidencial crítica para procedimientos judiciales o uso empresarial en un estado a prueba de manipulación durante el tiempo deseado hasta que se elimina la suspensión. Esta característica no se limita solo a los casos de uso legales, sino que también puede considerarse una suspensión basada en eventos o un bloqueo de la empresa, donde es necesario proteger los datos basados en desencadenadores de eventos o directivas corporativas.

## <a name="regulatory-compliance"></a>Cumplimiento de normativas

Microsoft ha usado una de las principales empresas de valoración independiente especializada en la administración de registros y la gobernanza de la información, Cohasset Associates. Dicha empresa se ha encargado de evaluar el almacenamiento inmutable de blobs y el cumplimiento con los requisitos específicos del sector de servicios financieros. Cohasset ha validado que el almacenamiento inmutable, cuando se ha usado para conservar los blobs en un estado WORM, cumplía los requisitos de almacenamiento pertinentes de las reglas CFTC 1.31(c)-(d), FINRA 4511 y SEC 17a-4(f). Microsoft se centró en este conjunto de reglas, ya que representan la guía más prescriptiva globalmente para la retención de registros en las instituciones financieras.

El informe de Cohasset está disponible en el [Centro de confianza de servicios de Microsoft](https://aka.ms/AzureWormStorage). El [Centro de confianza de Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contiene información detallada sobre las certificaciones de cumplimiento de Microsoft. Para solicitar una carta de certificación de Microsoft con respecto al cumplimiento de inmutabilidad de WORM, póngase en contacto con el [Soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## <a name="immutability-policy-scope"></a>Ámbito de las directivas de inmutabilidad

El ámbito de las directivas de inmutabilidad se puede establecer en una versión de blob (versión preliminar) o en un contenedor. El comportamiento de un objeto según una directiva de inmutabilidad depende del ámbito de la directiva. Para más información sobre el ámbito de las directivas para cada tipo de directiva de inmutabilidad, consulte las secciones siguientes:

- [Ámbito de las directivas de retención con duración definida](immutable-time-based-retention-policy-overview.md#time-based-retention-policy-scope)
- [Ámbito de la suspensión legal](immutable-legal-hold-overview.md#legal-hold-scope)

Puede configurar tanto una directiva de retención con duración definida como una suspensión legal para un recurso (contenedor o versión de blob), según el ámbito. En la tabla siguiente se resumen las directivas de inmutabilidad que se admiten para cada ámbito de recurso:

| Ámbito | El contenedor admite directivas de inmutabilidad de nivel de versión | El contenedor no admite directivas de inmutabilidad de nivel de versión |
|--|--|--|
| Contenedor | Admite una directiva de inmutabilidad predeterminada a nivel de versión. La directiva predeterminada se aplica a las versiones creadas en el contenedor después de configurarlo.<br /><br /> No admite la suspensión legal. | Admite una directiva de inmutabilidad a nivel de contenedor y una suspensión legal. Una directiva en una versión de blob puede invalidar una directiva predeterminada especificada en el contenedor. |
| Versión de un blob | Admite una directiva de inmutabilidad a nivel de versión y una suspensión legal. | N/D |

### <a name="about-the-preview"></a>Acerca de la versión preliminar

La versión preliminar de las directivas de inmutabilidad a nivel de versión está disponible en las siguientes regiones:

- Centro de Canadá
- Este de Canadá
- Centro de Francia
- Sur de Francia

> [!IMPORTANT]
> Las directivas de inmutabilidad a nivel de versión están actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="summary-of-immutability-scenarios"></a>Resumen de los escenarios de inmutabilidad

La protección que ofrece una directiva de inmutabilidad depende del ámbito de la directiva de inmutabilidad y, en el caso de una directiva de retención con duración definida, si está bloqueada o desbloqueada, y si está activa o ha expirado.

### <a name="scenarios-with-version-level-scope"></a>Escenarios con ámbito a nivel de versión

En la tabla siguiente encontrará un resumen de las protecciones que ofrecen las directivas de inmutabilidad a nivel de versión.

| Escenario | Operaciones prohibidas | Protección de blobs | Protección de contenedores | Protección de cuentas |
|--|--|--|--|--|
| Una versión de blob está protegida por una directiva de retención *activa* o hay en vigor una suspensión legal. | [Delete Blob](/rest/api/storageservices/delete-blob), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page) y [Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | No se puede eliminar la versión de blob. No se pueden escribir los metadatos de usuario. <br /><br /> Sobrescribir un blob con [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) o [Copy Blob](/rest/api/storageservices/copy-blob) crea una nueva versión<sup>2</sup>. | Se produce un error en la eliminación del contenedor si existe al menos un blob en el contenedor, independientemente de si la directiva está bloqueada o desbloqueada. | Se produce un error en la eliminación de la cuenta de almacenamiento si hay al menos un contenedor que tenga habilitado el almacenamiento inmutable a nivel de versión. |
| Una versión de blob está protegida por una directiva de retención *expirada* y no hay en vigor una suspensión legal. | [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page) y [Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | Se puede eliminar la versión de blob. No se pueden escribir los metadatos de usuario. <br /><br /> Sobrescribir un blob con [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) o [Copy Blob](/rest/api/storageservices/copy-blob) crea una nueva versión<sup>2</sup>. | Se produce un error en la eliminación del contenedor si existe al menos un blob en el contenedor, independientemente de si la directiva está bloqueada o desbloqueada. | La eliminación de la cuenta de almacenamiento produce un error si hay al menos un contenedor que incluye una versión de blob con una directiva de retención con duración definida bloqueada.<br /><br />Las directivas desbloqueadas no ofrecen protección contra eliminación. |

<sup>1</sup> Solo se permite la operación [Append Block](/rest/api/storageservices/append-block) para las directivas de retención de duración definida con la propiedad **allowProtectedAppendWrites** habilitada. Para más información, consulte cómo [permitir la escritura de blobs en anexos protegidos](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
<sup>2</sup> Las versiones de blob siempre son inmutables para el contenido. Si el control de versiones está habilitado para una cuenta de almacenamiento, una operación de escritura en un blob en bloques crea una nueva versión, con la excepción de la operación [Put Block](/rest/api/storageservices/put-block).

### <a name="scenarios-with-container-level-scope"></a>Escenarios con ámbito a nivel de contenedor

En la tabla siguiente encontrará un resumen de las protecciones que ofrecen las directivas de inmutabilidad a nivel de contenedor.

| Escenario | Operaciones prohibidas | Protección de blobs | Protección de contenedores | Protección de cuentas |
|--|--|--|--|--|
| Un contenedor está protegido por una directiva de retención con duración definida *activa* con ámbito de contenedor o hay en vigor una suspensión legal. | [Delete Blob](/rest/api/storageservices/delete-blob), [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>, [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page), [Set Blob Properties](/rest/api/storageservices/set-blob-properties), [Snapshot Blob](/rest/api/storageservices/snapshot-blob), [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), [Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | Todos los blobs del contenedor son inmutables para el contenido y los metadatos del usuario. | Se produce un error en la eliminación del contenedor si hay en vigor una directiva a nivel de contenedor. | Se produce un error en la eliminación de la cuenta de almacenamiento si hay un contenedor con al menos un blob presente. |
| Un contenedor está protegido por una directiva de retención con duración definida *expirada* con ámbito de contenedor y no hay en vigor una suspensión legal. | [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>, [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page), [Set Blob Properties](/rest/api/storageservices/set-blob-properties), [Snapshot Blob](/rest/api/storageservices/snapshot-blob), [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), [Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | Se permiten las operaciones de eliminación. No se permiten las operaciones de sobrescritura. | Se produce un error en la eliminación del contenedor si existe al menos un blob en el contenedor, independientemente de si la directiva está bloqueada o desbloqueada. | Se produce un error en la eliminación de la cuenta de almacenamiento si hay al menos un contenedor con una directiva de retención con duración definida bloqueada.<br /><br />Las directivas desbloqueadas no ofrecen protección contra eliminación. |

<sup>1</sup> Azure Storage permite que la operación [Put Blob](/rest/api/storageservices/put-blob) cree un blob. No se permiten las sucesivas operaciones de sobrescritura en una ruta de acceso de blob existente en un contenedor inmutable.

<sup>2</sup> Solo se permite la operación [Append Block](/rest/api/storageservices/append-block) para las directivas de retención de duración definida con la propiedad **allowProtectedAppendWrites** habilitada. Para más información, consulte cómo [permitir la escritura de blobs en anexos protegidos](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Algunas cargas de trabajo, como [Copia de seguridad de SQL a URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url), cree un y agréguela. Si un contenedor tiene una directiva de retención con duración definida activa o hay en vigor una suspensión legal, este patrón no funcionará.

## <a name="supported-account-configurations"></a>Configuraciones de cuenta admitidas

Las directivas de inmutabilidad se admiten para las cuentas de almacenamiento nuevas y existentes. En la tabla siguiente se muestran los tipos de cuentas de almacenamiento que se admiten para cada tipo de directiva:

| Tipo de directiva de inmutabilidad | Ámbito de la directiva | Tipos de cuentas de almacenamiento admitidos | Admite el espacio de nombres jerárquico (versión preliminar) |
|--|--|--|--|
| Directiva de retención con duración definida | Ámbito a nivel de versión (versión preliminar) | Uso general v2<br />Blobs en bloques Premium | No |
| Directiva de retención con duración definida | Ámbito a nivel de contenedor | Uso general v2<br />Blobs en bloques Premium<br />Uso general v1 (heredado)<sup>1</sup><br> Almacenamiento de blobs (heredado) | Sí |
| Suspensión legal | Ámbito a nivel de versión (versión preliminar) | Uso general v2<br />Blobs en bloques Premium | No |
| Suspensión legal | Ámbito a nivel de contenedor | Uso general v2<br />Blobs en bloques Premium<br />Uso general v1 (heredado)<sup>1</sup><br> Almacenamiento de blobs (heredado) | Sí |

<sup>1</sup> Microsoft recomienda actualizar las cuentas de uso general v1 a uso general v2 para que pueda aprovechar más características. Para obtener información sobre la actualización de una cuenta de almacenamiento de uso general v1 existente, vea [Actualización de una cuenta de almacenamiento](../common/storage-account-upgrade.md).

### <a name="access-tiers"></a>Niveles de acceso

Todos los niveles de acceso a blobs admiten almacenamiento inmutable. Puede cambiar el nivel de acceso de un blob con la operación Set Blob Tier. Para obtener más información, vea [Niveles de acceso de Azure Blob Storage: frecuente, esporádico y archivo](storage-blob-storage-tiers.md).

### <a name="redundancy-configurations"></a>Configuraciones de redundancia

Todas las configuraciones de redundancia admiten almacenamiento inmutable. En el caso de las configuraciones con redundancia geográfica, no se admite la conmutación por error administrada por el cliente. Para obtener más información sobre la configuración de redundancia, consulte la [Redundancia de Azure Storage](../common/storage-redundancy.md).

### <a name="hierarchical-namespace-support"></a>Compatibilidad con espacios de nombres jerárquicos

La compatibilidad del almacenamiento inmutable para cuentas con un espacio de nombres jerárquico está en versión preliminar. Para inscribirse en la versión preliminar, consulte [Características en versión preliminar de Azure Data Lake Storage](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u).

Tenga en cuenta que no puede cambiar el nombre ni mover un blob cuando el blob está en estado inmutable y la cuenta tiene habilitado un espacio de nombres jerárquico. Tanto el nombre del blob como la estructura de directorios brindan datos a nivel de contenedor fundamentales, que no se pueden modificar una vez que se ha implementado la directiva de inmutabilidad.

> [!IMPORTANT]
> El almacenamiento inmutable para Azure Blob Storage en cuentas que tienen habilitada la característica de espacio de nombres jerárquico se encuentra actualmente en versión preliminar. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="recommended-blob-types"></a>Tipos de blob recomendados

Microsoft recomienda configurar directivas de inmutabilidad principalmente para blobs en bloques y blobs en anexos. No se recomienda configurar una directiva de inmutabilidad para un blob en páginas que almacena un disco VHD para una máquina virtual activa, ya que las escrituras en el disco estarán bloqueadas. Microsoft recomienda revisar exhaustivamente la documentación y probar los escenarios antes de bloquear las directivas de duración definida.

## <a name="immutable-storage-with-blob-soft-delete"></a>Almacenamiento inmutable con eliminación temporal de blobs

Cuando se configura la eliminación temporal de blobs para una cuenta de almacenamiento, se aplica a todos los blobs dentro de la cuenta, independientemente de si hay en vigor una suspensión legal o una directiva de retención con duración definida. Microsoft recomienda habilitar la eliminación temporal para mayor protección antes de aplicar las directivas de inmutabilidad.

Si habilita la eliminación temporal de blobs y, a continuación, configura una directiva de inmutabilidad, los blobs que ya se hayan eliminado temporalmente se eliminarán de manera permanente una vez que la directiva de retención de eliminación temporal haya expirado. Los blobs eliminados temporalmente se pueden restaurar durante el período de retención de eliminación temporal. Un blob o una versión que aún no se han eliminado temporalmente están protegidos por la directiva de inmutabilidad, y no se pueden eliminar temporalmente hasta que la directiva de retención con duración definida haya expirado o se haya quitado la suspensión legal.

## <a name="use-blob-inventory-to-track-immutability-policies"></a>Uso del inventario de blobs para hacer un seguimiento de las directivas de inmutabilidad

El inventario de blobs de Azure Storage brinda información general de los contenedores en las cuentas de almacenamiento, y los blobs, las instantáneas y las versiones de blob dentro de ellas. Puede usar el informe de inventario de blobs para comprender los atributos de blobs y contenedores, incluido si un recurso tiene configurada una directiva de inmutabilidad.

Al habilitar el inventario de blobs, Azure Storage genera un informe de inventario a diario. El informe proporciona información general de los datos para los requisitos empresariales y de cumplimiento.

Para más información sobre el inventario de blobs de Azure Storage, consulte [Inventario de blobs de Azure Storage (versión preliminar)](blob-inventory.md).

## <a name="pricing"></a>Precios

No hay ningún cargo de capacidad adicional por el uso de almacenamiento inmutable. El precio de los datos inmutables es el mismo que el de los datos mutables. Para obtener información detallada sobre los precios de almacenamiento de blobs de Azure, vea la [página de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

La creación, modificación o eliminación de una directiva de retención con duración definida o una suspensión legal en una versión de blob generan un cargo por transacciones de escritura.

Si no paga la factura y su cuenta tiene en vigor una directiva de retención con duración definida activa, las directivas de retención de datos normales se aplicarán según lo previsto en los términos y condiciones del contrato con Microsoft. Para obtener información general, consulte [Administración de datos en Microsoft](https://www.microsoft.com/trust-center/privacy/data-management).

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades.

| Tipo de cuenta de almacenamiento                | Blob Storage (compatibilidad predeterminada)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) |![Sí](../media/icons/yes-icon.png)  <sup>2</sup>              | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |
| Blobs en bloques Premium          | ![Sí](../media/icons/yes-icon.png) |![Sí](../media/icons/yes-icon.png)  <sup>2</sup> | ![Sí](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2 y el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

<sup>2</sup> La característica se admite en el nivel de versión preliminar.

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre la protección de datos](data-protection-overview.md)
- [Directivas de retención con duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md)
- [Retenciones legales para datos de blob inmutables](immutable-legal-hold-overview.md)
- [Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)](immutable-policy-configure-version-scope.md)
- [Configuración de directivas de inmutabilidad para contenedores](immutable-policy-configure-container-scope.md)
