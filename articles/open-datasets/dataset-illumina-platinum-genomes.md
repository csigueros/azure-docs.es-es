---
title: Illumina Platinum Genomes
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos Illumina Platinum Genomes en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0a1b9d5a41a0dcc45bea0456c2c3714b787a5af8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284234"
---
# <a name="illumina-platinum-genomes"></a>Illumina Platinum Genomes

La secuenciación del genoma completo está permitiendo a investigadores de todo el mundo caracterizar el genoma humano de una forma más completa y precisa. Esto requiere un catálogo completo de todo el genoma de variantes de alta confianza asignadas en un conjunto de genomas para usarlo como punto de referencia. Illumina ha generado datos profundos de secuencia de todo el genoma de 17 individuos con un pedigrí de tres generaciones. Illumina ha asignado las variantes de cada genoma mediante una serie de algoritmos disponibles actualmente.

Para obtener más información sobre los datos, vea el [sitio oficial de Illumina](https://www.illumina.com/platinumgenomes.html).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Origen de datos

Este conjunto de datos es un reflejo de ftp://ussd-ftp.illumina.com/

## <a name="data-volumes-and-update-frequency"></a>Volúmenes de datos y frecuencia de actualización

Este conjunto de datos contiene unos 2 GB de datos y se actualiza a diario.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en las regiones Oeste de EE. UU. 2 y Centro-oeste de EE. UU. de Azure. Por afinidad, se recomienda asignar recursos de proceso de Oeste de EE. UU. 2 o Centro-oeste de EE. UU.

## <a name="data-access"></a>Acceso a datos

Oeste de EE. UU. 2: "https://datasetplatinumgenomes.blob.core.windows.net/dataset"

Centro-oeste de EE. UU.: "https://datasetplatinumgenomes-secondary.blob.core.windows.net/dataset"

[Token de SAS](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D

## <a name="use-terms"></a>Términos de uso

Los datos están disponibles sin restricciones. Para obtener más información y detalles de las citas, vea el [sitio oficial de Illumina](https://www.illumina.com/platinumgenomes.html).

## <a name="contact"></a>Contacto

Si tiene alguna pregunta o comentario sobre el conjunto de datos, póngase en contacto con platinumgenomes@illumina.com.

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes -->

> [!TIP]
> **[Descargue el conjunto de datos en su lugar](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes)** .

## <a name="getting-the-illumina-platinum-genomes-from-azure-open-datasets-and-doing-initial-analysis"></a>Obtención de Illumina Platinum Genomes desde Azure Open Datasets y análisis iniciales 

Use cuadernos de Jupyter, GATK y Picard para hacer lo siguiente:

1. Anotar genotipos mediante VariantFiltration
2. Seleccionar variantes concretas
3. Filtrar las variantes relevantes: sin asignaciones O regiones específicas
4. Realizar análisis de concordancia
5. Convertir los archivos VCF finales en una tabla 

**Dependencias:**

Este cuaderno necesita las siguientes bibliotecas:

- Azure Storage `pip install azure-storage-blob`

- Numpy `pip install numpy`

- Genome Analysis Toolkit (GATK) (*Los usuarios deben descargar GATK desde la página web de Broad Institute en el mismo entorno de proceso que este cuaderno: https://github.com/broadinstitute/gatk/releases* )

**Información importante: Este cuaderno usa el kernel de Python 3.6**

## <a name="getting-the-genomics-data-from-azure-open-datasets"></a>Obtención de los datos de genómica desde Azure Open Datasets

Varios datos de genómica públicos se han cargado como un conjunto de datos de Azure Open Datasets [aquí](https://azure.microsoft.com/services/open-datasets/catalog/). Creamos un servicio de blob vinculado a este conjunto de datos abierto. Puede encontrar ejemplos del procedimiento de asignación de datos de Azure Open Dataset para conjuntos de datos `Illumina Platinum Genomes` a continuación:

### <a name="downloading-the-specific-illumina-platinum-genomes"></a>Descarga de "Illumina Platinum Genomes"

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetplatinumgenomes', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D')     
blob_service_client.get_blob_to_path('dataset/2017-1.0/hg38/small_variants/NA12877', 'NA12877.vcf.gz', './NA12877.vcf.gz')
```

### <a name="1-annotate-genotypes-using-variantfiltration"></a>1. Anotación de genotipos mediante VariantFiltration

**Nota importante: Compruebe que GATK se está ejecutando en el sistema.**

Si se quiere filtrar genotipos heterocigóticos, se usa la opción `--genotype-filter-expression isHet == 1` de VariantFiltration. Es posible especificar el valor de anotación de la herramienta para etiquetar los genotipos heterocigóticos con la opción `--genotype-filter-name`. En este caso, el valor de este parámetro se establece en `isHetFilter`. En el primer ejemplo se ha usado `NA12877.vcf.gz` de Illimina Platinum Genomes, pero los usuarios pueden usar cualquier archivo VCF de otros conjuntos de datos: `Platinum Genomes`

```python
run gatk VariantFiltration -V NA12877.vcf.gz -O outputannot.vcf --genotype-filter-expression "isHet == 1" --genotype-filter-name "isHetFilter"
```

### <a name="2-select-specific-variants"></a>2. Selección de variantes concretas

Seleccione un subconjunto de variantes de un archivo VCF. Esta herramienta permite seleccionar un subconjunto de variantes en función de varios criterios para facilitar determinados análisis. Entre los ejemplos de estos análisis se incluyen la comparación y el contraste de casos frente a controles, la extracción de loci de variantes o no variantes que cumplen ciertos requisitos, o la solución de problemas de algunos resultados inesperados, por decir algunos.

Hay muchas opciones diferentes para seleccionar subconjuntos de variantes de un conjunto de asignaciones mayor:

Extraiga uno o varios ejemplos de un conjunto de asignaciones en función de un nombre de ejemplo completo o una coincidencia de patrón.
Especifique criterios de inclusión que coloquen umbrales en los valores de anotación, **por ejemplo, "DP > 1000" (profundidad de cobertura superior a 1000 veces), "AF < 0,25" (sitios con una frecuencia alélica inferior a 0,25)** . Estos criterios se escriben como "expresiones JEXL", que se documentan en el artículo sobre el uso de expresiones JEXL.
Proporcione pistas de concordancia o discrepancia para incluir o excluir variantes que también estén presentes en otros conjuntos de asignaciones determinados.
Seleccione variantes en función de criterios como su tipo (por ejemplo, solo indeles), evidencia de infracción mendeliana, estado de filtrado, alelicidad, etc. También hay varias opciones para registrar los valores originales de determinadas anotaciones, que se recalculan cuando uno fracciona el nuevo conjunto de asignaciones, recorta alelos, etc.

Entrada: un conjunto de asignaciones de variantes en formato VCF a partir del que se puede seleccionar un subconjunto.

Salida: un nuevo archivo VCF que contiene el subconjunto seleccionado de variantes.

```python
run gatk SelectVariants -R Homo_sapiens_assembly38.fasta -V outputannot.vcf --select-type-to-include SNP --select-type-to-include INDEL -O selective.vcf
```

### <a name="3-transform-filtered-genotypes-to-no-call"></a>3. Transformación de genotipos filtrados en no call 

La ejecución de SelectVariants con --set-filtered-gt-to-nocall transforma aún más los genotipos marcados con una asignación de genotipo nula. 

Esta conversión es necesaria porque las herramientas de nivel inferior no analizan el campo de filtro de nivel FORMAT.

Cómo se pueden filtrar las variantes con **"No call"**

```python
run gatk SelectVariants -V outputannot.vcf --set-filtered-gt-to-nocall -O outputnocall.vcf
```

### <a name="4-check-the-concordance-of-vcf-file-with-ground-truth"></a>4. Comprobación de la concordancia del archivo VCF con la verdad fundamental

Evalúe la concordancia de nivel de sitio de un VCF de entrada con respecto a truth VCF.
Esta herramienta compara dos conjuntos de asignaciones de variantes y genera una tabla de métricas de resumen de seis columnas. 

**Esta función hace lo siguiente:**

1. Estratifica asignaciones de SNP e indeles
2. Notifica asignaciones verdaderas positivas, falsas positivas y falsas negativas
3. Calcula la sensibilidad y la precisión

La herramienta supone que todos los registros de --truth VCF están pasando variantes de verdad. En el caso de -eval VCF, la herramienta solo usa asignaciones de paso sin filtrar.

Opcionalmente, la herramienta se puede establecer para generar VCF de los siguientes registros de variantes, anotados con el estado de concordancia de cada variante:

Verdaderos positivos y falsos negativos (es decir, todas las variantes de truth VCF): de utilidad para calcular la sensibilidad

Verdaderos positivos y falsos positivos (es decir, todas las variantes de eval VCF): de utilidad para obtener un conjunto de datos de aprendizaje para clasificadores de aprendizaje automático de artefactos

**Estos VCF de salida se pueden pasar a VariantsToTable para generar un archivo TSV para el análisis estadístico en R o Python.**

```python
 run gatk Concordance -R Homo_sapiens_assembly38.fasta -eval outputannot.vcf --truth outputnocall.vcf  --summary summary.tsv 
```

### <a name="5-variantstotable"></a>5. VariantsToTable

Extraiga campos de un archivo VCF en una tabla delimitada por tabulaciones. Esta herramienta extrae campos especificados de cada variante de un archivo VCF en una tabla delimitada por tabulaciones, con la que puede ser más fácil trabajar que con un VCF. De manera predeterminada, la herramienta solo extrae PASS o variantes (sin filtrar) del archivo VCF. Las variantes filtradas se pueden incluir en la salida si se agrega la marca --show-filtered. La herramienta puede extraer campos INFO (es decir, de nivel de sitio) y FORMAT (es decir, de nivel de ejemplo).

Campos de nivel de sitio o INFO:

Use el argumento `-F` para extraer campos INFO; cada campo ocupa una sola columna del archivo de salida. El campo puede ser cualquier columna de VCF estándar (por ejemplo, CHROM, ID, QUAL) o cualquier nombre de anotación del campo INFO (por ejemplo, AC, AF). La herramienta además admite los siguientes campos:

EVENTLENGTH (longitud del evento) TRANSITION (1 para una transición bialélica (SNP), 0 para transversión bialélica (SNP), -1 para indeles y multialélicos) HET (recuento de genotipos het) HOM-REF (recuento de genotipos de referencia homocigóticos) HOM-VAR (recuento de genotipos de variante homocigóticos) NO-CALL (recuento de genotipos sin asignación) TYPE (tipo de variante, los posibles valores son NO_VARIATION, SNP, MNP, INDEL, SYMBOLIC y MIXED VAR (recuento de genotipos sin referencia) NSAMPLES (número de ejemplos) NCALLED (número de ejemplos asignados) MULTI-ALLELIC (¿esta variante es multialélica? verdadero o falso)

Campos de nivel de ejemplo o FORMAT:

Use el argumento `-GF` para extraer campos de nivel de ejemplo o FORMAT. La herramienta crea una nueva columna por ejemplo con el nombre "SAMPLE_NAME. FORMAT_FIELD_NAME", por ejemplo, NA12877.GQ, NA12878.GQ.

Entrada:

Un archivo VCF que se va a convertir en una tabla

Salida:

Un archivo delimitado por tabulaciones que contiene los valores de los campos solicitados del archivo VCF.

```python
run gatk VariantsToTable -V NA12877.vcf.gz -F CHROM -F POS -F TYPE -F AC -F AD -F AF -GF DP -GF AD -O outputtable.table
```

## <a name="references"></a>Referencias

1. VariantFiltration: https://gatk.broadinstitute.org/hc/en-us/articles/360036827111-VariantFiltration 
2. SelectVariants: https://gatk.broadinstitute.org/hc/en-us/articles/360037052272-SelectVariants
3. Concordancia: https://gatk.broadinstitute.org/hc/en-us/articles/360041851651-Concordance
4. VariantsToTable: https://gatk.broadinstitute.org/hc/en-us/articles/360036882811-VariantsToTable 
5. Illumina Platinum Genomes: https://www.illumina.com/platinumgenomes.html 

<!-- nbend -->

---

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).