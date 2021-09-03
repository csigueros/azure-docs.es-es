---
title: Uso del agente In-Process de Java de Application Insights en Azure Spring Cloud
description: Supervisión de aplicaciones y microservicios mediante el agente In-Process de Java de Application Insights en Azure Spring Cloud
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1505837a316943c2d22f82a0107bb7a1990e0e83
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014588"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Agente In-Process de Java de Application Insights en Azure Spring Cloud (versión preliminar)

En este documento se explica cómo supervisar las aplicaciones y los microservicios mediante el agente Java de Application Insights en Azure Spring Cloud.

Con esta característica, puede hacer lo siguiente:

* Buscar datos de seguimiento con distintos filtros.
* Ver el mapa de dependencias de los microservicios.
* Comprobar el rendimiento de la solicitud.
* Supervisar las métricas en directo en tiempo real.
* Comprobar los errores de solicitud.
* Comprobar las métricas de aplicación.

Application Insights proporciona muchas perspectivas observables, entre las que se incluyen las siguientes:

* Mapa de aplicación
* Rendimiento
* Errores
* Métricas
* Live Metrics
* Disponibilidad

> [!NOTE]
> Esta característica en versión preliminar aún no se admite en Mooncake ni en las nuevas regiones, como EAU.

## <a name="enable-java-in-process-agent-for-application-insights"></a>Habilitar el agente In-Process de Java para Application Insights

Habilite la característica en versión preliminar del agente In-Process de Java mediante el procedimiento siguiente.

1. Vaya a la página de información general del servicio de la instancia de servicio.
2. Seleccione la entrada **Application Insights** en la hoja de supervisión.
3. Seleccione el botón **Habilitar Application Insights** para habilitar la integración de **Application Insights**.
4. Seleccione una instancia existente de Application Insights o cree una.
5. Seleccione **Enable Java in-process agent** (Habilitar el agente In-Process de Java) para habilitar la característica del agente In-Process de Java en versión preliminar. Aquí también puede personalizar la frecuencia de muestreo de 0 a 100.
6. Seleccione **Guardar** para guardar el cambio.

## <a name="portal"></a>Portal

1. Vaya a la página **servicio | Información general** y seleccione **Application Insights** en la sección **Supervisión**.
2. Seleccione **Habilitar Application Insights** para habilitar Application Insights en Azure Spring Cloud.
3. Seleccione **Enable Java in-process agent** (Habilitar el agente In-Process de Java) para habilitar la característica IPA de Java en versión preliminar. Cuando está habilitada una característica IPA en versión preliminar, puede configurar una frecuencia de muestreo opcional (valor predeterminado del 10,0 %).

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Uso de la característica Application Insights

Cuando está habilitada la característica **Application Insights**, puede hacer lo siguiente:

En el panel de navegación izquierdo, seleccione **Application Insights** para ir a la página **Información general** de Application Insights.

* Seleccione **Mapa de aplicación** para ver el estado de las llamadas entre las aplicaciones.

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Seleccione el vínculo entre el servicio para los clientes y `petclinic` para ver más detalles, como una consulta de SQL.

