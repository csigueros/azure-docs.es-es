---
title: COVID-19 Open Research Dataset
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos COVID-19 Open Research Dataset en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1225cc9f88fb07766f8f6e89e5a2502bdb83653a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039200"
---
# <a name="covid-19-open-research-dataset"></a>COVID-19 Open Research Dataset

Conjunto de datos de texto completo y metadatos de COVID-19 y artículos académicos relacionados con el coronavirus optimizados para la legibilidad por máquina y disponibles para que los utilice la comunidad de investigación global.

En respuesta a la pandemia de la COVID-19, el [Instituto Para la Inteligencia Artificial](https://allenai.org/) se ha asociado con grupos de investigación líderes para preparar y distribuir el conjunto de datos COVID-19 Open Research Dataset (CORD-19). Este conjunto de datos es un recurso gratuito de más de 47 000 artículos académicos, incluidos más de 36 000 con texto completo, sobre la COVID-19 y la familia de los coronavirus para uso de la comunidad investigadora mundial.

Este conjunto de datos moviliza a los investigadores para que apliquen los avances recientes en el procesamiento del lenguaje natural para generar nuevas conclusiones en apoyo de la lucha contra esta enfermedad infecciosa.

El corpus se puede actualizar a medida que se publique una nueva investigación en publicaciones revisadas por expertos y servicios de archivo, como [bioRxiv](https://www.biorxiv.org/), [medRxiv](https://www.medrxiv.org/), etc.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="license-terms"></a>Términos de licencia

Este conjunto de datos está disponible en el Instituto Para la Inteligencia Artificial y el [Semantic Scholar](https://pages.semanticscholar.org/coronavirus-research). Al acceder, descargar o, de otro modo, usar cualquier contenido proporcionado en el conjunto de datos CORD-19, acepta la [licencia del conjunto de datos](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/COVID.DATA.LIC.AGMT.pdf) relacionada con el uso de este conjunto de datos. En el archivo de metadatos se encuentra disponible información de licencia específica para artículos individuales del conjunto de datos. Puede obtener más información sobre licencias en el los sitios web de [PMC](https://www.ncbi.nlm.nih.gov/pmc/tools/openftlist/), [medRxiv](https://www.medrxiv.org/submit-a-manuscript) y [bioRxiv](https://www.biorxiv.org/about-biorxiv).

## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato JSON y la versión más reciente contiene más de 36 000 artículos de texto completo. Cada artículo se representa como un único objeto JSON. [Consulta del esquema](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt).

## <a name="storage-location"></a>Ubicación de almacenamiento
Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="citation"></a>Referencia bibliográfica

Al incluir datos de CORD-19 en una publicación o redistribución, cite el conjunto de datos de la siguiente forma:

En bibliografía:

Conjunto de datos de investigación abierto para el COVID-19 (CORD-19). 2020. Versión AAAA-MM-DD. Recuperado de [Conjunto de datos de investigación abierto para el COVID-19 (CORD-19)](https://pages.semanticscholar.org/coronavirus-research). Último acceso el AAAA-MM-DD. doi:10.5281/zenodo.3715505

En el texto: (CORD-19, 2020)

## <a name="contact"></a>Contacto

Si tiene preguntas relacionadas con este conjunto de datos, póngase en contacto con partnerships@allenai.org.

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research)** .

## <a name="the-cord-19-dataset"></a>Conjunto de datos CORD-19

CORD-19 es una colección de más de 50 000 artículos( incluidos más de 40 000 con texto completo) sobre la COVID-19, el SARS-CoV-2 y virus relacionados. Este conjunto de datos se ha puesto a disposición libre con el objetivo de ayudar a las comunidades de investigación a combatir la pandemia de la COVID-19.

El objetivo de este cuaderno es doble:

1. Demostrar cómo acceder al conjunto de datos CORD-19 en Azure: nos conectamos a la cuenta de Azure Blob Storage que contiene el conjunto de datos CORD-19.
2. Recorrer la estructura del conjunto de datos: los artículos del conjunto de datos se almacenan como archivos JSON. Se proporcionan ejemplos que muestran:

  - Cómo buscar los artículos (navegar por el contenedor)
  - Cómo leer los artículos (navegar por el esquema JSON)

Dependencias: este cuaderno necesita las siguientes bibliotecas.

- Azure Storage (por ejemplo, `pip install azure-storage`)
- NLTK ([docs](https://www.nltk.org/install.html))
- Pandas (por ejemplo, `pip install pandas`)

### <a name="getting-the-cord-19-data-from-azure"></a>Obtención de los datos de CORD-19 de Azure

Los datos de CORD-19 se han cargado como un conjunto de datos de Azure Open Datasets [aquí](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Creamos un servicio de blob vinculado a este conjunto de datos de CORD-19 abierto.


```python
from azure.storage.blob import BlockBlobService

# storage account details
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = "sv=2019-02-02&ss=bfqt&srt=sco&sp=rlcup&se=2025-04-14T00:21:16Z&st=2020-04-13T16:21:16Z&spr=https&sig=JgwLYbdGruHxRYTpr5dxfJqobKbhGap8WUtKFadcivQ%3D"

# create a blob service
blob_service = BlockBlobService(
    account_name=azure_storage_account_name,
    sas_token=azure_storage_sas_token,
)
```

Podemos usar este servicio de blobs como identificador en los datos. Podemos recorrer el conjunto de datos con las API de `BlockBlobService`. Obtenga más información aquí:

* [Conceptos de Blob service](/rest/api/storageservices/blob-service-concepts)
* [Operaciones con contenedores](/rest/api/storageservices/operations-on-containers)

Los datos de CORD-19 se almacenan en el contenedor `covid19temp`. Esta es la estructura de archivos dentro del contenedor junto con un archivo de ejemplo.

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

Cada archivo .json corresponde a un artículo individual del conjunto de datos. Aquí es donde se almacenan el título, los autores, los resúmenes y (si están disponibles) los datos de texto completo.

### <a name="using-metadatacsv"></a>Uso de metadata.csv
El conjunto de datos CORD-19 incluye `metadata.csv`, un único archivo que registra información básica sobre todos los documentos disponibles en el conjunto de datos CORD-19. El cuadro de búsqueda es un buen punto de partida.


```python
# container housing CORD-19 data
container_name = "covid19temp"

# download metadata.csv
metadata_filename = 'metadata.csv'
blob_service.get_blob_to_path(
    container_name=container_name,
    blob_name=metadata_filename,
    file_path=metadata_filename
)
```

```python
import pandas as pd

# read metadata.csv into a dataframe
metadata_filename = 'metadata.csv'
metadata = pd.read_csv(metadata_filename)
```

```python
metadata.head(3)
```

Esto es muy importante a primera vista, así que vamos a pulirlo un poco.

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```

```
There are 51078 many entries in this dataset:
-- 42511 have full text entries
-- 47741 have DOIs
-- 41082 have PubMed Central (PMC) ids
-- 964 have Microsoft Academic paper ids
```

## <a name="example-read-full-text"></a>Ejemplo: Lectura de texto completo

`metadata.csv` no contiene el texto completo en sí. Veamos un ejemplo de cómo leerlo. Busque y desempaquete el JSON de texto completo y conviértalo en una lista de oraciones.

```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pdf_json/{1}.json'.format(example_entry['full_text_file'], example_entry['sha'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)
```

Ahora podemos leer el contenido JSON asociado a este blob como se muestra a continuación.

```python
import json
blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

Para los fines de este ejemplo, nos interesa el `body_text`, que almacena los datos de texto como se muestra a continuación:

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

El esquema JSON completo está disponible [aquí](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt).


```python
from nltk.tokenize import sent_tokenize

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```    

### <a name="pdf-vs-pmc-xml-parse"></a>Análisis de PDF frente a XML de PMC

En el ejemplo anterior, hemos visto un caso con `has_pdf_parse == True`. En ese caso, la ruta de acceso del archivo de blob tenía el formato:

```
'<full_text_file>/pdf_json/<sha>.json'
```

Alternativamente, para casos con `has_pmc_xml_parse == True` utilice el siguiente formato:

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

Por ejemplo:


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)

blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
```
Full text blob for this entry:
custom_license/pmc_json/PMC546170.xml.json
An example sentence: Double-stranded small interfering RNA (siRNA) molecules have drawn much attention since it was unambiguously shown that they mediate potent gene knock-down in a variety of mammalian cells (1).
```    

## <a name="iterate-through-blobs-directly"></a>Recorrer en iteración los blobs directamente

En los ejemplos anteriores, se usa el archivo `metadata.csv` para navegar por los datos, construir la ruta de acceso del archivo de blob y leer datos del blob. Una alternativa es recorrer en iteración los propios blobs.


```python
# get and sort list of available blobs
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

Ahora podemos recorrer en iteración los blobs directamente. Por ejemplo, vamos a contar el número de archivos JSON disponibles.


```python
# we can now iterate directly though the blobs
count = 0
for blob in sorted_blobs:
    if blob.name[-5:] == ".json":
        count += 1
print("There are {} many json files".format(count))
```

```
There are 59784 many json files
```  

## <a name="appendix"></a>Apéndice

### <a name="data-quality-issues"></a>Problemas de calidad de los datos

Se trata de un conjunto de datos grande que, por motivos obvios, se ha recopilado con bastante prisa. Estos son algunos problemas de calidad de los datos que hemos observado.

#### <a name="multiple-shas"></a>Varios shas

Observamos que en algunos casos hay varios shas para una entrada determinada.

```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```
```
There are 1999 many entries with multiple shas
```

## <a name="layout-of-the-container"></a>Diseño del contenedor

Aquí se usa una expresión regular sencilla para explorar la estructura de archivos del contenedor en caso de que se actualice en el futuro.

```python
container_name = "covid19temp"
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

```python
import re
dirs = {}

pattern = '([\w]+)\/([\w]+)\/([\w.]+).json'
for blob in sorted_blobs:
    
    m = re.match(pattern, blob.name)
    
    if m:
        dir_ = m[1] + '/' + m[2]
        
        if dir_ in dirs:
            dirs[dir_] += 1
        else:
            dirs[dir_] = 1
        
dirs
```

<!-- nbend -->

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research)** .

## <a name="the-cord-19-dataset"></a>Conjunto de datos CORD-19

CORD-19 es una colección de más de 50 000 artículos( incluidos más de 40 000 con texto completo) sobre la COVID-19, el SARS-CoV-2 y virus relacionados. Este conjunto de datos se ha puesto a disposición libre con el objetivo de ayudar a las comunidades de investigación a combatir la pandemia de la COVID-19.

El objetivo de este cuaderno es doble:

1. Demostrar cómo acceder al conjunto de datos CORD-19 en Azure: usamos el conjunto de datos AzureML para proporcionar un contexto para los datos CORD-19.
2. Recorrer la estructura del conjunto de datos: los artículos del conjunto de datos se almacenan como archivos JSON. Se proporcionan ejemplos que muestran:

  - Cómo buscar los artículos (navegar por la estructura de directorios)
  - Cómo leer los artículos (navegar por el esquema JSON)

Dependencias: este cuaderno necesita las siguientes bibliotecas.

- SDK de Python de AzureML (por ejemplo, `pip install --upgrade azureml-sdk`)
- Pandas (por ejemplo, `pip install pandas`)
- NLTK ([docs](https://www.nltk.org/install.html)) (por ejemplo, `pip install nltk`)

Si su NLTK no tiene un paquete `punkt`, necesitará ejecutar:

```
import nltk
nltk.download('punkt')
```

### <a name="getting-the-cord-19-data-from-azure"></a>Obtención de los datos de CORD-19 de Azure

Los datos de CORD-19 se han cargado como un conjunto de datos de Azure Open Datasets [aquí](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). En este cuaderno, usamos AzureML [Dataset](/python/api/azureml-core/azureml.core.dataset.dataset) para hacer referencia al conjunto de datos abierto CORD-19.

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
cord19_dataset = Dataset.File.from_files('https://azureopendatastorage.blob.core.windows.net/covid19temp')
mount = cord19_dataset.mount()
```

El método `mount()` crea un administrador de contexto para montar secuencias del sistema de archivos definidas por el conjunto de datos como archivos locales.

Utilcie `mount.start()` y `mount.stop()` o, como alternativa, `with mount():` para administrar el contexto.

El montaje solo se admite en sistemas operativos Unix o similares a Unix, y libfuse debe estar presente. Si se ejecuta dentro de un contenedor de Docker, el contenedor de Docker debe iniciarse con la marca `--privileged` o iniciarse con `--cap-add SYS_ADMIN --device /dev/fuse`. Para más información, consulte los [docs](/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py#mount-mount-point-none----kwargs-&preserve-view=true)


```python
import os

COVID_DIR = '/covid19temp'
path = mount.mount_point + COVID_DIR

with mount:
    print(os.listdir(path))
```
```
['antiviral_with_properties_compressed.sdf', 'biorxiv_medrxiv', 'biorxiv_medrxiv_compressed.tar.gz', 'comm_use_subset', 'comm_use_subset_compressed.tar.gz', 'custom_license', 'custom_license_compressed.tar.gz', 'metadata.csv', 'noncomm_use_subset', 'noncomm_use_subset_compressed.tar.gz']
```

Esta es la estructura de archivos dentro del conjunto de datos CORD-19 junto con un archivo de ejemplo.

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

Cada archivo .json corresponde a un artículo individual del conjunto de datos. Aquí es donde se almacenan el título, los autores, los resúmenes y (si están disponibles) los datos de texto completo.

## <a name="using-metadatacsv"></a>Uso de metadata.csv

El conjunto de datos CORD-19 incluye `metadata.csv`, un único archivo que registra información básica sobre todos los documentos disponibles en el conjunto de datos CORD-19. El cuadro de búsqueda es un buen punto de partida.


```python
import pandas as pd

# create mount context
mount.start()

# specify path to metadata.csv
COVID_DIR = 'covid19temp'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, COVID_DIR, 'metadata.csv')

# read metadata
metadata = pd.read_csv(metadata_filename)
metadata.head(3)
```

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```    

### <a name="example-read-full-text"></a>Ejemplo: Lectura de texto completo

`metadata.csv` no contiene el texto completo en sí. Veamos un ejemplo de cómo leerlo. Busque y desempaquete el JSON de texto completo y conviértalo en una lista de oraciones.


```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
filepath = '{0}/{1}/pdf_json/{2}.json'.format(path, example_entry['full_text_file'], example_entry['sha'])
print("Full text filepath:")
print(filepath)
```    

Ahora podemos leer el contenido JSON asociado a este archivo como se muestra a continuación.

```python
import json

try:
    with open(filepath, 'r') as f:
        data = json.load(f)
except FileNotFoundError as e:
    # in case the mount context has been closed
    mount.start()
    with open(filepath, 'r') as f:
        data = json.load(f)
        
# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

```
Keys within data: paper_id, metadata, abstract, body_text, bib_entries, ref_entries, back_matter
```

Para los fines de este ejemplo, nos interesa el `body_text`, que almacena los datos de texto como se muestra a continuación:

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

Vea el [esquema completo JSON](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt).


```python
from nltk.tokenize import sent_tokenize
# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
    

#### <a name="pdf-vs-pmc-xml-parse"></a>Análisis de PDF frente a XML de PMC

En el ejemplo anterior, hemos visto un caso con `has_pdf_parse == True`. En ese caso, la ruta de acceso del archivo tenía el formato:

```
'<full_text_file>/pdf_json/<sha>.json'
```

Alternativamente, para casos con `has_pmc_xml_parse == True` utilice el siguiente formato:

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

Por ejemplo:


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
filename = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Path to file: {}\n".format(filename))

with open(mount.mount_point + '/' + COVID_DIR + '/' + filename, 'r') as f:
    data = json.load(f)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```

## <a name="appendix"></a>Apéndice

### <a name="data-quality-issues"></a>Problemas de calidad de los datos

Se trata de un conjunto de datos grande que, por motivos obvios, se ha recopilado con bastante prisa. Estos son algunos problemas de calidad de los datos que hemos observado.


```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```

<!-- nbend -->

---

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).