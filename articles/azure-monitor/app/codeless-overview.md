---
title: 'Supervisión de las aplicaciones sin cambios de código: instrumentación automática para Azure Monitor Application Insights | Microsoft Docs'
description: 'Información general de la instrumentación automática para Azure Monitor Application Insights: administración del rendimiento de aplicaciones sin código'
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/31/2021
ms.reviewer: mbullwin
ms.openlocfilehash: f7193489c35384a9ede10606a9ad335ea69bb974
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434887"
---
# <a name="what-is-auto-instrumentation-for-azure-monitor-application-insights"></a>¿Qué es la instrumentación automática para Azure Monitor Application Insights?

La instrumentación automática permite habilitar la supervisión de las aplicaciones con Application Insights sin modificar el código.  

Application Insights se integra con varios proveedores de recursos y funciona en distintos entornos. En resumen, solo debe habilitar y, en algunos casos, configurar el agente, que recopilará la telemetría automáticamente. En unos instantes, podrá ver las métricas, las solicitudes y las dependencias en el recurso de Application Insights, lo que le permitirá detectar el origen de los posibles problemas antes de que se produzcan y analizar la causa raíz con la vista de transacciones integral.

## <a name="supported-environments-languages-and-resource-providers"></a>Entornos, lenguajes y proveedores de recursos admitidos

A medida que agregamos nuevas integraciones, la matriz de funcionalidades de instrumentación automática se vuelve compleja. En la tabla siguiente se muestra el estado actual de la situación en lo que se refiere a la compatibilidad con varios proveedores de recursos, lenguajes y entornos.

|Entorno/proveedor de recursos          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service en Windows           | Disponibilidad general, OnBD*       | Disponibilidad general, opcional      | Vista previa pública  | Vista previa pública  | No compatible   |
|Azure App Service en Linux             | N/D             | No compatible   | Disponibilidad general              | GA              | No compatible   |
|Azure Functions: básico                | Disponibilidad general, OnBD*       | Disponibilidad general, OnBD*       | Disponibilidad general, OnBD*       | Disponibilidad general, OnBD*       | Disponibilidad general, OnBD*       |
|Azure Functions: dependencias         | No compatible   | No compatible   | Vista previa pública  | No compatible   | A través de la [ extensión](monitor-functions.md#distributed-tracing-for-python-function-apps)   |
|Azure Spring Cloud                     | No compatible   | No compatible   | Vista previa pública  | No compatible   | No compatible   |
|Azure Kubernetes Service               | N/D             | No compatible   | Mediante el agente   | No compatible   | No compatible   |
|VM de Azure con Windows                      | Vista previa pública  | No compatible   | Mediante el agente   | No compatible   | No compatible   |
|VM locales con Windows                | Disponibilidad general, opcional      | No compatible   | Mediante el agente   | No compatible   | No compatible   |
|Agente independiente: cualquier ent.            | No compatible   | No compatible   | Disponibilidad general              | No compatible   | No compatible   |

*OnBD significa "Activado de forma predeterminada"; Application Insights se habilitará automáticamente una vez que implemente la aplicación en los entornos compatibles. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

La supervisión de aplicaciones en Azure App Service en Windows está disponible para aplicaciones de **[.NET](./azure-web-apps.md?tabs=net)** (habilitada de forma predeterminada), **[.NET Core](./azure-web-apps.md?tabs=netcore)** , **[Java](./azure-web-apps.md?tabs=java)** y **[Node.js](./azure-web-apps.md?tabs=nodejs)** . Para supervisar una aplicación de Python, agregue el [SDK](./opencensus-python.md) al código.

> [!NOTE]
> Para Windows, la supervisión de aplicaciones está disponible actualmente para los servicios administrados o basados en código en App Service. La supervisión de aplicaciones en contenedores de Windows en App Service todavía no se admite mediante la integración con Application Insights.

### <a name="linux"></a>Linux
Puede habilitar la supervisión de aplicaciones de **[Java](./azure-web-apps.md?tabs=java)** y **[Node.js](./azure-web-apps.md?tabs=nodejs)** que se ejecutan en Linux en App Service mediante el portal. La experiencia de ambos lenguajes está en GA y disponible en todas las regiones. 

Para otros lenguajes, como [.NET Core](./asp-net-core.md) y [Python](./opencensus-python.md), use el SDK.

## <a name="azure-functions"></a>Azure Functions

La supervisión básica de Azure Functions está habilitada de forma predeterminada para recopilar el registro, el rendimiento, los datos de error y las solicitudes HTTP. En el caso de las aplicaciones Java, puede habilitar una supervisión más completa con seguimiento distribuido y obtener los detalles de la transacción de un extremo a otro. Esta funcionalidad de Java está en versión preliminar pública y puede [habilitarla en Azure Portal](./monitor-functions.md).

## <a name="azure-spring-cloud"></a>Azure Spring Cloud

### <a name="java"></a>Java 
La supervisión de aplicaciones Java que se ejecutan en Azure Spring Cloud está integrada en el portal; puede habilitar Application Insights directamente desde Azure Portal, tanto para los recursos de Azure Spring Cloud recién creados como para los ya existentes.  

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

La instrumentación sin código de Azure Kubernetes Service está disponible actualmente para aplicaciones Java a través del [agente independiente](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Conjunto de escalado de máquinas virtuales y máquinas virtuales de Azure con Windows

La instrumentación automática para máquinas virtuales de Azure y el conjunto de escalado de máquinas virtuales está disponible para [.NET](./azure-vm-vmss-apps.md) y [Java](./java-in-process-agent.md). Esta experiencia no está integrada en el portal. La supervisión se habilita mediante unos pocos pasos con una solución independiente y no requiere ningún cambio en el código.  

## <a name="on-premises-servers"></a>Servidores locales
Puede habilitar fácilmente la supervisión de sus [servidores de Windows locales para las aplicaciones .NET](./status-monitor-v2-overview.md) y para las [aplicaciones Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Otros entornos
El versátil agente independiente de Java funciona en cualquier entorno, por lo que no es necesario instrumentar el código. [Siga esta guía](./java-in-process-agent.md) para habilitar Application Insights y obtener información sobre las increíbles funcionalidades del agente de Java. El agente se encuentra en versión preliminar pública y está disponible en todas las regiones. 

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Application Insights](./app-insights-overview.md)
* [Mapa de aplicación](./app-map.md)
* [Supervisión del rendimiento de un extremo a otro](../app/tutorial-performance.md)
