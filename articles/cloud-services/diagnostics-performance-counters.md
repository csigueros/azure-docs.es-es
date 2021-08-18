---
title: Recopilación de contadores de rendimiento en Azure Cloud Services (clásico) | Microsoft Docs
description: Obtenga información acerca de cómo detectar, usar y crear contadores de rendimiento en Cloud Services con Azure Diagnostics y Application Insights.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f18fa4986b9a4a855af6e2b9f14c4e2367213f6e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113089996"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Recopilación de contadores de rendimiento para Azure Cloud Services (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

Los contadores de rendimiento le ofrecen una forma de realizar un seguimiento del rendimiento de su aplicación y host. Windows Server proporciona diferentes contadores de rendimiento relacionados con hardware, aplicaciones, sistema operativo, etc. Al recopilar y enviar información de los contadores de rendimiento a Azure, puede analizar esta información para ayudar a tomar mejores decisiones. 

## <a name="discover-available-counters"></a>Detectar contadores disponibles

Un contador de rendimiento se compone de dos partes, un nombre de conjunto (también conocido como categoría) y uno o varios contadores. Puede usar PowerShell para obtener una lista de los contadores de rendimiento disponibles:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

La propiedad `CounterSetName` representa un conjunto (o categoría) y es un buen indicador de con qué están relacionados los contadores de rendimiento. La propiedad `Paths` representa una colección de contadores de un conjunto. También puede obtener la propiedad `Description` para obtener más información acerca del conjunto de contadores.

Para obtener todos los contadores de un conjunto, use el valor `CounterSetName` y expanda la colección `Paths`. Cada elemento de ruta de acceso es un contador que se puede consultar. Por ejemplo, para obtener los contadores disponibles relacionadas con el conjunto `Processor`, expanda la colección `Paths`:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Estas rutas de contadores individuales se pueden agregar al marco de diagnóstico que usa su servicio en la nube. Para obtener más información acerca de cómo se crea una ruta del contador de rendimiento, consulte [Specifying a Counter Path](/windows/win32/perfctrs/specifying-a-counter-path) (Especificar una ruta de contador).

## <a name="collect-a-performance-counter"></a>Recopilar un contador de rendimiento

