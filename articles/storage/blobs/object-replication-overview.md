---
title: Información general sobre la replicación de objetos
titleSuffix: Azure Storage
description: La replicación de objetos copia asincrónicamente los blobs en bloques entre una cuenta de almacenamiento de origen y una de destino. Utilice la replicación de objetos para minimizar la latencia de las solicitudes de lectura, aumentar la eficacia de las cargas de trabajo de proceso, optimizar la distribución de los datos y minimizar los costos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6808160b97d6d1d46917f2e1733b7b1badb4c5f4
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534372"
---
# <a name="object-replication-for-block-blobs"></a>Replicación de objetos para blobs en bloques

La replicación de objetos copia asincrónicamente los blobs en bloques entre una cuenta de almacenamiento de origen y una de destino. Algunos escenarios que admite la replicación de objetos son:

- **Minimización de latencia.** La replicación de objetos puede reducir la latencia de las solicitudes de lectura al permitir a los clientes consumir datos de una región que esté más cerca físicamente.
- **Aumentar la eficiencia de las cargas de trabajo de proceso.** Con la replicación de objetos, las cargas de trabajo de proceso pueden procesar los mismos conjuntos de blob en bloques en diferentes regiones.
- **Optimizar la distribución de datos.** Puede procesar o analizar los datos en una sola ubicación y luego replicar sólo los resultados en regiones adicionales.
- **Optimizar los costos.** Después de replicar los datos, puede reducir los costos moviéndolos al nivel de archivo mediante las directivas de administración del ciclo de vida.

El siguiente diagrama muestra cómo la replicación de objetos replica blob en bloques de una cuenta de almacenamiento de origen en una región a cuentas de destino en dos regiones diferentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagrama que muestra cómo funciona la replicación de objetos":::

Para información sobre cómo configurar la replicación de objetos, consulte [Configuración de la replicación de objetos](object-replication-configure.md).

## <a name="prerequisites-for-object-replication"></a>Requisitos previos de la replicación de objetos

La replicación de objetos requiere que también estén habilitadas las características de Azure Storage siguientes:

- [Fuente de cambios](storage-blob-change-feed.md): debe estar habilitada en la cuenta de origen. Para información sobre cómo habilitar la fuente de cambios, consulte [Habilitar y deshabilitar la fuente de cambios](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Control de versiones de blobs](versioning-overview.md): debe estar habilitado en la cuenta de origen y en la cuenta de destino. Para información sobre cómo habilitar el control de versiones, consulte [Habilitación y administración del control de versiones de blobs](versioning-enable.md).