* En el panel de navegación izquierdo, seleccione **Rendimiento** para ver los datos de rendimiento de las operaciones de todas las aplicaciones, así como las dependencias y los roles.

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* En el panel de navegación izquierdo, seleccione **Errores** para ver si sucede algo inesperado en las aplicaciones.

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* En el panel de navegación izquierdo, seleccione **Métricas** y seleccione el espacio de nombres. Verá las métricas de Spring Boot y las métricas personalizadas, si las hay.

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* En el panel de navegación izquierdo, seleccione **Live Metrics** para ver las métricas en tiempo real de las distintas dimensiones.

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* En el panel de navegación izquierdo, seleccione **Disponibilidad** para supervisar la disponibilidad y la capacidad de respuesta de las aplicaciones web mediante la creación de [pruebas de disponibilidad en Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Plantilla de ARM

Para usar la plantilla de Azure Resource Manager, copie el siguiente contenido en `azuredeploy.json`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI

Aplique la plantilla de ARM con el comando de la CLI:

* Para una instancia existente de Azure Spring Cloud:

   ```azurecli
   az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate]    "samplingRate" --name "assignedName" --resource-group "resourceGroupName"
   ```

* Para una instancia recién creada de Azure Spring Cloud:

   ```azurecli
   az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName"    --disable-app-insights false --enable-java-agent true --name "assignedName" --resource-group    "resourceGroupName"
   ```

* Para deshabilitar Application Insights:

   ```azurecli
   az spring-cloud app-insights update --disable --name "assignedName" --resource-group "resourceGroupName"
   ```

## <a name="java-agent-updateupgrade"></a>Actualización del agente de Java

El agente de Java se actualizará periódicamente con el JDK, lo que puede afectar a los escenarios siguientes.

> [!Note]
> La versión de JDK se actualizará trimestralmente al año.

* Las aplicaciones existentes que usan el agente de Java antes de actualizar no se verán afectadas.
* Las aplicaciones creadas después de la actualización aprovecharán la nueva versión del agente de Java.
* Las aplicaciones existentes que no usaron previamente el agente de Java requerirán un reinicio o una nueva implementación para aprovechar la nueva versión del agente de Java.

## <a name="java-agent-configuration-hot-loading"></a>Carga activa de la configuración del agente de Java

Azure Spring Cloud ha habilitado un mecanismo de carga activa para ajustar la configuración del agente sin reiniciar las aplicaciones.

> [!Note]
> El mecanismo de carga activa tiene un retraso en minutos.

* Cuando el agente de Java se ha habilitado previamente, los cambios en la instancia de Application Insights o SamplingRate NO requieren que se reinicien las aplicaciones.
* Si habilita el agente de Java, debe reiniciar las aplicaciones.
* Al deshabilitar el agente de Java, las aplicaciones dejarán de enviar todos los datos de supervisión después de un retraso en minutos. Puede reiniciar las aplicaciones para quitar el agente del entorno en tiempo de ejecución de Java.

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Coincidencia de conceptos entre Azure Spring Cloud y Application Insights

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __Mapa de aplicación__/Rol<br />* __Live Metrics__/Rol<br />* __Errores__/Roles/Rol en la nube<br />* __Rendimiento__/Roles/Rol en la nube |
| `App Instance`     | * __Mapa de aplicación__/Instancia de rol<br />* __Live Metrics__/Nombre del servicio<br />* __Errores__/Roles/Instancia en la nube<br />* __Rendimiento__/Roles/Instancia en la nube |

El nombre `App Instance` de Azure Spring Cloud se cambiará o se generará en los escenarios siguientes:

* Creación de una aplicación.
* Implementación de un archivo JAR o un código fuente en una aplicación existente.
* Inicio de una implementación azul-verde.
* Reinicio de la aplicación.
* Detención de la implementación de una aplicación y posterior reinicio.

Cuando los datos se almacenan en Application Insights, este contiene el historial de las instancias de aplicación de Azure Spring Cloud creadas o implementadas desde que se habilitó el agente de Java. Esto significa que, en el portal de Application Insights, puede ver los datos de aplicación creados ayer, pero luego eliminados durante un intervalo de tiempo específico; por ejemplo, las últimas 24 horas. En los escenarios siguientes, se muestra el funcionamiento:

* Ha creado una aplicación alrededor de las 8:00 de hoy desde Azure Spring Cloud con el agente de Java habilitado y, después, ha implementado un archivo JAR en esta aplicación alrededor de las 8:10 de hoy. Después de algunas pruebas, cambia el código e implementa un nuevo archivo JAR en esta aplicación a las 8:30 de hoy. A continuación, se toma un respiro y, cuando vuelve a ello alrededor de las 11:00, comprueba algunos datos de Application Insights. Verá lo siguiente:
  * Tres instancias de Application Map con intervalos de tiempo en las últimas 24 horas, así como información de errores, rendimiento y métricas.
  * Una instancia de Application Map con un intervalo de tiempo en la última hora, así como información de errores, rendimiento y métricas.
  * Una instancia de Live Metrics.
* Ha creado una aplicación alrededor de las 8:00 de hoy desde Azure Spring Cloud con el agente de Java habilitado y, después, ha implementado un archivo JAR en esta aplicación alrededor de las 8:10 de hoy. Alrededor de las 8:30 de hoy, probará una implementación azul-verde con otro archivo JAR. Actualmente, tiene dos implementaciones para esta aplicación. Después de un descanso, alrededor de las 11:00 de hoy, quiere comprobar algunos datos de Application Insights. Verá lo siguiente:
  * Tres instancias de Application Map con intervalos de tiempo en las últimas 24 horas, así como información de errores, rendimiento y métricas.
  * Dos instancias de Application Map con intervalos de tiempo en la última hora, así como información de errores, rendimiento y métricas.
  * Dos instancias de Live Metrics.

## <a name="see-also"></a>Consulte también

* [Uso del seguimiento distribuido con Azure Spring Cloud](./how-to-distributed-tracing.md)
* [Análisis de registros y métricas](diagnostic-services.md)
* [Streaming de registros en tiempo real](./how-to-log-streaming.md)
