---
title: 'Notas de la versión para el paquete NuGet Microsoft.ApplicationInsights.SnapshotCollector: Application Insights'
description: Notas de la versión para el paquete NuGet Microsoft.ApplicationInsights.SnapshotCollector utilizado por Snapshot Debugger de Application Insights.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: a2b3dff6331e235d0c4b608a38038a89996c35b4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217776"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Notas de la versión para Microsoft.ApplicationInsights.SnapshotCollector

Este artículo contiene las notas de la versión para el paquete NuGet Microsoft.ApplicationInsights.SnapshotCollector para aplicaciones de .NET, que es utilizado por Snapshot Debugger de Application Insights.

[Más información](./snapshot-debugger.md) sobre Snapshot Debugger de Application Insights para aplicaciones .NET.

Para notificar informes de errores y comentarios, abra un problema en GitHub en https://github.com/microsoft/ApplicationInsights-SnapshotCollector.

## <a name="release-notes"></a>Notas de la versión

## <a name="142"></a>[1.4.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.2)
Una versión puntual para solucionar un error notificado por el usuario.
### <a name="bug-fixes"></a>Corrección de errores
- Corrija [ArgumentException: los delegados deben ser del mismo tipo.](https://github.com/microsoft/ApplicationInsights-SnapshotCollector/issues/16)

## <a name="141"></a>[1.4.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.1)
Una versión puntual para revertir un cambio importante introducido en la versión 1.4.0.
### <a name="bug-fixes"></a>Corrección de errores
- Corrija [Método no encontrado en WebJobs](https://github.com/microsoft/ApplicationInsights-SnapshotCollector/issues/15)

## <a name="140"></a>[1.4.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.0)
Se abordan varias mejoras y se ha agregado compatibilidad con la autenticación de Azure Active Directory (AAD) para la ingesta de Application Insights.
### <a name="changes"></a>Cambios
- El tamaño de paquete de Snapshot Collector se ha reducido en un 60 %. De 10,34 MB a 4,11 MB.
- Netstandard2.0 se establece como destino solo en Snapshot Collector.
- Se ha actualizado la dependencia del SDK de Application Insights a la versión 2.15.0.
- Se vuelve a agregar MinidumpWithThreadInfo al escribir volcados.
- Se agrega CompatibilityVersion para mejorar la sincronización entre el agente de Snapshot Collector y el usuario de carga en los cambios importantes.
- Se ha cambiado el algoritmo de nomenclatura SnapshotUploader LogFile para evitar demasiadas operaciones de E/S de archivos en App Service.
- Se agrega el PID, nombre de rol y hora de inicio del proceso a los metadatos de blob que se cargan.
- Use System.Diagnostics.Process siempre que sea posible en Snapshot Collector y Snapshot Uploader.
### <a name="new-features"></a>Nuevas características
- Se agrega la autenticación de Azure Active Directory a SnapshotCollector. Obtenga más información sobre la autenticación de Azure AD en Application Insights [aquí](./azure-ad-authentication.md).

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
Una versión puntual para retroimportar una corrección de 1.4.0-pre.
### <a name="bug-fixes"></a>Corrección de errores
- Corrija [ObjectDisposedException en el apagado](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097).

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Una versión puntual para solucionar un problema detectado en la prueba del escenario para adjuntar sin código de Azure App Service.
### <a name="changes"></a>Cambios
- El destino de netcoreapp 3.0 depende ahora de Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (anteriormente >= 2.1.2).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Versión de carácter puntual para abordar un par de problemas de gran impacto.
### <a name="bug-fixes"></a>Corrección de errores
- Se ha corregido la detección de PDB en la carpeta wwwroot/bin, que se interrumpió al cambiar el algoritmo de búsqueda de símbolos en la versión 1.3.6.
- Se ha corregido un efecto de ruido en ExtractWasCalledMultipleTimesException en la telemetría.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Cambios
- El destino de netcoreapp2.0 de SnapshotCollector depende de una versión de Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (de nuevo). Esto revierte el comportamiento a la manera en la que estaba antes de la versión 1.3.5. Hemos intentado actualizarlo en la versión 1.3.6, pero se han interrumpido algunos escenarios de Azure App Service.
### <a name="new-features"></a>Nuevas características
- Snapshot Collector lee y analiza la cadena de conexión desde la variable de entorno APPLICATIONINSIGHTS_CONNECTION_STRING o desde TelemetryConfiguration. Esto se usa principalmente para establecer el punto de conexión para conectarse al servicio de instantáneas. Para más información, consulte [Cadenas de conexión](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Corrección de errores
- Se ha cambiado para usar HttpClient para todos los destinos excepto net45, porque se produjo un error de WebRequest en algunos entornos debido a un protocolo de seguridad incompatible (requiere TLS 1.2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Cambios
- SnapshotCollector ahora depende de una versión de Microsoft.ApplicationInsights >= 2.5.1 para todas las plataformas de destino. Esto puede ser un cambio importante si la aplicación depende de una versión anterior del SDK de Microsoft.ApplicationInsights.
- Se ha eliminado la compatibilidad con TLS 1.0 y 1.1 en el cargador de instantáneas.
- El período de los exámenes de PDB ahora tiene un valor predeterminado de 24 horas en lugar de 15 minutos. Configurable mediante PdbRescanInterval en SnapshotCollectorConfiguration.
- El examen de PDB busca solo en las carpetas de nivel superior, en lugar de una búsqueda recursiva. Esto puede ser un cambio importante si los símbolos están en subcarpetas de la carpeta de archivos binarios.
### <a name="new-features"></a>Nuevas características
- Rotación del registro en SnapshotUploader para evitar que se llene la carpeta de registros con archivos antiguos.
- Compatibilidad con la desoptimización (mediante ReJIT en adjuntos) para aplicaciones de .NET Core 3.0.
- Se han agregado símbolos al paquete NuGet.
- Se establecen metadatos adicionales al cargar minivolcados.
- Se ha agregado una propiedad inicializada a SnapshotCollectorTelemetryProcessor. Es un token de cancelación que se cancela cuando Snapshot Collector se inicializa completamente y se conecta al punto de conexión de servicio.
- Ahora se pueden capturar instantáneas para excepciones en métodos generados dinámicamente. Por ejemplo, los árboles de expresión compilados generados por las consultas de Entity Framework.
### <a name="bug-fixes"></a>Corrección de errores
- Se produce la excepción AmbiguousMatchException en la carga de Snapshot Collector debido a Monitor de estado.
- Ahora, el método de extensión GetSnapshotCollector busca en todos los destinos de telemetría.
- No inicie el cargador de instantáneas en plataformas no compatibles.
- Se controla InvalidOperationException al desoptimizar métodos dinámicos (por ejemplo, Entity Framework).

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Se ha agregado compatibilidad para las nubes soberanas (las versiones anteriores no funcionan en nubes soberanas).
- Se facilita la adición de Snapshot Collector mediante el uso de AddSnapshotCollector(). Puede encontrar más información [aquí](./snapshot-debugger-appservice.md).
- Se utiliza la configuración de MD5 de FISMA para comprobar los bloques de blobs. Esto evita el algoritmo criptográfico predeterminado MD5 de .NET, que no está disponible cuando el sistema operativo está establecido en el modo compatible con FIPS.
- Se omiten los marcos de .NET Framework al desoptimizar las llamadas de función. Este comportamiento se puede controlar mediante la opción de configuración DeoptimizeIgnoredModules.
- Se ha agregado la opción de configuración `DeoptimizeMethodCount`, que permite la desoptimización de más de una llamada de función. Encontrará más información aquí.

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Se permiten claves de instrumentación estructuradas.
- Se ha aumentado la solidez de SnapshotUploader: se continua con el inicio incluso si no se pueden mover los registros anteriores del cargador.
- Se han vuelto a habilitar los informes de telemetría adicionales cuando SnapshotUploader.exe se cierra inmediatamente (se deshabilitó en la versión 1.3.3).
- Se ha simplificado la telemetría interna.
- _Característica experimental_: planes de recopilación de puntos de instantánea: Se ha agregado "snapshotOnFirstOccurence". Encontrará más información [aquí](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Se ha corregido un error que provocaba que SnapshotUploader.exe dejara de responder y no cargara instantáneas de las aplicaciones de .NET Core.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Característica experimental_: planes de recopilación de puntos de instantánea. Encontrará más información [aquí](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe se cerrará cuando el entorno de ejecución descargue el dominio de aplicación desde el que se carga SnapshotCollector, en lugar de esperar a que se cierre el proceso. Esto mejora la confiabilidad del recopilador cuando se hospeda en IIS.
- Se ha agregado una configuración para permitir que varias instancias de SnapshotCollector que usan la misma clave de instrumentación compartan el mismo proceso de SnapshotUploader: ShareUploaderProcess (el valor predeterminado es `true`).
- Se informan datos de telemetría adicionales cuando SnapshotUploader.exe se cierra inmediatamente.
- Se ha reducido el número de archivos auxiliares que necesita SnapshotUploader.exe para escribir en el disco.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Se ha eliminado la compatibilidad para recopilar instantáneas con la API RtlCloneUserProcess y solo se admite la API PssCaptureSnapshots.
- Se ha aumentado de 1 a 3 el límite predeterminado del número de instantáneas que se pueden capturar en 10 minutos.
- Se permite que SnapshotUploader.exe negocie TLS 1.1 y 1.2.
- Se informan datos de telemetría adicionales cuando SnapshotUploader registra una advertencia o un error.
- Se dejan de tomar instantáneas cuando el servicio de back-end informa de que se ha alcanzado la cuota diaria (50 instantáneas al día).
- Se ha agregado una comprobación adicional en SnapshotUploader.exe para no permitir que se ejecuten dos instancias al mismo tiempo.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Cambios
- En el caso de las aplicaciones que tienen como destino .NET Framework, Snapshot Collector ahora depende de la versión 2.3.0 o superior de Microsoft.ApplicationInsights.
Antes era la versión 2.2.0 o superior.
Creemos que esto no será un problema para la mayoría de las aplicaciones, pero háganoslo saber si este cambio le impide usar la versión más reciente de Snapshot Collector.
- Se utilizan retrasos de retroceso exponencial en Snapshot Uploader al reintentar las cargas con errores.
- Se utiliza ServerTelemetryChannel (si está disponible) para obtener informes de telemetría más confiables.
- Se utiliza "SdkInternalOperationsMonitor" en la conexión inicial al servicio Snapshot Debugger para que el seguimiento de dependencias lo omita.
- Se ha mejorado la telemetría sobre la conexión inicial con el servicio Snapshot Debugger.
- Se informa de datos de telemetría adicionales para:
  - Versión de Azure App Service.
  - Instancias de proceso de Azure.
  - Contenedores.
  - Aplicación de funciones de Azure.
### <a name="bug-fixes"></a>Corrección de errores
- Cuando el intervalo de restablecimiento del contador de problemas se establece en 24 días, se interpretaba como 24 horas.
- Se ha corregido un error que hacía que el cargador de instantáneas dejara de procesar nuevas instantáneas si se produjo una excepción al eliminar una instantánea.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Se ha corregido la firma de nombre seguro con los archivos binarios del cargador de instantáneas.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Cambios
- Los archivos necesarios para SnapshotUploader(64).exe ahora se insertan como recursos en el archivo DLL principal. Esto significa que ya no se crea la carpeta SnapshotCollectorFiles, lo que simplifica la compilación y la implementación y reduce la aglomeración en el Explorador de soluciones. Tenga cuidado al realizar la actualización para revisar los cambios en el archivo `.csproj`. Ya no es necesario el archivo `Microsoft.ApplicationInsights.SnapshotCollector.targets`.
- La telemetría se registra en el recurso de Application Insights incluso si ProvideAnonymousTelemetry está establecido en false. Esto es para que podamos implementar una característica de comprobación de estado en Azure Portal. ProvideAnonymousTelemetry afecta solo a la telemetría enviada a Microsoft para la mejora y soporte técnico del producto.
- Cuando TempFolder o ShadowCopyFolder se redirigen a variables de entorno, mantenga inactivo el recopilador hasta que se establezcan esas variables de entorno.
- En el caso de las aplicaciones que se conectan a Internet mediante un servidor proxy, Snapshot Collector ahora detectará automáticamente la configuración del proxy y la pasará a SnapshotUploader.exe.
- Se reduce la prioridad del proceso SnapshotUplaoder (siempre que sea posible). Esta prioridad se puede invalidar mediante la opción IsLowPrioirtySnapshotUploader.
- Se ha agregado el método de extensión GetSnapshotCollector en TelemetryConfiguration para escenarios en los que se desea configurar Snapshot Collector mediante programación.
- Se establece la versión del SDK de Application Insights (en lugar de la versión de la aplicación) en la telemetría orientada al cliente.
- Se envía el primer evento de latido después de dos minutos.
### <a name="bug-fixes"></a>Corrección de errores
- Se ha corregido NullReferenceException cuando las excepciones tienen diccionarios de datos nulos o inmutables.
- En el cargador, se vuelve a intentar la coincidencia de PDB varias veces si se obtiene una infracción de uso compartido.
- Se han corregido los datos de telemetría duplicados cuando más de un subproceso llama a la canalización de telemetría en el inicio.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Cambios
- Los archivos de comentarios de documentos XML se incluyen ahora en el paquete NuGet.
- Se ha agregado el método de extensión ExcludeFromSnapshotting en `System.Exception` para escenarios en los que sabe que tiene una excepción ruidosa y desea evitar la creación de instantáneas para ella.
- Se ha agregado la propiedad de configuración IsEnabledWhenProfiling, cuyo valor predeterminado es true. Se trata de un cambio respecto a las versiones anteriores, en las que la creación de instantáneas estaba deshabilitada temporalmente si Application Insights Profiler estaba realizando una recopilación detallada. El comportamiento anterior se puede recuperar si se establece esta propiedad en false.
### <a name="bug-fixes"></a>Corrección de errores
- Se firma SnapshotUploader64.exe correctamente.
- Se protege frente a una inicialización doble del procesador de telemetría.
- Se evita el doble registro de datos de telemetría en aplicaciones con varias canalizaciones.
- Se ha corregido un error con la fecha de expiración de un plan de recopilación, lo que podría impedir las instantáneas después de 24 horas.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
El cambio más importante en esta versión (de ahí el traslado a un nuevo número de versión secundaria) es una reescritura de la canalización de creación y control de instantáneas. En versiones anteriores, esta funcionalidad se implementaba en código nativo (ProductionBreakpoints *.dll y SnapshotHolder*.exe). La nueva implementación es todo código administrado con P/Invoke. Esta primera versión que usa la nueva canalización no se ha alejado del comportamiento original. La nueva implementación permite un mejor informe de errores y nos prepara para futuras mejoras.

### <a name="other-changes-in-this-version"></a>Otros cambios en esta versión
- Se ha cambiado el nombre de MinidumpUploader.exe a SnapshotUploader.exe (o SnapshotUploader64.exe).
- Se ha agregado telemetría de temporización a las solicitudes para desoptimizar o volver a optimizar.
- Se ha agregado compresión gzip para las cargas de minivolcados.
- Se ha corregido un problema en el que los archivos PDB estaban bloqueados, lo que impide la actualización del sitio.
- Se registra el nombre de la carpeta original (SnapshotCollectorFiles) en la creación de instantáneas.
- Se han ajustado los límites de memoria para los procesos de 64 bits para evitar que se reinicie el sitio debido a OOM.
- Se ha corregido un problema en el que las instantáneas se recopilaban incluso después de la deshabilitación.
- Se registran los eventos de latido en el recurso de IA del cliente.
- Se ha mejorado la velocidad de las instantáneas mediante la eliminación del elemento "Source" del identificador del problema.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Cambios
Se ha aumentado los datos de telemetría sobre el uso.
- Se detecta y se informa sobre la versión de .NET y el sistema operativo.
- Se detecta y se informa sobre los entornos de Azure adicionales (Cloud Services, Service Fabric).
- Se registra y se informa sobre las métricas de excepción (el número de excepciones de primera oportunidad y el número de llamadas a TrackException) en la telemetría de los latidos.
### <a name="bug-fixes"></a>Corrección de errores
- Se ha corregido el control de SqlException en el que no se produce la excepción interna (Win32Exception).
- Se han recortado los espacios finales de las carpetas de símbolos, lo que provocaba un análisis incorrecto de los argumentos de la línea de comandos para MinidumpUploader.
- Se impide el reintento infinito de conexiones erróneas con el punto de conexión del agente de Snapshot Debugger.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Cambios
- Se ha agregado la protección de la memoria del host. Esta característica reduce el impacto en la memoria del equipo host.
- Se ha mejorado la experiencia de visualización de instantáneas en Azure Portal.
