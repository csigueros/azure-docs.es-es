---
title: Descripción de los conjuntos de recursos
description: En este artículo se explica qué son los conjuntos de recursos y cómo los crea Azure Purview.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: d1d15fb4ff3bc2d820311b4f847c21236d83b6f3
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708769"
---
# <a name="understanding-resource-sets"></a>Descripción de los conjuntos de recursos

Este artículo le ayuda a entender cómo Azure Purview usa conjuntos de recursos para asignar recursos de datos a recursos lógicos.
## <a name="background-info"></a>Información de contexto

Los sistemas de procesamiento de datos a escala normalmente almacenan una sola tabla en un almacenamiento en varios archivos. En el catálogo de datos de Azure Purview, este concepto se representa mediante conjuntos de recursos. Un conjunto de recursos es un único objeto del catálogo que representa una gran cantidad de recursos en almacenamiento.

Por ejemplo, supongamos que el clúster de Spark ha conservado un dataframe en un origen de datos de Azure Data Lake Storage (ADLS) Gen2. Aunque en Spark la tabla parece un único recurso lógico, en el disco es probable que haya miles de archivos de Parquet, cada uno de los cuales representa una partición del contenido total de un dataframe. Los datos de IoT y los datos de registro web presentan el mismo reto. Imagine que tiene un sensor que genera archivos de registro varias veces por segundo. No tardará mucho en tener cientos de miles de archivos de registro de ese único sensor.

## <a name="how-azure-purview-detects-resource-sets"></a>Cómo detecta Azure Purview conjuntos de recursos

Azure Purview admite la detección de conjuntos de recursos en Azure Blob Storage, ADLS Gen1, ADLS Gen2, Azure Files y Amazon S3.

Azure Purview detecta automáticamente los conjuntos de recursos cuando se examinan. Esta característica examina todos los datos que se ingieren a través de un examen y los compara con un conjunto de patrones definidos.

Por ejemplo, supongamos que examina un origen de datos cuya dirección URL es `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet`. Azure Purview examina los segmentos de trazado y determina si coinciden con algún patrón integrado. Este servicio dispone de patrones integrados para GUID, números, formatos de fecha, códigos de localización (por ejemplo, en-US), etc. En este caso, el patrón de números coincide con *23*. Azure Purview presupone que este archivo forma parte de un conjunto de recursos denominado `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet`.

O bien, para una dirección URL como `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json`, Azure Purview hace coincidir el patrón de localización y el patrón de números, lo que genera un conjunto de recursos denominado `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`.

Con esta estrategia, Azure Purview asignaría los siguientes recursos al mismo conjunto de recursos, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

### <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipos de archivo que Azure Purview no detectará como conjuntos de recursos

Purview no intenta clasificar a propósito la mayoría de los tipos de archivo de documento, como Word, Excel o PDF, como conjuntos de recursos. La excepción es el formato CSV, ya que es un formato de archivo común con particiones.

## <a name="how-azure-purview-scans-resource-sets"></a>Cómo Azure Purview examina los conjuntos de recursos

Cuando Azure Purview detecta recursos que considera forman parte de un conjunto de recursos, cambia de un examen completo a un examen de muestra. Un examen de muestra abre solo un subconjunto de los archivos que considera que están en el conjunto de recursos. En cada archivo que abre, usa su esquema y ejecuta sus clasificadores. A continuación, Azure Purview busca el recurso más reciente entre los recursos abiertos y usa el esquema y las clasificaciones de ese recurso con el conjunto de recursos completo del catálogo en la entrada.

## <a name="advanced-resource-sets"></a>Conjuntos de recursos avanzados