Puede agregar un contador de rendimiento a su servicio en la nube para Azure Diagnostics o Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights para Cloud Services le permite especificar qué contadores de rendimiento quiere recopilar. Después de [agregar Application Insights al proyecto](../azure-monitor/app/cloudservices.md#sdk), se agregar un archivo de configuración denominado **ApplicationInsights.config** al proyecto de Visual Studio. Este archivo de configuración define qué tipo de información Application Insights recopila y envía a Azure.

Abra el archivo **ApplicationInsights.config** y busque el elemento **ApplicationInsights** > **TelemetryModules**. Cada elemento secundario `<Add>` define un tipo de telemetría para recopilar, junto con su configuración. El tipo de módulo de telemetría del contador de rendimiento es `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Si este elemento ya está definido, no lo agregue por segunda vez. Cada contador de rendimiento que se puede recopilar se define en un nodo denominado `<Counters>`. Este es un ejemplo que recopila los contadores de rendimiento de la unidad:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Cada contador de rendimiento se representa como un elemento `<Add>` en `<Counters>`. El atributo `PerformanceCounter` define el contador de rendimiento que se va a recopilar. El atributo `ReportAs` es el título que se mostrará en Azure Portal para el contador de rendimiento. Cualquier contador de rendimiento que recopile se colocará en una categoría denominada **Personalizada** en el portal. A diferencia de Azure Diagnostics, no puede establecer el intervalo durante el cual estos contadores de rendimiento se recopilan y envían a Azure. Con Application Insights, los contadores de rendimiento se recopilan y se envían cada minuto. 

Application Insights recopila automáticamente los siguientes contadores de rendimiento:

* \Process(??APP_WIN32_PROC??)\% Tiempo de procesador
* \Memoria\Bytes disponibles
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* Procesador(_Total)\% Hora del procesador

Para más información, vea [Contadores de rendimiento de sistema en Application Insights](../azure-monitor/app/performance-counters.md) y [Application Insights para Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Aunque todos estos datos se agregan a la cuenta de almacenamiento, el portal **no** ofrece una forma nativa de crear gráficos de los datos. Se recomienda encarecidamente que integre otro servicio de diagnóstico, como Application Insights, en la aplicación.

La extensión de Azure Diagnostics para Cloud Services le permite que especificar qué contadores de rendimiento quiere recopilar. Para configurar Azure Diagnostics, consulte [Introducción a la supervisión de servicios en la nube](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Los contadores de rendimiento que quiere recopilar se definen en el archivo **diagnostics.wadcfgx**. Abra este archivo (se define según el rol) en Visual Studio y busque el elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Agregue un nuevo elemento **PerformanceCounterConfiguration** como secundario. Este elemento tiene dos atributos: `counterSpecifier` y `sampleRate`. El atributo `counterSpecifier` define qué conjunto de contadores de rendimiento del sistema (descrito en la sección anterior) se van a recopilar. El valor `sampleRate` indica la frecuencia con la que se sondea ese valor. En conjunto, los contadores de rendimiento se transfieren a Azure según el valor del atributo `scheduledTransferPeriod` del elemento principal `PerformanceCounters`.

Para obtener más información acerca del elemento del esquema `PerformanceCounters`, vea el [esquema de Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element).

El período definido por el atributo `sampleRate` usa el tipo de datos de duración XML para indicar con qué frecuencia se sondea el contador de rendimiento. En el ejemplo siguiente, la frecuencia se establece en `PT3M`, lo que significa `[P]eriod[T]ime[3][M]inutes`, es decir cada tres minutos.

Para obtener más información acerca de cómo se definen `sampleRate` y `scheduledTransferPeriod`, consulte la sección **Tipo de datos de duración** en el tutorial [W3 XML Date and Time Date Types](https://www.w3schools.com/XML/schema_dtypes_date.asp) (Tipos W3 XML Date y Time Date).

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Crear un nuevo contador de rendimiento

Puede crear un nuevo contador de rendimiento y permitir que su código lo use. El código que crea un nuevo contador de rendimiento se debe ejecutar con privilegios elevados, de lo contrario se producirá un error. El código de inicio `OnStart` del servicio en la nube puede crear el contador de rendimiento, y puede solicitarle que ejecute el rol en un contexto con privilegios elevados. O bien, puede crear una tarea de inicio que se ejecuta con privilegios elevados y crea el contador de rendimiento. Para más información, consulte [Configuración y ejecución de tareas de inicio para un servicio en la nube](cloud-services-startup-tasks.md).

Para configurar el rol para ejecutarse con privilegios elevados, agregue un elemento `<Runtime>` al archivo [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef).

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Puede crear y registrar un nuevo contador de rendimiento con unas pocas líneas de código. Use la sobrecarga del método `System.Diagnostics.PerformanceCounterCategory.Create` que crea la categoría y el contador. El código siguiente comprueba primero si existe la categoría y, si no se especifica, crea la categoría y el contador.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Cuando quiera usar el contador, solo tiene que llamar al método `Increment` o al método `IncrementBy`.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Ahora que la aplicación usa el contador personalizado, debe configurar Azure Diagnostics o Application Insights para que realicen un seguimiento del contador.


### <a name="application-insights"></a>Application Insights

Como se mencionó anteriormente, los contadores de rendimiento para Application Insights se definen en el archivo **ApplicationInsights.config**. Abra el archivo **ApplicationInsights.config** y busque el elemento **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters**. Cree un elemento secundario `<Add>` y establezca el atributo `PerformanceCounter` en la categoría y el nombre del contador de rendimiento que creó en el código. Asigne un nombre descriptivo al atributo `ReportAs` que desea ver en el portal.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Como se indicó anteriormente, los contadores de rendimiento que quiere recopilar se definen en el archivo **diagnostics.wadcfgx**. Abra este archivo (se define según el rol) en Visual Studio y busque el elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Agregue un nuevo elemento **PerformanceCounterConfiguration** como secundario. Establezca el atributo `counterSpecifier` en la categoría y el nombre del contador de rendimiento que creó en el código. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Más información

- [Application Insights para Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Contadores de rendimiento de sistema en Application Insights](../azure-monitor/app/performance-counters.md)
- [Specifying a Counter Path](/windows/win32/perfctrs/specifying-a-counter-path) (Especificar una ruta de acceso de contador)
- [Esquema de Azure Diagnostics - Contadores de rendimiento](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element)