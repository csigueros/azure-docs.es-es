---
title: Envío de un flujo de trabajo con una entrada de archivo BAM
titleSuffix: Microsoft Genomics
description: Este artículo muestra cómo enviar un flujo de trabajo al servicio de Microsoft Genomics, si el archivo de entrada es un archivo simple BAM.
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 09e06e528f2e47338871d7a564b763534a363766
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255757"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Envío de un flujo de trabajo con una entrada de archivo BAM

Este artículo muestra cómo enviar un flujo de trabajo al servicio de Microsoft Genomics, si el archivo de entrada es un archivo simple BAM. En este tema se da por supuesto que ya ha instalado y ejecutado el cliente `msgen` y está familiarizado con el uso de Azure Storage. Si ha enviado correctamente un flujo de trabajo usando los datos de ejemplo proporcionados, puede continuar con este artículo. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configuración: Carga del archivo BAM en Azure Storage
Supongamos que tiene un único archivo BAM, *reads.bam*, y lo ha cargado en la cuenta de almacenamiento *myaccount* en Azure como **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>**. Tiene la dirección URL de la API y la clave de acceso. Desea los resultados en **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.



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
  --input-blob-name-1 reads.bam ^
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
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Si prefiere usar un archivo de configuración, esto es lo que podría contener:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envíe el archivo `config.txt` con esta invocación: `msgen submit -f config.txt`

## <a name="next-steps"></a>Pasos siguientes
En este artículo, se va a cargar un archivo BAM en Azure Storage y se va a enviar un flujo de trabajo al servicio Microsoft Genomics mediante el cliente de Python `msgen`. Para obtener información adicional sobre el envío del flujo de trabajo y otros comandos que puede usar con el servicio Microsoft Genomics, vea nuestras [preguntas más frecuentes](frequently-asked-questions-genomics.yml). 
