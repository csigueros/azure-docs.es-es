---
title: 'Supervisión del rendimiento de aplicaciones web Java: Azure Application Insights'
description: Supervisión extendida del rendimiento y el uso de su sitio web de Java con Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3489591a39901a79323faf307b8700863f71522a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110105460"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Supervisión de dependencias, excepciones detectadas y tiempos de ejecución del método en aplicaciones web de Java

> [!CAUTION]
> Este documento se aplica a Application Insights para Java 2.x, que ya no se recomienda.
>
> La documentación de la versión más reciente se puede encontrar en [Application Insights para Java 3.x](./java-in-process-agent.md).

Si ha [instrumentado la aplicación web de Java con el SDK de Application Insights][java], puede usar el agente de Java para obtener información más detallada, sin tener que realizar cambios de código:

* **Dependencias:** datos sobre las llamadas realizadas por la aplicación a otros componentes, por ejemplo:
  * Se capturan las **llamadas HTTP salientes** realizadas a través de Apache HttpClient, OkHttp y `java.net.HttpURLConnection`.
  * Se capturan las **llamadas Redis** realizadas a través del cliente de Jedis.
  * **Consultas JDBC**: en el caso de MySQL y PostgreSQL, si la llamada tarda más de 10 segundos, el agente notifica el plan de consulta.

* **Registro de aplicaciones**: puede capturar y poner en correlación los registros de aplicaciones con solicitudes HTTP y otra telemetría.
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **Nomenclatura mejorada para las opciones**: (se usa para la agregación de solicitudes en el portal).
  * **Spring**: se basa en `@RequestMapping`.
  * **JAX-RS**: se basa en `@Path`. 

Para usar el agente de Java, debe instalarlo en el servidor. Las aplicaciones web deben instrumentarse con el [SDK de Application Insights para Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalación del agente de Application Insights para Java
1. En la máquina que ejecuta el servidor de Java, [descargue el agente 2.x](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2). Asegúrese de que la versión del agente Java 2 x que usa coincide con la versión del SDK Application Insights de 2.x para Java que usa.
2. Edite el script de inicio del servidor de aplicaciones y agregue el siguiente argumento de JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Por ejemplo, en Tomcat en un equipo Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie el servidor de aplicaciones.

## <a name="configure-the-agent"></a>Configuración del agente
Cree un archivo denominado `AI-Agent.xml` y colóquelo en la misma carpeta que el archivo JAR del agente.

Establezca el contenido del archivo XML. Edite el ejemplo siguiente para incluir u omitir las características que desee.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Configuración adicional (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para Azure App Service, haga lo siguiente:

* Seleccione Configuración > Configuración de la aplicación.
* En Configuración de la aplicación, agregue un nuevo par clave-valor:

Clave: `JAVA_OPTS` Valor: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

El agente debe estar empaquetado como un recurso en el proyecto de forma que termine en el directorio D:/home/site/wwwroot/. Para confirmar que el agente está en el directorio correcto de App Service, vaya a **Herramientas de desarrollo** > **Herramientas avanzadas** > **Consola de depuración** y examine el contenido del directorio del sitio.    

* Guarde la configuración y reinicie la aplicación. (Estos pasos solo se aplican a las instancias de App Services que se ejecutan en Windows).

> [!NOTE]
> AI-Agent.xml y el archivo jar del agente deben estar en la misma carpeta. A menudo se colocan juntos en la carpeta `/resources` del proyecto.  

#### <a name="enable-w3c-distributed-tracing"></a>Habilitación del seguimiento distribuido de W3C

Agregue lo siguiente al archivo AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> El modo de compatibilidad con versiones anteriores está habilitado de forma predeterminada, y el parámetro enableW3CBackCompat es opcional y debe usarse solo cuando quiera desactivarlo. 

Lo ideal sería cuando todos los servicios se han actualizado a la versión más reciente de los SDK compatibles con el protocolo W3C. Se recomienda encarecidamente pasar a la versión más reciente de los SDK con compatibilidad con W3C lo antes posible.

Asegúrese de que **las configuraciones (del agente) tanto [entrantes](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) como salientes** sean exactamente iguales.

## <a name="view-the-data"></a>Visualización de los datos
En el recurso de Application Insights, aparecen tiempos de ejecución agregados de métodos y dependencias remotos [en el icono Rendimiento][metrics].

Para buscar instancias individuales de informes de dependencia, excepción y método, abra [Buscar][diagnostic].

[Más información sobre diagnósticos de problemas de dependencia](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>¿Tiene preguntas? ¿Tiene problemas?
* ¿No hay datos? [Establecer excepciones del firewall](./ip-addresses.md)
* [Solución de problemas de Java](java-2x-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-in-process-agent.md
[javalogs]: java-2x-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

