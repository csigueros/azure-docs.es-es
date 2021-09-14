---
title: Referencia de variables de entorno y configuración de la aplicación
description: Describe las variables de entorno que se usan habitualmente y cuáles se pueden modificar con la configuración de la aplicación.
ms.topic: article
ms.date: 06/14/2021
ms.openlocfilehash: 5cba4a7d66f5b2bb705df8c685b6c8be05e04a08
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272356"
---
# <a name="environment-variables-and-app-settings-in-azure-app-service"></a>Variables de entorno y configuración de la aplicación en Azure App Service

En [Azure App Service](overview.md), ciertas configuraciones están disponibles para la implementación o el entorno de ejecución como variables de entorno. Algunas de estas opciones se pueden personalizar al establecerlas manualmente como [configuración de la aplicación](configure-common.md#configure-app-settings). En esta documentación de referencia, se muestran las variables que puede usar o personalizar.

## <a name="app-environment"></a>Entorno de la aplicación

Las siguientes variables de entorno están relacionadas con el entorno de la aplicación en general.

| Nombre del valor| Descripción | Ejemplo |
|-|-|-|
| `WEBSITE_SITE_NAME` | Solo lectura. Nombre de la aplicación. ||
| `WEBSITE_RESOURCE_GROUP` | Solo lectura. Nombre del grupo de recursos de Azure que contiene el recurso de la aplicación. ||
| `WEBSITE_OWNER_NAME` | Solo lectura. Contiene el identificador de suscripción de Azure que posee la aplicación, el grupo de recursos y el espacio web. ||
| `REGION_NAME` | Solo lectura. Nombre de la región de la aplicación. ||
| `WEBSITE_PLATFORM_VERSION` | Solo lectura. Versión de la plataforma de App Service. ||
| `HOME` | Solo lectura. Ruta de acceso al directorio principal (por ejemplo, `D:\home` para Windows). ||
| `SERVER_PORT` | Solo lectura. Puerto en el que debe escuchar la aplicación. | |
| `WEBSITE_WARMUP_PATH`  | Ruta de acceso relativa para hacer ping para activar la aplicación; comienza por una barra diagonal. El valor predeterminado es `/`, que hace ping a la ruta de acceso raíz. Un cliente no autenticado, como Azure Traffic Manager, puede hacer ping a la ruta de acceso específica incluso si la [autenticación de App Service](overview-authentication-authorization.md) está establecida para rechazar clientes no autenticados. (NOTA: Esta configuración de la aplicación no cambia la ruta de acceso utilizada por AlwaysOn). ||
| `WEBSITE_COMPUTE_MODE` | Solo lectura. Especifica si la aplicación se ejecuta en máquinas virtuales dedicadas (`Dedicated`) o compartidas (`Shared`). ||
| `WEBSITE_SKU` | Solo lectura. SKU de la aplicación. Los valores posibles son `Free`, `Shared`, `Basic` y `Standard`. ||
| `SITE_BITNESS` | Solo lectura. Muestra si la aplicación es de 32 bits (`x86`) o 64 bits (`AMD64`). ||
| `WEBSITE_HOSTNAME` | Solo lectura. Nombre de host principal de la aplicación. Los nombres de host personalizados no se tienen en cuenta aquí. ||
| `WEBSITE_VOLUME_TYPE` | Solo lectura. Muestra el tipo de volumen de almacenamiento actualmente en uso. ||
| `WEBSITE_NPM_DEFAULT_VERSION` | Versión predeterminada de npm que usa la aplicación. ||
| `WEBSOCKET_CONCURRENT_REQUEST_LIMIT` | Solo lectura. Límite de solicitudes simultáneas de websocket. Para el nivel **estándar** y superiores, el valor es `-1`, pero aún hay un límite por cada máquina virtual en función del tamaño de la máquina virtual (consulte [Límites numéricos en máquinas virtuales](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)). ||
| `WEBSITE_PRIVATE_EXTENSIONS` | Establézcalo en `0` para deshabilitar el uso de extensiones de sitio privadas. ||
| `WEBSITE_TIME_ZONE` | De manera predeterminada, la zona horaria de la aplicación siempre es UTC. Puede cambiarla a cualquiera de los valores válidos que se enumeran en [TimeZone](/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). Si no se reconoce el valor especificado, se usa UTC. | `Atlantic Standard Time` |
| `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG` | En el caso de una conmutación por error o una reconfiguración del volumen de almacenamiento, la aplicación se cambia a un volumen de almacenamiento en espera. La configuración predeterminada establecida en `1` impide que el proceso de trabajo se recicle cuando cambie la infraestructura de almacenamiento. Si ejecuta una aplicación de Windows Communication Foundation (WCF), establezca el valor en `0` para deshabilitar esta opción. La configuración es específica de la ranura, por lo que debe establecerla en todas las ranuras. ||
| `WEBSITE_PROACTIVE_AUTOHEAL_ENABLED` | De manera predeterminada, una instancia de máquina virtual se "recupera automáticamente" de forma proactiva cuando usa más del 90 % de la memoria asignada durante más de 30 segundos o cuando el 80 % del total de solicitudes de los últimos dos minutos tarda más de 200 segundos. Si una instancia de máquina virtual ha desencadenado una de estas reglas, el proceso de recuperación es un reinicio superpuesto de la instancia. Establézcalo en `false` para deshabilitar este comportamiento de recuperación. El valor predeterminado es `true`. Para más información, consulte [Recuperación automática proactiva](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html). ||
| `WEBSITE_PROACTIVE_CRASHMONITORING_ENABLED` | Siempre que el proceso w3wp.exe de una instancia de máquina virtual de la aplicación se bloquee debido a una excepción no controlada más de tres veces en 24 horas, se adjunta un proceso del depurador al proceso de trabajo principal de esa instancia y se recopila un volcado de memoria cuando el proceso de trabajo se vuelva a bloquear. A continuación, se analiza este volcado de memoria y la pila de llamadas del subproceso que produjo el bloqueo se registra en los registros de la instancia de App Service. Establézcalo en `false` para deshabilitar este comportamiento de supervisión automático. El valor predeterminado es `true`. Para más información, consulte [Supervisión proactiva de los bloqueos](https://azure.github.io/AppService/2021/03/01/Proactive-Crash-Monitoring-in-Azure-App-Service.html). ||
| `WEBSITE_DAAS_STORAGE_SASURI` | Durante la supervisión de los bloqueos (proactiva o manual), los volcados de memoria se eliminan de manera predeterminada. Para guardar los volcados de memoria en un contenedor de blobs de almacenamiento, especifique el identificador URI de SAS.  ||
| `WEBSITE_CRASHMONITORING_ENABLED` | Establézcalo en `true` para habilitar la [supervisión de los bloqueos](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) de forma manual. También debe establecer `WEBSITE_DAAS_STORAGE_SASURI` y `WEBSITE_CRASHMONITORING_SETTINGS`. El valor predeterminado es `false`. Esta configuración no tiene ningún efecto si está habilitada la depuración remota. Además, si esta configuración se establece en `true`, se deshabilita la [supervisión proactiva de bloqueos](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html). ||
| `WEBSITE_CRASHMONITORING_SETTINGS` | Código JSON con el formato siguiente: `{"StartTimeUtc": "2020-02-10T08:21","MaxHours": "<elapsed-hours-from-StartTimeUtc>","MaxDumpCount": "<max-number-of-crash-dumps>"}`. Necesario para configurar la [supervisión de bloqueos](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) si se especifica `WEBSITE_CRASHMONITORING_ENABLED`. Para registrar solo la pila de llamadas sin guardar el volcado de memoria en la cuenta de almacenamiento, agregue `,"UseStorageAccount":"false"` en el código JSON. ||
| `REMOTEDEBUGGINGVERSION` | Versión de depuración remota. ||
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | De manera predeterminada, App Service crea un almacenamiento compartido durante la creación de la aplicación. Para usar una cuenta de almacenamiento personalizada en su lugar, se debe establecer en la cadena de conexión de la cuenta de almacenamiento. Para las funciones, consulte [Referencia de configuración de aplicación para Functions](../azure-functions/functions-app-settings.md#website_contentazurefileconnectionstring). | `DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>` |
| `WEBSITE_CONTENTSHARE` | Cuando se especifica una cuenta de almacenamiento personalizada con `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`, App Service crea un recurso compartido de archivos en esa cuenta de almacenamiento para la aplicación. Para usar un nombre personalizado, establezca esta variable en el nombre que desee. Si no existe un recurso compartido de archivos con el nombre especificado, App Service lo crea automáticamente. | `myapp123` |
| `WEBSITE_SCM_ALWAYS_ON_ENABLED` | Solo lectura. Muestra si Always On está habilitado (`1`) o no (`0`). ||
| `WEBSITE_SCM_SEPARATE_STATUS` | Solo lectura. Muestra si la aplicación de Kudu se ejecuta en un proceso independiente (`1`) o no (`0`). ||

<!-- 
WEBSITE_PROACTIVE_STACKTRACING_ENABLED
WEBSITE_CLOUD_NAME
WEBSITE_MAXIMUM_CONCURRENTCOLDSTARTS
HOME_EXPANDED
USERPROFILE
WEBSITE_ISOLATION
WEBSITE_OS | only appears on windows
WEBSITE_CLASSIC_MODE
 -->

## <a name="variable-prefixes"></a>Prefijos de variable

En la tabla siguiente, se muestran los prefijos de variables de entorno que utiliza App Service para varios propósitos.

| Nombre del valor | Descripción |
|-|-|
| `APPSETTING_` | Indica que el cliente establece una variable como una valor de la aplicación en la configuración de la aplicación. Se inserta en una aplicación .NET como una configuración de la aplicación. |
| `MAINSITE_` | Indica que una variable es específica de la propia aplicación. |
| `SCMSITE_` | Indica que una variable es específica de la aplicación de Kudu. |
| `SQLCONNSTR_` | Indica una cadena de conexión de SQL Server en la configuración de la aplicación. Se inserta en una aplicación .NET como una cadena de conexión. |
| `SQLAZURECONNSTR_` | Indica una cadena de conexión de Azure SQL Database en la configuración de la aplicación. Se inserta en una aplicación .NET como una cadena de conexión. |
| `POSTGRESQLCONNSTR_` | Indica una cadena de conexión de PostgreSQL en la configuración de la aplicación. Se inserta en una aplicación .NET como una cadena de conexión. |
| `CUSTOMCONNSTR_` | Indica una cadena de conexión personalizada en la configuración de la aplicación. Se inserta en una aplicación .NET como una cadena de conexión. |
| `MYSQLCONNSTR_` | Indica una cadena de conexión de Azure SQL Database en la configuración de la aplicación. Se inserta en una aplicación .NET como una cadena de conexión. |
| `AZUREFILESSTORAGE_` | Cadena de conexión a un recurso compartido de archivos de Azure personalizado para una aplicación de contenedor. |
| `AZUREBLOBSTORAGE_` | Cadena de conexión a una instancia de Azure Blob Storage personalizada para una aplicación de contenedor. |

## <a name="deployment"></a>Implementación

Las siguientes variables de entorno están relacionadas con la implementación de aplicaciones. Para ver las variables relacionadas con la automatización de compilaciones de App Service, consulte [Automatización de compilaciones](#build-automation).

| Nombre del valor| Descripción |
|-|-|
| `DEPLOYMENT_BRANCH`| Para una implementación de [Git local](deploy-local-git.md) o [Git en la nube](deploy-continuous-deployment.md) (como GitHub), establezca el valor en la rama de Azure en la que quiere realizar la implementación. De forma predeterminada, es `master`. |
| `WEBSITE_RUN_FROM_PACKAGE`| Establezca el valor en `1` para ejecutar la aplicación desde un paquete ZIP local o establézcalo en la dirección URL de una dirección URL externa para ejecutar la aplicación desde un paquete ZIP remoto. Para más información, consulte [Ejecución de una aplicación en Azure App Service directamente desde un paquete ZIP](deploy-run-package.md). |
| `WEBSITE_USE_ZIP` | En desuso. Use `WEBSITE_RUN_FROM_PACKAGE`. |
| `WEBSITE_RUN_FROM_ZIP` | En desuso. Use `WEBSITE_RUN_FROM_PACKAGE`. | 
| `WEBSITE_WEBDEPLOY_USE_SCM` | Establezca el valor en `false` para que WebDeploy deje de usar el motor de implementación de Kudu. El valor predeterminado es `true`. Para implementar en aplicaciones Linux mediante Visual Studio (WebDeploy/MSDeploy), establézcalo en `false`. |
| `MSDEPLOY_RENAME_LOCKED_FILES` | Establezca el valor en `1` para intentar cambiar el nombre de los archivos DLL si no se pueden copiar durante una implementación de WebDeploy. Esta configuración no es aplicable si `WEBSITE_WEBDEPLOY_USE_SCM` está establecido en `false`. |
| `WEBSITE_DISABLE_SCM_SEPARATION` | De manera predeterminada, la aplicación principal y la aplicación de Kudu se ejecutan en distintos espacios aislados. Cuando se detiene la aplicación, la aplicación de Kudu todavía está en ejecución y puede seguir usando la implementación de Git y MSDeploy. Cada aplicación tiene sus propios archivos locales. Desactivar esta separación (establecer en `false`) es un modo heredado que ya no es totalmente compatible. |
| `WEBSITE_ENABLE_SYNC_UPDATE_SITE` | Establezca el valor en `1` para garantizar que las llamadas a la API REST para actualizar `site` y `siteconfig` se aplican completamente a todas las instancias antes de devolver. El valor predeterminado es `1` si se implementa con una plantilla de ARM, para evitar condiciones de carrera con llamadas de ARM posteriores. |
| `WEBSITE_START_SCM_ON_SITE_CREATION` | En una implementación de plantilla de ARM, establezca el valor en `1` en la plantilla de ARM para iniciar previamente la aplicación de Kudu como parte de la creación de la aplicación. |
| `WEBSITE_START_SCM_WITH_PRELOAD` | En el caso de las aplicaciones Linux, establezca el valor en `true` para forzar la carga previa de la aplicación de Kudu cuando Always On esté habilitado; para ello, haga ping a su dirección URL. El valor predeterminado es `false`. Para aplicaciones Windows, la aplicación de Kudu siempre se carga previamente. |

<!-- 
WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID
-->

## <a name="build-automation"></a>Automatización de compilaciones

# <a name="kudu-windows"></a>[Kudu (Windows)](#tab/kudu)

La configuración de compilación de Kudu se aplica a las aplicaciones Windows nativas y se usa para controlar el comportamiento de las implementaciones basadas en Git (o basadas en ZIP).

| Nombre del valor| Descripción | Ejemplo |
|-|-|-|
| `SCM_BUILD_ARGS` | Agregue elementos al final de la línea de comandos de msbuild, de modo que invalide cualquier parte anterior de la línea de comandos predeterminada. | Para realizar una compilación limpia: `-t:Clean;Compile`|
| `SCM_SCRIPT_GENERATOR_ARGS` | Kudu usa el comando `azure site deploymentscript`, que se describe [aquí](http://blog.amitapple.com/post/38418009331/azurewebsitecustomdeploymentpart2), para generar un script de implementación. Detecta automáticamente el tipo de marco de lenguaje y determina los parámetros que se pasan al comando. Esta configuración invalida los parámetros generados automáticamente. | Para tratar el repositorio como archivos de contenido sin formato: `--basic -p <folder-to-deploy>` |
| `SCM_TRACE_LEVEL` | Nivel de seguimiento de compilación. El valor predeterminado es `1`. Establézcalo en valores superiores, hasta 4, para un seguimiento mayor. | `4` |
| `SCM_COMMAND_IDLE_TIMEOUT` | Tiempo de espera en segundos para cada comando que inicia el proceso de compilación para esperar antes sin generar ninguna salida. Después de ese tiempo, el comando se considera inactivo y se elimina. El valor predeterminado es `60` (un minuto). En Azure, también hay un tiempo de espera de solicitud de inactividad general que desconecta a los clientes después de 230 segundos. Sin embargo, el comando seguirá ejecutándose en el lado servidor después de eso. | |
| `SCM_LOGSTREAM_TIMEOUT` | Tiempo de espera de inactividad en segundos antes de detener el streaming de registro. El valor predeterminado es `1800` ( 30 minutos).| |
| `SCM_SITEEXTENSIONS_FEED_URL` | Dirección URL de la galería de extensiones de sitio. El valor predeterminado es `https://www.nuget.org/api/v2/`. La dirección URL de la fuente anterior es `http://www.siteextensions.net/api/v2/`. | |
| `SCM_USE_LIBGIT2SHARP_REPOSITORY` | Establezca el valor en `0` para usar git.exe en lugar de libgit2sharp para las operaciones de Git. | |
| `WEBSITE_LOAD_USER_PROFILE` | En caso de recibir el error `The specified user does not have a valid profile.` (El usuario especificado no tiene un perfil válido) durante la automatización de la compilación de ASP.NET (por ejemplo, durante la implementación de Git), establezca esta variable en `1` para cargar un perfil de usuario completo en el entorno de compilación. Esta configuración solo es aplicable cuando el valor de `WEBSITE_COMPUTE_MODE` es `Dedicated`. | |
| `WEBSITE_SCM_IDLE_TIMEOUT_IN_MINUTES` | Tiempo de espera en minutos para el sitio de SCM (Kudu). El valor predeterminado es `20`. | |
| `SCM_DO_BUILD_DURING_DEPLOYMENT` | Con la [implementación de archivo ZIP](deploy-zip.md), el motor de implementación da por supuesto que un archivo ZIP está listo para ejecutarse tal cual y no ejecuta ninguna automatización de la compilación. Para habilitar la misma automatización de compilación que en la [implementación de Git](deploy-local-git.md), establezca el valor en `true`. |

<!-- 
SCM_GIT_USERNAME
SCM_GIT_EMAIL
 -->

# <a name="oryx-linux"></a>[Oryx (Linux)](#tab/oryx)

La configuración de compilación de Oryx se aplica a las aplicaciones Linux y se usa para controlar el comportamiento de las implementaciones basadas en Git (o basadas en ZIP). Consulte [Configuración de Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

-----

## <a name="language-specific-settings"></a>Opciones específicas del lenguaje

En esta sección, se muestra la configuración en tiempo de ejecución que se puede configurar para cada marco de lenguaje admitido. Hay disponibles configuraciones adicionales durante la [automatización de la compilación](#build-automation) en tiempo de implementación.

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- 
| DOTNET_HOSTING_OPTIMIZATION_CACHE | 
 -->
| Nombre del valor | Descripción |
|-|-|
| `PORT` | Solo lectura. En el caso de las aplicaciones Linux, el puerto en el que escucha el entorno de ejecución de .NET en el contenedor. |
| `WEBSITE_ROLE_INSTANCE_ID` | Solo lectura. Identificador de la instancia actual. |
| `HOME` | Solo lectura. Directorio que apunta al almacenamiento compartido (`/home`). |
| `DUMP_DIR` | Solo lectura. Directorio para los volcados de memoria (`/home/logs/dumps`). |
| `APP_SVC_RUN_FROM_COPY` | Solo aplicaciones Linux. De manera predeterminada, la aplicación se ejecuta desde `/home/site/wwwroot`, un directorio compartido para todas las instancias de escalado horizontal. Establezca esta variable en `true` para copiar la aplicación en un directorio local del contenedor y ejecutarla desde allí. Al usar esta opción, asegúrese de no codificar de forma rígida ninguna referencia a `/home/site/wwwroot`. En su lugar, use una ruta de acceso relativa a `/home/site/wwwroot`. |
| `MACHINEKEY_Decryption` | Para aplicaciones nativas de Windows o aplicaciones de contenedor de Windows, esta variable se inserta en el entorno de la aplicación o el contenedor para habilitar las rutinas criptográficas de ASP.NET (consulte [Elemento machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Para invalidar el valor `decryption` predeterminado, configúrelo como valor de aplicación de App Service, o establézcalo directamente en el elemento `machineKey` del archivo *Web.config*. |
| `MACHINEKEY_DecryptionKey` | Para aplicaciones nativas de Windows o aplicaciones de contenedor de Windows, esta variable se inserta en el entorno de la aplicación o el contenedor para habilitar las rutinas criptográficas de ASP.NET (consulte [Elemento machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Para invalidar el valor `decryptionKey` generado automáticamente, configúrelo como valor de aplicación de App Service, o establézcalo directamente en el elemento `machineKey` del archivo *Web.config*.|
| `MACHINEKEY_Validation` | Para aplicaciones nativas de Windows o aplicaciones de contenedor de Windows, esta variable se inserta en el entorno de la aplicación o el contenedor para habilitar las rutinas criptográficas de ASP.NET (consulte [Elemento machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Para invalidar el valor `validation` predeterminado, configúrelo como valor de aplicación de App Service, o establézcalo directamente en el elemento `machineKey` del archivo *Web.config*.|
| `MACHINEKEY_ValidationKey` | Para aplicaciones nativas de Windows o aplicaciones de contenedor de Windows, esta variable se inserta en el entorno de la aplicación o el contenedor para habilitar las rutinas criptográficas de ASP.NET (consulte [Elemento machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Para invalidar el valor `validationKey` generado automáticamente, configúrelo como valor de aplicación de App Service, o establézcalo directamente en el elemento `machineKey` del archivo *Web.config*.|
<!-- | `USE_DOTNET_MONITOR` | if =true then /opt/dotnetcore-tools/dotnet-monitor collect --urls "http://0.0.0.0:50051" --metrics true --metricUrls "http://0.0.0.0:50050" > /dev/null 2>&1 & -->

# <a name="java"></a>[Java](#tab/java)

| Nombre del valor | Descripción | Ejemplo |
|-|-|-|
| `JAVA_HOME` | Ruta de acceso del directorio de instalación de Java ||
| `JAVA_OPTS` | En el caso de las aplicaciones de Java SE, las variables de entorno se pasan en el comando `java`. Puede contener variables del sistema. Para Tomcat, use `CATALINA_OPTS`. | `-Dmysysproperty=%DRIVEPATH%` |
| `AZURE_JAVA_APP_PATH` | Los scripts personalizados pueden usar la variable de entorno, por lo que tienen una ubicación para app.jar después de que se copie en el entorno local. | |
| `SKIP_JAVA_KEYSTORE_LOAD` | Un valor de 1 para deshabilitar en App Service la carga automática de los certificados en el almacén de claves. ||
| `WEBSITE_JAVA_JAR_FILE_NAME` | Archivo .jar que se va a utilizar. Anexe .jar si la cadena no termina en .jar. El valor predeterminado es app.jar. ||
| `WEBSITE_JAVA_WAR_FILE_NAME` | Archivo .war que se va a utilizar. Anexe .war si la cadena no termina en .war. El valor predeterminado es app.war. ||
| `JAVA_ARGS` | Opciones de Java requeridas por distintos servidores web de Java. De manera predeterminada, su valor es `-noverify -Djava.net.preferIPv4Stack=true`. ||
| `JAVA_WEBSERVER_PORT_ENVIRONMENT_VARIABLES` | Variables de entorno usadas por marcos web de Java populares para el puerto del servidor. Algunos marcos incluidos son: Spring, Micronaut, Grails, MicroProfile Thorntail, Helidon, Ratpack y Quarkus ||
| `JAVA_TMP_DIR` | Se ha agregado a los argumentos de Java como `-Dsite.tempdir`. Tiene como valor predeterminado `TEMP`. ||
| `WEBSITE_SKIP_LOCAL_COPY` | De manera predeterminada, el archivo app.jar implementado se copia desde `/home/site/wwwroot` en una ubicación local. Para deshabilitar este comportamiento y cargar el archivo app.jar directamente desde `/home/site/wwwroot`, establezca esta variable en `1` o `true`. Esta configuración no tiene ningún efecto si la memoria caché local está habilitada. | |
| `TOMCAT_USE_STARTUP_BAT` | Solo aplicaciones Windows nativas. De manera predeterminada, el servidor Tomcat se inicia con su archivo `startup.bat`. Para iniciar con el archivo `catalina.bat`, establezca el valor en `0` o `False`. | `%LOCAL_EXPANDED%\tomcat` |
| `CATALINA_OPTS` | En el caso de las aplicaciones de Tomcat, las variables de entorno se pasan en el comando `java`. Puede contener variables del sistema. | |
| `CATALINA_BASE` | Para usar una instalación personalizada de Tomcat, establezca el valor en la ubicación de la instalación. | |
| `WEBSITE_JAVA_MAX_HEAP_MB` | Montón máximo de Java en MB. Esta configuración solo es efectiva cuando se usa una versión experimental de Tomcat. | |
| `WEBSITE_DISABLE_JAVA_HEAP_CONFIGURATION` | Para deshabilitar manualmente `WEBSITE_JAVA_MAX_HEAP_MB`, establezca esta variable en `true` o `1`. | |
| `WEBSITE_AUTH_SKIP_PRINCIPAL` | De manera predeterminada, se hidratan las siguientes [interfaces HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) de Tomcat cuando habilita la [autenticación](overview-authentication-authorization.md) integrada: `isSecure`, `getRemoteAddr`, `getRemoteHost`, `getScheme` y `getServerPort`. Para deshabilitarlo, establezca el valor en `1`.  | |
| `WEBSITE_SKIP_FILTERS` | Para deshabilitar todos los filtros de servlet agregados por App Service, establezca el valor en `1`. ||
| `IGNORE_CATALINA_BASE` | De manera predeterminada, App Service comprueba si la variable `CATALINA_BASE` de Tomcat está definida. Si no lo está, busca la existencia de `%HOME%\tomcat\conf\server.xml`. Si existe el archivo, establece `CATALINA_BASE` en `%HOME%\tomcat`. Para deshabilitar este comportamiento y quitar `CATALINA_BASE`, establezca esta variable en `1` o `true`. ||
| `PORT` | Solo lectura. En el caso de las aplicaciones Linux, el puerto en el que escucha el entorno de ejecución de Java en el contenedor. | |
| `WILDFLY_VERSION` | Solo lectura. Para las aplicaciones de JBoss (Linux), es la versión de WildFly. | |
| `TOMCAT_VERSION` | Solo lectura. Para las aplicaciones de Tomcat de Linux, es la versión de Tomcat. ||
| `JBOSS_HOME` | Solo lectura. Para las aplicaciones de JBoss (Linux), es la ruta de acceso de la instalación de WildFly. | |
| `AZURE_JETTY9_CMDLINE` | Solo lectura. Para las aplicaciones Windows nativas, son los argumentos de la línea de comandos para iniciar Jetty 9. | |
| `AZURE_JETTY9_HOME` | Solo lectura. Para las aplicaciones Windows nativas, es la ruta de acceso a la instalación de Jetty 9.| |
| `AZURE_JETTY93_CMDLINE` | Solo lectura. Para las aplicaciones Windows nativas, son los argumentos de la línea de comandos para iniciar Jetty 9.3. | |
| `AZURE_JETTY93_HOME` | Solo lectura. Para las aplicaciones Windows nativas, es la ruta de acceso a la instalación de Jetty 9.3. | |
| `AZURE_TOMCAT7_CMDLINE` | Solo lectura. Para las aplicaciones Windows nativas, son los argumentos de la línea de comandos para iniciar Tomcat 7. | |
| `AZURE_TOMCAT7_HOME` | Solo lectura. Para las aplicaciones Windows nativas, es la ruta de acceso a la instalación de Tomcat 7. | |
| `AZURE_TOMCAT8_CMDLINE` | Solo lectura. Para las aplicaciones Windows nativas, son los argumentos de la línea de comandos para iniciar Tomcat 8. | |
| `AZURE_TOMCAT8_HOME` | Solo lectura. Para las aplicaciones Windows nativas, es la ruta de acceso a la instalación de Tomcat 8. | |
| `AZURE_TOMCAT85_CMDLINE` | Solo lectura. Para las aplicaciones Windows nativas, son los argumentos de la línea de comandos para iniciar Tomcat 8.5. | |
| `AZURE_TOMCAT85_HOME` | Solo lectura. Para las aplicaciones Windows nativas, es la ruta de acceso a la instalación de Tomcat 8.5. | |
| `AZURE_TOMCAT90_CMDLINE` | Solo lectura. Para las aplicaciones Windows nativas, son los argumentos de la línea de comandos para iniciar Tomcat 9. | |
| `AZURE_TOMCAT90_HOME` | Solo lectura. Para las aplicaciones Windows nativas, es la ruta de acceso a la instalación de Tomcat 9. | |
| `AZURE_SITE_HOME` | El valor se ha agregado a los argumentos de Java como `-Dsite.home`. El valor predeterminado es el valor de `HOME`. | |
| `HTTP_PLATFORM_PORT` | Se ha agregado a los argumentos de Java como `-Dport.http`. Las siguientes variables de entorno utilizadas por diferentes marcos web de Java también se establecen en este valor: `SERVER_PORT`, `MICRONAUT_SERVER_PORT`, `THORNTAIL_HTTP_PORT`, `RATPACK_PORT`, `QUARKUS_HTTP_PORT` y `PAYARAMICRO_PORT`. ||
| `AZURE_LOGGING_DIR` | Solo aplicaciones Windows nativas. Se ha agregado a los argumentos de Java como `-Dsite.logdir`. El valor predeterminado es `%HOME%\LogFiles\`. ||

<!-- 
WEBSITE_JAVA_COPY_ALL
AZURE_SITE_APP_BASE
 -->

# <a name="nodejs"></a>[Node.js](#tab/node)

| Nombre del valor | Descripción |
|-|-|
| `PORT` | Solo lectura. En el caso de las aplicaciones Linux, el puerto en el que escucha la aplicación de Node.js en el contenedor. |
| `WEBSITE_ROLE_INSTANCE_ID` | Solo lectura. Identificador de la instancia actual. |
| `PM2HOME` | |
| `WEBSITE_NODE_DEFAULT_VERSION` | Para las aplicaciones Windows nativas, es la versión predeterminada del nodo que usa la aplicación. Aquí se puede usar cualquiera de las [versiones de Node.js admitidas](configure-language-nodejs.md#show-nodejs-version). |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_REMOTE_DEBUGGING_BREAK
APPSVC_TUNNEL_PORT -->

# <a name="python"></a>[Python](#tab/python)

| Nombre del valor | Descripción |
|-|-|
| `APPSVC_VIRTUAL_ENV` | Solo lectura. |
| `PORT` | Solo lectura. En el caso de las aplicaciones Linux, el puerto en el que escucha la aplicación de Python en el contenedor. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_TUNNEL_PORT | -debugAdapter ptvsd -debugPort $APPSVC_TUNNEL_PORT"
APPSVC_REMOTE_DEBUGGING_BREAK | debugArgs+=" -debugWait" -->

# <a name="php"></a>[PHP](#tab/php)

| Nombre del valor | Descripción | Ejemplo|
|-|-|-|
| `PHP_Extensions` | Lista separada por comas de extensiones de PHP. | `extension1.dll,extension2.dll,Name1=value1` |
| `PHP_ZENDEXTENSIONS` | Para aplicaciones Windows nativas, establezca el valor en la ruta de acceso de la extensión XDebug, por ejemplo, `D:\devtools\xdebug\2.6.0\php_7.2\php_xdebug-2.6.0-7.2-vc15-nts.dll`. En el caso de las aplicaciones Linux, establezca el valor en `xdebug` para usar la versión de XDebug del contenedor de PHP. ||
| `PHP_VERSION` | Solo lectura. Versión de PHP seleccionada. ||
| `PORT` | Solo lectura. Puerto en el que escucha el servidor Apache en el contenedor. ||
| `WEBSITE_ROLE_INSTANCE_ID` | Solo lectura. Identificador de la instancia actual. ||
| `WEBSITE_PROFILER_ENABLE_TRIGGER` | Establezca el valor en `TRUE` para agregar `xdebug.profiler_enable_trigger=1` y `xdebug.profiler_enable=0` al archivo `php.ini` predeterminado. ||
| `WEBSITE_ENABLE_PHP_ACCESS_LOGS` | Establezca el valor en `TRUE` para registrar las solicitudes al servidor (se agrega `CustomLog \dev\stderr combined` al archivo `/etc/apache2/apache2.conf`). ||
| `APACHE_SERVER_LIMIT` | Variable específica de Apache. El valor predeterminado es `1000`. ||
| `APACHE_MAX_REQ_WORKERS` | Variable específica de Apache. El valor predeterminado es `256`. ||

<!-- 
ZEND_BIN_PATH
MEMCACHESHIM_REDIS_ENABLE
MEMCACHESHIM_PORT 
APACHE_LOG_DIR | RUN sed -i 's!ErrorLog ${APACHE_LOG_DIR}/error.log!ErrorLog /dev/stderr!g' /etc/apache2/apache2.conf 
APACHE_RUN_USER | RUN sed -i 's!User ${APACHE_RUN_USER}!User www-data!g' /etc/apache2/apache2.conf 
APACHE_RUN_GROUP | RUN sed -i 's!User ${APACHE_RUN_GROUP}!Group www-data!g' /etc/apache2/apache2.conf  
-->

# <a name="ruby"></a>[Ruby](#tab/ruby)

| Nombre del valor | Descripción | Ejemplo |
|-|-|-|
| `PORT` | Solo lectura. Puerto en el que escucha la aplicación de Rails en el contenedor. ||
| `WEBSITE_ROLE_INSTANCE_ID` | Solo lectura. Identificador de la instancia actual. ||
| `RAILS_IGNORE_SPLASH` | De manera predeterminada, se muestra una página de presentación predeterminada cuando no se encuentra ningún archivo Gemfile. Establezca esta variable en cualquier valor para deshabilitar la página de presentación. ||
| `BUNDLE_WITHOUT` | Para agregar opciones `--without` a `bundle install`, establezca la variable en los grupos que quiere excluir, separados por un espacio. De manera predeterminada, se instalan todos los gems. | `test development` |
| `BUNDLE_INSTALL_LOCATION` | Directorio para instalar los gems. El valor predeterminado es `/tmp/bundle`. ||
| `RUBY_SITE_CONFIG_DIR` | Directorio de configuración del sitio. El valor predeterminado es `/home/site/config`. El contenedor comprueba si hay gems comprimidos en este directorio. ||
| `SECRET_KEY_BASE` | De manera predeterminada, se genera una base de clave secreta aleatoria. Para usar una base de clave secreta personalizada, establezca esta variable en la base de clave deseada. ||
| `RAILS_ENV` | Entorno de Rails. El valor predeterminado es `production`. ||
| `GEM_PRISTINE` | Establezca esta variable en cualquier valor para ejecutar `gem pristine --all`. ||

-----

## <a name="domain-and-dns"></a>Dominio y DNS

| Nombre del valor| Descripción | Ejemplo |
|-|-|-|
| `WEBSITE_DNS_SERVER` | Dirección IP del servidor DNS principal para las conexiones salientes (por ejemplo, a un servicio de back-end). El servidor DNS predeterminado de App Service es Azure DNS, cuya dirección IP es `168.63.129.16`. Si la aplicación usa [integración de red virtual](web-sites-integrate-with-vnet.md) o está en un entorno de [App Service Environment](environment/intro.md), hereda la configuración del servidor DNS de la red virtual de manera predeterminada. | `10.0.0.1` |
| `WEBSITE_DNS_ALT_SERVER` | Dirección IP del servidor DNS de reserva para las conexiones salientes. Vea `WEBSITE_DNS_SERVER`. | |

<!-- 
DOMAIN_OWNERSHIP_VERIFICATION_IDENTIFIERS
 -->

## <a name="tlsssl"></a>TLS/SSL

Para más información, consulte [Uso de un certificado SSL en el código de Azure App Service](configure-ssl-certificate-in-code.md).

| Nombre del valor| Descripción |
|-|-|
| `WEBSITE_LOAD_CERTIFICATES` | Valores de huella digital separados por comas para el certificado que desea cargar en el código o `*` para permitir que todos los certificados se carguen en el código. Solo se pueden cargar los [certificados agregados a la aplicación](configure-ssl-certificate.md). |
| `WEBSITE_PRIVATE_CERTS_PATH` | Solo lectura. Ruta de acceso en un contenedor Windows a los certificados privados cargados. |
| `WEBSITE_PUBLIC_CERTS_PATH` | Solo lectura. Ruta de acceso en un contenedor Windows a los certificados públicos cargados. |
| `WEBSITE_INTERMEDIATE_CERTS_PATH` | Solo lectura. Ruta de acceso en un contenedor Windows a los certificados intermedios cargados. |
| `WEBSITE_ROOT_CERTS_PATH` | Solo lectura. Ruta de acceso en un contenedor Windows a los certificados raíz cargados. |

## <a name="deployment-slots"></a>Ranuras de implementación 

Para más información sobre las ranuras de implementación, consulte [Configuración de entornos de ensayo en Azure App Service](deploy-staging-slots.md).

| Nombre del valor| Descripción | Ejemplo |
|-|-|-|
|`WEBSITE_SLOT_NAME`| Solo lectura. Nombre de la ranura de implementación actual. El nombre de la ranura de producción es `Production`. ||
|`WEBSITE_OVERRIDE_STICKY_EXTENSION_VERSIONS`| De manera predeterminada, las versiones de las extensiones de sitio son específicas de cada ranura. Esto evita el comportamiento imprevisto de la aplicación debido al cambio de las versiones de las extensiones después de un intercambio. Si también quiere intercambiar las versiones de extensión, establezca el valor en `1` en *todas las ranuras*. ||
|`WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`| Designa determinadas opciones como [permanentes o no intercambiables de manera predeterminada](deploy-staging-slots.md#which-settings-are-swapped). El valor predeterminado es `true`. O bien, establezca esta configuración en `false` o `0` para *todas las ranuras de implementación* para que sean intercambiables. No hay ningún control detallado para tipos de configuración específicos. ||
|`WEBSITE_SWAP_WARMUP_PING_PATH`| Ruta de acceso para hacer ping para activar la ranura de destino en un intercambio; comienza por una barra diagonal. El valor predeterminado es `/`, que hace ping a la ruta de acceso raíz. | `/statuscheck` |
|`WEBSITE_SWAP_WARMUP_PING_STATUSES`| Códigos de respuesta HTTP válidos para la operación de activación durante un intercambio. Si el código de estado devuelto no está en la lista, las operaciones de preparación e intercambio se detienen. Por defecto, todos los códigos de respuesta son válidos. | `200,202` |
| `WEBSITE_SLOT_NUMBER_OF_TIMEOUTS_BEFORE_RESTART` | Durante un intercambio de ranura, el número máximo de tiempos de espera expirados tras los cuales se fuerza el reinicio del sitio en una instancia de máquina virtual específica. El valor predeterminado es `3`. ||
| `WEBSITE_SLOT_MAX_NUMBER_OF_TIMEOUTS` | Durante un intercambio de ranura, número máximo de solicitudes de tiempo de espera para una única dirección URL que se debe realizar antes de desistir. El valor predeterminado es `5`. ||
| `WEBSITE_SKIP_ALL_BINDINGS_IN_APPHOST_CONFIG` | Establezca el valor en `true` o `1` para omitir todos los enlaces en el archivo `applicationHost.config`. El valor predeterminado es `false`. Si la aplicación desencadena un reinicio porque `applicationHost.config` se ha actualizado con los nombres de host de las ranuras intercambiados, establezca esta variable en `true` para evitar un reinicio de este tipo. Si ejecuta una aplicación de Windows Communication Foundation (WCF), no establezca esta variable. ||

<!-- 
|`WEBSITE_SWAP_SLOTNAME`||| 
-->

## <a name="custom-containers"></a>Contenedores personalizados

Para más información sobre los contenedores personalizados, consulte [Ejecución de un contenedor personalizado en Azure](quickstart-custom-container.md).

| Nombre del valor| Descripción | Ejemplo |
|-|-|-|
| `WEBSITES_ENABLE_APP_SERVICE_STORAGE` | Establezca el valor en `true` para permitir que el directorio `/home` se comparta entre las instancias de escalado. El valor predeterminado es `false` para los contenedores personalizados. ||
| `WEBSITES_CONTAINER_START_TIME_LIMIT` | Cantidad de tiempo en segundos que se va a esperar para que el contenedor complete el inicio antes de reiniciar el contenedor. El valor predeterminado es `230`. Puede aumentarlo hasta el valor máximo de `1800`. ||
| `DOCKER_REGISTRY_SERVER_URL` | Dirección URL del servidor del registro cuando se ejecuta un contenedor personalizado en App Service. Por motivos de seguridad, no se pasa esta variable al contenedor. | `https://<server-name>.azurecr.io` |
| `DOCKER_REGISTRY_SERVER_USERNAME` | Nombre de usuario para autenticarse en el servidor del registro en `DOCKER_REGISTRY_SERVER_URL`. Por motivos de seguridad, no se pasa esta variable al contenedor. ||
| `DOCKER_REGISTRY_SERVER_PASSWORD` | Contraseña para autenticarse en el servidor del registro en `DOCKER_REGISTRY_SERVER_URL`. Por motivos de seguridad, no se pasa esta variable al contenedor. ||
| `WEBSITES_WEB_CONTAINER_NAME` | En una aplicación de Docker Compose, solo uno de los contenedores puede ser accesible desde Internet. Establezca el valor en el nombre del contenedor definido en el archivo de configuración para invalidar la selección de contenedor predeterminada. De manera predeterminada, el contenedor accesible desde Internet es el primer contenedor que define el puerto 80 u 8080, o bien, cuando no se encuentra dicho contenedor, el primer contenedor definido en el archivo de configuración. |  |
| `WEBSITES_PORT` | Para un contenedor personalizado, el número de puerto personalizado del contenedor de App Service al que se van a enrutar las solicitudes. De manera predeterminada, App Service intenta la detección automática de los puertos 80 y 8080. Esta configuración *no* se inserta en el contenedor como variable de entorno. ||
| `WEBSITE_CPU_CORES_LIMIT` | De forma predeterminada, un contenedor de Windows se ejecuta con todos los núcleos disponibles del plan de tarifa elegido. Para reducir el número de núcleos, establezca el valor en el límite de núcleos deseado. Para más información, consulte [Personalización del número de núcleos de proceso](configure-custom-container.md?pivots=container-windows#customize-the-number-of-compute-cores).||
| `WEBSITE_MEMORY_LIMIT_MB` | De forma predeterminada, todos los contenedores de Windows implementados en Azure App Service tienen un límite de 1 GB de RAM. Establezca el valor en el límite de memoria deseado expresado en MB. El total acumulado de esta configuración entre todas las aplicaciones del mismo plan no debe superar la cantidad permitida por el plan de tarifa elegido. Para más información, consulte [Personalización de la memoria del contenedor](configure-custom-container.md?pivots=container-windows#customize-container-memory). ||
| `CONTAINER_WINRM_ENABLED` | Para un contenedor Windows, establezca el valor en `1` para habilitar la Administración remota de Windows (WIN-RM). ||

<!-- 
CONTAINER_ENCRYPTION_KEY
CONTAINER_NAME
CONTAINER_IMAGE_URL
AzureWebEncryptionKey
CONTAINER_START_CONTEXT_SAS_URI
CONTAINER_AZURE_FILES_VOLUME_MOUNT_PATH
CONTAINER_START_CONTEXT
DOCKER_ENABLE_CI
WEBSITE_DISABLE_PRELOAD_HANG_MITIGATION
 -->

## <a name="scaling"></a>Ampliación

| Nombre del valor| Descripción |
|-|-|
| `WEBSITE_INSTANCE_ID` | Solo lectura. Identificador único de la instancia de máquina virtual actual cuando la aplicación se escala horizontalmente a varias instancias. |
| `WEBSITE_IIS_SITE_NAME` | En desuso. Use `WEBSITE_INSTANCE_ID`. |
| `WEBSITE_DISABLE_OVERLAPPED_RECYCLING` | El reciclaje superpuesto hace que antes de que se apague la instancia de máquina virtual actual de una aplicación, se inicie una nueva instancia de máquina virtual. En algunos casos, puede provocar problemas de bloqueo de archivos. Puede probar a desactivarlo; para ello, establezca el valor en `1`. |
| `WEBSITE_DISABLE_CROSS_STAMP_SCALE` | De manera predeterminada, las aplicaciones pueden escalar entre stamps si usan Azure Files o un contenedor de Docker. Establezca el valor en `1` o `true` para deshabilitar el escalado entre unidades de escalado dentro de la región de la aplicación. El valor predeterminado es `0`. Los contenedores de Docker personalizados que establecen `WEBSITES_ENABLE_APP_SERVICE_STORAGE` en `true` o `1` no se pueden escalar entre unidades de escalado porque su contenido no está completamente encapsulado en el contenedor de Docker. |

## <a name="logging"></a>Registro

| Nombre del valor| Descripción | Ejemplo |
|-|-|-|
| `WEBSITE_HTTPLOGGING_ENABLED` | Solo lectura. Muestra si el registro del servidor web para aplicaciones Windows nativas está habilitado (`1`) o no (`0`). ||
| `WEBSITE_HTTPLOGGING_RETENTION_DAYS` | Período de retención en días de los registros del servidor web para aplicaciones Windows nativas si los registros del servidor web están habilitados. | `10` |
| `WEBSITE_HTTPLOGGING_CONTAINER_URL` | Dirección URL de SAS del contenedor de almacenamiento de blobs para almacenar los registros del servidor web para aplicaciones Windows nativas si los registros del servidor web están habilitados. Si no se establece, los registros del servidor web se almacenan en el sistema de archivos de la aplicación (almacenamiento compartido predeterminado). | |
| `DIAGNOSTICS_AZUREBLOBRETENTIONINDAYS` | Período de retención en días de los registros de aplicaciones para aplicaciones Windows nativas si los registros de aplicaciones están habilitados. | `10` |
| `DIAGNOSTICS_AZUREBLOBCONTAINERSASURL` | Dirección URL de SAS del contenedor de almacenamiento de blobs para almacenar los registros de aplicaciones para aplicaciones Windows nativas si los registros de aplicaciones están habilitados. | |
| `APPSERVICEAPPLOGS_TRACE_LEVEL` | Nivel mínimo de registro que se va a enviar a Log Analytics para el tipo de registro [AppServiceAppLogs](troubleshoot-diagnostic-logs.md#supported-log-types). | |
| `DIAGNOSTICS_LASTRESORTFILE` | Nombre del archivo que se va a crear, o una ruta de acceso relativa al directorio de registro, para registrar los errores internos para solucionar problemas del cliente de escucha. El valor predeterminado es `logging-errors.txt`. ||
| `DIAGNOSTICS_LOGGINGSETTINGSFILE` | Ruta de acceso al archivo de configuración de registro, relativa a `D:\home` o `/home`. El valor predeterminado es `site\diagnostics\settings.json`. | |
| `DIAGNOSTICS_TEXTTRACELOGDIRECTORY` | Carpeta de registro, relativa a la raíz de la aplicación (`D:\home\site\wwwroot` o `/home/site/wwwroot`). | `..\..\LogFiles\Application`|
| `DIAGNOSTICS_TEXTTRACEMAXLOGFILESIZEBYTES` | Tamaño máximo del archivo de registro en bytes. El valor predeterminado es `131072` (128 KB). ||
| `DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES` | Tamaño máximo de la carpeta de registro en bytes. El valor predeterminado es `1048576` (1 MB). ||
| `DIAGNOSTICS_TEXTTRACEMAXNUMLOGFILES` | Número máximo de archivos de registro que se van a conservar. El valor predeterminado es `20`. | |
| `DIAGNOSTICS_TEXTTRACETURNOFFPERIOD` | Tiempo de espera en milisegundos para mantener habilitado el registro de aplicaciones. El valor predeterminado es `43200000` (12 horas). ||
| `WEBSITE_LOG_BUFFERING` | El almacenamiento en búfer del registro está habilitado de manera predeterminada. Para deshabilitarlo, establezca el valor en `0`. ||
| `WEBSITE_ENABLE_PERF_MODE` | Para aplicaciones Windows nativas, establezca el valor en `TRUE` para desactivar las entradas del registro de IIS para las solicitudes correctas devueltas en un plazo de 10 segundos. Se trata de una manera rápida de establecer un punto de referencia de rendimiento mediante la eliminación del registro extendido. ||

<!-- 
| `DIAGNOSTICS_AZURETABLESASURL` | old? | |
| WEBSITE_APPSERVICEAPPLOGS_TRACE_ENABLED | Read-only. Added when | | 
| AZMON_LOG_CATEGORY_APPSERVICEAPPLOGS_ENABLED | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICEPLATFORMLOGS_ENABLED  | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICECONSOLELOGS_ENABLED | Read-only. Shows when the AppServiceConsoleLogs category in Azure Monitor settings is enabled. |
WEBSITE_FUNCTIONS_AZUREMONITOR_CATEGORIES
WINDOWS_TRACING_FLAGS
WINDOWS_TRACING_LOGFILE
WEBSITE_FREB_DISABLE
WEBSITE_ARR_SESSION_AFFINITY_DISABLE

-->

## <a name="performance-counters"></a>Contadores de rendimiento

Las siguientes son variables de entorno "falsas" que no existen si las enumera, pero devuelven su valor si las busca individualmente. El valor es dinámico y puede cambiar en cada búsqueda.

| Nombre del valor| Descripción |
|-|-|
| `WEBSITE_COUNTERS_ASPNET` | Objeto JSON que contiene los contadores de rendimiento de ASP.NET. |
| `WEBSITE_COUNTERS_APP` | Objeto JSON que contiene los contadores del espacio aislado. |
| `WEBSITE_COUNTERS_CLR` | Objeto JSON que contiene los contadores de CLR. |
| `WEBSITE_COUNTERS_ALL` | Objeto JSON que contiene la combinación de las otras tres variables. |

## <a name="caching"></a>Almacenamiento en memoria caché

| Nombre del valor| Descripción |
|-|-|
| `WEBSITE_LOCAL_CACHE_OPTION` | Determina si la memoria caché local está habilitada. Las opciones disponibles son la siguientes: <br/>- `Default`: hereda la configuración global de nivel de stamp.<br/>- `Always`: habilitado para la aplicación.<br/>- OnStorageUnavailability<br/>- `Disabled`: deshabilitado para la aplicación. |
| `WEBSITE_LOCAL_CACHE_READWRITE_OPTION` | Opciones de lectura y escritura de la memoria caché local. Las opciones disponibles son la siguientes: <br/>- `ReadOnly`: la memoria caché es de solo lectura.<br/>- `WriteWithCopyBack`: permite escrituras en la memoria caché local y las copia periódicamente en el almacenamiento compartido. Solo se aplica a las aplicaciones de instancia única, ya que el sitio de SCM apunta a la memoria caché local.<br/>- `WriteButDiscardChanges`: permite escrituras en la memoria caché local, pero descarta los cambios realizados localmente. |
| `WEBSITE_LOCAL_CACHE_SIZEINMB` | Tamaño de la memoria caché local en MB. El valor predeterminado es `1000` (1 GB). |
| `WEBSITE_LOCALCACHE_READY` | Marca de solo lectura que indica si la aplicación usa la memoria caché local. |
| `WEBSITE_DYNAMIC_CACHE` | Debido a la naturaleza compartida de los archivos de red para permitir el acceso a varias instancias, la memoria caché dinámica mejora el rendimiento al almacenar en caché los archivos a los que se ha accedido recientemente localmente en una instancia. La memoria caché se invalida cuando se modifica el archivo. La ubicación de la memoria caché es `%SYSTEMDRIVE%\local\DynamicCache` (se aplica la misma cuota de `%SYSTEMDRIVE%\local`). De manera predeterminada, está habilitado el almacenamiento en caché del contenido completo (establecido en `1`), lo que incluye tanto el contenido de los archivo como los metadatos de directorio y archivo (marcas de tiempo, tamaño y contenido del directorio). Para preservar el uso del disco local, establezca el valor en `2` para almacenar en caché solo los metadatos de directorio y archivo (marcas de tiempo, tamaño y contenido del directorio). Para desactivar el almacenamiento en caché, establezca el valor en `0`. |
| `WEBSITE_READONLY_APP` | Al usar el almacenamiento en caché dinámico, puede deshabilitar el acceso de escritura a la raíz de la aplicación (`D:\home\site\wwwroot` o `/home/site/wwwroot`); para ello, establezca esta variable en `1`. A excepción del directorio `App_Data`, no se permiten bloqueos exclusivos, por lo que los archivos bloqueados no bloquean las implementaciones. |

<!-- 
HTTP_X_LOCALCACHE_READY_CHECK
HTTP_X_APPINIT_CHECK
X_SERVER_ROUTED
HTTP_X_MS_REQUEST_ID
HTTP_X_MS_APIM_HOST
HTTP_X_MS_FORWARD_HOSTNAMES
HTTP_X_MS_FORWARD_TOKEN
HTTP_MWH_SECURITYTOKEN
IS_SERVICE_ENDPOINT
VNET_CLIENT_IP
HTTP_X_MS_SITE_RESTRICTED_TOKEN
HTTP_X_FROM
| LOCAL_ADDR | internal private IP address of app |
SERVERS_FOR_HOSTING_SERVER_PROVIDER
NEED_PLATFORM_AUTHORIZATION
TIP_VALUE
TIP_RULE_NAME
TIP_RULE_MAX_AGE
REWRITE_PATH
NEGOTIATE_CLIENT_CERT
| CLIENT_CERT_MODE | used with ClientCertEnabled. Required means ClientCert is required.  Optional means ClientCert is optional or accepted. OptionalInteractiveUser is similar to Optional; however, it will not ask user for Certificate in Browser Interactive scenario.|
| HTTPS_ONLY | set with terraform? |
 -->

## <a name="networking"></a>Redes

Las siguientes variables de entorno están relacionadas con las [conexiones híbridas](app-service-hybrid-connections.md) y la [integración de red virtual](web-sites-integrate-with-vnet.md).

| Nombre del valor | Descripción |
|-|-|
| `WEBSITE_RELAYS` | Solo lectura. Datos necesarios para configurar la conexión híbrida, incluidos los puntos de conexión y los datos de Service Bus. |
| `WEBSITE_REWRITE_TABLE` | Solo lectura. Se usa en tiempo de ejecución para realizar las búsquedas y reescribir las conexiones correctamente. | 
| `WEBSITE_VNET_ROUTE_ALL` | De manera predeterminada, si usa la [integración de red virtual regional](web-sites-integrate-with-vnet.md#regional-vnet-integration), la aplicación solo enruta el tráfico RFC1918 a la red virtual. Establezca el valor en `1` para enrutar todo el tráfico saliente a la red virtual y que esté sujeto a los mismos NSG y UDR. Esta configuración le permite acceder a puntos de conexión que no son RFC1918 mediante la red virtual, proteger todo el tráfico saliente que sale de la aplicación y forzar la tunelización de todo el tráfico saliente a un dispositivo de red de su elección. |
| `WEBSITE_PRIVATE_IP` | Solo lectura. Dirección IP asociada a la aplicación cuando [se integra con una red virtual](web-sites-integrate-with-vnet.md). En el caso de la integración con red virtual regional, el valor es una dirección IP del intervalo de direcciones de la subred delegada y, en el de la integración con red virtual con requisito de puerta de enlace, el valor es una dirección IP del intervalo de direcciones del grupo de direcciones de punto a sitio configurado en la puerta de enlace de red virtual. La aplicación utiliza esta dirección IP para conectarse a los recursos mediante la red virtual. Además, puede cambiar dentro del intervalo de direcciones descrito. |
| `WEBSITE_PRIVATE_PORTS` | Solo lectura. En la integración con red virtual, muestra qué puertos puede usar la aplicación para comunicarse con otros nodos. |

<!-- | WEBSITE_SLOT_POLL_WORKER_FOR_CHANGE_NOTIFICATION | Poll worker before pinging the site to detect when change notification has been processed. |
WEBSITE_SPECIAL_CACHE
WEBSITE_SOCKET_STATISTICS_ENABLED
| `WEBSITE_ENABLE_NETWORK_HEALTHCHECK` | Enable network health checks that won't be blocked by CORS or built-in authentication. Three check methods can be utilized: <br/>- Ping an IP address (configurable by `WEBSITE_NETWORK_HEALTH_IPADDRS`). <br/>- Check DNS resolution (configurable by `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS`). <br/>- Poll URI endpoints (configurable by `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS`).<br/> |
| `WEBSITE_NETWORK_HEALTH_IPADDRS` | https://msazure.visualstudio.com/One/_git/AAPT-Antares-EasyAuth/pullrequest/3763264 |
| `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_INTEVALSECS` | Interval of the network health check in seconds. The minimum value is 30 seconds. | |
| `WEBSITE_NETWORK_HEALTH_TIMEOUT_INTEVALSECS` | Time-out of the network health check in seconds. | |

-->
<!-- | CONTAINER_WINRM_USERNAME |
| CONTAINER_WINRM_PASSWORD| -->

## <a name="key-vault-references"></a>Referencias de Key Vault

Las siguientes variables de entorno están relacionadas con las [referencias de Key Vault](app-service-key-vault-references.md).

| Nombre del valor | Descripción |
|-|-|
| `WEBSITE_KEYVAULT_REFERENCES` | Solo lectura. Contiene información (incluidos los estados) para todas las referencias de Key Vault que están configuradas actualmente en la aplicación. |
| `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` | Si establece la conexión de almacenamiento compartido de la aplicación (mediante `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`) en una referencia de Key Vault, la aplicación no puede resolver la referencia del almacén de claves durante la creación o actualización de la aplicación si se cumple una de las condiciones siguientes: <br/>- La aplicación accede al almacén de claves con una identidad asignada por el sistema.<br/>- La aplicación accede al almacén de claves con una identidad asignada por el usuario y el almacén de claves está [bloqueado con una red virtual](../key-vault/general/overview-vnet-service-endpoints.md).<br/>Para evitar errores en tiempo de creación o actualización, establezca esta variable en `1`. |
| `WEBSITE_DELAY_CERT_DELETION` | Los usuarios pueden establecer esta variable de entorno en 1 para asegurarse de que un certificado del que depende un proceso de trabajo no se elimine hasta que finalice. |
<!-- | `WEBSITE_ALLOW_DOUBLE_ESCAPING_URL` | TODO | -->

## <a name="cors"></a>CORS

Las siguientes variables de entorno están relacionadas con la configuración del uso compartido de recursos entre orígenes (CORS).

| Nombre del valor | Descripción |
|-|-|
| `WEBSITE_CORS_ALLOWED_ORIGINS` | Solo lectura. Muestra los orígenes permitidos para CORS. |
| `WEBSITE_CORS_SUPPORT_CREDENTIALS` | Solo lectura. Muestra si establecer el encabezado `Access-Control-Allow-Credentials` en `true` está habilitado (`True`) o no (`False`). |

## <a name="authentication--authorization"></a>Autenticación y autorización

Las siguientes variables de entorno están relacionadas con la [autenticación de App Service](overview-authentication-authorization.md).

| Nombre del valor| Descripción|
|-|-|
| `WEBSITE_AUTH_DISABLE_IDENTITY_FLOW`  | Cuando se establece en `true`, deshabilita la asignación de la identidad de la entidad de seguridad del subproceso en aplicaciones web basadas en ASP.NET (incluidas las aplicaciones de funciones v1). Esto está diseñado para permitir a los desarrolladores proteger el acceso al sitio con autenticación, pero seguir haciendo que use un mecanismo de inicio de sesión independiente dentro de la lógica de la aplicación. El valor predeterminado es `false`. |
| `WEBSITE_AUTH_HIDE_DEPRECATED_SID` | `true` o `false`. El valor predeterminado es `false`. Se trata de una configuración para la integración heredada de Azure App Service Mobile Apps para Azure App Service. Si se establece en `true`, se resuelve un problema por el que el SID (identificador de seguridad) generado para los usuarios autenticados podría cambiar si el usuario cambia su información de perfil. El cambio de este valor puede dar lugar a que cambien los identificadores de usuario existentes de Azure App Service Mobile Apps. La mayoría de las aplicaciones no necesitan usar esta configuración. |
| `WEBSITE_AUTH_NONCE_DURATION`| Un valor de _timespan_ con el formato `_hours_:_minutes_:_seconds_`. El valor predeterminado es `00:05:00` (5 minutos). Esta configuración controla la vigencia del [valor nonce criptográfico](https://en.wikipedia.org/wiki/Cryptographic_nonce) generado para todos los inicios de sesión desde un explorador. Si un inicio de sesión no se completa en el tiempo especificado, se reintentará automáticamente el flujo de inicio de sesión. Esta configuración de aplicación está pensada para su uso con la experiencia de configuración V1 (clásica). Si usa el esquema de configuración de autenticación V2, debe usar el valor de configuración `login.nonce.nonceExpirationInterval` en su lugar. |
| `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` | Cuando se establece en `true` y los usuarios hacen clic en vínculos de la aplicación que contienen fragmentos de dirección URL, el proceso de inicio de sesión garantizará que la parte del fragmento de dirección URL de la dirección URL no se pierda en el proceso de redirección del inicio de sesión. Para más información, consulte [Personalización del inicio y cierre de sesión en la autenticación de Azure App Service](configure-authentication-customize-sign-in-out.md#preserve-url-fragments). |
| `WEBSITE_AUTH_USE_LEGACY_CLAIMS` | Para mantener la compatibilidad con versiones anteriores en las actualizaciones, el módulo de autenticación usa la asignación de notificaciones heredada de nombres cortos a nombres largos en la API `/.auth/me`, por lo que se excluyen determinadas asignaciones (por ejemplo, "roles"). Para obtener la versión más moderna de las asignaciones de notificaciones, establezca esta variable en `False`. En el ejemplo de los "roles", se asignaría al nombre largo de la notificación "http://schemas.microsoft.com/ws/2008/06/identity/claims/role". |
| `WEBSITE_AUTH_DISABLE_WWWAUTHENTICATE` | `true` o `false`. El valor predeterminado es `false`. Cuando se establece en `true`, quita el encabezado de respuesta HTTP [`WWW-Authenticate`](https://developer.mozilla.org/docs/Web/HTTP/Headers/WWW-Authenticate) de las respuestas HTTP 401 generadas por el módulo. Esta configuración de aplicación está pensada para su uso con la experiencia de configuración V1 (clásica). Si usa el esquema de configuración de autenticación V2, debe usar el valor de configuración `identityProviders.azureActiveDirectory.login.disableWwwAuthenticate` en su lugar. |
| `WEBSITE_AUTH_STATE_DIRECTORY`  | Ruta de acceso del directorio del sistema de archivos local donde se almacenan los tokens cuando se habilita el almacén de tokens basado en archivos. El valor predeterminado es `%HOME%\Data\.auth`. Esta configuración de aplicación está pensada para su uso con la experiencia de configuración V1 (clásica). Si usa el esquema de configuración de autenticación V2, debe usar el valor de configuración `login.tokenStore.fileSystem.directory` en su lugar. |
| `WEBSITE_AUTH_TOKEN_CONTAINER_SASURL` | Dirección URL completa del contenedor de blobs. Indica al módulo de autenticación que almacene y cargue todos los tokens cifrados en el contenedor de Blob Storage especificado en lugar de usar el sistema de archivos local predeterminado.  |
| `WEBSITE_AUTH_TOKEN_REFRESH_HOURS` | Cualquier número decimal positivo. El valor predeterminado es `72` (horas). Esta configuración controla la cantidad de tiempo después de que expire un token de sesión para que se pueda usar la API `/.auth/refresh` para actualizarlo. Está pensado principalmente para su uso con Azure App Service Mobile Apps, que se basa en tokens de sesión. Los intentos de actualización después de este período producirán un error y los usuarios finales tendrán que iniciar sesión de nuevo. Esta configuración de aplicación está pensada para su uso con la experiencia de configuración V1 (clásica). Si usa el esquema de configuración de autenticación V2, debe usar el valor de configuración `login.tokenStore.tokenRefreshExtensionHours` en su lugar. |
| `WEBSITE_AUTH_TRACE_LEVEL`| Controla el nivel de detalle de los seguimientos de autenticación que se escriben en [Application Logging](troubleshoot-diagnostic-logs.md#enable-application-logging-windows). Los valores válidos son: `Off`, `Error`, `Warning`, `Information` y `Verbose`. El valor predeterminado es `Verbose`. |
| `WEBSITE_AUTH_VALIDATE_NONCE`| `true` o `false`. El valor predeterminado es `true`. Este valor nunca de debe establecer en `false` excepto para depurar temporalmente los errores de validación del [valor nonce criptográfico](https://en.wikipedia.org/wiki/Cryptographic_nonce) que se producen durante los inicios de sesión interactivos. Esta configuración de aplicación está pensada para su uso con la experiencia de configuración V1 (clásica). Si usa el esquema de configuración de autenticación V2, debe usar el valor de configuración `login.nonce.validateNonce` en su lugar. |
| `WEBSITE_AUTH_V2_CONFIG_JSON` | Esta variable de entorno se rellena automáticamente mediante la plataforma de Azure App Service y se usa para configurar el módulo de autenticación integrada. El valor de esta variable de entorno corresponde a la configuración de autenticación V2 (no clásica) de la aplicación actual en Azure Resource Manager. No está pensado para ser configurado de forma explícita. |
| `WEBSITE_AUTH_ENABLED` | Solo lectura. Se inserta en una aplicación Windows o Linux para indicar si está habilitada la autenticación de App Service. |
| `WEBSITE_AUTH_ENCRYPTION_KEY` | De manera predeterminada, la clave generada automáticamente se usa como clave de cifrado. Para invalidarla, establezca el valor en la clave deseada. Esto se recomienda si desea compartir tokens o sesiones entre varias aplicaciones. Si se especifica, reemplaza al valor `MACHINEKEY_DecryptionKey`. ||
| `WEBSITE_AUTH_SIGNING_KEY` | De manera predeterminada, la clave generada automáticamente se usa como clave de firma. Para invalidarla, establezca el valor en la clave deseada. Esto se recomienda si desea compartir tokens o sesiones entre varias aplicaciones. Si se especifica, reemplaza al valor `MACHINEKEY_ValidationKey`. ||

<!-- System settings
WEBSITE_AUTH_RUNTIME_VERSION
WEBSITE_AUTH_API_PREFIX
WEBSITE_AUTH_TOKEN_STORE
WEBSITE_AUTH_MOBILE_COMPAT
WEBSITE_AUTH_AAD_BYPASS_SINGLE_TENANCY_CHECK
WEBSITE_AUTH_COOKIE_EXPIRATION_TIME
WEBSITE_AUTH_COOKIE_EXPIRATION_MODE
WEBSITE_AUTH_PROXY_HEADER_CONVENTION
WEBSITE_AUTH_PROXY_HOST_HEADER
WEBSITE_AUTH_PROXY_PROTO_HEADER
WEBSITE_AUTH_REQUIRE_HTTPS
WEBSITE_AUTH_DEFAULT_PROVIDER
WEBSITE_AUTH_UNAUTHENTICATED_ACTION
WEBSITE_AUTH_EXTERNAL_REDIRECT_URLS
WEBSITE_AUTH_CUSTOM_IDPS
WEBSITE_AUTH_CUSTOM_AUTHZ_SETTINGS
WEBSITE_AUTH_CLIENT_ID
WEBSITE_AUTH_CLIENT_SECRET
WEBSITE_AUTH_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_CLIENT_SECRET_CERTIFICATE_THUMBPRINT
WEBSITE_AUTH_OPENID_ISSUER
WEBSITE_AUTH_ALLOWED_AUDIENCES
WEBSITE_AUTH_LOGIN_PARAMS
WEBSITE_AUTH_AUTO_AAD
WEBSITE_AUTH_AAD_CLAIMS_AUTHORIZATION
WEBSITE_AUTH_GOOGLE_CLIENT_ID
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GOOGLE_SCOPE
WEBSITE_AUTH_FB_APP_ID
WEBSITE_AUTH_FB_APP_SECRET
WEBSITE_AUTH_FB_APP_SECRET_SETTING_NAME
WEBSITE_AUTH_FB_SCOPE
WEBSITE_AUTH_GITHUB_CLIENT_ID
WEBSITE_AUTH_GITHUB_CLIENT_SECRET
WEBSITE_AUTH_GITHUB_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GITHUB_APP_SCOPE
WEBSITE_AUTH_TWITTER_CONSUMER_KEY
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_CLIENT_ID
WEBSITE_AUTH_MSA_CLIENT_SECRET
WEBSITE_AUTH_MSA_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_SCOPE
WEBSITE_AUTH_FROM_FILE
WEBSITE_AUTH_FILE_PATH
| `WEBSITE_AUTH_CONFIG_DIR` | (Used for the deprecated "routes" feature) |
| `WEBSITE_AUTH_ZUMO_USE_TOKEN_STORE_CLAIMS` | (looks like only a tactical fix) ||
 -->

## <a name="managed-identity"></a>Identidad administrada

Las siguientes variables de entorno están relacionadas con las [identidades administradas](overview-managed-identity.md).

|Nombre del valor | Descripción |
|-|-|
|`IDENTITY_ENDPOINT` | Solo lectura. Dirección URL para recuperar el token de la [identidad administrada](overview-managed-identity.md) de la aplicación. |
| `MSI_ENDPOINT` | En desuso. Use `IDENTITY_ENDPOINT`. |
| `IDENTITY_HEADER` | Solo lectura. Valor que se debe agregar al encabezado `X-IDENTITY-HEADER` al realizar una solicitud HTTP GET a `IDENTITY_ENDPOINT`. La plataforma se encarga de cambiarlo. |
| `MSI_SECRET` | En desuso. Use `IDENTITY_HEADER`. |
<!-- | `WEBSITE_AUTHENTICATION_ENDPOINT_ENABLED` | ¿Deshabilitado de manera predeterminada? TODO | -->

## <a name="health-check"></a>Comprobación de estado

Las siguientes variables de entorno están relacionadas con las [comprobaciones de estado](monitor-instances-health-check.md).

| Nombre del valor | Descripción |
|-|-|
| `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | Número máximo de pings con error antes de quitar la instancia. Establezca un valor entre `2` y `100`. Al escalar vertical u horizontalmente, App Service hace ping a la ruta de acceso de comprobación de estado para asegurarse de que las nuevas instancias estén listas. Para más información, consulte [Supervisión de instancias de App Service mediante la comprobación de estado](monitor-instances-health-check.md).|
| `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | Para evitar saturar las instancias correctas, no se excluirán más de la mitad de las instancias. Por ejemplo, si un plan de App Service se escala a cuatro instancias y tres son incorrectas, se excluirán dos como máximo. Las otras dos instancias (una correcta y otra incorrecta) seguirán recibiendo solicitudes. En el peor de los casos, si todas las instancias están en mal estado, no se excluye ninguna. Para invalidar este comportamiento, establezca un valor entre `0` y `100`. Un valor mayor significa que se quitarán más instancias incorrectas. El valor predeterminado es `50` (50 %). |

## <a name="push-notifications"></a>Notificaciones de inserción

Las siguientes variables de entorno están relacionadas con la característica de [notificaciones de inserción](/previous-versions/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub).

| Nombre del valor | Descripción |
|-|-|
| `WEBSITE_PUSH_ENABLED` | Solo lectura. Se agrega cuando se habilitan las notificaciones de inserción. |
| `WEBSITE_PUSH_TAG_WHITELIST` | Solo lectura. Contiene las etiquetas del registro de la notificación. |
| `WEBSITE_PUSH_TAGS_REQUIRING_AUTH` | Solo lectura. Contiene una lista de las etiquetas del registro de la notificación que requieren la autenticación del usuario. |
| `WEBSITE_PUSH_TAGS_DYNAMIC` | Solo lectura. Contiene una lista de las etiquetas del registro de la notificación que se han agregado automáticamente. | 

<!-- 
## WellKnownAppSettings

WEBSITE_ALWAYS_PERFORM_PRELOAD
| WEBSITE_DISABLE_PRIMARY_VOLUMES | Set to `true` to disable the primary storage volume for that app. The default is `false`. |
| WEBSITE_DISABLE_STANDBY_VOLUMES | Set to `true` to disable the stand-by storage volume for that app. The default is `false`. This setting has no effect if `WEBSITE_DISABLE_PRIMARY_VOLUMES` is `true`. |
WEBSITE_FAILOVER_ONLY_ON_SBX_NW_FAILURES
WEBSITE_ENABLE_SYSTEM_LOG
WEBSITE_FRAMEWORK_JIT
WEBSITE_ADMIN_SITEID
WEBSITE_STAMP_DEPLOYMENT_ID
| WEBSITE_DEPLOYMENT_ID | Read-only. internal ID of deployment slot |
| WEBSITE_DISABLE_MSI | deprecated |
WEBSITE_VNET_BLOCK_FOR_SETUP_MAIN_SITE
WEBSITE_VNET_BLOCK_FOR_SETUP_SCM_SITE

 -->

## <a name="webjobs"></a>WebJobs

Las siguientes variables de entorno están relacionadas con los [WebJobs](webjobs-create.md).

| Nombre del valor| Descripción |
|-|-|
| `WEBJOBS_RESTART_TIME`|En el caso de los trabajos continuos, es el retraso expresado en segundos cuando el proceso de un trabajo se cierra por cualquier motivo antes de volver a iniciarlo. |
| `WEBJOBS_IDLE_TIMEOUT`| Para los trabajos desencadenados, es el tiempo de espera expresado en segundos después del cual el trabajo se anula si está inactivo, no tiene tiempo de CPU ni salida. |
| `WEBJOBS_HISTORY_SIZE`| Para los trabajos desencadenados, es el número máximo de ejecuciones que se mantienen en el directorio del historial por cada trabajo. El valor predeterminado es `50`. |
| `WEBJOBS_STOPPED`| Establezca el valor en `1` para deshabilitar la ejecución de cualquier trabajo y detener todos los trabajos que se están ejecutando actualmente. |
| `WEBJOBS_DISABLE_SCHEDULE`| Establezca el valor en `1` para desactivar todo el desencadenamiento programado. Los trabajos todavía se pueden invocar manualmente. |
| `WEBJOBS_ROOT_PATH`| Ruta de acceso absoluta o relativa de los archivos del WebJob. Para una ruta de acceso relativa, el valor se combina con la ruta de acceso raíz predeterminada (`D:/home/site/wwwroot/` o `/home/site/wwwroot/`). |
| `WEBJOBS_LOG_TRIGGERED_JOBS_TO_APP_LOGS`| Establezca el valor en true para enviar la salida de los WebJobs desencadenados a la canalización de registros de la aplicación (que admite el sistema de archivos, blobs y tablas). |
| `WEBJOBS_SHUTDOWN_FILE` | Archivo creado por App Service cuando se detecta una solicitud de apagado. Es responsabilidad del proceso del WebJob detectar la presencia de este archivo e iniciar el apagado. Cuando se usa el SDK de WebJobs, esta parte se controla automáticamente. |
| `WEBJOBS_PATH` | Solo lectura. Ruta de acceso raíz del trabajo actualmente en ejecución (estará en algún directorio temporal). |
| `WEBJOBS_NAME` | Solo lectura. Nombre del trabajo actual. |
| `WEBJOBS_TYPE` | Solo lectura. Tipo de trabajo actual (`triggered` o `continuous`). |
| `WEBJOBS_DATA_PATH` | Solo lectura. Ruta de acceso de metadatos del trabajo actual que va a contener los registros, el historial y cualquier artefacto del trabajo. |
| `WEBJOBS_RUN_ID` | Solo lectura. Para los trabajos desencadenados, es el identificador de ejecución actual del trabajo. |

## <a name="functions"></a>Functions

| Nombre del valor | Descripción |
|-|-|
| `WEBSITE_FUNCTIONS_ARMCACHE_ENABLED` | Establezca el valor en `0` para deshabilitar el almacenamiento en caché de las funciones. |
| `WEBSITE_MAX_DYNAMIC_APPLICATION_SCALE_OUT` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_EXTENSION_VERSION` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
`AzureWebJobsSecretStorageType` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_WORKER_RUNTIME` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `AzureWebJobsStorage` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTSHARE` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTOVERVNET` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_ENABLE_BROTLI_ENCODING` | [Referencia de configuración de aplicación para Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_USE_PLACEHOLDER` | Establezca el valor en `0` para deshabilitar la optimización de funciones de marcador de posición en el plan de consumo. El marcador de posición es una optimización que [mejora el arranque en frío](../azure-functions/functions-scale.md#cold-start-behavior). |
| `WEBSITE_PLACEHOLDER_MODE` | Solo lectura. Muestra si la aplicación de funciones se ejecuta en un host de marcador de posición (`generalized`) o en su propio host (`specialized`). |
| `WEBSITE_DISABLE_ZIP_CACHE` | Cuando la aplicación se ejecuta desde un [paquete ZIP](deploy-run-package.md) (`WEBSITE_RUN_FROM_PACKAGE=1`), los cinco paquetes ZIP implementados más recientemente se almacenan en caché en el sistema de archivos de la aplicación (D:\home\data\SitePackages). Establezca esta variable en `1` para deshabilitar está caché. En el caso de las aplicaciones de consumo de Linux, la memoria caché de paquetes ZIP está deshabilitada de manera predeterminada. |
<!--
| `FUNCTIONS_RUNTIME_SCALE_MONITORING_ENABLED` | TODO |
| `WEBSITE_SKIP_FUNCTION_APP_WARMUP` | Apps can use appsetting to opt out of warmup. Restricted to linux only since this is primarily for static sites that use Linux dynamic function apps. Linux dynamic sites are used as placeholder sites for static sites. Function apps don't get specialized until static sites are deployed. This allows function apps used by static sites to skip warmup and using up containers before any content is deployed. TODO |
 WEBSITE_IDLE_TIMEOUT_IN_MINUTES | removed WEBSITE_IDLE_TIMEOUT_IN_MINUTES because they aren't used in Linux Consumption.???
| `WEBSITE_DISABLE_FUNCTIONS_STARTUPCONTEXT_CACHE`| This env var can be set to 1 by users in order to avoid using the Functions StartupContext Cache feature. |
| `WEBSITE_CONTAINER_READY` | The env var is set to '1' to indicate to the Functions Runtime that it can proceed with initializing/specializing 
        // itself. For placeholders, it is set once specialization is complete on DWAS side and detours are reinitialized. For 
        // non-placeholder function apps, it is simply set to 1 when the process is started, because detours are initialized 
        // as part of starting the process (when PicoHelper.dll is loaded, well before any managed code is running).
        // NOTE: This is set on all sites, irrespective of whether it is a Functions site, because the EnvSettings module depends 
        // upon it to decide when to inject the app-settings.|
| `WEBSITE_PLACEHOLDER_PING_PATH` | This env var can be used to set a special warmup ping path on placeholder template sites. |
| ` WEBSITE_PLACEHOLDER_DISABLE_AUTOSPECIALIZATION` | This env var can be used to disabe specialization from being enabled automatically for a given placeholder template site. |
| `WEBSITE_FUNCTIONS_STARTUPCONTEXT_CACHE` | This env var is set only during specialization of a placeholder, to indicate to the Functions Runtime that
        // some function-app related data needed at startup, like secrets, are available in a file at the path specified
        // by this env var. |
WEBSITE_ENABLE_COLD_START_PROFILING | This env var can be set to 1 by internal SLA sites in order to trigger collection of perf profiles, if feature is enabled on the stamp. |
WEBSITE_CURRENT_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_HOME_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_ELASTIC_SCALING_ENABLED
WEBSITE_FILECHANGEAUDIT_ENABLED
| `WEBSITE_HTTPSCALEV2_ENABLED` | This is the default behavior for both v1 and v2 Azure Functions apps. | 
WEBSITE_CHANGEANALYSISSCAN_ENABLED
WEBSITE_DISABLE_CHILD_SPECIALIZATION
 -->

<!-- 
## Server variables
|HTTP_HOST| |
|HTTP_DISGUISED_HOST|the runtime site name for inbound requests.|
HTTP_CACHE_CONTROL
HTTP_X_SITE_DEPLOYMENT_ID
HTTP_WAS_DEFAULT_HOSTNAME
HTTP_X_ORIGINAL_URL
HTTP_X_FORWARDED_FOR
HTTP_X_ARR_SSL
HTTP_X_FORWARDED_PROTO
HTTP_X_APPSERVICE_PROTO
HTTP_X_FORWARDED_TLSVERSION
X-WAWS-Unencoded-URL
CLIENT-IP
X-ARR-LOG-ID
DISGUISED-HOST
X-SITE-DEPLOYMENT-ID
WAS-DEFAULT-HOSTNAME
X-Original-URL
X-MS-CLIENT-PRINCIPAL-NAME
X-MS-CLIENT-DISPLAY-NAME
X-Forwarded-For
X-ARR-SSL
X-Forwarded-Proto
X-AppService-Proto
X-Forwarded-TlsVersion
URL
HTTP_CLIENT_IP
APP_WARMING_UP |Regular/external requests made while warmup is in progress will have a APP_WARMING_UP server variable set to 1|
HTTP_COOKIE
SERVER_NAME
HTTP_X_FORWARDED_HOST
| HTTP_X_AZURE_FDID | Azure Front Door ID. See [](../frontdoor/front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
HTTP_X_FD_HEALTHPROBE
|WEBSITE_LOCALCACHE_ENABLED | shows up in w3wp.exe worker process |
HTTP_X_ARR_LOG_ID
| SCM_BASIC_AUTH_ALLOWED | set to "false" or "0" to disable basic authentication |
HTTP_X_MS_WAWS_JWT
HTTP_MWH_SecurityToken
LB_ALGO_FOR_HOSTING_SERVER_PROVIDER
ENABLE_CLIENT_AFFINITY
HTTP_X_MS_FROM_GEOMASTER
HTTP_X_MS_USE_GEOMASTER_CERT
HTTP_X_MS_STAMP_TOKEN
HTTPSCALE_REQUEST_ID
HTTPSCALE_FORWARD_FRONTEND_KEY
HTTPSCALE_FORWARD_REQUEST
IS_VALID_STAMP_TOKEN
NEEDS_SITE_RESTRICTED_TOKEN
HTTP_X_MS_PRIVATELINK_ID
  -->
