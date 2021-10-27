---
title: 'Procedimientos recomendados de Azure Monitor: configuración de la recopilación de datos'
description: Instrucciones y recomendaciones para configurar la recopilación de datos en Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 408103657b3d1485d8807768e83cc1d9721fd22b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181586"
---
# <a name="azure-monitor-best-practices---configure-data-collection"></a>Procedimientos recomendados de Azure Monitor: configuración de la recopilación de datos
Este artículo forma parte del escenario de [recomendaciones para configurar Azure Monitor](best-practices.md). Describe los pasos recomendados para configurar la recopilación de datos necesaria para habilitar las características de Azure Monitor para las aplicaciones y los recursos híbridos y de Azure.


> [!IMPORTANT]
> Las características de Azure Monitor y su configuración variarán en función de los requisitos empresariales que se equilibran con el costo de las características habilitadas. En cada paso siguiente se identificará si existe algún costo potencial y debe evaluar estos costos antes de continuar. Consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obtener información detallada sobre los precios.
> 

## <a name="create-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics
Necesita al menos un área de trabajo de Log Analytics para habilitar los [registros de Azure Monitor](logs/data-platform-logs.md), lo que es necesario para recopilar estos datos como registros de recursos de Azure, recopilar datos del sistema operativo invitado de Azure Virtual Machines y para la mayoría de la información de Azure Monitor. Otros servicios, como Azure Sentinel y Azure Security Center, también usan un área de trabajo de Log Analytics y pueden compartir la misma que se usa para Azure Monitor. Puede empezar con una sola área de trabajo para admitir esta supervisión, pero consulte [Diseño de la implementación de registros de Azure Monitor](logs/design-logs-deployment.md) para obtener instrucciones sobre cuándo usar varias áreas de trabajo.

No hay ningún costo por la creación de un área de trabajo de Log Analytics, pero se puede realizar un cargo adicional una vez configurados los datos que se van a recopilar. Consulte [Administración del uso y los costos con los registros de Azure Monitor](logs/manage-cost-storage.md) para más información.  

Consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](logs/quick-create-workspace.md) para crear un área de trabajo de Log Analytics inicial. Consulte [Administración del acceso a los datos de registro y las áreas de trabajo en Azure Monitor](logs/manage-access.md) para configurar el acceso. Sin embargo, puede usar métodos escalables (como las plantillas de Resource Manager) para configurar áreas de trabajo, aunque esto no suele ser necesario, ya que la mayoría de los entornos requerirán un número mínimo.

## <a name="collect-data-from-azure-resources"></a>Recopilación de datos a partir de recursos de Azure
La supervisión de los recursos de Azure está disponible automáticamente sin necesidad de configuración, pero debe realizar los pasos de configuración para recopilar datos de supervisión adicionales. En la tabla siguiente se muestran los pasos de configuración necesarios para recopilar todos los datos disponibles de los recursos de Azure, incluido el paso en el que se envían los datos a Métricas de Azure Monitor y Registros de Azure Monitor. Cada uno de los pasos se describe en detalle en las secciones que aparecen a continuación.

