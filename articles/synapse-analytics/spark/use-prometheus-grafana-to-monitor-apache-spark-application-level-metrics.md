---
title: 'Tutorial: Supervisión de las métricas de nivel de aplicación de Apache Spark con Prometheus y Grafana'
description: 'Tutorial: Aprenda a implementar la solución de métricas de aplicación de Apache Spark en un clúster de Azure Kubernetes Service (AKS) y obtenga información sobre cómo integrar los paneles de Grafana.'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: b32ddb38395d95e1c262c05aef878a1beeddc38c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734714"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Tutorial: Supervisión de las métricas de nivel de aplicación de Apache Spark con Prometheus y Grafana

## <a name="overview"></a>Información general

En este tutorial aprenderá a implementar la solución de métricas de aplicación de Apache Spark en un clúster de Azure Kubernetes Service (AKS) y obtendrá información sobre cómo integrar los paneles de Grafana.

Puede usar esta solución para recopilar y consultar datos de métricas de Apache Spark casi en tiempo real. Los paneles de Grafana integrados permiten diagnosticar y supervisar la aplicación Apache Spark. El código fuente y las configuraciones son de código abierto en GitHub.

## <a name="prerequisites"></a>Requisitos previos

1.  [CLI de Azure](/cli/azure/install-azure-cli)
2.  [Cliente de Helm 3.30+](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)

O bien, use [Azure Cloud Shell](https://shell.azure.com/), que ya incluye la CLI de Azure, el cliente Helm y kubectl de serie.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Creación de una instancia de Azure Kubernetes Service (AKS)

Use el comando de la CLI de Azure para crear un clúster de Kubernetes en la suscripción.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Nota: Este paso se puede omitir si ya tiene un clúster de AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Creación de una entidad de servicio y concesión de permisos para el área de trabajo de Synapse

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

El resultado debería tener este aspecto:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Anote el identificador de la aplicación, la contraseña y el identificador de inquilino.

[![Captura de pantalla de la concesión de permisos SRBAC](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Inicie sesión en el [área de trabajo de Azure Synapse Analytics](https://web.azuresynapse.net/) como administrador de Synapse.

2. En Synapse Studio, en el panel de la izquierda, seleccione **Administrar > Control de acceso**.

3. Haga clic en el botón Agregar situado en la parte superior izquierda para **agregar una asignación de roles**.

4. Como ámbito, elija **Área de trabajo**.

5. Seleccione el rol **Operador de procesos de Synapse**.

6. En Seleccionar usuario, escriba el **<nombre_de_la_entidad_de_servicio>** y haga clic en ella.

7. Haga clic en **Aplicar** (espere tres minutos para que se apliquen los permisos).

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Instalación del conector, servidor de Prometheus, panel de Grafana

1. Agregue el repositorio synapse-charts al cliente de Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Instale los componentes mediante el cliente de Helm:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: nombre del área de trabajo de Synapse.
 - subscription_id: identificador de la suscripción del área de trabajo de Synapse.
 - workspace_resource_group_name: nombre del grupo de recursos del área de trabajo de Synapse.
 - tenant_id: identificador de inquilino del área de trabajo de Synapse.
 - service_principal_app_id: valor "appId" de la entidad de servicio.
 - service_principal_password: contraseña de la entidad de servicio que ha creado.

## <a name="log-in-to-grafana"></a>Iniciar sesión en Grafana

Obtenga la contraseña y la dirección predeterminadas de Grafana. Puede cambiar la contraseña en la configuración de Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Obtenga la dirección IP del servicio, copie y pegue la dirección IP externa en el explorador e inicie sesión con el nombre de usuario "admin" y la contraseña.

## <a name="use-grafana-dashboards"></a>Uso de paneles de Grafana

Busque el panel de Synapse en la esquina superior izquierda de la página Grafana (Inicio -> Área de trabajo de Synapse / Aplicación Synapse), intente ejecutar un código de ejemplo en Synapse Studio y espere unos segundos para la extracción de las métricas.

Además, puede usar los paneles "Área de trabajo de Synapse/Área de trabajo" y "Área de trabajo de Synapse/Grupos de Spark" para obtener información general sobre el área de trabajo y los grupos de Apache Spark.

## <a name="uninstall"></a>Desinstalación

Quite los componentes con el comando de Helm como se indica a continuación.

```bash
helm delete <release_name> -n <namespace>
```

Elimine el clúster de AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Introducción a los componentes

Azure Synapse Analytics proporciona un [gráfico de Helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) basado en el operador Prometheus y el conector Prometheus de Synapse. El gráfico de Helm incluye el servidor de Prometheus y Grafana Server, y paneles de Grafana para métricas de nivel de aplicación de Apache Spark. Puede usar [Prometheus](https://prometheus.io/), un conocido sistema de supervisión de código abierto, para recopilar estas métricas casi en tiempo real, y utilizar [Grafana](https://github.com/grafana/grafana) para la visualización.

### <a name="synapse-prometheus-connector"></a>Conector Prometheus de Synapse

El conector Prometheus de Synapse ayuda a conectar el grupo de Apache Spark de Azure Synapse y el servidor de Prometheus. Implementa lo siguiente:

1.  Autenticación: es la autenticación basada en AAD y puede actualizar automáticamente el token de AAD de la entidad de servicio para la detección de aplicaciones, la ingesta de métricas y otras funciones.
2.  Detección de aplicaciones de Apache Spark: cuando se envían aplicaciones en el área de trabajo de destino, el conector Prometheus de Synapse puede detectarlas de forma automática.
3.  Metadatos de aplicación de Apache Spark: recopila información básica de la aplicación y exporta los datos a Prometheus.

El conecto Prometheus de Synapse se publica como una imagen de Docker hospedada en [Microsoft Container Registry](https://github.com/microsoft/containerregistry). Es de código abierto y se encuentra en las [métricas de aplicación de Spark de Azure Synapse](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Servidor Prometheus

Prometheus es un kit de herramientas de alerta y supervisión de código abierto. Prometheus nació de la Cloud Native Computing Foundation (CNCF) y se ha convertido en el estándar de facto para la supervisión nativa en la nube. Puede facilitar las tareas de recopilar, consultar y almacenar grandes cantidades de datos de series temporales, y se puede integrar fácilmente con Grafana. En esta solución, se implementa el componente Prometheus basado en el gráfico de Helm.

### <a name="grafana-and-dashboards"></a>Grafana y los paneles

Grafana es un software de análisis y visualización de código abierto. Permite consultar, visualizar, enviar alertas y explorar las métricas. Azure Synapse Analytics proporciona un conjunto de paneles de Grafana predeterminados para visualizar métricas de nivel de aplicación de Apache Spark.

En el panel "Área de trabajo de Synapse / Área de trabajo" se proporciona una vista de nivel de área de trabajo de todos los grupos de Apache Spark, recuentos de aplicaciones, núcleos de CPU, etc.

[![Captura de pantalla del área de trabajo de paneles](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

El panel "Área de trabajo de Synapse / Grupos de Spark" contiene las métricas de aplicaciones de Apache Spark que se ejecutan en el grupo de Apache Spark seleccionado durante el período de tiempo.

[![Captura de pantalla del panel de grupos de Spark](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

El panel "Área de trabajo de Synapse / Aplicación de Spark" contiene la aplicación de Apache Spark seleccionada.

[![Captura de pantalla del panel de aplicación](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Las plantillas de panel anteriores son de código abierto en las [métricas de aplicación de Spark de Azure Synapse](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).