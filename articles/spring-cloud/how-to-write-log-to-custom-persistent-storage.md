---
title: Cómo usar Logback para escribir registros en el almacenamiento persistente personalizado de Azure Spring Cloud | Microsoft Docs
description: Cómo usar Logback para escribir registros en el almacenamiento persistente personalizado de Azure Spring Cloud.
author: karlerickson
ms.author: xuycao
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: 558ca29f8faa03857d4debe760c4859ab43b17e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404303"
---
# <a name="how-to-use-logback-to-write-logs-to-custom-persistent-storage"></a>Cómo usar Logback para escribir registros en el almacenamiento persistente personalizado

**Este artículo se aplica a:** ✔️ Java

En este artículo se muestra cómo cargar Logback y escribir registros en el almacenamiento persistente personalizado de Azure Spring Cloud.

> [!NOTE]
> Cuando un archivo de la ruta de clase de la aplicación tiene uno de los siguientes nombres, Spring Boot lo cargará automáticamente en la configuración predeterminada para Logback:
> - *logback-spring.xml*
> - *logback.xml*
> - *logback-spring.groovy*
> - *logback.groovy*

## <a name="prerequisites"></a>Requisitos previos

* Un recurso de almacenamiento existente enlazado a una instancia de Azure Spring Cloud. Si necesita enlazar un recurso de almacenamiento, consulte [Habilitación del almacenamiento persistente en Azure Spring Cloud](./how-to-custom-persistent-storage.md).
* La dependencia de Logback incluida en la aplicación. Para obtener más información sobre Logback, vea [A Guide To Logback](https://www.baeldung.com/logback) (Guía de Logback).
* [Extensión de Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) para la CLI de Azure

## <a name="edit-the-logback-configuration-to-write-logs-into-a-specific-path"></a>Edición de la configuración de Logback para escribir registros en una ruta de acceso específica

Puede establecer la ruta de acceso donde se escribirán los registros utilizando el archivo de ejemplo logback-spring.xml.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="Console"
              class="ch.qos.logback.core.ConsoleAppender">
        <!-- please feel free to customize the log layout -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 'LOGS' here is a value to be read from the application's environment variable -->
        <file>${LOGS}/spring-boot-logger.log</file>
        <!-- please feel free to customize the log layout pattern -->
        <encoder
                class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!-- LOG everything at the INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <!-- LOG "com.baeldung*" at the TRACE level -->
    <logger name="com.baeldung" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>

</configuration>
```

En el ejemplo anterior, hay dos marcadores de posición denominados `{LOGS}` en la ruta de acceso para escribir los registros de la aplicación. Se debe asignar un valor a la variable de entorno `LOGS` para que el registro escriba tanto en la consola como en el almacenamiento persistente. 

## <a name="use-the-azure-cli-to-create-and-deploy-logback-to-write-logs-to-persistent-storage"></a>Uso del CLI de Azure para crear e implementar Logback para escribir registros en el almacenamiento persistente

1. Use el siguiente comando para crear una aplicación en Azure Spring Cloud con el almacenamiento persistente habilitado y la variable de entorno definida:

   ```azurecli
   az spring-cloud app create \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-json-file> \
        --env LOGS=/byos/logs
   ```

    Debe montar el almacenamiento persistente en la misma ruta de acceso que se usa en la aplicación. Este es un ejemplo del archivo JSON pasado al parámetro `--persistent-storage` en el comando create. Asegúrese de pasar el mismo valor para la variable de entorno en el comando de la CLI anterior y en la propiedad `mountPath` siguiente: 

    ```json
    {
        "customPersistentDisks": [
            {
                "storageName": "<Storage-Resource-Name>",
                "customPersistentDiskProperties": {
                    "type": "AzureFileVolume",
                    "shareName": "<Azure-File-Share-Name>",
                    "mountPath": "/byos/logs",
                    "readOnly": false
                }
            }
        ]
    }
    ```
  
1. Use el siguiente comando para implementar la aplicación:

   ```azurecli
   az spring-cloud app deploy \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --jar-path <path-to-jar-file>
   ```

1. Use el siguiente comando para comprobar el registro de consola de la aplicación:

   ```azurecli
   az spring-cloud app logs \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name>
   ```

    Vaya al recurso Azure Storage Account que ha enlazado y busque el recurso compartido de archivos de Azure que se ha adjuntado como almacenamiento persistente. En este ejemplo, los registros se escribirán en el archivo *spring-boot-logger.log* de la ruta de acceso principal del recurso compartido de archivos de Azure. Todos los archivos de registro rotados se almacenarán en la carpeta */archived* del recurso compartido de archivos de Azure.

## <a name="next-steps"></a>Pasos siguientes

* [Registro de aplicaciones estructurado para Azure Spring Cloud](./structured-app-log.md)
* [Análisis de registros y métricas con la configuración de diagnóstico](./diagnostic-services.md)