Habilitar la fuente de cambios y el control de versiones de blob puede suponer costos adicionales. Para más información, consulte la página [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

La replicación de objetos es compatible con las cuentas de almacenamiento de uso general v2 y las cuentas de almacenamiento de blobs en bloques prémium en versión preliminar. Las cuentas de origen y de destino deben ser cuentas de uso  v2 o de blob en bloques prémium. La replicación de objetos solo admite los blobs en bloques; no se admiten los blobs en anexos ni los blobs en páginas.

> [!IMPORTANT]
> La replicación de objetos para las cuentas de blob en bloques prémium se encuentra actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="how-object-replication-works"></a>Funcionamiento de la replicación de objetos

La replicación asincrónica de objetos copia blobs en bloques en un contenedor según las reglas que configure. El contenido del blob, las versiones asociadas al blob y los metadatos y las propiedades del blob se copian desde el contenedor de origen al contenedor de destino.

> [!IMPORTANT]
> Debido a que los datos de blob en bloques se replican asincrónicamente, la cuenta de origen y la de destino no están inmediatamente sincronizadas. Actualmente no hay ningún acuerdo de nivel de servicio sobre cuánto tiempo se tarda en replicar los datos en la cuenta de destino. Puede comprobar el estado de replicación en el blob de origen para determinar si se ha completado la replicación. Para más información, consulte el apartado [Comprobación del estado de replicación de un blob](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Control de versiones de blobs

La replicación de objetos requiere que el control de versiones de los blobs esté habilitado en las cuentas de origen y de destino. Cuando se modifica un blob replicado de la cuenta de origen, se crea una nueva versión del blob en la cuenta de origen que refleja el estado anterior del mismo antes de la modificación. La versión actual de la cuenta de origen refleja las actualizaciones más recientes. Tanto la versión actual como cualquier versión anterior se replican en la cuenta de destino. Para más información sobre la forma en que las operaciones de escritura afectan a las versiones del blob, consulte [Control de versiones en operaciones de escritura](versioning-overview.md#versioning-on-write-operations).

Cuando se elimina un blob de la cuenta de origen, la versión actual del blob se convierte en una versión anterior y deja de haber una versión actual. Todas las versiones del blob que ya existieran previamente se conservan. Este estado se replica en la cuenta de destino. Para más información sobre la forma en que las operaciones de eliminación afectan a las versiones del blob, consulte [Control de versiones en operaciones de eliminación](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Instantáneas

La replicación de objetos no admite instantáneas de blobs. Las instantáneas de un blob de la cuenta de origen no se replican en la cuenta de destino.

### <a name="blob-tiering"></a>Niveles de blob

La replicación de objetos se admite cuando las cuentas de origen y de destino se encuentran en el nivel de acceso frecuente o esporádico. Las cuentas de origen y destino pueden estar en niveles diferentes. Sin embargo, se producirá un error en la replicación de objetos si un blob de las cuentas de origen o destino se ha movido al nivel de archivo. Para más información sobre los niveles de blob, consulte [Niveles de acceso frecuente, esporádico y de archivo de los blobs](access-tiers-overview.md).

### <a name="immutable-blobs"></a>Blobs inalterables

Las directivas de inmutabilidad para Azure Blob Storage incluyen directivas de retención de duración definida y suspensiones legales. Cuando hay una directiva de inmutabilidad en vigor en la cuenta de destino, la replicación de objetos puede verse afectada. Para obtener más información sobre las directivas de inmutabilidad, consulte [Almacenamiento de datos de blobs críticos para la empresa con almacenamiento inmutable](immutable-storage-overview.md).

Si hay una directiva de inmutabilidad de nivel de contenedor en vigor para un contenedor de la cuenta de destino y un objeto del contenedor de origen se actualiza o se elimina, puede que la operación del contenedor de origen se realice correctamente, pero se producirá un error en la replicación de dicha operación en el contenedor de destino. Para obtener más información sobre qué operaciones están prohibidas con una directiva de inmutabilidad cuyo ámbito es un contenedor, consulte [Escenarios con ámbito a nivel de contenedor](immutable-storage-overview.md#scenarios-with-container-level-scope).

Si hay una directiva de inmutabilidad de nivel de versión en vigor para la versión de un blob de la cuenta de destino y se realiza una operación de eliminación o actualización en la versión del blob del contenedor de origen, puede que la operación se realice correctamente en el objeto de origen, pero se producirá un error en la replicación de dicha operación en el objeto de destino. Para obtener más información sobre qué operaciones están prohibidas con una directiva de inmutabilidad cuyo ámbito es un contenedor, consulte [Escenarios con ámbito a nivel de versión](immutable-storage-overview.md#scenarios-with-version-level-scope).

## <a name="object-replication-policies-and-rules"></a>Reglas y directivas de replicación de objetos

Al configura la replicación de objetos, se crea una política de replicación que especifica la cuenta de almacenamiento de origen y la cuenta de destino. Una directiva de replicación incluye una o más reglas que especifican un contenedor de origen y un contenedor de destino e indican qué blob en bloques del contenedor de origen se replicarán.

Después de configurar la replicación de objetos, Azure Storage comprueba periódicamente la fuente de cambios de la cuenta de origen y replica asincrónicamente cualquier operación de escritura o borrada a la cuenta de destino. La latencia de replicación depende del tamaño del blob en bloques que se está replicando.

### <a name="replication-policies"></a>Directivas de replicación

Al configurar la replicación de objetos, se crea una directiva de replicación en la cuenta de destino a través del proveedor de recursos de Azure Storage. Una vez creada la directiva de replicación, Azure Storage le asigna un identificador de directiva. A continuación, debe asociar esa directiva de replicación con la cuenta de origen mediante el identificador de directiva. El identificador de directiva de las cuentas de origen y de destino debe ser el mismo para que tenga lugar la replicación.

Una cuenta de origen se puede replicar en un máximo de dos cuentas de destino, con una directiva para cada cuenta de destino. De forma similar, una cuenta puede actuar como cuenta de destino para un máximo de dos directivas de replicación.

Las cuentas de origen y de destino pueden estar en la misma región o en regiones diferentes. También pueden residir en la misma suscripción o en suscripciones diferentes. Opcionalmente, las cuentas de origen y destino pueden residir en inquilinos diferentes de Azure Active Directory. Solo se puede crear una directiva de replicación para cada par de cuentas de origen/cuentas de destino.

### <a name="replication-rules"></a>Reglas de replicación

Las reglas de replicación especifican el modo en que Azure Storage replicará los blobs de un contenedor de origen a un contenedor de destino. Puede especificar un máximo de 10 reglas de replicación para cada directiva de replicación. Cada regla de replicación define un único contenedor de origen y de destino, y cada uno de estos contenedores solo se puede usar en una regla, lo que significa que en una única directiva de replicación no pueden participar más de 10 contenedores de origen y 10 contenedores de destino.

Al crear una regla de replicación, de forma predeterminada solo se copian los blobs en bloques nuevos que se agreguen posteriormente al contenedor de origen. Puede especificar que se copien tanto los blob en bloques nuevos como los ya existentes, o bien puede definir un ámbito de copia personalizado que copie los blobs en bloques creados a partir de un momento determinado.

También puede especificar uno o varios filtros como parte de una regla de replicación para filtrar los blobs en bloques por prefijo. Cuando se especifica un prefijo, solo los blobs que coinciden con ese prefijo en el contenedor de origen se copiarán en el contenedor de destino.

Los contenedores de origen y de destino deben existir antes de poder especificarlos en una regla. Después de crear la directiva de replicación, no se permiten las operaciones de escritura en el contenedor de destino. Cualquier intento de escribir en el contenedor de destino producirá un error con el código de error 409 (Conflicto). Para escribir en un contenedor de destino para el que esté configurada una regla de replicación, debe eliminar la regla que está configurada para ese contenedor o quitar la directiva de replicación. Las operaciones de lectura y eliminación en el contenedor de destino se permiten cuando la directiva de replicación está activa.

Puede llamar a la operación [Establecer el nivel del blob](/rest/api/storageservices/set-blob-tier) en un blob en el contenedor de destino para moverla al nivel de archivo. Para más información sobre el nivel de archivo, consulte [Niveles de acceso frecuente, esporádico y de archivo de datos de blob](access-tiers-overview.md#archive-access-tier).

## <a name="policy-definition-file"></a>Archivo de definición de directiva

Un archivo JSON define una directiva de replicación de objetos. Puede obtener el archivo de definición de directiva de una directiva de replicación de objetos ya existente. También puede crear una directiva de replicación de objetos cargando un archivo de definición de directiva.

### <a name="sample-policy-definition-file"></a>Archivo de definición de directiva de ejemplo

En el ejemplo siguiente se define una directiva de replicación en la cuenta de destino con una única regla que coincide con el prefijo *b* y se establece el tiempo de creación mínimo para los blobs que se van a replicar. No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "destinationAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2021-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

### <a name="specify-full-resource-ids-for-the-source-and-destination-accounts"></a>Especificar identificadores de recursos completos para las cuentas de origen y de destino

Al crear el archivo de definición de directiva, especifique los identificadores completos de los recursos de Azure Resource Manager para las entradas **sourceAccount** y **destinationAccount,** como se indica en el ejemplo de la sección anterior. Para más información sobre cómo buscar el identificador de recurso de una cuenta de almacenamiento, consulte [Obtención del identificador de recurso de una cuenta de almacenamiento](../common/storage-account-get-info.md#get-the-resource-id-for-a-storage-account).

El identificador de recurso completo tiene el formato siguiente:

```http
/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Anteriormente, el archivo de definición de directiva solo requería el nombre de cuenta, en lugar del identificador de recurso completo de la cuenta de almacenamiento. Con la introducción de la propiedad de seguridad **AllowCrossTenantReplication** en la versión 2021-02-01 de la API REST del proveedor de recursos de Azure Storage, ahora debe proporcionar el identificador de recurso completo para todas las directivas de replicación de objetos que se crean cuando no se permite la replicación entre inquilinos para una cuenta de almacenamiento que participa en la directiva de replicación. Azure Storage usa el identificador de recurso completo para comprobar si las cuentas de origen y destino residen en el mismo inquilino. Para más información sobre cómo no permitir directivas de replicación entre inquilinos, consulte [Impedir la replicación entre inquilinos de Azure AD](#prevent-replication-across-azure-ad-tenants).

Aunque se admite proporcionar solo el nombre de cuenta cuando se permite la replicación entre inquilinos de una cuenta de almacenamiento, Microsoft recomienda proporcionar siempre el identificador de recurso completo como procedimiento recomendado. Todas las versiones anteriores de la API REST del proveedor de recursos de Azure Storage admiten el uso de la ruta de acceso del identificador de recurso completo en las directivas de replicación de objetos.

En la tabla siguiente se describe lo que sucede cuando se crea una directiva de replicación con el identificador de recurso completo especificado, en lugar de especificar el nombre de cuenta, en los escenarios en los que se permite o no la replicación entre inquilinos para la cuenta de almacenamiento.

| Identificador de la cuenta de almacenamiento en la definición de directiva | Se permite la replicación entre inquilinos | No se permite la replicación entre inquilinos |
|--|--|--|
| Identificador completo del recurso | Se pueden crear directivas del mismo inquilino.<br /><br /> Se pueden crear directivas entre inquilinos. | Se pueden crear directivas del mismo inquilino.<br /><br /> No se pueden crear directivas entre inquilinos. |
| Solo el nombre de cuenta | Se pueden crear directivas del mismo inquilino.<br /><br /> Se pueden crear directivas entre inquilinos. | No se pueden crear directivas entre inquilinos ni en el mismo inquilino. Se produce un error porque Azure Storage no puede comprobar que las cuentas de origen y destino están en el mismo inquilino. El error indica que debe especificar el identificador de recurso completo para las entradas **sourceAccount** y **destinationAccount** en el archivo de definición de directiva. |

### <a name="specify-the-policy-and-rule-ids"></a>Especificación de los identificadores de directiva y regla

En la tabla siguiente se resumen los valores que se deben usar para las entradas **policyId** y **ruleId** en el archivo JSON en cada escenario.

| Cuando cree el archivo de definición de directiva para esta cuenta... | Establezca el id. de la directiva en este valor | Establezca los id. de regla en este valor |
|-|-|-|
| Cuenta de destino | Valor de cadena *predeterminado*. Azure Storage creará el id. de directiva automáticamente. | Una cadena vacía. Azure Storage creará los valores de id. de regla automáticamente. |
| Cuenta de origen | El valor del identificador de directiva devuelto al descargar el archivo de definición de directiva para la cuenta de destino. | Los valores de los identificadores de reglas devueltos al descargar el archivo de definición de directiva para la cuenta de destino. |

## <a name="prevent-replication-across-azure-ad-tenants"></a>Impedir la replicación entre inquilinos de Azure AD

Un inquilino de Azure Active Directory es una instancia dedicada de Azure AD que representa a una organización para la administración de identidades y acceso. Cada suscripción de Azure tiene una relación de confianza con un inquilino de Azure AD. Todos los recursos de una suscripción, incluidas las cuentas de almacenamiento, están asociados al mismo inquilino de Azure AD. Para más información, consulte [¿Qué es Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

De forma predeterminada, un usuario con los permisos adecuados puede configurar la replicación de objetos con una cuenta de almacenamiento de origen que se encuentre en un inquilino de Azure AD y una cuenta de destino que se encuentre en otro inquilino. Si las directivas de seguridad requieren que limite la replicación de objetos a cuentas de almacenamiento que residan solo en el mismo inquilino, puede no permitir la replicación entre inquilinos estableciendo una propiedad de seguridad denominada **AllowCrossTenantReplication** (versión preliminar). Cuando no se permite la replicación de objetos entre inquilinos para una cuenta de almacenamiento, para cualquier directiva de replicación de objetos configurada con esa cuenta de almacenamiento como cuenta de origen o destino, Azure Storage requiere que las cuentas de origen y de destino residan en el mismo inquilino de Azure AD. Para más información sobre cómo no permitir la replicación de objetos entre inquilinos, consulte [Impedir la replicación de objetos entre inquilinos de Azure Active Directory](object-replication-prevent-cross-tenant-policies.md).

Para no permitir la replicación de objetos entre inquilinos para una cuenta de almacenamiento, establezca la propiedad **AllowCrossTenantReplication** en *false*. Si la cuenta de almacenamiento no participa actualmente en ninguna directiva de replicación de objetos entre inquilinos, establecer la propiedad **AllowCrossTenantReplication** en *false* impide la configuración futura de directivas de replicación de objetos entre inquilinos con esta cuenta de almacenamiento como origen o destino.

Si la cuenta de almacenamiento participa actualmente en una o varias directivas de replicación de objetos entre inquilinos, no se permite establecer la propiedad **AllowCrossTenantReplication** en *false*. Debe eliminar las directivas entre inquilinos existentes para poder no permitir la replicación entre inquilinos.

De forma predeterminada, la propiedad **AllowCrossTenantReplication** no está establecida para una cuenta de almacenamiento y su valor es *null*, lo cual equivale a *true*. Cuando el valor de la propiedad **AllowCrossTenantReplication** de una cuenta de almacenamiento es *null* o *true*, los usuarios autorizados pueden configurar directivas de replicación de objetos entre inquilinos con esta cuenta como origen o destino. Para más información sobre cómo configurar directivas entre inquilinos, consulte [Configuración de la replicación de objetos para blobs en bloques](object-replication-configure.md).

Puede usar Azure Policy para auditar un conjunto de cuentas de almacenamiento para asegurarse de que la propiedad **AllowCrossTenantReplication** esté establecida para evitar la replicación de objetos entre inquilinos. También puede usar Azure Policy para aplicar la gobernanza para un conjunto de cuentas de almacenamiento. Por ejemplo, puede crear una directiva con el efecto deny para impedir que un usuario cree una cuenta de almacenamiento donde la propiedad **AllowCrossTenantReplication** esté establecida en *true*, o modificar una cuenta de almacenamiento existente para cambiar el valor de la propiedad a *true*.

## <a name="replication-status"></a>Estado de replicación

Puede comprobar el estado de replicación de un blob en la cuenta de origen. Para más información, consulte el apartado [Comprobación del estado de replicación de un blob](object-replication-configure.md#check-the-replication-status-of-a-blob).

Si el estado de replicación de un blob en la cuenta de origen indica un error, investigue las posibles causas que se mencionan a continuación:

- Asegúrese de que la directiva de replicación de objetos está configurada en la cuenta de destino.
- Compruebe que el contenedor de destino aún existe.
- Si el blob de origen se cifró con una clave proporcionada por el cliente como parte de una operación de escritura, se producirá un error en la replicación de objetos. Para más información sobre las claves proporcionadas por el cliente, consulte [Especificación de una clave de cifrado en una solicitud a Blob Storage](encryption-customer-provided-keys.md).

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades.

| Tipo de cuenta de almacenamiento                | Blob Storage (compatibilidad predeterminada)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) |![No](../media/icons/no-icon.png)              | ![No](../media/icons/no-icon.png) |
| Blobs en bloques Premium          | ![Sí](../media/icons/yes-icon.png) |![No](../media/icons/no-icon.png)              | ![No](../media/icons/no-icon.png) |

<sup>1</sup> Tanto Data Lake Storage Gen2 como el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

## <a name="billing"></a>Facturación

La replicación de objetos incurre en costos adicionales por las transacciones de lectura y escritura en las cuentas de origen y de destino, así como los cargos de salida por la replicación de datos de la cuenta de origen a la cuenta de destino y cargos de lectura para procesar la fuente de cambios.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la replicación de objetos](object-replication-configure.md)
- [Impedir la replicación de objetos entre inquilinos de Azure Active Directory](object-replication-prevent-cross-tenant-policies.md)
- [Control de versiones de blobs](versioning-overview.md)
- [Compatibilidad con la fuente de cambios en Azure Blob Storage (versión preliminar)](storage-blob-change-feed.md)
