---
title: Configuración del diagnóstico de las opciones de JVM para la solución de problemas avanzada en Azure Spring Cloud
description: Varios procedimientos recomendados de configuración de JVM para establecer el volcado de memoria del montón, JFR y los registros de GC.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: fd29b9b7356bc16132d46f125d3f3cf2792c1654
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000568"
---
# <a name="tutorial-diagnostic-settings-of-jvm-options-for-advanced-troubleshooting-in-azure-spring-cloud"></a>Tutorial: configuración del diagnóstico de las opciones de JVM para la solución de problemas avanzada en Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ❌ C#

Hay varios parámetros de inicio de aplicación basados en JVM relacionados con los registros de volcado de memoria del montón, JFR y GC. En Azure Spring Cloud, se admite la configuración de JVM mediante jvm-options. En este artículo, se proporcionan varios ejemplos de ellos, lo que puede resultar útil para nuestros clientes.

## <a name="prerequisites"></a>Requisitos previos
Para completar este ejercicio, necesitará lo siguiente:

* Una instancia del servicio Azure Spring Cloud implementada. Para comenzar, siga nuestro [inicio rápido sobre la implementación de una aplicación mediante la CLI de Azure](./quickstart.md).
* Al menos una aplicación ya creada en su instancia de servicio.
* Al menos un [almacenamiento persistente ya enlaza en la aplicación](how-to-built-in-persistent-storage.md) para guardar los archivos de diagnóstico generados.

Los usuarios pueden seguir la [documentación de implementación](https://docs.microsoft.com/cli/azure/spring-cloud/app/deployment?view=azure-cli-latest) para configurar los parámetros de inicio de la aplicación basada en JVM. En la sección siguiente se proporcionan varios ejemplos. **Lo que debe hacer es agregar el parámetro en jvm-options.**

## <a name="generate-a-heap-dump-when-out-of-memory"></a>Generación de un volcado de memoria del montón cuando la memoria es insuficiente
```heap dump when OOM
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<PATH_TO_HEAP_DUMP_FOLDER> 
```
También se podría indicar el nombre específico del archivo. Pero solo se recomienda encarecidamente especificar la ruta de acceso de la carpeta. Una vez que se especifica el nombre de archivo, solo generará un volcado de memoria del montón en la primera OOM. Como no se puede cubrir el archivo con formato hprof, los usuarios no podrán obtener el volcado de memoria del montón de la siguiente OOM. Si los usuarios solo especifican la ruta de acceso a la carpeta, recibirán volcados de memoria del montón de todas las OOM con un nombre generado automáticamente.

Toda la ruta de acceso que el usuario especifica a continuación debe estar bajo la ruta de acceso de montaje del almacenamiento persistente del usuario que ya enlaza con la aplicación del cliente.

## <a name="gc-logs"></a>Registros de GC
Aquí solo se ofrece un ejemplo de cómo usar las opciones de JVM relacionadas con los registros de GC. Para más información, puede consultar la documentación oficial de JVM.
```GC Logs
   -XX:+PrintGCDetails -Xloggc:<PATH_TO_GC_LOG_FILE>
```

## <a name="jfr-on-exit"></a>JFR al salir
Aquí solo se ofrece un ejemplo de cómo usar las opciones de JVM relacionadas con JFR. Para más información, puede consultar la documentación oficial de JVM.
```JFR on exit
   -XX:StartFlightRecording=dumponexit=true,dumponexitpath=<PATH_TO_JFR_FILE>
```

## <a name="path-of-generated-file"></a>Ruta de acceso del archivo generado.
Preste atención, ya que debe asegurarse de que la ruta de acceso de destino del archivo generado debe estar en el almacenamiento persistente de la aplicación para que pueda obtener el archivo en el almacenamiento.
Por ejemplo, los clientes darán un archivo JSON la primera vez que los usuarios creen un almacenamiento persistente en Azure Spring Cloud.
```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
```
O bien, el cliente puede usar los siguientes comandos para anexar el almacenamiento persistente.
    
```
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path e.g. /test/Path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
```
   
   Si los clientes desean tener el volcado en su propio archivo de Azure dado anteriormente, <PATH_TO_HEAP_DUMP_FOLDER> como "/test/Path". Si los clientes desean usar la ruta de acceso en la ruta de acceso de montaje, asegúrese de que ya se ha creado la ruta de acceso secundaria.




