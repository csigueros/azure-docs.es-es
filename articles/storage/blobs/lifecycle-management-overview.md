---
title: Optimizar los costes mediante la administración automática del ciclo de vida de los datos
titleSuffix: Azure Storage
description: Use directivas de administración del ciclo de vida de Azure Storage para crear reglas automatizadas para mover datos entre los niveles de acceso frecuente, esporádico o de archivo.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 1f7b4152bee090e39c598b559ffa9d2e8aea8e88
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477751"
---
# <a name="optimize-costs-by-automatically-managing-the-data-lifecycle"></a>Optimizar los costes mediante la administración automática del ciclo de vida de los datos

Los conjuntos de datos tienen ciclos de vida únicos. Al principio del ciclo de vida, las personas acceden con frecuencia a algunos datos. Pero la necesidad de acceso suele descender drásticamente a medida que los datos se hacen más antiguos. Algunos datos permanecen inactivos en la nube y, una vez almacenados, no se suele acceder a ellos. Algunos conjuntos de datos expiran días o meses después de su creación, mientras que otros conjuntos de datos se leen y modifican de forma activa en el transcurso de sus ciclos de vida. La administración del ciclo de vida de Azure Storage ofrece una directiva basada en reglas que se puede usar para trasladar los datos de blob al nivel de acceso adecuado y para hacer que los datos expiren cuando finalice su ciclo de vida.

La directiva de administración del ciclo de vida permite hacer lo siguiente:

- Pasar blobs de nivel de acceso esporádico a nivel de acceso frecuente en el mismo instante en que se accede a ellos para optimizar el rendimiento.
- Pasar blobs, versiones de blobs e instantáneas de blobs a un nivel de almacenamiento de acceso esporádico si no se accede a estos objetos o si se modifican durante un período de tiempo para optimizar el coste. En este escenario, la directiva de administración del ciclo de vida puede mover objetos del nivel de acceso frecuente al esporádico o de archivo y, asimismo, del nivel de acceso esporádico al de archivo.
- Eliminar blobs, versiones de blobs e instantáneas de blobs al final de su ciclo de vida.
- Definir reglas que se ejecutarán una vez al día en el nivel de cuenta de almacenamiento
- Aplicar reglas a contenedores o a un subconjunto de blobs mediante prefijos de nombre o [etiquetas de índice de blobs](storage-manage-find-blobs.md) como filtros.

Pensemos en un escenario donde se accede frecuentemente a los datos durante las primeras fases del ciclo de vida, pero solo ocasionalmente al cabo de dos semanas. Transcurrido el primer mes, rara vez se accede al conjunto de datos. En este escenario, es mejor el almacenamiento de acceso frecuente durante las primeras etapas. El almacenamiento de acceso esporádico es más adecuado para un acceso ocasional. El almacenamiento de archivo es la mejor opción de nivel una vez que los datos tengan un mes. Al mover datos al nivel de almacenamiento adecuado en función de su antigüedad mediante las reglas de directivas de administración del ciclo de vida, se puede diseñar la solución más asequible para sus necesidades.

Se pueden usar directivas de administración del ciclo de vida con blobs en bloques y blobs en anexos en cuentas de uso general v2, en cuentas de almacenamiento Premium de blobs en bloques y en cuentas de Blob Storage. La administración del ciclo de vida no afecta a los contenedores del sistema como *$logs* o *$web*.

> [!IMPORTANT]
> Si un conjunto de datos debe ser legible, no establezca una directiva para mover blobs al nivel de archivo, ya que los blobs en este nivel no se pueden leer a menos que se rehidraten antes, un proceso que puede conllevar mucho tiempo y dinero. Para más información, consulte [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).

## <a name="lifecycle-management-policy-definition"></a>Definición de la directiva de administración del ciclo de vida

Una directiva de administración del ciclo de vida es una colección de reglas en un documento JSON. En el siguiente JSON de ejemplo se muestra una definición de regla completa:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Una directiva es una colección de reglas, como se describe en la siguiente tabla:

| Nombre de parámetro | Tipo de parámetro | Notas |
|----------------|----------------|-------|
| `rules`        | Una matriz de objetos de regla | Se requiere al menos una regla en una directiva. Puede definir hasta 100 reglas en una directiva.|

Cada regla de la directiva tiene varios parámetros, descritos en la siguiente tabla:

