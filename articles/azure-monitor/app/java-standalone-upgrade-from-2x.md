---
title: 'Actualización de la versión 2.x: Application Insights Java de Azure Monitor'
description: Actualización de Application Insights Java de Azure Monitor 2.x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cbfdc8c7e07a68335083c529e545143a513b1808
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858279"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Actualización del SDK de Java de Application Insights 2.x

Si ya está usando el SDK de Application Insights para Java 2.x en la aplicación, puede seguir usándolo.
El agente de Application Insights Java 3.x lo detectará, y capturará y correlacionará cualquier telemetría personalizada que envíe a través del SDK 2.x, al mismo tiempo que suprimirá cualquier recopilación automática que realice el SDK 2.x para evitar la telemetría duplicada.

Si usaba el agente 2.x de Application Insights, debe quitar el argumento JVM `-javaagent:` que señalaba al agente 2.x.

En el resto de este documento se describen las limitaciones y los cambios que puede encontrar al actualizar de 2.x a 3.x, así como algunas soluciones alternativas que pueden resultar útiles.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers y TelemetryProcessors

Los elementos Telemetryinitializer y TelemetryProcessor del SDK 2.x no se ejecutarán cuando se use el agente 3.x.
Muchos de los casos de uso que anteriormente lo requerían se pueden resolver en Application Insights Java 3.x mediante la configuración de [dimensiones personalizadas](./java-standalone-config.md#custom-dimensions) o de [procesadores de telemetría](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Varias aplicaciones en una sola máquina virtual Java

Actualmente, Application Insights Java 3.x solo admite una [cadena de conexión y un nombre de rol](./java-standalone-config.md#connection-string-and-role-name) por proceso en ejecución. En concreto, todavía no puede tener varias aplicaciones web de Tomcat en la misma implementación de Tomcat con cadenas de conexión o nombres de rol diferentes.

## <a name="operation-names"></a>Nombres de operación

En el SDK de Application Insights Java 2.x, en algunos casos, los nombres de las operaciones contenían la ruta de acceso completa; por ejemplo:

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Captura de pantalla que muestra los nombres de las operaciones con la ruta de acceso completa":::

Los nombres de las operaciones de Application Insights Java 3.x han cambiado para proporcionar una mejor vista agregada en la experiencia del usuario con el portal de Application Insights, por ejemplo.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-parameterized.png" alt-text="Captura de pantalla que muestra los nombres de operación con parámetros":::

Sin embargo, si para algunas aplicaciones prefiere la vista agregada de la experiencia del usuario que proporcionaban los nombres de operaciones anteriores, puede usar la característica [procesadores de telemetría](./java-standalone-telemetry-processors.md) (versión preliminar) de la versión 3.x para replicar el comportamiento anterior.

En el siguiente fragmento de código se configuran tres procesadores de telemetría que se combinan para replicar el comportamiento anterior.
Los procesadores de telemetría realizan las siguientes acciones (en orden):

1. El primer procesador de telemetría es un procesador de atributos (tiene el tipo `attribute`), lo que significa que se aplica a toda la telemetría que tiene atributos (actualmente `requests` y `dependencies`, pero pronto también `traces`).

   Coincidirá con cualquier telemetría que tenga atributos denominados `http.method` y `http.url`.

   A continuación, extraerá la parte de la ruta de acceso del atributo `http.url` en un nuevo atributo denominado `tempName`.

2. El segundo procesador de telemetría es un procesador de intervalos (tiene el tipo `span`), lo que significa que se aplica a `requests` y `dependencies`.

   Coincidirá con cualquier intervalo que tenga un atributo denominado `tempPath`.

   A continuación, actualizará el nombre del intervalo del atributo `tempPath`.

3. El último procesador de telemetría es un procesador de atributos, del mismo tipo que el primer procesador de telemetría.

   Coincidirá con cualquier telemetría que tenga un atributo denominado `tempPath`.

   A continuación, se eliminará el atributo denominado `tempPath`, para que no se notifique como una dimensión personalizada.

```
{
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "actions": [
          {
            "key": "http.url",
            "pattern": "https?://[^/]+(?<tempPath>/[^?]*)",
            "action": "extract"
          }
        ]
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempPath" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="2x-sdk-logging-appenders"></a>Elementos appender de registro de SDK 2.x

Application Insights Java 3.x [recopila de forma automática el registro](./java-standalone-config.md#auto-collected-logging) sin necesidad de configurar ningún elemento appender de registro.
Si usa elementos appender de registro de SDK 2.x, los puede quitar, ya que Application Insights Java 3.x los suprimirá de todos modos.

## <a name="2x-sdk-spring-boot-starter"></a>Spring Boot Starter de SDX 2.x

No hay ningún Spring Boot Starter de Application Insights Java 3.x.
La instalación y configuración de 3.x sigue los mismos [pasos sencillos](./java-in-process-agent.md#get-started), tanto si usa Spring Boot como si no.

Al realizar la actualización desde Spring Boot Starter del SDK de Application Insights Java 2.x, tenga en cuenta que el nombre del rol en la nube ya no será `spring.application.name` de forma predeterminada.
Consulte los [documentos de configuración de 3.x](./java-standalone-config.md#cloud-role-name) para establecer el nombre del rol en la nube en 3.x mediante la configuración de json o la variable de entorno.
