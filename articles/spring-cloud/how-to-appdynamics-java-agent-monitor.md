---
title: Supervisión de aplicaciones Spring Boot con el agente de Java de AppDynamics (versión preliminar)
titleSuffix: Azure Spring Cloud
description: Aquí se explica cómo usar el agente de Java de AppDynamics para supervisar aplicaciones Spring Boot en Azure Spring Cloud.
author: KarlErickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 8c4a708cd04334f10f44c07d5daf9468f7137acf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722703"
---
# <a name="how-to-monitor-spring-boot-apps-with-the-appdynamics-java-agent-preview"></a>Supervisión de aplicaciones Spring Boot con el agente de Java de AppDynamics (versión preliminar)

En este artículo se explica cómo usar el agente de Java de AppDynamics para supervisar aplicaciones Spring Boot en Azure Spring Cloud.

Con el agente de Java de AppDynamics puede:

- Supervisión de aplicaciones
- Configurar el agente de Java de AppDynamics mediante variables de entorno
- Comprobar todos los datos de supervisión del panel de AppDynamics

En el vídeo siguiente se presenta el agente In-Process de Java de AppDynamics.

<br>

> [!VIDEO https://www.youtube.com/embed/4dZuRX5bNAs]

## <a name="prerequisites"></a>Requisitos previos

* [CLI de Azure](/cli/azure/install-azure-cli)
* [Una cuenta de AppDynamics](https://www.appdynamics.com/)

## <a name="activate-the-appdynamics-java-in-process-agent"></a>Activación del agente In-Process de Java de AppDynamics

Para el flujo de trabajo completo, debe:

* Activar el agente In-Process de Java de AppDynamics en Azure Spring Cloud para generar datos de métricas de aplicación.
* Conectar el agente de AppDynamics al controlador de AppDynamics para recopilar y visualizar los datos del controlador.

![Diagrama que muestra una aplicación Spring Boot en el cuadro "Azure Spring Cloud" con una flecha bidireccional que lo conecta a un cuadro "Agente de AppDynamics", que también tiene una flecha que apunta a un cuadro "Controlador de AppDynamics"](media/how-to-appdynamics-java-agent-monitor/appdynamics-activation.jpg)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-cli"></a>Activación de una aplicación con el agente de AppDynamics mediante la CLI de Azure

Para activar una aplicación mediante la CLI de Azure, siga estos pasos.

1. Cree un grupo de recursos.
1. Cree una instancia de Azure Spring Cloud.
1. Cree una aplicación con el comando siguiente. Reemplace los marcadores de posición *\<...>* por sus propios valores.

    ```azurecli
    az spring-cloud app create \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --is-public true
    ```

1. Cree una implementación con el agente de AppDynamics mediante variables de entorno.

    ```azurecli
    az spring-cloud app deploy \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --jar-path app.jar \
        --jvm-options="-javaagent:/opt/agents/appdynamics/java/javaagent.jar" \
        --env APPDYNAMICS_AGENT_APPLICATION_NAME=<your-app-name> \
              APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY=<your-agent-access-key> \
              APPDYNAMICS_AGENT_ACCOUNT_NAME=<your-agent-account-name> \
              APPDYNAMICS_AGENT_NODE_NAME=<your-agent-node-name> \
              APPDYNAMICS_AGENT_TIER_NAME=<your-agent-tier-name> \
              APPDYNAMICS_CONTROLLER_HOST_NAME=<your-AppDynamics-controller-host-name> \
              APPDYNAMICS_CONTROLLER_SSL_ENABLED=true \
              APPDYNAMICS_CONTROLLER_PORT=443
    ```

Azure Spring Cloud preinstala el agente de Java de AppDynamics en la ruta de acceso */opt/agents/appdynamics/java/javaagent.jar*. Puede activar el agente desde las opciones de JVM de las aplicaciones y luego configurarlo mediante variables de entorno. Puede encontrar valores para estas variables en [Supervisión de Azure Spring Cloud con el agente de Java](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent). Para obtener más información sobre cómo ayudan estas variables a ver y organizar informes en la interfaz de usuario de AppDynamics, vea [Niveles y nodos](https://docs.appdynamics.com/21.9/en/application-monitoring/tiers-and-nodes).

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-portal"></a>Activación de una aplicación con el agente de AppDynamics mediante Azure Portal

Para activar una aplicación mediante Azure Portal, siga estos pasos.

1. Vaya a la instancia de Azure Spring Cloud en Azure Portal.

1. Seleccione **Aplicaciones** en la sección **Configuración** del panel de navegación izquierdo.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png" alt-text="Captura de pantalla de Azure Portal que muestra la sección Aplicaciones" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png":::

1. Seleccione la aplicación para ir a la página **Información general**.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png" alt-text="Captura de pantalla de Azure Portal de la página Información general de la aplicación" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png":::

1. Seleccione **Configuración** en el panel de navegación izquierdo para agregar, actualizar o eliminar las variables de entorno de la aplicación.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png" alt-text="Captura de pantalla de Azure Portal que muestra la sección &quot;Variables de entorno&quot; de la página Configuración de la aplicación" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png":::

1. Seleccione **Configuración general** para agregar, actualizar o eliminar las opciones de JVM de la aplicación.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png" alt-text="Captura de pantalla de Azure Portal que muestra la sección &quot;Configuración general&quot; de la página Configuración de la aplicación, con &quot;Opciones de JVM&quot; resaltado" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png":::

## <a name="automate-provisioning"></a>Aprovisionamiento automatizado

También puede ejecutar una canalización de automatización de aprovisionamiento mediante Terraform o una plantilla de Azure Resource Manager (plantilla de ARM). Esta canalización puede proporcionar una experiencia práctica completa para instrumentar y supervisar las nuevas aplicaciones que cree e implemente.

### <a name="automate-provisioning-using-terraform"></a>Aprovisionamiento automatizado mediante Terraform

Para configurar las variables de entorno en una plantilla de Terraform, agregue el código que se muestra a continuación a la plantilla y reemplace los marcadores de posición *\<...>* por sus propios valores. Para obtener más información, consulte el tema sobre la [administración de una implementación activa de Azure Spring Cloud](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/appdynamics/java/javaagent.jar"
  ...
    environment_variables = {
      "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
      "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
      "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
      "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
      "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
      "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
      "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
      "APPDYNAMICS_CONTROLLER_PORT" : "443"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>Aprovisionamiento automatizado mediante una plantilla de ARM

Para configurar las variables de entorno en una plantilla de ARM, agregue el código a la plantilla que se muestra a continuación y reemplace los marcadores de posición *\<...>* por sus propios valores. Para obtener más información, consulte [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
    "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
    "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
    "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
    "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
    "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
    "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
    "APPDYNAMICS_CONTROLLER_PORT" : "443"
  },
  "jvmOptions": "-javaagent:/opt/agents/appdynamics/java/javaagent.jar",
  ...
}
```

## <a name="review-reports-in-the-appdynamics-dashboard"></a>Revisión de informes en el panel de AppDynamics

En esta sección se muestran varios informes en AppDynamics.

En la captura de pantalla siguiente se muestra información general de las aplicaciones en el panel de AppDynamics:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg" alt-text="Captura de pantalla de AppDynamics que muestra la página Aplicaciones" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg":::

El **panel Aplicación** muestra la información general de cada una de las aplicaciones, como se muestra en las capturas de pantalla siguientes mediante aplicaciones de ejemplo:

- `api-gateway`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg" alt-text="Captura de pantalla de AppDynamics que muestra el panel Aplicación de la aplicación de ejemplo api-gateway" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg":::

- `customers-service`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg" alt-text="Captura de pantalla de AppDynamics que muestra el panel Aplicación de la aplicación de ejemplo customers-service" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg":::

En la captura de pantalla siguiente se muestra cómo obtener información básica del panel **Llamadas a base de datos**.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg" alt-text="Captura de pantalla de AppDynamics que muestra el panel Llamadas a base de datos" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg":::

También se puede obtener información sobre las llamadas a base de datos más lentas, como se muestra en estas capturas de pantalla:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg" alt-text="Captura de pantalla de AppDynamics que muestra la página Llamadas a base de datos más lentas" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg":::

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg" alt-text="Captura de pantalla de AppDynamics que muestra la página Instantáneas correlacionadas a la que se ha accedido desde la página Llamadas a base de datos más lentas" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg":::

En la captura de pantalla siguiente se muestra el análisis de uso de memoria en la sección **Montón** de la página **Memoria**:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg" alt-text="Captura de pantalla de AppDynamics que muestra la sección Montón de la página Memoria" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg":::

También puede ver el proceso de recolección de elementos no utilizados, como se muestra en esta captura de pantalla:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg" alt-text="Captura de pantalla de AppDynamics que muestra la sección Recolección de elementos no utilizados de la página Memoria" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg":::

En la captura de pantalla siguiente se muestra la página **Transacciones lentas**: 

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg" alt-text="Captura de pantalla de AppDynamics que muestra la página Transacciones lentas" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg":::

Puede definir más métricas para JVM, como se muestra en esta captura de pantalla del **explorador de métricas**:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg" alt-text="Captura de pantalla de AppDynamics que muestra el explorador de métricas" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg":::

## <a name="view-appdynamics-agent-logs"></a>Visualización de registros del agente de AppDynamics

De manera predeterminada, Azure Spring Cloud imprime los registros de nivel de *información* del agente de AppDynamics en `STDOUT`. Los registros se mezclarán con los registros de aplicaciones. Puede encontrar la versión explícita del agente en los registros de aplicaciones.

También puede obtener los registros del agente de AppDynamics desde las siguientes ubicaciones:

* Registros de Azure Spring Cloud
* Azure Spring Cloud Application Insights
* LogStream de Azure Spring Cloud

## <a name="learn-about-appdynamics-agent-upgrade"></a>Más información sobre la actualización del agente de AppDynamics

El agente de AppDynamics se actualiza periódicamente con JDK (trimestralmente). La actualización del agente puede afectar a los escenarios siguientes:

* Las aplicaciones existentes que usaban el agente de AppDynamics antes de la actualización no se modifican, pero necesitan reiniciarse o volver a implementarse para interactuar con la nueva versión del agente de AppDynamics.
* Las aplicaciones creadas después de la actualización usan la nueva versión del agente de AppDynamics.

## <a name="configure-vnet-injection-instance-outbound-traffic"></a>Configuración del tráfico de salida de la instancia de inyección de red virtual

En el caso de las instancias de inyección de red virtual de Azure Spring Cloud, asegúrese de que el tráfico de salida esté configurado correctamente para el agente de AppDynamics. Para obtener detalles, vea [Dominios de SaaS e intervalos IP](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges) y [Responsabilidades del cliente para ejecutar Azure Spring Cloud en una red virtual](vnet-customer-responsibilities.md).

## <a name="understand-the-limitations"></a>Descripción de las limitaciones

Para comprender las limitaciones del agente de AppDynamics, vea [Supervisión de Azure Spring Cloud con el agente de Java](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent).

## <a name="next-steps"></a>Pasos siguientes

* [Use el agente In-Process de Java de Application Insights en Azure Spring Cloud](./how-to-application-insights.md)