---
title: Envío de un flujo de trabajo con entradas de archivo FASTQ
titleSuffix: Microsoft Genomics
description: En este artículo se muestra cómo enviar un flujo de trabajo al servicio de Microsoft Genomics, si los archivos de entrada son un par sencillo de archivos FASTQ.
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 8b151632e2fb33b7bb984258a6efae54261bf419
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255724"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Inicio rápido: Envío de un flujo de trabajo con entradas de archivo FASTQ en Microsoft Genomics

En este artículo se muestra cómo enviar un flujo de trabajo al servicio de Microsoft Genomics, si los archivos de entrada son un par sencillo de archivos FASTQ. En este tema se da por supuesto que ya ha instalado y ejecutado el cliente `msgen` y está familiarizado con el uso de Azure Storage. Si ha enviado correctamente un flujo de trabajo usando los datos de ejemplo proporcionados, puede continuar con este artículo. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Configuración: Carga de los archivos FASTQ en Azure Storage
Supongamos que tiene dos archivos, *reads_1.fq.gz* y *reads_2.fq.gz* y que los ha cargado en la cuenta de almacenamiento *myaccount* de Azure como **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads_1 <span></span>.fq <span></span>.gz <span></span>** y **https://<span></span>myaccount.blob.core.<span></span>windows <span></span>.net/<span></span>inputs/<span></span>reads_2.fq <span></span>.gz <span></span>**. Tiene la dirección URL de la API y la clave de acceso. Desea los resultados en **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.


## <a name="submit-your-job-to-the-msgen-client"></a>Envío del trabajo al cliente `msgen` 

Este es el conjunto mínimo de argumentos que debe proporcionar al cliente `msgen`; los saltos de línea se han agregado para mayor claridad:

Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Para Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Si prefiere usar un archivo de configuración, esto es lo que podría contener:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envíe el archivo `config.txt` con esta invocación: `msgen submit -f config.txt`

## <a name="next-steps"></a>Pasos siguientes
En este artículo, se van a cargar un par de archivos FASTQ en Azure Storage y se va a enviar un flujo de trabajo al servicio Microsoft Genomics mediante el cliente de Python `msgen`. Para saber más del envío del flujo de trabajo y otros comandos que puede usar con el servicio Microsoft Genomics, consulte nuestras [preguntas más frecuentes](frequently-asked-questions-genomics.yml). 
