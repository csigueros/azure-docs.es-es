---
title: Azure Monitor Application Insights para Java
description: Supervisión del rendimiento de aplicaciones para aplicaciones Java que se ejecutan en cualquier entorno sin necesidad de modificar el código. Seguimiento distribuido y mapa de aplicación.
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 9ebfaea28e249af5f8ecd140e08178398f38fd6f
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858315"
---
# <a name="azure-monitor-opentelemetry-based-auto-instrumentation-for-java-applications"></a>Instrumentación automática basada en OpenTelemetry de Azure Monitor para aplicaciones Java

En este artículo se describe cómo habilitar y configurar la oferta de Java de Azure Monitor basada en OpenTelemetry. Cuando termine de leer las instrucciones de este artículo, podrá usar Application Insights de Azure Monitor para supervisar la aplicación.

## <a name="get-started"></a>Introducción
La instrumentación automática de Java se puede habilitar sin cambios en el código.

### <a name="prerequisites"></a>Prerrequisitos
- Aplicación de Java con la versión 8+
- Una suscripción a Azure: [cree una de forma gratuita](https://azure.microsoft.com/free/).
- Recurso de Application Insights: [Creación de un recurso de Application Insights](create-workspace-resource.md#create-workspace-based-resource)

### <a name="enable-azure-monitor-application-insights"></a>Habilitación de Azure Monitor Application Insights
**1. Descarga del archivo JAR de instrumentación automática**

#### <a name="1-download-jar-file"></a>1. Descargar el archivo JAR.

Descargue el archivo [applicationinsights-agent-3.2.0.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.0/applicationinsights-agent-3.2.0.jar).

> [!WARNING]
> 
> - **Si va actualizar desde la versión preliminar 3.0**
>
>    Revise todas las [opciones de configuración](./java-standalone-config.md) con cuidado, ya que la estructura JSON ha cambiado por completo, además del nombre de archivo, que es en minúsculas.
> 
> - **Si va actualizar desde la versión 3.0.x**
> 
>    Los nombres de operación y los nombres de telemetría de solicitudes ahora tienen el método HTTP como prefijo (`GET`, `POST`, etc.).
>    Como consecuencia, los paneles o las alertas personalizados pueden verse afectados si se basaban en los valores anteriores.
>    Consulte las [notas de la versión 3.1.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0) para obtener más detalles.
>
> - **Si va actualizar desde la versión 3.1.x**
> 
>    Los nombres de dependencias de bases de datos ahora son más concisos y siguen teniendo la consulta completa (saneada) en el campo `data`. Además, los nombres de dependencias HTTP ahora son más descriptivos.
>    Como consecuencia, los paneles o las alertas personalizados pueden verse afectados si se basaban en los valores anteriores.
>    Consulte las [notas de la versión 3.2.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0) para más información.

#### <a name="2-point-the-jvm-to-the-jar-file"></a>2. Apuntar la JVM al archivo JAR.

Agregue `-javaagent:path/to/applicationinsights-agent-3.2.0.jar` a los argumentos de JVM de la aplicación. 

> [!TIP]
> Para obtener ayuda con la configuración de los argumentos de JVM de la aplicación, consulte [Sugerencias para actualizar los argumentos de JVM](./java-standalone-arguments.md).

#### <a name="3-set-application-insights-connection-string"></a>3. Establecer la cadena de conexión de Application Insights.

Apunte el archivo JAR al recurso de Application Insights, ya sea estableciendo una variable de entorno:

```console
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

O creando un archivo de configuración denominado `applicationinsights.json` y colocándolo en el mismo directorio que `applicationinsights-agent-3.2.0.jar`, con el siguiente contenido:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Busque la cadena de conexión en el recurso de Application Insights.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Cadena de conexión de Application Insights":::

#### <a name="4-confirm-data-is-flowing"></a>4. Confirmar que los datos fluyen.

Ejecute la aplicación y abra la pestaña Recurso de Application Insights en Azure Portal. Los datos pueden tardar unos minutos en aparecer en el portal.

> [!NOTE]
> Si no puede ejecutar la aplicación o no obtiene los datos esperados, vaya a [Solución de problemas](#troubleshooting).

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Captura de pantalla de la pestaña Información general de Application Insights con las solicitudes de servidor y el tiempo de respuesta del servidor resaltados.":::

> [!IMPORTANT]
> Si tiene dos o más servicios que emiten telemetría al mismo recurso de Application Insights, debe [establecer nombres de rol en la nube](java-standalone-config.md#cloud-role-name) para representarlos correctamente en el mapa de aplicación.


## <a name="configuration-options"></a>Opciones de configuración

En el archivo `applicationinsights.json`, también puede configurar lo siguiente:

* Nombre del rol en la nube
* Instancia de rol en la nube
* muestreo
* Métricas JMX
* Dimensiones personalizadas
* Procesadores de telemetría (versión preliminar)
* Registros recopilados automáticamente
* Métricas de Micrometer recopiladas automáticamente (incluidas las métricas del accionador de Spring Boot)
* Latido
* Proxy HTTP
* Diagnóstico automático

Consulte las [opciones de configuración](./java-standalone-config.md) para obtener todos los detalles.

## <a name="instrumentation-libraries"></a>Bibliotecas de instrumentación

Java 3.X incluye las siguientes bibliotecas de instrumentación.

### <a name="auto-collected-requests"></a>Solicitudes recopiladas automáticamente

* Consumidores de JMS
* Consumidores de Kafka
* Netty/WebFlux
* Servlets
* Programación de Spring

### <a name="auto-collected-dependencies"></a>Dependencias recopiladas automáticamente

Dependencias recopiladas automáticamente más la propagación de seguimiento distribuido de bajada:

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS Client
* Jetty HttpClient
* JMS
* Kafka
* Cliente Netty
* OkHttp

Dependencias recopiladas automáticamente (sin propagación de seguimiento distribuido de bajada):

* Cassandra
* JDBC
* MongoDB (asincrónico y sincrónico)
* Redis (Lettuce y Jedis)

### <a name="auto-collected-logs"></a>Registros recopilados automáticamente

* java.util.logging
* Log4j (incluidas las propiedades de MDC)
* SLF4J/Logback (incluidas las propiedades de MDC)

### <a name="auto-collected-metrics"></a>Métricas recopiladas automáticamente

* Micrometer (incluidas las métricas del actuador de Spring Boot)
* Métricas JMX

### <a name="azure-sdks-preview"></a>SDK de Azure (versión preliminar)

La telemetría emitida por estos SDK de Azure se recopila automáticamente de forma predeterminada:

* [App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Cognitive Search](/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Communication Phone Numbers](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0+
* [Communication SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Digital Twins: básico](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0+
* [Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs: almacén de puntos de control de Azure Blob Storage](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identity](/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault: certificados](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault: claves](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault: secretos](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Storage: Blobs](/java/api/overview/azure/storage-blob-readme) 12.11.0+
* [Storage: Blobs Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0+
* [Storage: Blobs Cryptography](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0+
* [Storage: Common](/java/api/overview/azure/storage-common-readme) 12.11.0+
* [Storage: Files Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0+
* [Storage: Files Shares](/java/api/overview/azure/storage-file-share-readme) 12.9.0+
* [Storage: Queues](/java/api/overview/azure/storage-queue-readme) 12.9.0+
* [Text Analytics](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "los nombres y vínculos anteriores que se han extraído de https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "y la versión se sincronizó manualmente con la versión más antigua del centro de Maven en función de azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    continue"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="modify-telemetry"></a>Modificación de la telemetría

### <a name="add-span-attributes"></a>Incorporación de atributos de intervalo
Puede usar `opentelemetry-api` para agregar atributos a intervalos. Estos atributos pueden incluir la incorporación de una dimensión empresarial personalizada a la telemetría. También puede usar atributos para establecer campos opcionales en el esquema de Application Insights, como Id. de usuario o IP del cliente.

#### <a name="add-custom-dimension"></a>Adición de dimensiones personalizadas
Al agregar una o varias dimensiones personalizadas, se rellenará el campo _customDimensions_ en la tabla de solicitudes, dependencias o excepciones.

> [!NOTE]
> Esta característica solo está disponible en la versión 3.2.0 y posteriores.

Agregue `opentelemetry-api-1.6.0.jar` a la aplicación.

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Agregue también dimensiones personalizadas al código:

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("mycustomdimension", "myvalue1");
```

#### <a name="set-user-id"></a>Establecimiento del identificador de usuario
Rellene el campo de identificador de usuario en la tabla de solicitudes, dependencias o excepciones.

> [!IMPORTANT]
> Consulte las leyes de privacidad aplicables antes de establecer el identificador de usuario autenticado.

> [!NOTE]
> Esta característica solo está disponible en la versión 3.2.0 y posteriores.

Agregue `opentelemetry-api-1.6.0.jar` a la aplicación.

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Establezca `user_Id` en el código:

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("enduser.id", "myuser");
```

### <a name="get-trace-id-or-span-id"></a>Obtención del identificador de seguimiento o el identificador de intervalo

Puede usar `opentelemetry-api` para obtener el identificador de seguimiento o el identificador de intervalo. Esta acción puede hacerse para agregar estos identificadores a la telemetría de registro existente a fin de mejorar la correlación al depurar y diagnosticar problemas.

> [!NOTE]
> Esta característica solo está disponible en la versión 3.2.0 y posteriores.

Agregue `opentelemetry-api-1.6.0.jar` a la aplicación.

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

y obtenga el identificador de seguimiento de la solicitud y el identificador de intervalo en el código:

```java
import io.opentelemetry.api.trace.Span;

String traceId = Span.current().getSpanContext().getTraceId();
String spanId = Span.current().getSpanContext().getSpanId();
```
## <a name="custom-telemetry"></a>Telemetría personalizada

Nuestro objetivo con Application Insights para Java 3.x es permitirle enviar telemetría personalizada mediante API estándar.

Por ahora, puede usar Micrometer, plataformas de registro populares y el SDK de Java 2.x de Application Insights.
Application Insights para Java 3.x capturará automáticamente la telemetría, la enviará a través de las API y la correlacionará con la telemetría recopilada automáticamente.

### <a name="supported-custom-telemetry"></a>Telemetría personalizada admitida

En la tabla siguiente se representan los tipos de telemetría personalizados admitidos actualmente que se pueden habilitar para complementar el agente Java 3.x. En resumen, las métricas personalizadas se admiten a través de un micrómetro, las excepciones y seguimientos personalizados se pueden habilitar a través de las plataformas de registro, las solicitudes personalizadas, las dependencias y las excepciones se pueden habilitar a través de `opentelemetry-api` y cualquier tipo de telemetría personalizada se admite mediante el [SDK de Java 2.x para Application Insights](#send-custom-telemetry-using-the-2x-sdk).

|                     | Micrómetro | Log4j, logback, JUL | SDK 2.x | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| **Eventos personalizados**   |            |                     |  Sí    |                   |
| **Métricas personalizadas**  |  Sí       |                     |  Sí    |                   |
| **Dependencias**    |            |                     |  Sí    |  Sí              |
| **Excepciones**      |            |  Sí                |  Sí    |  Sí              |
| **Vistas de página**      |            |                     |  Sí    |                   |
| **Solicitudes**        |            |                     |  Sí    |  Sí              |
| **Traces**          |            |  Sí                |  Sí    |                   |

No tenemos previsto publicar ningún SDK con Application Insights 3.x en este momento.

Application Insights para Java 3.x ya escucha la telemetría que se envía al SDK de Application Insights para Java 2.x. Esta funcionalidad es una parte importante de la historia de actualización de los usuarios existentes de 2.x y llena una brecha importante en la compatibilidad con la telemetría personalizada hasta que OpenTelemetry API ofrezca disponibilidad general.

### <a name="send-custom-metrics-using-micrometer"></a>Envío de métricas personalizadas mediante Micrometer

Agregue Micrometer a la aplicación:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Use el [registro global](https://micrometer.io/docs/concepts#_global_registry) de Micrometer para crear un medidor:

```java
static final Counter counter = Metrics.counter("test_counter");
```

y úselo para registrar las métricas:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Envío de seguimientos y excepciones personalizados mediante su plataforma de registro favorita

Log4j, Logback y java.util.logging se instrumentan automáticamente y los registros creados mediante estas plataformas de registro se recopilan automáticamente como telemetría de seguimiento y excepciones.

De forma predeterminada, los registros solo se recopilan cuando se crean en el nivel INFO o superior.
Consulte las [opciones de configuración](./java-standalone-config.md#auto-collected-logging) para cambiar este nivel.

Si quiere adjuntar dimensiones personalizadas a los registros, puede usar [Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) o [Logback MDC](http://logback.qos.ch/manual/mdc.html), y Application Insights para Java 3.x se encargará de capturar automáticamente las propiedades de MDC como dimensiones personalizadas en la telemetría de seguimiento y excepciones.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Envío de telemetría personalizada mediante el SDK 2.x

Agregue `applicationinsights-core-2.6.3.jar` a la aplicación (todas las versiones 2.x son compatibles con Application Insights para Java 3.x, pero merece la pena usar la más reciente si es posible):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.3</version>
</dependency>
```

Cree un elemento TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

y úselo para enviar telemetría personalizada:

##### <a name="events"></a>Eventos

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Métricas

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Dependencias

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Registros

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Excepciones

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

## <a name="troubleshooting"></a>Solución de problemas
Vea [Solución de problemas](java-standalone-troubleshoot.md).

## <a name="support"></a>Soporte técnico
- Revise [Pasos de solución de problemas](java-standalone-troubleshoot.md).
- En caso de problemas de Soporte técnico de Azure, abra una [incidencia de Soporte técnico de Azure](https://azure.microsoft.com/support/create-ticket/).
- En caso de problemas de OpenTelemetry, póngase en contacto directamente con la [comunidad de OpenTelemetry](https://opentelemetry.io/community/).

## <a name="opentelemetry-feedback"></a>Comentarios de OpenTelemetry
- Rellene la [encuesta de comentarios de los clientes](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform) de la comunidad de OpenTelemetry.
- Únase a la [comunidad de usuarios pioneros de OpenTelemetry](https://aka.ms/AzMonOTel/) y cuente a Microsoft un poco sobre sí mismo.
- Interactúe con otros usuarios de Azure Monitor en [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).

## <a name="next-steps"></a>Pasos siguientes

- Revise el código fuente en el [repositorio de GitHub de instrumentación automática de Java para Azure Monitor](https://github.com/Microsoft/ApplicationInsights-Java).
- Para más información sobre OpenTelemetry y su comunidad, visite el [repositorio de GitHub de Java para OpenTelemetry](https://github.com/open-telemetry/opentelemetry-java-instrumentation).
- [Habilite la supervisión de usuarios web o de explorador](javascript.md) para habilitar experiencias de uso.
