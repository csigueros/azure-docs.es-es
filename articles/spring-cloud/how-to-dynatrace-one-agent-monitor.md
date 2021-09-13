---
title: Supervisión de aplicaciones de Azure Spring Cloud con Dynatrace Java OneAgent
description: Uso de Dynatrace Java OneAgent para supervisar aplicaciones de Azure Spring Cloud
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: ea4ce0946239dd6355174674f443a1f29c2b9d06
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438883"
---
# <a name="how-to-monitor-azure-spring-cloud-applications-with-dynatrace-java-oneagent"></a>Supervisión de aplicaciones de Azure Spring Cloud con Dynatrace Java OneAgent

En este artículo se explica cómo usar Dynatrace OneAgent para supervisar las aplicaciones de Azure Spring Cloud.

Con Dynatrace OneAgent, puede hacer lo siguiente:

* Supervisar las aplicaciones con Dynatrace OneAgent.
* Configurar Dynatrace OneAgent mediante variables de entorno.
* Comprobar todos los datos de supervisión del panel de Dynatrace.

En el siguiente vídeo se presenta Dynatrace OneAgent.

<br>

> [!VIDEO https://www.youtube.com/embed/PF0_SxuiZ2w]

## <a name="prerequisites"></a>Requisitos previos

* [CLI de Azure](/cli/azure/install-azure-cli)
* [Una cuenta de Dynatrace](https://www.dynatrace.com/)
* [Un token de PaaS de Dynatrace y un token de inquilino](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

## <a name="activate-dynatrace-oneagent"></a>Activación de Dynatrace OneAgent

En las secciones siguientes se describe cómo activar Dynatrace OneAgent.

### <a name="prepare-your-azure-spring-cloud-environment"></a>Preparación del entorno de Azure Spring Cloud

1. Cree una instancia de Azure Spring Cloud.
1. Cree una aplicación de la que desee informar a Dynatrace mediante la ejecución del siguiente comando. Reemplace los marcadores de posición *\<...>* por sus propios valores.
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

### <a name="determine-the-values-for-the-required-environment-variables"></a>Determinación de los valores de las variables de entorno necesarias

Para activar Dynatrace OneAgent en la instancia de Azure Spring Cloud, debe configurar cuatro variables de entorno: `DT_TENANT`, `DT_TENANTTOKEN`, `DT_CONNECTION_POINT` y `DT_CLUSTER_ID`. Para más información, consulte el tema sobre la [integración de OneAgent con Azure Spring Cloud](https://www.dynatrace.com/support/help/shortlink/azure-spring).

En el caso de aplicaciones con varias instancias, Dynatrace tiene varias formas de agruparlas. `DT_CLUSTER_ID` es una de las formas. Para obtener más información, consulte el tema sobre la [personalización de la estructura de los grupos de control de procesos](https://www.dynatrace.com/support/help/how-to-use-dynatrace/process-groups/configuration/adapt-the-composition-of-default-process-groups/).

### <a name="add-the-environment-variables-to-your-application"></a>Incorporación de las variables de entorno a la aplicación

Puede agregar los pares clave-valor de variable de entorno a la aplicación mediante Azure Portal o el CLI de Azure.

#### <a name="option-1-azure-cli"></a>Opción 1: CLI de Azure

Para agregar los pares clave-valor mediante el CLI de Azure, ejecute el siguiente comando, reemplazando los marcadores de posición *\<...>* por los valores determinados en los pasos anteriores.

```azurecli
az spring-cloud app deploy \
    --resource-group <your-resource-group-name> \
    --service <your-Azure-Spring-Cloud-name> \
    --name <your-application-name> \
    --jar-path app.jar \
    --env \
        DT_TENANT=<your-environment-ID> \
        DT_TENANTTOKEN=<your-tenant-token> \
        DT_CONNECTION_POINT=<your-communication-endpoint>
```

#### <a name="option-2-portal"></a>Opción 2: Azure Portal

Para agregar los pares clave-valor mediante Azure Portal, siga estos pasos:

1. Vaya a la lista de aplicaciones existentes.

   :::image type="content" source="media/dynatrace-oneagent/existing-applications.png" alt-text="Captura de pantalla de Azure Portal que muestra la sección de aplicaciones de Azure Spring Cloud." lightbox="media/dynatrace-oneagent/existing-applications.png":::

1. Seleccione una aplicación para ir a su página de **información general**.

   :::image type="content" source="media/dynatrace-oneagent/overview-application.png" alt-text="Captura de pantalla de la sección Información general de la aplicación." lightbox="media/dynatrace-oneagent/overview-application.png":::

1. Seleccione **Configuraciones** para agregar, actualizar o eliminar valores en la sección **Variables de entorno** de la aplicación.

   :::image type="content" source="media/dynatrace-oneagent/configuration-application.png" alt-text="Captura de pantalla de la pestaña &quot;Variables de entorno&quot; de la sección Configuración de la aplicación." lightbox="media/dynatrace-oneagent/configuration-application.png":::

## <a name="automation"></a>Automation

Con Terraform o una plantilla de Azure Resource Manager (plantilla de ARM), también puede ejecutar una canalización de automatización de aprovisionamiento. Esta canalización puede proporcionar una experiencia práctica completa para instrumentar y supervisar las nuevas aplicaciones que cree e implemente.

### <a name="terraform"></a>Terraform

Para configurar las variables de entorno en una plantilla de Terraform, agregue el código que se muestra a continuación a la plantilla y reemplace los marcadores de posición *\<...>* por sus propios valores. Para obtener más información, consulte el tema sobre la [administración de una implementación activa de Azure Spring Cloud](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
environment_variables = {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

### <a name="arm-template"></a>Plantilla ARM

Para configurar las variables de entorno en una plantilla de ARM, agregue el código a la plantilla que se muestra a continuación y reemplace los marcadores de posición *\<...>* por sus propios valores. Para obtener más información, consulte [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"environmentVariables": {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

## <a name="view-reports-in-dynatrace"></a>Visualización de informes en Dynatrace

En esta sección se describe cómo buscar varios informes en Dynatrace.

> [!NOTE]
> El menú y la interfaz de usuario de Dynatrace evolucionarán gradualmente. Por este motivo, el panel puede moverse a otras secciones del sitio web de Dynatrace y es posible que las capturas de pantalla que se muestran a continuación no reflejen la versión actual de la interfaz de usuario.

Después de agregar las variables de entorno a la aplicación, Dynatrace comienza a recopilar datos. Para ver informes, use el [menú de Dynatrace](https://www.dynatrace.com/support/help/get-started/navigation/), vaya a **Servicios** y, a continuación, seleccione la aplicación.

Puede encontrar el **Flujo de servicio** en **\<your-app-name>/Details/Service flow**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png" alt-text="Captura de pantalla del informe &quot;Flujo de servicio&quot; de Dynatrace." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png":::

Puede encontrar las **Zonas activas de método** en **\<your-app-name>/Details/Method hotspots**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png" alt-text="Captura de pantalla del informe &quot;Zonas activas de método&quot;." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png":::

Puede encontrar las **Instrucciones de bases de datos** en **\<your-app-name>/Details/Response time analysis**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png" alt-text="Captura de pantalla del informe &quot;Análisis de tiempo de respuesta&quot; y la sección &quot;Instrucciones de bases de datos&quot;." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png":::

A continuación, vaya a la sección **Multidimensional analysis (Análisis multidimensional)** .

Puede encontrar las **Instrucciones principales de bases de datos** en **Multidimensional analysis/Top database statements**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png" alt-text="Captura de pantalla del informe &quot;Instrucciones principales de bases de datos&quot;." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png":::

Puede encontrar la **Información general sobre excepciones** en **Multidimensional analysis/Exceptions overview**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png" alt-text="Captura de pantalla del informe &quot;Información general sobre excepciones&quot;." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png":::

A continuación, vaya a la sección **Profiling and optimization (Generación de perfiles y optimización)** .

Puede encontrar el **Análisis de CPU** en **Profiling and optimization/CPU analysis**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png" alt-text="Captura de pantalla del informe &quot;Análisis de CPU&quot;." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png":::

A continuación, vaya a la sección **Bases de datos**.

Puede encontrar el **Seguimiento regresivo** en **Databases/Details/Backtrace**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png" alt-text="Captura de pantalla del informe &quot;Seguimiento regresivo&quot;." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png":::

## <a name="dynatrace-oneagent-logging"></a>Registro de Dynatrace OneAgent

De manera predeterminada, Azure Spring Cloud imprimirá los registros de nivel de *información* de Dynatrace OneAgent en `STDOUT`. Los registros se mezclarán con los registros de aplicaciones. Puede encontrar la versión explícita del agente en los registros de aplicaciones.

También puede obtener los registros del agente de Dynatrace desde las siguientes ubicaciones:

* Registros de Azure Spring Cloud
* Azure Spring Cloud Application Insights
* LogStream de Azure Spring Cloud

Puede aplicar algunas variables de entorno proporcionadas por Dynatrace para configurar el registro de Dynatrace OneAgent. Por ejemplo, `DT_LOGLEVELCON` controla el nivel de registros.

> [!CAUTION]
> Se recomienda encarecidamente que no invalide el comportamiento de registro predeterminado que proporciona Azure Spring Cloud para Dynatrace. Si lo hace, se bloquearán los escenarios de registro señalados anteriormente y es posible que se pierda el archivo de registro. Por ejemplo, no debe pasar la variable de entorno `DT_LOGLEVELFILE` a las aplicaciones.

## <a name="dynatrace-oneagent-upgrade"></a>Actualización de Dynatrace OneAgent

La actualización automática de Dynatrace OneAgent está deshabilitada y se actualizará trimestralmente con el JDK. La actualización del agente puede afectar a los escenarios siguientes:

* Las aplicaciones existentes que usan Dynatrace OneAgent antes de la actualización no se modificarán, pero necesitarán reiniciarse o volver a implementarse para interactuar con la nueva versión de Dynatrace OneAgent.
* Las aplicaciones creadas después de la actualización usarán la nueva versión de Dynatrace OneAgent.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Configuración del tráfico de salida de la instancia de inyección de red virtual

En el caso de una instancia de inyección de red virtual de Azure Spring Cloud, debe asegurarse de que el tráfico de salida de los puntos de conexión de comunicación de Dynatrace esté configurado correctamente para Dynatrace OneAgent. Para más información sobre cómo obtener `communicationEndpoints`, consulte el tema acerca de la [API de implementación: obtención de información de conectividad para OneAgent](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/deployment/oneagent/get-connectivity-info/). Para obtener más información, consulte [Responsabilidades del cliente para ejecutar Azure Spring Cloud en una red virtual](vnet-customer-responsibilities.md).

## <a name="dynatrace-support-model"></a>Modelo de compatibilidad de Dynatrace

Para obtener información sobre las limitaciones al implementar Dynatrace OneAgent en modo de solo aplicación, consulte la sección [Plataformas de aplicaciones en la nube](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix/#cloud-application-platforms) de la [matriz de compatibilidad de funcionalidad y plataforma de OneAgent](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix).

## <a name="next-steps"></a>Pasos siguientes

* [Uso del seguimiento distribuido con Azure Spring Cloud](how-to-distributed-tracing.md)
