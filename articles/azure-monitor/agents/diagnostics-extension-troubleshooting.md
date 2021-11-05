---
title: Solución de problemas de la extensión de Azure Diagnostics
description: Solucione problemas al utilizar Diagnostics de instancias de Azure Virtual Machines, Service Fabric o Cloud Services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 30715eee331547fe3747ff121797bb3e0939380f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012309"
---
# <a name="azure-diagnostics-troubleshooting"></a>Solución de problemas de Azure Diagnostics
En este artículo se proporciona información para la solución de problemas relacionados con el uso de Azure Diagnostics. Para obtener información sobre Azure Diagnostics, consulte la [introducción a Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Componentes lógicos
**Diagnostics Plugin Launcher (DiagnosticsPluginLauncher.exe)** : inicia la extensión Azure Diagnostics. Funciona como el proceso de punto de entrada.

**Diagnostics Plugin (DiagnosticsPlugin.exe)** : configura, inicia y administra la duración del agente de supervisión. Es el proceso principal que ejecuta el iniciador.

**Agente de supervisión (procesos de MonAgent\*.exe)** : supervisa, recopila y transfiere los datos de diagnóstico.

## <a name="logartifact-paths"></a>Rutas de acceso de registro y de artefacto
Estas son las rutas de acceso a algunos de los registros y artefactos más importantes. Se tendrá en cuenta esta información a lo largo del documento.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefacto | Path |
| --- | --- |
| **Archivo de configuración de Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Archivos de registro** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Almacén local para los datos de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Archivo de configuración del agente de supervisión** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Paquete de extensión de Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Ruta de acceso a la utilidad de recopilación de registros** | %SystemDrive%\Packages\GuestAgent\ |
| **Archivo de registro de MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuales
| Artefacto | Path |
| --- | --- |
| **Archivo de configuración de Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Archivos de registro** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Almacén local para los datos de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Archivo de configuración del agente de supervisión** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Archivo de estado** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Paquete de extensión de Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Ruta de acceso a la utilidad de recopilación de registros** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Archivo de registro de MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Los datos métricos no aparecen en Azure Portal
Azure Diagnostics proporciona una serie de datos métricos, que se pueden mostrar en Azure Portal. Si tiene problemas para ver los datos en el portal, consulte la tabla WADMetrics\* de la cuenta de almacenamiento de Azure Diagnostics para ver si los registros de las métricas correspondientes están presentes y para asegurarse que el [proveedor de recursos](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft.Insights esté registrado.

En este caso, el elemento **PartitionKey** de la tabla se compone del id. de recursos, la máquina virtual o el conjunto de escalado de máquinas virtuales. **RowKey** es el nombre de la métrica (también conocido como el nombre del contador de rendimiento).

Si el identificador del recurso es incorrecto, compruebe la opción **Diagnóstico** **Configuración** > **Métricas** > **ResourceId** para ver si el identificador del recurso está bien definido.

Si no hay ningún dato para la métrica específica, compruebe la opción **Configuración de diagnóstico** > **Contador de rendimiento** para ver si se incluye la métrica (el contador de rendimiento). Los siguientes contadores se habilitan de forma predeterminada:
- Procesador(_Total)\% Hora del procesador
- \Memoria\Bytes disponibles
- \ASP.NET Applications(__Total__)\Requests/Sec [\Aplicaciones ASP.NET(Total)\Solicitudes/s]
- \ASP.NET Applications(__Total__)\Errors Total/Sec [\Aplicaciones ASP.NET(Total)\Total de errores/s]
- \ASP.NET\Requests Queued (\ASP.NET\Solicitudes en cola)
- \ASP.NET\Requests Rejected (\ASP.NET\Solicitudes rechazadas)
- \Processor(w3wp)\% Processor Time [\Procesador(w3wp) Tiempo de procesador]
- \Process(w3wp)\Private Bytes [\Proceso(w3wp)\Bytes privados]
- \Process(WaIISHost)\% Processor Time [\Proceso(WaIISHost) Tiempo de procesador]
- \Process(WaIISHost)\Private Bytes [\Proceso(WallSHost)\Bytes privados]
- \Process(WaWorkerHost)\% Processor Time [\Proceso(WaWorkerHost) Tiempo de procesador]
- \Process(WaWorkerHost)\Private Bytes (\Proceso(WaWorkerHost)\Bytes privados)
- \Memory\Page Faults/sec (\Memoria\Errores de página/s)
- \.NET CLR Memory(_Global_)\% Time in GC [Memoria CLR NET(Global) Tiempo en GC]
- \LogicalDisk(C:)\Disk Write Bytes/sec [\DiscoLógico(C:)\Bytes de escritura en disco/segundo]
- \LogicalDisk(C:)\Disk Read Bytes/sec [\DiscoLógico(C:)\Bytes de lectura en disco/s]
- \LogicalDisk(D:)\Disk Write Bytes/sec [\DiscoLógico(D:)\Bytes de escritura en disco/s]
- \LogicalDisk(D:)\Disk Read Bytes/sec [\DiscoLógico(D:)\Bytes de lectura en disco/s]

Si la configuración se ha establecido correctamente, pero todavía no puede ver los datos métricos, siga las instrucciones siguientes para solucionar cualquier problema que tenga.

## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics no se inicia
Examine los archivos **DiagnosticsPluginLauncher.log** y **DiagnosticsPlugin.log** en la ubicación de los archivos de registro proporcionada anteriormente, para obtener información sobre por qué Azure Diagnostics no se pudo iniciar.

Si estos registros indican `Monitoring Agent not reporting success after launch`, significa que hubo un error al iniciar MonAgentHost.exe. Examine los registros en la ubicación que se indicó para `MonAgentHost log file` en la sección anterior.

La última línea de los archivos de registro contiene el código de salida.

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Si encuentra un código de salida **negativo**, consulte la [tabla de códigos de salida](#azure-diagnostics-plugin-exit-codes) en la sección [Referencias](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Los datos de diagnóstico no se registran en Azure Storage
Compruebe si no aparece ningún dato o si solo aparece parte de ellos.

### <a name="diagnostics-infrastructure-logs"></a>Registros de infraestructura de diagnóstico
Diagnostics registra todos los errores de los registros de infraestructura de diagnóstico. Asegúrese de que ha habilitado la opción de [captura de registros de infraestructura de diagnóstico en la configuración](#how-to-check-diagnostics-extension-configuration). A continuación, puede buscar rápidamente los errores más relevantes que aparezcan en la `DiagnosticInfrastructureLogsTable` tabla de la cuenta de almacenamiento configurada.

### <a name="no-data-is-appearing"></a>No aparece ningún dato
La razón más común para que los datos de evento no aparezcan en absoluto, es que la información de la cuenta de almacenamiento se ha definido incorrectamente.

Solución: corrija la configuración de Diagnostics y vuelva a instalar esta extensión.

Si la cuenta de almacenamiento está configurada correctamente, conéctese a la máquina mediante acceso remoto y asegúrese de que *DiagnosticsPlugin.exe* y *MonAgentCore.exe* se están ejecutando. Si no se están ejecutando, siga las instrucciones de la sección [Azure Diagnostics no se inicia](#azure-diagnostics-is-not-starting).

Si los procesos están en ejecución, vaya a [¿Se capturan los datos localmente?](#is-data-getting-captured-locally) y siga las instrucciones que se detallan allí.

Si el problema no se soluciona, pruebe a llevar a cabo las acciones siguientes:

1. Desinstalación del agente
2. Eliminación del directorio C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Instalación del agente de nuevo

### <a name="part-of-the-data-is-missing"></a>No están todos los datos
Si puede ver algunos de los datos pero no todos, esto quiere decir que la colección de datos o la canalización de transferencia están configuradas correctamente. Siga las subsecciones que se muestran aquí para acotar el problema.

#### <a name="is-the-collection-configured"></a>¿Está configurada la colección?
La configuración de diagnóstico contiene instrucciones que indican que se debe recopilar un tipo determinado de datos. [Revise la configuración](#how-to-check-diagnostics-extension-configuration) para comprobar que solo está buscando los datos que configuró en la colección.

#### <a name="is-the-host-generating-data"></a>¿Genera datos el host?
- **Contadores de rendimiento**: abra el monitor de rendimiento y compruebe el contador.

- **Registros de seguimiento**: conéctese a la máquina virtual mediante Escritorio remoto y agregue TextWriterTraceListener al archivo de configuración de la aplicación.  Consulte https://msdn.microsoft.com/library/sk36c28t.aspx para configurar el agente de escucha de texto.  Asegúrese de que el elemento `<trace>` tenga `<trace autoflush="true">`.<br />
Si no ve que se generen registros de seguimiento, consulte Más información sobre los registros de seguimiento que faltan.

- **Seguimientos de ETW**: conéctese a la máquina virtual mediante Escritorio remoto e instale PerfView.  En PerfView, ejecute **Archivo** > **Comando de usuario** > **Escuchar etwprovder1** > **etwprovider2** etc. Tenga en cuenta que el comando de **escucha** distingue mayúsculas de minúsculas y que no puede haber espacios entre la lista de proveedores de ETW separados por comas. Si no se puede ejecutar el comando, puede hacer clic en el botón **Registrar** en la esquina inferior derecha de la herramienta Perfview para ver lo que se intentó ejecutar y cuál fue el resultado.  Suponiendo que la entrada es correcta, aparece una nueva ventana. En unos segundos, empezará a ver los seguimientos de ETW.

- **Registros de eventos**: conéctese a la máquina virtual mediante Escritorio remoto. Abra `Event Viewer` y asegúrese de que los eventos estén ahí.

#### <a name="is-data-getting-captured-locally"></a>¿Se capturan los datos localmente?
A continuación, asegúrese de que los datos se capturen localmente.
Los datos se almacenan localmente en archivos `*.tsf` en el almacén local de datos de diagnóstico. Diferentes tipos de registros se recopilan en diferentes archivos `.tsf`. Los nombres son similares a los nombres de tablas de Azure Storage.

Por ejemplo, los `Performance Counters` se obtienen en `PerformanceCountersTable.tsf`. Los registros de eventos se obtienen en `WindowsEventLogsTable.tsf`. Siga las instrucciones de la sección [Extracción de registros locales](#local-log-extraction) para abrir los archivos de recopilación locales y asegúrese de que se recopilan en el disco.

Si no ve que los registros se recopilen localmente y ya ha comprobado que el host genera datos, es probable que tenga un problema de configuración. Revise las opciones de configuración.

Igualmente, revise la configuración que se generó para MonitoringAgent MaConfig.xml. Compruebe que haya una sección que describa el origen de los registros correspondientes. A continuación, que no se pierdan en la conversión entre la configuración de Diagnostics y la del agente de supervisión.

#### <a name="is-data-getting-transferred"></a>¿Se transfieren los datos?
Si ha comprobado que los datos se capturan localmente, pero sigue sin verlos en su cuenta de almacenamiento, siga estos pasos:

- Asegúrese de que haya proporcionado una cuenta de almacenamiento correcta y que no haya sustituido las claves de la cuenta de almacenamiento propuesta. Al usar Azure Cloud Services, hay que tener en cuenta que en ocasiones los usuarios no actualizan `useDevelopmentStorage=true`.

- Compruebe que la cuenta de almacenamiento proporcionada es correcta. Asegúrese de que no tenga restricciones de red que no permitan que los componentes alcancen los puntos de conexión de almacenamiento público. Una forma de hacerlo consiste en conectarse a la máquina mediante Escritorio remoto e intentar escribir algo en la misma cuenta de almacenamiento.

- Por último, intente ver qué errores notifica el agente de supervisión. El agente de supervisión escribe sus registros en `maeventtable.tsf`, que se encuentra en el almacén local de datos de diagnóstico. Siga las instrucciones de la sección [Extracción de registros locales](#local-log-extraction) para abrir este archivo. A continuación, intente determinar si hay `errors` que indiquen la imposibilidad de leer archivos locales o de escribir en el almacenamiento.

### <a name="capturing-and-archiving-logs"></a>Captura y archivo de registros
Si va a ponerse en contacto con el servicio técnico, lo primero que podrían pedirle es que recopilara registros de su máquina. Para ahorrar tiempo, hágalo usted mismo. Ejecute la utilidad `CollectGuestLogs.exe` en Ruta de acceso a la utilidad de recopilación de registros. Se creará un archivo .zip que contiene todos los registros relevantes de Azure en la misma carpeta.

## <a name="diagnostics-data-tables-not-found"></a>Tablas de datos de diagnóstico no encontradas
Las tablas de Azure Storage que contienen eventos de ETW se nombran mediante el código siguiente:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Este es un ejemplo:

```xml
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Este código genera cuatro tablas:

| Evento | Nombre de la tabla |
| --- | --- |
| proveedor="prov1" &lt;Id. de evento="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| proveedor ="prov1" &lt;ID. de evento="2" eventDestination="dest1" /&gt; |WADdest1 |
| proveedor="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| proveedor="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referencias

### <a name="how-to-check-diagnostics-extension-configuration"></a>Cómo comprobar la configuración de la extensión de Diagnostics
La manera más sencilla de comprobar la configuración de su extensión es ir a [Azure Resource Explorer](https://resources.azure.com) y desplazarse a la máquina virtual o al servicio en la nube en los que se encuentra la extensión de Azure Diagnostics (IaaSDiagnostics/PaaDiagnostics) en cuestión.

Como alternativa, puede conectarse a la máquina mediante Escritorio remoto y examinar el archivo de configuración de Azure Diagnostics que se describe en la sección Rutas de acceso de registro y artefacto.

En cualquier caso, busque **Microsoft.Azure.Diagnostics** y, a continuación, el campo **xmlCfg** o **WadCfg**.

Si está buscando en alguna máquina virtual y está presente el campo **WadCfg**, significa que la configuración está en formato JSON. Si el campo **xmlCfg** está presente, significa que la configuración se ha realizado en XML y está codificada en Base 64. Debe [descodificarlo](https://www.bing.com/search?q=base64+decoder) para ver el XML que cargó Diagnostics.

En cuanto al rol de servicio en la nube, si elige la configuración del disco, los datos se codifican en Base64, por lo que deberá [descodificarlos](https://www.bing.com/search?q=base64+decoder) para ver el XML que se cargó mediante Diagnostics.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de salida del complemento Azure Diagnostics
El complemento devuelve los siguientes códigos de salida:

| Código de salida | Descripción |
| --- | --- |
| 0 |Correcto. |
| -1 |Error genérico. |
| -2 |No se puede cargar el archivo rcf.<p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -3 |No se puede cargar el archivo de configuración de Diagnósticos.<p><p>Solución: Esto se debe a que un archivo de configuración no supera la validación del esquema. La solución es proporcionar un archivo de configuración que cumpla el esquema. |
| -4 |Ya hay otra instancia de Diagnósticos del agente de supervisión que usa el directorio de recursos local.<p><p>Solución: especifique un valor distinto para **LocalResourceDirectory**. |
| -6 |El iniciador del complemento del agente invitado intentó iniciar Diagnósticos con una línea de comandos no válida.<p><p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -10 |El complemento Diagnósticos se cerró con una excepción no controlada. |
| -11 |El agente invitado no pudo crear el proceso encargado de iniciar y supervisar al agente de supervisión.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos.<p> |
| -101 |Argumentos no válidos al llamar al complemento Diagnósticos.<p><p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -102 |El proceso del complemento no se puede iniciar por sí mismo.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos. |
| -103 |El proceso del complemento no se puede iniciar por sí mismo. Concretamente, no puede crear el objeto del registrador.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos. |
| -104 |No se puede cargar el archivo rcf proporcionado por el agente invitado.<p><p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -105 |El complemento Diagnósticos no puede abrir el archivo de configuración de Diagnósticos.<p><p>Este error interno solo debería ocurrir si el complemento de Diagnostics se invoca manualmente de forma incorrecta en la máquina virtual. |
| -106 |No se puede leer el archivo de configuración de Diagnósticos.<p><p>Esto se debe a que un archivo de configuración no supera la validación del esquema. <br><br>Solución: proporcione un archivo de configuración que cumpla el esquema. Para obtener más información, consulte [Cómo comprobar la configuración de la extensión de Diagnostics](#how-to-check-diagnostics-extension-configuration). |
| -107 |El directorio de recursos pasado al agente de supervisión no es válido.<p><p>Este error interno solo debería ocurrir si el agente de supervisión se invoca manualmente y de forma incorrecta en la máquina virtual.</p> |
| -108 |No se puede convertir el archivo de configuración de Diagnósticos al archivo de configuración del agente de supervisión.<p><p>Este error interno solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido. |
| -110 |Error de configuración general de Diagnósticos.<p><p>Este error interno solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido. |
| -111 |No se puede iniciar el agente de supervisión.<p><p>Solución: compruebe que hay suficientes recursos del sistema disponibles. |
| -112 |Error general |

### <a name="local-log-extraction"></a>Extracción de registros locales
El agente de supervisión recopila registros y artefactos como archivos `.tsf`. El archivo `.tsf` no se puede leer pero puede convertirlo a `.csv` de la manera siguiente:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Un archivo nuevo llamado `<relevantLogFile>.csv` se crea en la misma ruta de acceso que el archivo `.tsf` correspondiente.

> [!NOTE]
> Solo debe ejecutar esta utilidad con el archivo .tsf principal (por ejemplo, PerformanceCountersTable.tsf). Los archivos complementarios (por ejemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf etc.) se procesarán automáticamente.

### <a name="more-about-missing-trace-logs"></a>Más información sobre los registros de seguimiento que faltan

> [!NOTE]
> La información que se ofrece a continuación, se aplica principalmente a Azure Cloud Services, a no ser que haya configurado la utilidad DiagnosticsMonitorTraceListener en una aplicación que se ejecute en la máquina virtual de IaaS.

- Asegúrese de que **DiagnosticMonitorTraceListener** esté configurado en el archivo web.config o app.config.  Esto se configura de forma predeterminada en los proyectos de servicios de nube. Sin embargo, algunos clientes lo convierten en comentario, lo que hace que los diagnósticos no recopilen las instrucciones de seguimiento.

- Si no se escriben registros con el método **OnStart** o **Run**, asegúrese de que **DiagnosticMonitorTraceListener** se encuentre en app.config.  De forma predeterminada está en web.config, pero eso solo se aplica al código que se ejecuta en w3wp.exe. Debido a ello, debe estar en app.config para que se capturen los seguimientos que se ejecutan en WaIISHost.exe.

- Asegúrese de que usa **Diagnostics.Trace.TraceXXX** en lugar de **Diagnostics.Debug.WriteXXX.** Las instrucciones de depuración se quitarán de una compilación de versión.

- Asegúrese de que el código compilado tenga realmente **líneas de Diagnostics.Trace** (use Reflector, ildasm o ILSpy para comprobarlo). Los comandos **Diagnostics.Trace** se quitan del binario compilado a menos que se use el símbolo de compilación condicional TRACE. Si usa msbuild para compilar el proyecto, este es un problema habitual con el que se encontrará.

## <a name="known-issues-and-mitigations"></a>Problemas conocidos y mitigaciones
Esta es una lista de problemas conocidos con mitigaciones conocidas:

**1. Dependencia de .NET 4.5**

La extensión de Windows Azure Diagnostics tiene una dependencia de tiempo de ejecución en la plataforma .NET 4.5 o superior. En el momento de escribir este artículo, todas las máquinas aprovisionadas de Azure Cloud Services y todas las imágenes de base oficiales de máquinas virtuales de Azure tienen instalado .NET 4.5 o una versión superior.

Aún así, es posible encontrarse en una situación en la que intente ejecutar Windows Azure Diagnostics en una máquina que no tenga .NET 4.5 o superior. Esto sucede cuando crea su máquina a partir de una imagen o instantánea anterior, o cuando trae su propio disco personalizado.

Generalmente se manifiesta como un código de salida **255** al ejecutar **DiagnosticsPluginLauncher.exe.** El error se produce debido a la siguiente excepción no controlada:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigación:** instale .NET 4.5 o una versión superior en la máquina.

**2. Los datos de los contadores de rendimiento están disponibles en el almacenamiento, pero no se muestran en el portal**

La experiencia del portal de las máquinas virtuales muestra de forma predeterminada determinados contadores de rendimiento. Si no ve estos contadores de rendimiento y sabe que los datos se están generando porque están disponibles en el almacenamiento, consulte lo siguiente:

- Si los datos del almacenamiento tienen nombres de contadores en inglés. Si los nombres de los contadores no están en inglés, el gráfico de métricas del portal no podrá reconocerlos. **Mitigación**: cambie el idioma de la máquina a inglés en las cuentas del sistema. Para ello, seleccione **Panel de control** > **Región** > **Administración** > **Copiar configuración**. A continuación, anule la selección de la opción **Cuentas de sistema y de pantalla de bienvenida** para que el lenguaje personalizado no se aplique a la cuenta del sistema.

- Si usa caracteres comodín (\*) en los nombres de los contadores de rendimiento, el portal no podrá correlacionar el contador de rendimiento configurado y el recopilado cuando estos se envíen al receptor de Azure Storage. **Mitigación**: para asegurarse de que puede usar caracteres comodín y que tiene el portal expandido (\*), enrute los contadores de rendimiento hacia el receptor de Azure Monitor.
