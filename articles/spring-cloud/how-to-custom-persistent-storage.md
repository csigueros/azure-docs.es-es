---
title: Habilitación del almacenamiento persistente en Azure Spring Cloud | Microsoft Docs
description: Cómo incorporar su propio almacenamiento como almacenamientos persistentes en Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: xuycao
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2bf84684851b19665e33af0cbfe902145b8ea565
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478414"
---
# <a name="how-to-enable-your-own-persistent-storage-in-azure-spring-cloud"></a>Habilitación del almacenamiento persistente en Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ✔️ C#

En este artículo se explica cómo habilitar su propio almacenamiento persistente en Azure Spring Cloud.

Cuando se usa el almacenamiento persistente integrado en Azure Spring Cloud, los artefactos generados por la aplicación se cargan en las cuentas de Azure Storage. Microsoft controla las directivas de administración de la vigencia y el cifrado en reposo para esos artefactos. 

Con Traiga su propio almacenamiento, estos artefactos se cargan en una cuenta de almacenamiento que usted controla. Esto significa que controla la directiva de cifrado en reposo, la directiva de administración de la vigencia y el acceso a la red. Sin embargo, será responsable de los costos asociados a esa cuenta de almacenamiento.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure Storage existente y un recurso compartido de archivos de Azure creado previamente. Si necesita crear una cuenta de almacenamiento y un recurso compartido de archivos en Azure, vea [Creación de un recurso compartido de archivos de Azure](../storage/files/storage-how-to-create-file-share.md).
* [Extensión de Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) para la CLI de Azure

## <a name="use-the-azure-cli-to-enable-your-own-extra-persistent-storage"></a>Uso de la CLI de Azure para habilitar su propio almacenamiento persistente adicional

Puede habilitar su propio almacenamiento con la CLI de Azure mediante los pasos siguientes.

1. Use el siguiente comando para enlazar la cuenta de Azure Storage como un recurso de almacenamiento en la instancia de Azure Spring Cloud:

    ```azurecli
   az spring-cloud storage add --storage-type StorageAccount --account-name <account-name> --account-key <account-key>  -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
    ```

1. Use el siguiente comando para crear una aplicación con su propio almacenamiento persistente.

    ```azurecli
    az spring-cloud app create -n <app-name> -g <resource-group-name> -s <spring-instance-name> --persistent-storage <path-to-JSON-file>
    ```

    Este es un ejemplo del archivo JSON pasado al parámetro `--persistent-storage` en el comando create:

    ```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
    ```

1. Opcionalmente, agregue almacenamiento persistente adicional a una aplicación existente mediante el siguiente comando:

    ```azurecli
    az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
    ```

1. Opcionalmente, enumere todo el almacenamiento persistente existente de un recurso de almacenamiento específico mediante el siguiente comando:

   ```azurecli
   az spring-cloud storage list-persistent-storage -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
   ```

## <a name="use-best-practices"></a>Uso de procedimientos recomendados

Estos son los procedimientos recomendados que se deben usar para agregar almacenamiento persistente propio a Azure Spring Cloud.

- Para evitar posibles problemas de latencia, coloque la instancia de Azure Spring Cloud y la cuenta de Azure Storage en la misma región de Azure.

- En la cuenta de Azure Storage, evite volver a generar la clave de cuenta que se está utilizando. La cuenta de almacenamiento contiene dos claves diferentes. Use un enfoque paso a paso para asegurarse de que su propio almacenamiento persistente permanece disponible para las aplicaciones durante la regeneración de claves. 

   Por ejemplo, suponiendo que usó key1 para enlazar una cuenta de almacenamiento a Azure Spring Cloud, debe seguir estos pasos:

   1. Vuelva a generar key2.
   1. Actualice la clave de cuenta del recurso de almacenamiento para usar la clave regenerada key2.
   1. Reinicie las aplicaciones que montan el almacenamiento persistente desde este recurso de almacenamiento. (Puede usar ```az spring-cloud storage list-persistent-storage``` para enumerar todas las aplicaciones relacionadas).
   1. Vuelva a generar key1.

- Si elimina una cuenta de Azure Storage o un recurso compartido de archivos de Azure, quite el recurso de almacenamiento correspondiente o el almacenamiento persistente de las aplicaciones para evitar posibles errores.

## <a name="faqs"></a>Preguntas más frecuentes

Estas son las preguntas más frecuentes (P+F) sobre el uso de su propio almacenamiento persistente con Azure Spring Cloud.

- Si tengo habilitado el almacenamiento persistente integrado y, a continuación, he habilitado mi propio almacenamiento como almacenamiento persistente adicional, ¿se migrarán mis datos a mi cuenta de almacenamiento?

   *No. Pero vamos a proporcionar un documento para ayudarle a realizar la migración usted mismo pronto*.

- ¿Cuáles son las rutas de montaje reservadas?

   *Estas rutas de montaje están reservadas por el servicio Azure Spring Cloud*:
   - */tmp*
   - */persistent*
   - */secrets*
   - */app-insights/agents*
   - */etc/azure-spring-cloud/certs*
   - */app-insights/agents/settings*
   - */app-lifecycle/settings*

- ¿Cuáles son las opciones de montaje disponibles?

   *Actualmente se admiten las siguientes opciones de montaje*:
   - `uid`
   - `gid`
   - `file_mode`
   - `dir_mode`
   
   *La propiedad `mountOptions` es opcional. Los valores predeterminados de las opciones de montaje anteriores son: ["uid=0", "gid=0", "file_mode=0777", "dir_mode=0777"]*

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre las [cuotas de servicio y la aplicación](./quotas.md).
* Aprenda a [escalar manualmente su aplicación](./how-to-scale-manual.md).