[ ![Supervisión de la implementación de recursos de Azure](media/best-practices-data-collection/best-practices-azure-resources.png)](media/best-practices-data-collection/best-practices-azure-resources.png#lightbox)

### <a name="collect-tenant-and-subscription-logs"></a>Recopilación de registros de inquilinos y de suscripciones
Aunque los [registros de Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) del inquilino y el [registro de actividad](essentials/platform-logs-overview.md) de la suscripción se recopilan automáticamente, al enviarlos a un área de trabajo de Log Analytics, se pueden analizar estos eventos con otros datos de registro mediante consultas de registro en Log Analytics. Esto también le permite crear alertas de consulta de registro, que es la única manera de alertar sobre los registros de Azure Active Directory y proporcionar una lógica más compleja que las alertas del registro de actividad.

No hay ningún costo por enviar el registro de actividad a un área de trabajo, pero hay una ingesta de datos y un cargo de retención para los registros de Azure Active Directory. 

Consulte [Integración de registros de Azure AD con registros de Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) y [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](essentials/diagnostic-settings.md) para crear una configuración de diagnóstico para el inquilino y la suscripción para enviar entradas de registro a su área de trabajo de Log Analytics. 




### <a name="collect-resource-logs-and-platform-metrics"></a>Recopilación de registros de recursos y de métricas de plataforma
Los recursos de Azure generan automáticamente [registros de recursos](essentials/platform-logs-overview.md) que proporcionan detalles de las operaciones realizadas dentro del recurso. Sin embargo, a diferencia de las métricas de plataforma, debe configurar los registros de recursos que se van a recopilar. Cree una configuración de diagnóstico para enviarlos a un área de trabajo de Log Analytics y combinarlos con los demás datos usados con los registros de Azure Monitor. Se puede usar la misma configuración de diagnóstico para enviar también las métricas de la plataforma de la mayoría de los recursos a la misma área de trabajo, lo que permite analizar datos de métricas mediante consultas de registro con otros datos recopilados.

La recopilación de registros de recursos en el área de trabajo de Log Analytics tiene un costo asociado; por lo tanto, seleccione solo las categorías de registro con datos valiosos. La recopilación de todas las categorías genera costos por recopilar datos con poco valor. Consulte la documentación sobre supervisión de cada servicio de Azure para obtener una descripción de las categorías y recomendaciones sobre cuáles deben recopilarse. Consulte también [Administrar el uso y los costos con los registros de Azure Monitor](logs/manage-cost-storage.md) para obtener más información sobre cómo optimizar el costo de la recopilación de registros.

Consulte [Creación de una configuración de diagnóstico para recopilar registros y métricas en Azure](essentials/diagnostic-settings.md#create-in-azure-portal) para crear una configuración de diagnóstico para un recurso de Azure. 

Dado que es necesario crear una configuración de diagnóstico para cada recurso de Azure, use Azure Policy para crear automáticamente una configuración de diagnóstico a medida que se crea cada recurso. Cada tipo de recurso de Azure tiene un conjunto único de categorías que se deben enumerar en la configuración de diagnóstico. Debido a esto, cada tipo de recurso requiere una definición de directiva independiente. Algunos tipos de recursos tienen definiciones de directivas integradas que se pueden asignar sin modificaciones. Para otros tipos de recursos, debe crear una definición personalizada.

Consulte [Creación a escala mediante Azure Policy](essentials/diagnostic-settings.md#create-at-scale-using-azure-policy) para ver un proceso de creación de definiciones de directiva para un servicio de Azure determinado y detalles para crear la configuración de diagnóstico a escala.

### <a name="enable-insights"></a>Habilitación de Insights
Las conclusiones proporcionan una experiencia de supervisión personalizada para un servicio determinado. Usan los mismos datos que ya se recopilan, como métricas de plataforma y registros de recursos, pero proporcionan libros personalizados que le ayudan a identificar y a analizar los datos más críticos. La mayoría de las conclusiones estarán disponibles en Azure Portal sin necesidad de configuración alguna, aparte de recopilar registros de recursos para ese servicio. Consulte la documentación sobre supervisión de cada servicio de Azure para determinar si tiene una conclusión y si requiere configuración.

Las conclusiones no tienen costo alguno, pero puede que se le cobren los datos recopilados.

Consulte [¿Qué supervisa Azure Monitor?](monitor-reference.md) para obtener una lista de las conclusiones y soluciones disponibles en Azure Monitor. Consulte la documentación de cada una de las configuraciones o la información de precios.

> [!IMPORTANT]
> Las conclusiones siguientes son significativamente más complejas que otras y tienen instrucciones adicionales para su configuración.
> 
> - [VM Insights](#monitor-virtual-machines)
> - [Container Insights](#monitor-containers)
> - [Supervisar aplicaciones](#monitor-applications)


## <a name="monitor-virtual-machines"></a>Supervisión de máquinas virtuales
Las máquinas virtuales generan datos similares a otros recursos de Azure, pero requieren un agente para recopilar datos del sistema operativo invitado. Las máquinas virtuales también tienen requisitos de supervisión únicos debido a las distintas cargas de trabajo que se ejecutan en ellas. Consulte [Supervisión de máquinas virtuales de Azure con Azure Monitor](vm/monitor-vm-azure.md) para obtener un escenario dedicado sobre la supervisión de máquinas virtuales con Azure Monitor.

## <a name="monitor-containers"></a>Supervisión de contenedores
Las máquinas virtuales generan datos similares a otros recursos de Azure, pero requieren una versión contenedorizada del agente de Log Analytics para recopilar los datos necesarios. La información del contenedor le ayuda a preparar el entorno contenedorizado para la supervisión y funciona junto con herramientas de terceros a fin de proporcionar una supervisión completa de AKS y de los flujos de trabajo que admite. Consulte [Supervisión de Azure Kubernetes Service (AKS) con Azure Monitor](../aks/monitor-aks.md?toc=/azure/azure-monitor/toc.json) para ver un escenario dedicado sobre la supervisión de AKS con Azure Monitor.

## <a name="monitor-applications"></a>Supervisión de aplicaciones
Azure Monitor supervisa las aplicaciones personalizadas con [Application Insights](app/app-insights-overview.md), que debe configurar para cada aplicación que quiera supervisar. El proceso de configuración variará en función del tipo de aplicación que se esté supervisando y del tipo de supervisión que quiera realizar. Los datos recopilados por Application Insights se almacenan en las métricas de Azure Monitor, los registros de Azure Monitor y Azure Blob Storage, en función de la característica. Los datos de rendimiento se almacenan en las métricas de Azure Monitor y los registros de Azure Monitor sin necesidad de configuración adicional.

### <a name="create-an-application-resource"></a>Creación de un recurso de aplicación
Application Insights es la característica de Azure Monitor para supervisar las aplicaciones híbridas y nativas de nube.

Debe crear un recurso en Application Insights para cada aplicación que vaya a supervisar. En las aplicaciones basadas en el área de trabajo, los datos de registro recopilados por Application Insights se almacenan en los registros de Azure Monitor. Los datos de registro de las aplicaciones clásicas se almacenan de forma independiente del área de trabajo de Log Analytics, tal como se describe en [Estructura de los datos](logs/data-platform-logs.md#data-structure).

 Al crear la aplicación, debe seleccionar si quiere usar la versión clásica o basada en el área de trabajo. Consulte [Creación de recursos en Application Insights](app/create-new-resource.md) para crear una aplicación clásica. Consulte [Recursos de Application Insights basados en área de trabajo (versión preliminar)](app/create-workspace-resource.md) para crear una aplicación basada en el área de trabajo.


 Una decisión de diseño fundamental es si debe usarse un recurso de aplicación independiente o único para varias aplicaciones. Los recursos independientes pueden ahorrar costos e impedir que se mezclen datos de distintas aplicaciones, pero un único recurso puede simplificar la supervisión al conservar todos los datos de telemetría pertinentes juntos. Consulte [¿Cuántos recursos de Application Insights se deben implementar?](app/separate-resources.md) para obtener criterios detallados sobre cómo tomar esta decisión de diseño.



### <a name="configure-codeless-or-code-based-monitoring"></a>Configuración de la supervisión no codificada o basada en código
Para habilitar la supervisión de una aplicación, debe decidir si utilizará la supervisión sin código o basada en código. El proceso de configuración variará en función de esta decisión y del tipo de aplicación que vaya a supervisar.

La **supervisión sin código** es más fácil de implementar y se puede configurar después del desarrollo del código. No requiere ninguna actualización en el código. Consulte los siguientes recursos para obtener más información sobre cómo habilitar la supervisión en función de la aplicación.

- [Aplicaciones hospedadas en Azure Web Apps](app/azure-web-apps.md)
- [Aplicaciones de Java](app/java-in-process-agent.md)
- [Aplicaciones ASP.NET hospedadas en IIS en una máquina virtual de Azure o un conjunto de escalado de máquinas virtuales de Azure](app/azure-vm-vmss-apps.md)
- [Aplicaciones ASP.NET hospedadas en una máquina virtual en el entorno local de IIS](app/status-monitor-v2-overview.md)


La **supervisión basada en código** es más personalizable y recopila telemetría adicional, pero requiere agregar una dependencia al código en los paquetes NuGet del SDK de Application Insights. Consulte los siguientes recursos para obtener más información sobre cómo habilitar la supervisión en función de la aplicación.

- [Aplicaciones ASP.NET](app/asp-net.md)
- [Aplicación ASP.NET Core](app/asp-net-core.md)
- [Aplicaciones de consola .NET](app/console.md)
- [Java](app/java-in-process-agent.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Otras plataformas](app/platforms.md)

### <a name="configure-availability-testing"></a>Configuración de las pruebas de disponibilidad
Las pruebas de disponibilidad en Application Insights son pruebas periódicas que supervisan la disponibilidad y la capacidad de respuesta de la aplicación a intervalos regulares desde puntos de todo el mundo. Puede crear una prueba de ping simple de forma gratuita o crear una secuencia de solicitudes web para simular las transacciones de usuario con un costo asociado. 

Consulte [Supervisión de la disponibilidad de un sitio web](app/monitor-web-app-availability.md) para obtener un resumen de los diferentes tipos de pruebas y detalles sobre cómo crearlas.

### <a name="configure-profiler"></a>Configuración de Profiler
Profiler en Application Insights proporciona seguimientos de rendimiento para aplicaciones .NET. Le ayuda a identificar la ruta de acceso "activa" al código que tarda más tiempo cuando se atiende una solicitud web determinada. El proceso para configurar el generador de perfiles varía en función del tipo de aplicación. 

Consulte [Generación de perfiles de aplicaciones de producción en Azure con Application Insights](app/profiler-overview.md) para obtener más información sobre la configuración de Profiler.

### <a name="configure-snapshot-debugger"></a>Configuración de Snapshot Debugger
Snapshot Debugger en Application Insights supervisa la telemetría de excepciones de la aplicación .NET y recopila instantáneas en las principales excepciones que se producen para que tenga la información que necesita para diagnosticar problemas en producción. El proceso para configurar Snapshot Debugger varía en función del tipo de aplicación. 

Consulte [Depurar instantáneas cuando se producen excepciones en aplicaciones de .NET](app/snapshot-debugger.md) para obtener más información sobre la configuración de Snapshot Debugger.

## <a name="next-steps"></a>Pasos siguientes

- Con la recopilación de datos configurada para todos los recursos de Azure, consulte [Análisis y visualización de datos](best-practices-analysis.md) para ver las opciones de análisis de estos datos. 