| Nombre de parámetro | Tipo de parámetro | Notas | Obligatorio |
|--|--|--|--|
| `name` | String | Un nombre de regla puede incluir hasta 256 caracteres alfanuméricos. El nombre de regla distingue mayúsculas de minúsculas. Debe ser único dentro de una directiva. | True |
| `enabled` | Boolean | Un valor booleano opcional para permitir que una regla se deshabilite de forma temporal. El valor predeterminado es true si no se establece. | False |
| `type` | Un valor de enumeración | El tipo actual válido es `Lifecycle`. | True |
| `definition` | Un objeto que define la regla del ciclo de vida | Cada definición se compone de un conjunto de filtros y un conjunto de acciones. | True |

## <a name="lifecycle-management-rule-definition"></a>Definición de regla de administración del ciclo de vida

Cada definición de regla incluye un conjunto de filtros y un conjunto de acciones. El [conjunto de filtros](#rule-filters) limita las acciones de regla a un determinado conjunto de objetos dentro de un contenedor o nombres de objetos. El [conjunto de acciones ](#rule-actions) aplica las acciones de nivel o eliminación al conjunto filtrado de objetos.

### <a name="sample-rule"></a>Ejemplo de regla

La siguiente regla de ejemplo filtra la cuenta para ejecutar las acciones en objetos que existen dentro de `sample-container` y empiezan por `blob1`.

- Establecer el nivel de blob en nivel esporádico 30 días después de la última modificación
- Establecer el nivel de blob en nivel de almacenamiento de archivo 90 días después de la última modificación
- Eliminar el blob 2555 días (siete años) después de la última modificación
- Eliminar las versiones de blobs anteriores 90 días después de su creación

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "sample-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "sample-container/blob1"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtros de reglas

Los filtros limitan las acciones de regla a un subconjunto de blobs dentro de la cuenta de almacenamiento. Si se define más de un filtro, se ejecuta un valor lógico `AND` en todos los filtros.

Entre los filtros están los siguientes:

| Nombre de filtro | Tipo de filtro | Notas | Es obligatorio |
|-------------|-------------|-------|-------------|
| blobTypes   | Una matriz de valores de enumeración predefinidos. | La versión actual admite `blockBlob` y `appendBlob`. En `appendBlob` solo se puede eliminar, no se puede establecer el nivel. | Sí |
| prefixMatch | Una matriz de cadenas de prefijos con los que debe hacer coincidencias. Cada regla puede definir hasta 10 prefijos (con distinción entre mayúsculas y minúsculas). Una cadena de prefijos debe comenzar con el nombre de un contenedor. Por ejemplo, si quiere que todos los blobs de `https://myaccount.blob.core.windows.net/sample-container/blob1/...` coincidan en una regla, prefixMatch es `sample-container/blob1`. | Si no define prefixMatch, la regla se aplica a todos los blobs de la cuenta de almacenamiento. | No |
| blobIndexMatch | Una matriz de valores de diccionario que se compone de las condiciones de clave y valor de la etiqueta de índice de blobs con las que debe haber coincidencias. Cada regla puede definir hasta 10 condiciones de etiqueta de índice de blobs. Por ejemplo, si quiere que todos los blobs coincidan con `Project = Contoso` en `https://myaccount.blob.core.windows.net/` en relación a una regla, el valor de blobIndexMatch es `{"name": "Project","op": "==","value": "Contoso"}`. | Si no define blobIndexMatch, la regla se aplica a todos los blobs de la cuenta de almacenamiento. | No |

Para obtener más información sobre la característica de índice de blobs, así como sus limitaciones y problemas conocidos, consulte [Administración y búsqueda de datos en Azure Blob Storage con el índice de blobs](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Acciones de regla

Las acciones se aplican a los blobs filtrados cuando se cumple la condición de ejecución.

La administración del ciclo de vida admite tanto la organización en niveles como la eliminación de blobs, versiones anteriores de los blobs e instantáneas de blobs. Defina al menos una acción para cada regla en los blobs de base, las versiones anteriores de los blobs o las instantáneas de los blobs.

| Acción                      | Blob de base                                  | Instantánea      | Versión
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Se admite para `blockBlob`                  | Compatible     | Compatible     |
| enableAutoTierToHotFromCool | Se admite para `blockBlob`                  | No compatible | No compatible |
| tierToArchive               | Se admite para `blockBlob`                  | Compatible     | Compatible     |
| delete                      | Compatible en `blockBlob` y `appendBlob`. | Compatible     | Compatible     |

>[!NOTE]
>Si define más de una acción en el mismo blob, la administración del ciclo de vida aplica la acción menos cara al blob. Por ejemplo, la acción `delete` es más económica que la acción `tierToArchive`. La acción `tierToArchive` es más económica que la acción `tierToCool`.

Las condiciones de ejecución se basan en la antigüedad. Para realizar el seguimiento de la antigüedad, los blobs de base usan la hora de la última modificación, las versiones de los blobs usan la hora de creación de la versión y las instantáneas de los blobs usan la hora de creación de la instantánea.

| Condición de ejecución de acción | Valor de la condición | Descripción |
|--|--|--|
| daysAfterModificationGreaterThan | Valor entero que indica la antigüedad en días | Condición de las acciones de blob de base |
| daysAfterCreationGreaterThan | Valor entero que indica la antigüedad en días | La condición de la versión del blob y de las acciones de la instantánea del blob |
| daysAfterLastAccessTimeGreaterThan | Valor entero que indica la antigüedad en días | Condición de las acciones del blob base cuando el seguimiento de acceso está habilitado |

## <a name="examples-of-lifecycle-policies"></a>Ejemplos de directivas de ciclo de vida

Los ejemplos siguientes muestran cómo abordar escenarios comunes con las reglas de directivas del ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Cambio de los datos antiguos a un nivel de acceso más esporádico

En este ejemplo se muestra cómo realizar la transición de blobs en bloques con el prefijo `sample-container/blob1` o `container2/blob2`. La directiva realiza la transición de los blobs que no se han modificado durante más de 30 días al almacenamiento de acceso esporádico, y los blobs no modificados en 90 días al nivel de acceso de archivo:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "sample-container/blob1", "container2/blob2" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-time"></a>Mover datos en función de la hora de último acceso

Se puede habilitar el seguimiento de la hora de último acceso para mantener un registro de cuándo se lee o escribe por última vez el blob y también como filtro para administrar los niveles y la retención de los datos de blob. Para obtener información sobre cómo habilitar el seguimiento de la hora de último acceso, consulte [Habilitar el seguimiento de hora de acceso opcionalmente](lifecycle-management-policy-configure.md#optionally-enable-access-time-tracking).

Si se habilita el seguimiento de la hora del último acceso, la propiedad de blob denominada `LastAccessTime` se actualiza cuando se lee o se escribe en un blob. Una operación [Get Blob](/rest/api/storageservices/get-blob) se considera una operación de acceso. [Get Blob Properties](/rest/api/storageservices/get-blob-properties), [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata) y [Get Blob Tags](/rest/api/storageservices/get-blob-tags) no son operaciones de acceso y, por lo tanto, no actualizan la hora del último acceso.

Para reducir el impacto en la latencia del acceso de lectura, solo la primera lectura de las últimas 24 horas actualiza la hora del último acceso. Las lecturas posteriores en el mismo período de 24 horas no la actualizan. Si se modifica un blob entre lecturas, la hora del último acceso es la más reciente de los dos valores.

En el siguiente ejemplo, los blobs se mueven al almacenamiento esporádico si no se ha accedido a ellos durante 30 días. La propiedad `enableAutoTierToHotFromCool` es un valor booleano que indica si un blob debe pasar automáticamente del nivel de acceso esporádico al nivel de acceso frecuente si se vuelve a acceder a él una vez que se haya pasado al nivel de acceso esporádico.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

### <a name="archive-data-after-ingest"></a>Archivado de datos después de la ingesta

Algunos datos permanecen inactivos en la nube y no se accede a ellos prácticamente nunca. La siguiente directiva del ciclo de vida está configurada para archivar los datos poco después de que se ingieran. En este ejemplo se realiza la transición de blobs en bloques de un contenedor denominado `archivecontainer` a un nivel de archivo. La transición se realiza al actuar en los blobs 0 días después de la hora de la última modificación:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

> [!NOTE]
> Microsoft recomienda cargar los blobs directamente en el nivel de archivo para lograr una mayor eficacia. El nivel de archivo se puede especificar en el encabezado *x-ms-access-tier* de las operaciones [Put Blob](/rest/api/storageservices/put-blob) o [Put Block List](/rest/api/storageservices/put-block-list). El encabezado *x-ms-access-tier* se puede usar con la versión de REST 2018-11-09 y versiones más recientes o con las bibliotecas cliente de Blob Storage más recientes.

### <a name="expire-data-based-on-age"></a>Expiración de datos en función de la antigüedad

Se espera que algunos datos expiren días o meses después de la creación. Puede configurar una directiva de administración del ciclo de vida para que los datos expiren mediante eliminación en función de su antigüedad. En el ejemplo siguiente se muestra una directiva que elimina todos los blobs en bloques con una antigüedad superior a 365 días.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Eliminar datos con etiquetas de índice de blobs

Algunos datos solo deben expirar si se marcan explícitamente para su eliminación. Puede configurar una directiva de administración del ciclo de vida para que expiren los datos etiquetados con los atributos de clave/valor del índice de blobs. En el ejemplo siguiente se muestra una directiva que elimina todos los blobs en bloques con `Project = Contoso`. Para obtener más información sobre el índice de blobs, consulte [Administración y búsqueda de datos en Azure Blob Storage con el Índice de blobs](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Administración de versiones

En el caso de datos que se modifican y a los que se accede de forma regular a lo largo de toda su duración, puede habilitar el control de versiones de Blob Storage para mantener de forma automática las versiones anteriores de un objeto. Puede crear una directiva para organizar en niveles o eliminar las versiones anteriores. La antigüedad de la versión se determina mediante la evaluación de la hora de creación de la misma. Esta regla de directiva crea niveles de las versiones anteriores en el contenedor `activedata` que sean 90 días, o más, posteriores a la creación de la versión en el nivel de acceso esporádico, y elimina las versiones anteriores que tengan 365 días, o más.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades. 

| Tipo de cuenta de almacenamiento                | Blob Storage (compatibilidad predeterminada)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) |![Sí](../media/icons/yes-icon.png)              | ![Sí](../media/icons/yes-icon.png) | 
| Blobs en bloques Premium          | ![Sí](../media/icons/yes-icon.png)|![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |

<sup>1</sup> Tanto Data Lake Storage Gen2 como el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

## <a name="regional-availability-and-pricing"></a>Disponibilidad regional y precios

La característica de administración del ciclo de vida está disponible en todas las regiones de Azure.

Las directivas de administración del ciclo de vida son gratuitas. A los clientes se les cobra el coste operativo estándar derivado de las llamadas API [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Las operaciones de eliminación también son gratuitas.

Cada actualización a la hora de último acceso de un blob se factura bajo la categoría [Todas las demás operaciones](https://azure.microsoft.com/pricing/details/storage/blobs/).

Para más información sobre los precios, consulte [Precios de los blobs en bloques](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Preguntas más frecuentes

**He creado una directiva, ¿por qué las acciones no se ejecutan inmediatamente?**

La plataforma ejecuta la directiva del ciclo de vida una vez al día. Una vez que configure una directiva, algunas acciones pueden tardar hasta 24 horas en ejecutarse por primera vez.

**Si actualizo una directiva existente, ¿cuánto tiempo tardan en ejecutarse las acciones?**

La directiva actualizada tarda hasta 24 horas en entrar en vigor. Una vez que la directiva está en vigor, las acciones pueden tardar hasta 24 horas en ejecutarse. Por lo tanto, las acciones de la directiva pueden tardar hasta 48 horas en completarse. Si la actualización va a deshabilitar o eliminar una regla y se ha usado enableAutoTierToHotFromCool, se seguirán haciendo niveles automáticos en el nivel de acceso de acceso frecuente. Por ejemplo, establezca una regla que incluya enableAutoTierToHotFromCool en función del último acceso. Si la regla está deshabilitada o eliminada, y un blob se encuentra actualmente en estado de nivel de acceso esporádico y, después, se accede a él, volverá al nivel de acceso frecuente, que es el que se aplica en el acceso fuera de la administración del ciclo de vida. Luego, el blob no pasará de nivel de acceso frecuente a nivel de acceso esporádico, ya que la regla de administración del ciclo de vida está deshabilitada o eliminada.  La única manera de evitar autoTierToHotFromCool es desactivar el seguimiento de la hora del último acceso.

**He rehidratado manualmente un blob archivado, ¿cómo evito que vuelva temporalmente al nivel de archivo?**

Cuando se mueve un blob desde un nivel de acceso a otro, su hora de última modificación no cambia. Si rehidrata manualmente un blob archivado al nivel de acceso frecuente, el motor de administración del ciclo de vida podría devolverlo al nivel de archivo. Deshabilite la regla que afecte temporalmente a este blob para impedir que se vuelva a archivar. Vuelva a habilitar la regla cuando el blob se pueda volver a mover con seguridad al nivel de archivo. También puede copiar el blob en otra ubicación si necesita permanecer en el nivel de acceso frecuente o esporádico de forma permanente.

## <a name="next-steps"></a>Pasos siguientes

- [Configurar una directiva de administración del ciclo de vida](lifecycle-management-policy-configure.md)
- [Niveles de acceso de Azure Blob Storage: frecuente, esporádico y archivo](storage-blob-storage-tiers.md)
- [Administración y búsqueda de datos en Azure Blob Storage con el Índice de blobs](storage-manage-find-blobs.md)