De forma predeterminada, Azure Purview determina el esquema y las clasificaciones de los conjuntos de recursos en función de las [reglas de muestreo de archivos del conjunto de recursos](sources-and-scans.md#resource-set-file-sampling). Azure Purview puede personalizar y enriquecer aún más los recursos del conjunto de recursos a través de la funcionalidad **Conjuntos de recursos avanzados**. Cuando se habilitan los conjuntos de recursos avanzados, Azure Purview ejecuta agregaciones adicionales para calcular la siguiente información sobre los recursos del conjunto de recursos:

- El esquema y las clasificaciones más actualizados para reflejar con precisión el desfase de esquema de los cambiantes metadatos.
- Una ruta de acceso de ejemplo desde un archivo que compone el conjunto de recursos.
- Un número de particiones que muestra el número de archivos que forma el conjunto de recursos. 
- Un número de esquemas que muestra el número de esquemas únicos que se han encontrado. Este valor es un número entre 1 y 5 o valores mayores que 5, 5+.
- Una lista de tipos de partición cuando se incluye más de un único tipo de partición en el conjunto de recursos. Por ejemplo, un sensor de IoT puede generar archivos XML y JSON, aunque ambos forman parte del mismo conjunto de recursos de manera lógica.
- El tamaño total de todos los archivos que componen el conjunto de recursos. 

Estas propiedades se pueden encontrar en la página de detalles del recurso del conjunto de recursos.

:::image type="content" source="media/concept-resource-sets/resource-set-properties.png" alt-text="Las propiedades calculadas cuando los conjuntos de recursos avanzados están activos" border="true":::

La habilitación de conjuntos de recursos avanzados también permite la creación de [reglas de patrones de conjuntos de recursos](how-to-resource-set-pattern-rules.md) que personalizan la forma en que Azure Purview agrupa los conjuntos de recursos durante el examen. 

### <a name="turning-on-advanced-resource-sets"></a>Activación de conjuntos de recursos avanzados

Los conjuntos de recursos avanzados están desactivados de forma predeterminada en todas las instancias nuevas de Azure Purview. Los conjuntos de recursos avanzados se pueden habilitar desde la **información de la cuenta** en el centro de administración.

> [!NOTE]
> Todas las instancias de Purview creadas antes del 19 de agosto de 2021 tienen el conjunto de recursos avanzado activado de forma predeterminada.

:::image type="content" source="media/concept-resource-sets/advanced-resource-set-toggle.png" alt-text="Active el conjuntos de recursos avanzados." border="true":::

Después de habilitar los conjuntos de recursos avanzados, los enriquecimientos adicionales se producirán en todos los recursos recién ingeridos. El equipo de Azure Purview recomienda esperar una hora antes de examinar los nuevos datos del lago de datos después de activar la característica.

> [!IMPORTANT]
> La habilitación de conjuntos de recursos avanzados afectará a la tasa de actualización de la información de la clasificación y los recursos. Cuando los conjuntos de recursos avanzados están activos, la información de la clasificación y los recursos solo se actualizará dos veces al día.

## <a name="built-in-resource-set-patterns"></a>Patrones de conjuntos de recursos integrados

Azure Purview admite los siguientes patrones de conjunto de recursos. Estos patrones pueden aparecer como un nombre en un directorio o como parte de un nombre de archivo.
### <a name="regex-based-patterns"></a>Patrones basados en Regex

| Nombre del patrón | Display Name (Nombre para mostrar) | Descripción |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Identificador único global, como se define en [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Número       | {N}          | Uno o más dígitos. |
| Formatos de fecha y hora | {Año}{mes}{día}{N}     | Admitimos varios formatos de fecha y hora, pero todos se representan con {año}[delimitador]{mes}[delimitador]{día} o series de {N}. |
| 4ByteHex     | {HEX}        | Número hexadecimal de 4 dígitos. |
| Localización | {LOC}        | Una etiqueta de idioma tal y como se define en [BCP 47](https://tools.ietf.org/html/bcp47); se admiten los nombres con - y _ (por ejemplo, en_ca y en-ca). |

### <a name="complex-patterns"></a>Patrones complejos

| Nombre del patrón | Display Name (Nombre para mostrar) | Descripción |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identificador de archivo de partición de Spark |
| Date(yyyy/mm/dd)InPath  | {Año}/{mes}/{día} | Patrón de año/mes/día que abarca varias carpetas |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-data-catalog"></a>Cómo se muestran los conjuntos de recursos en el catálogo de datos de Azure Purview

Cuando Azure Purview coincide con un grupo de activos en un conjunto de recursos, intenta extraer la información más útil para usarla como nombre para mostrar en el catálogo. Algunos ejemplos de la convención de nomenclatura predeterminada aplicada: 

### <a name="example-1"></a>Ejemplo 1

Nombre completo: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nombre para mostrar: "name of spark output"

### <a name="example-2"></a>Ejemplo 2

Nombre completo: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nombre para mostrar: "my partitioned data"

### <a name="example-3"></a>Ejemplo 3

Nombre completo: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nombre para mostrar: "data"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Personalización de la agrupación de conjunto de recursos mediante reglas de patrón

Al analizar una cuenta de almacenamiento, Azure Purview usa un conjunto de patrones definidos para determinar si un grupo de recursos es un conjunto de recursos. En algunos casos, es posible que la agrupación de un conjunto de recursos de Azure Purview no refleje adecuadamente el estado de los datos. Entre los posibles problemas se pueden incluir:

- Marcar incorrectamente un recurso como un conjunto de recursos
- Colocar un recurso en un conjunto de recursos equivocado
- Marcar incorrectamente un recurso como que no es un conjunto de recursos

Para personalizar o invalidar la forma en que Azure Purview detecta qué recursos se agrupan como conjuntos de recursos, además de cómo se muestran dentro del catálogo, puede definir reglas de patrón en el centro de administración. Para obtener instrucciones paso a paso y sintaxis, consulte las [reglas de patrón del conjunto de recursos](how-to-resource-set-pattern-rules.md).
## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con Azure Purview, consulte [Inicio rápido: Creación de una cuenta de Azure Purview](create-catalog-portal.md).
