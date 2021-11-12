---
title: 'Límites: respuesta a preguntas'
description: La respuesta a preguntas tiene metalímites para partes de la base de conocimiento y del servicio. Es importante mantener la base de conocimiento dentro de esos límites para probar y publicar.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 185a76aeb4aeeadf4fea9b0b316b5905b1f598a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478258"
---
# <a name="project-limits-and-boundaries"></a>Límites del proyecto

Los límites de la respuesta a preguntas que se indican a continuación son una combinación de los [límites del plan de tarifa de Azure Cognitive Search](../../../../search/search-limits-quotas-capacity.md) y los límites de la respuesta a preguntas. Ambos conjuntos de límites afectan al número de bases de conocimiento que puede crear por recurso y el tamaño que puede alcanzar cada base de conocimiento.

## <a name="knowledge-bases"></a>Bases de conocimiento

El número máximo de bases de conocimiento se basa en los [límites de plan de Azure Cognitive Search](../../../../search/search-limits-quotas-capacity.md).

|**Nivel de Azure Cognitive Search** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo permitido de bases de conocimiento publicadas|2|14|49|199|199|2999|

 Por ejemplo, si el nivel tiene 15 índices permitidos, puede publicar 14 bases de conocimiento (un índice por cada base de conocimiento publicada). El decimoquinto índice, `testkb`, se usa para todas las bases de conocimiento en lo relativo a la creación y las pruebas.

## <a name="extraction-limits"></a>Límites de extracción

### <a name="file-naming-constraints"></a>Restricciones de nomenclatura de los archivos

Los nombres de archivo no pueden incluir los siguientes caracteres:

|No use el carácter|
|--|
|Comillas simples `'`|
|Comillas dobles `"`|

### <a name="maximum-file-size"></a>Tamaño de archivo máximo

|Formato|Tamaño máximo de archivo (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Número máximo de archivos

> [!NOTE]
> La respuesta a preguntas actualmente no tiene límites en lo que respecta al número de orígenes que se pueden agregar. Su rendimiento está actualmente limitado a 10 transacciones por segundo tanto para las API de administración como para las de predicción.

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de vínculos profundos de la dirección URL

El número máximo de vínculos profundos que se pueden rastrear para la extracción de pares de preguntas y respuestas desde una página URL es **20**.

## <a name="metadata-limits"></a>Límites de metadatos

Los metadatos se presentan como un par `key:value` basado en texto, como `product:windows 10`. Se almacenan y se comparan en minúsculas. El número máximo de campos de metadatos se basa en los **[límites de los niveles de Azure Cognitive Search](../../../../search/search-limits-quotas-capacity.md)** .

Si elige proyectos con varios lenguajes en un único recurso de lenguaje, hay un índice de prueba dedicado por proyecto o base de conocimiento. Por lo tanto, el límite se aplica por proyecto o base de conocimiento en el servicio de lenguaje.

|**Nivel de Azure Cognitive Search** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de campos de metadatos por servicio de lenguaje (por base de conocimiento)|1,000|100*|1,000|1,000|1,000|1,000|

Si no elige la opción de tener proyectos con varios lenguajes diferentes, los límites se aplican en todas las bases de conocimiento del servicio de lenguaje.

|**Nivel de Azure Cognitive Search** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de campos de metadatos por servicio de lenguaje (en todas las bases de conocimiento)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Por nombre y valor

La longitud y los caracteres aceptables para el nombre y el valor de los metadatos se muestran en la tabla siguiente.

|Elemento|Caracteres permitidos|Coincidencia de patrón regex|Número máximo de caracteres|
|--|--|--|--|
|Nombre (clave)|Permite<br>Caracteres alfanuméricos (letras y dígitos)<br>`_` (subrayado)<br> No debe contener espacios.|`^[a-zA-Z0-9_]+$`|100|
|Value|Permite todo excepto<br>`:` (dos puntos)<br>`|` (barra vertical)<br>Solo se permite un valor.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Límites de contenido de la base de conocimiento
Límites generales del contenido de la base de conocimiento:
* Longitud del texto de la respuesta: 25 000 caracteres
* Longitud del texto de la pregunta: 1000 caracteres
* Longitud del texto de la clave de los metadatos: 100 caracteres
* Longitud del texto del valor de los metadatos: 500 caracteres
* Caracteres admitidos para el nombre de metadatos: alfabéticos, dígitos y `_`
* Caracteres admitidos para el valor de metadatos: Todos excepto `:` y `|`
* Longitud del nombre de archivo: 200
* Formatos de archivo admitidos: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de preguntas alternativas: 300
* Número máximo de pares de preguntas y respuestas: en función del **[nivel de Azure Cognitive Search](../../../../search/search-limits-quotas-capacity.md#document-limits)** elegido. Un par de pregunta y respuesta se asigna a un documento en el índice de Azure Cognitive Search.
* Página HTML/URL: 1 millón de caracteres

## <a name="create-project-call-limits"></a>Creación de límites de llamadas de proyecto:

Representan los límites de cada acción de creación de proyecto o base de conocimiento; es decir, la selección de *Crear proyecto* o la llamada a la API REST para crear un proyecto o base de conocimiento.

* Número máximo recomendado de preguntas alternativas por respuesta: 300
* Número máximo de direcciones URL: 10
* Número máximo de archivos: 10
* Número máximo de preguntas y respuestas permitidas por llamada: 1000

## <a name="update-knowledge-base-call-limits"></a>Límites de llamadas de actualización de la base de conocimiento

Representan los límites para cada acción de actualización; es decir, la selección de *Guardar* o la llamada a la API REST con una solicitud de actualización.
* Longitud de cada nombre de origen: 300
* Número máximo recomendado de preguntas alternativas agregadas o eliminadas: 300
* Número máximo de campos de metadatos agregados o eliminados: 10
* Número máximo de direcciones URL que se pueden actualizar: 5
* Número máximo de preguntas y respuestas permitidas por llamada: 1000

## <a name="add-unstructured-file-limits"></a>Adición de límites de archivo no estructurados

> [!NOTE]
> * Si necesita usar archivos con un tamaño superior al límite permitido, puede dividir el archivo en fragmentos más pequeños antes de enviarlos a la API. 

Representan los límites cuando se usan archivos no estructurados para *Crear proyecto* o llamar a la API REST para crear una base de conocimiento:
* Longitud del archivo: extraeremos los primeros 32 000 caracteres.
* Máximo de tres respuestas por archivo.

## <a name="prebuilt-question-answering-limits"></a>Límites de respuesta a preguntas precompilados

> [!NOTE]
> * Si necesita usar documentos con un tamaño superior al límite permitido, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

Representan los límites cuando se usa la API REST para responder a una pregunta sin necesidad de crear un proyecto o base de conocimiento:
* Número de documentos: 5
* Tamaño máximo de un solo documento: 5120 caracteres.
* Máximo de tres respuestas por documento.