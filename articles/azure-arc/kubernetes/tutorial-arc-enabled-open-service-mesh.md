---
title: Open Service Mesh habilitado para Azure Arc (versión preliminar)
description: Extensión Open Service Mesh (OSM) en un clúster de Kubernetes habilitado para Arc
services: azure-arc
ms.service: azure-arc
ms.date: 07/23/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: c8a10873f420b5aba75596a4377bfa4f0b37d4f7
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606898"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>Open Service Mesh habilitado para Azure Arc (versión preliminar)

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) es una malla de servicio nativa ligera, extensible y en la nube que permite a los usuarios administrar de forma uniforme características de observación para entornos de microservicios muy dinámicos, así como protegerlas y disfrutar de ellas de inmediato.

OSM ejecuta un plano de control basado en Envoy en Kubernetes, se puede configurar con API de [SMI](https://smi-spec.io/) y funciona insertando un proxy de Envoy como un contenedor sidecar junto a cada instancia de la aplicación. [Obtenga más información](https://docs.openservicemesh.io/#features) sobre los escenarios de mallas de servicio que habilita Open Service Mesh.

### <a name="support-limitations-for-arc-enabled-open-service-mesh"></a>Limitaciones de compatibilidad para Open Service Mesh habilitado para Arc

- Solo se puede implementar una instancia de Open Service Mesh en un clúster de Kubernetes conectado a Arc.
- La versión preliminar pública está disponible para Open Service Mesh versión 0.8.4 y posteriores. Consulte la versión más reciente [aquí](https://github.com/Azure/osm-azure/releases). Las versiones admitidas se anexan con notas. No tenga en cuenta las etiquetas asociadas a las versiones intermedias. 
- Actualmente se admiten las siguientes distribuciones de Kubernetes
    - Motor de AKS
    - AKS en HCI
    - Cluster API de Azure
    - Google Kubernetes Engine
    - Distribución Canonical Kubernetes
    - Rancher Kubernetes Engine
    - Distribución OpenShift Kubernetes
    - Amazon Elastic Kubernetes Service
- La integración de Azure Monitor con Open Service Mesh habilitado para Azure Arc está disponible con [compatibilidad limitada](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md).


[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Requisitos previos

- Asegúrese de cumplir con todos los requisitos previos comunes para las extensiones de clúster [que se enumeran aquí](extensions.md#prerequisites).
- Use la CLI de az k8s-extension versión 0.4.0 o posteriores.

## <a name="install-arc-enabled-open-service-mesh-osm-on-an-arc-enabled-kubernetes-cluster"></a>Instalación de Open Service Mesh (OSM) habilitado para Arc en un clúster de Kubernetes habilitado para Arc

En los pasos siguientes se da por sentado que ya posee un clúster con una distribución de Kubernetes compatible conectada a Azure Arc.

### <a name="install-a-specific-version-of-osm"></a>Instalación de una versión específica de OSM

Asegúrese de que la variable de entorno KUBECONFIG apunta al archivo kubeconfig del clúster de Kubernetes en el que quiere instalar la extensión de OSM.

Establezca las variables de entorno:

```azurecli-interactive
export VERSION=0.8.4
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```

Aunque Open Service Mesh habilitado para Arc se encuentra en versión preliminar, el comando `az k8s-extension create` solo acepta `pilot` para la marca `--release-train`. El valor de `--auto-upgrade-minor-version` siempre está establecido en `false` y se debe especificar una versión. Si tiene un clúster de OpenShift, siga los pasos de la [sección correspondiente](#install-a-specific-version-of-osm-on-openshift-cluster).

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION
```

Debería obtener una salida similar a la que se muestra a continuación. El gráfico de Helm real de la instancia de OSM puede tardar entre 3 y 5 minutos en implementarse en el clúster. Hasta que se realice dicha implementación, installState seguirá mostrándose como Pendiente.

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T17:50:11.4116524+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-29T17:50:11.4116525+00:00",
  "lastStatusTime": null,
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```

### <a name="install-a-specific-version-of-osm-on-openshift-cluster"></a>Instalación de una versión específica de OSM en un clúster de OpenShift

1. Copie y guarde el siguiente contenido en un archivo JSON. Si ya ha creado un archivo de configuración, agregue la siguiente línea al archivo existente para conservar los cambios anteriores.
   ```json
   {
       "osm.OpenServiceMesh.enablePrivilegedInitContainer": "true"
   }
   ```

   Configure la ruta de acceso del archivo como una variable de entorno:
   ```azurecli-interactive
   export SETTINGS_FILE=<json-file-path>
   ```
   
2. Ejecute el comando `az k8s-extension create` que se usa para crear la extensión OSM y pase el archivo de configuración mediante los valores de configuración:
   ```azurecli-interactive
   az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
   ```
   
3. Agregue la [restricción de contexto de seguridad](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html) con privilegios a cada cuenta de servicio correspondiente a las aplicaciones de la malla.
   ```azurecli-interactive
   oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
   ```

El gráfico de Helm real de la instancia de OSM puede tardar entre 3 y 5 minutos en implementarse en el clúster. Hasta que se realice dicha implementación, installState seguirá mostrándose como Pendiente.

Para asegurarse de que la configuración del contenedor de inicialización con privilegios no se revierta al valor predeterminado, pase el valor de configuración "osm.OpenServiceMesh.enablePrivilegedInitContainer" : "true" en todos los comandos az k8s-extension create posteriores.

### <a name="install-arc-enabled-osm-using-arm-template"></a>Instalación de OSM habilitado para Arc mediante una plantilla de ARM

Después de conectar el clúster a Azure Arc, cree un archivo JSON con el formato siguiente y asegúrese de actualizar el valor <cluster-name>:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "osm",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "0.8.4",
            "type": "String",
            "metadata": {
                "description": "The extension type version."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.openservicemesh",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "Pilot",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2020-07-01-preview",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```

Ahora, configure las variables de entorno:

```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

Por último, ejecute este comando para instalar la extensión OSM mediante la CLI de az:

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```

Ahora, debería poder ver los recursos de OSM y usar la extensión OSM en el clúster.

## <a name="validate-the-arc-enabled-open-service-mesh-installation"></a>Validación de la instalación de Open Service Mesh habilitado para Arc

Ejecute el siguiente comando:

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm
```

Debería mostrarse una salida JSON similar a la siguiente:

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T19:22:00.7649729+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-29T19:22:00.7649731+00:00",
  "lastStatusTime": "2021-04-29T19:23:27.642+00:00",
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```
## <a name="osm-controller-configuration"></a>Configuración del controlador de OSM
OSM implementa un recurso de MeshConfig `osm-mesh-config` como parte de su plano de control en el espacio de nombres arc-osm-system. El propósito de este recurso de MeshConfig es proporcionar al propietario u operador de la malla la capacidad de actualizar algunas de las configuraciones de la malla en función de sus necesidades. Para ver los valores predeterminados, use el comando siguiente.

```azurecli-interactive
kubectl describe meshconfig osm-mesh-config -n arc-osm-system
```
En el resultado se mostrarán los valores predeterminados:

```azurecli-interactive
Certificate:
    Service Cert Validity Duration:  24h
  Feature Flags:
    Enable Egress Policy:      true
    Enable Multicluster Mode:  false
    Enable WASM Stats:         true
  Observability:
    Enable Debug Server:  false
    Osm Log Level:        info
    Tracing:
      Address:   jaeger.osm-system.svc.cluster.local
      Enable:    false
      Endpoint:  /api/v2/spans
      Port:      9411
  Sidecar:
    Config Resync Interval:            0s
    Enable Privileged Init Container:  false
    Envoy Image:                       mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    Init Container Image:              mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    Log Level:                         error
    Max Data Plane Connections:        0
    Resources:
  Traffic:
    Enable Egress:                          false
    Enable Permissive Traffic Policy Mode:  true
    Inbound External Authorization:
      Enable:              false
      Failure Mode Allow:  false
      Stat Prefix:         inboundExtAuthz
      Timeout:             1s
    Use HTTPS Ingress:     false
```
Consulte la [referencia de la API de configuración](https://docs.openservicemesh.io/docs/api_reference/config/v1alpha1/) para obtener más información. Fíjese en que **spec.traffic.enablePermissiveTrafficPolicyMode** está establecido en **true**. El modo de la directiva de tráfico permisivo en OSM es un modo en el que se omite la aplicación de directivas de tráfico de [SMI](https://smi-spec.io/). En este modo, OSM detecta automáticamente los servicios que forman parte de la malla de servicio y programa reglas de la directiva de tráfico en cada sidecar del proxy de Envoy para comunicarse con estos servicios.

### <a name="making-changes-to-osm-controller-configuration"></a>Aplicación de cambios en la configuración del controlador de OSM

> [!NOTE]
> Los valores del recurso de MeshConfig `osm-mesh-config` son persistentes en todas las actualizaciones.

Los cambios en `osm-mesh-config` pueden realizarse con el comando de revisión kubectl. En el ejemplo siguiente, el modo de directiva de tráfico permisiva se cambia a false.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

Si se usa un valor incorrecto, las validaciones del CRD de MeshConfig impedirán el cambio con un mensaje de error en el que se explicará por qué el valor no es válido. Por ejemplo, el comando siguiente muestra lo que sucede si se revisa enableEgress con un valor no booleano.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge

# Validations on the CRD will deny this change
The MeshConfig "osm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```

## <a name="osm-controller-configuration-version-v084"></a>Configuración del controlador de OSM (versión v0.8.4)

Actualmente, puede acceder a la configuración del controlador de OSM y ajustarla a través de ConfigMap. Para ver las opciones de configuración del controlador de OSM, consulte el archivo `osm-config` ConfigMap mediante `kubectl` para ver sus opciones de configuración.

```azurecli-interactive
kubectl get configmap osm-config -n arc-osm-system -o json
```

Salida:

```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false"
}
```

Consulte la [documentación de ConfigMap de OSM](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/) para comprender cada una de las configuraciones disponibles. 

Para realizar cambios en el objeto ConfigMap del OSM para la versión v0.8.4, siga las instrucciones siguientes:

1. Copie y guarde los cambios que quiere realizar en un archivo JSON. En este ejemplo, vamos a cambiar el valor de permissive_traffic_policy_mode de true a false. Cada vez que realice un cambio en `osm-config`, tendrá que proporcionar la lista completa de cambios (en comparación con el valor `osm-config` predeterminado) en un archivo JSON.
    ```json
    {
        "osm.OpenServiceMesh.enablePermissiveTrafficPolicy" : "false"
    }
    ```
    
    Configure la ruta de acceso del archivo como una variable de entorno:
    
    ```azurecli-interactive
    export SETTINGS_FILE=<json-file-path>
    ```
    
2. Ejecute el mismo comando `az k8s-extension create` que se usó para crear la extensión, pero pase ahora el archivo de configuración: 
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```
    
    > [!NOTE]
    > Para asegurarse de que los cambios de ConfigMap no se reviertan al valor predeterminado, pase los mismos valores de configuración a todos los comandos az k8s-extension create posteriores.

## <a name="using-the-arc-enabled-open-service-mesh"></a>Uso de Open Service Mesh habilitado para Arc

Para empezar a usar las funcionalidades de OSM, primero debe incorporar los espacios de nombres de la aplicación a la malla de servicio. Descargue la CLI de OSM desde la [página de versiones de OSM en GitHub](https://github.com/openservicemesh/osm/releases/). Una vez que los espacios de nombres se hayan agregado a la malla, podrá configurar las directivas de SMI para lograr la funcionalidad de OSM deseada.

### <a name="onboard-namespaces-to-the-service-mesh"></a>Incorporación de espacios de nombres a la malla de servicio

Agregue los espacios de nombres a la malla mediante la ejecución del comando siguiente:

```azurecli-interactive
osm namespace add <namespace_name>
```

Para obtener más información acerca de la incorporación de servicios, consulte [este vínculo](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services).

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>Configuración de OSM con directivas de Service Mesh Interface (SMI)

Puede empezar con una [aplicación demo](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/#deploy-applications) o usar el entorno de pruebas para probar las directivas de SMI.

> [!NOTE] 
> Asegúrese de que la versión de la aplicación de librería que está ejecutando coincide con la versión de la extensión de OSM instalada en el clúster. Por ejemplo, si usa la versión 0.8.4 de la extensión de OSM, use la demo de librería de la rama release-v0.8 del repositorio de nivel superior de OSM.

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>Configuración de sus propias instancias de Jaeger, Prometheus y Grafana

La extensión del OSM no instala complementos como [Jaeger](https://www.jaegertracing.io/docs/getting-started/), [Prometheus](https://prometheus.io/docs/prometheus/latest/installation/) o [Grafana](https://grafana.com/docs/grafana/latest/installation/) para que, en su lugar, los usuarios puedan integrar el OSM con sus propias instancias en ejecución de esas herramientas. Para realizar la integración con sus propias instancias, consulte la siguiente documentación:

> [!NOTE]
> Use los comandos proporcionados en la documentación de GitHub sobre el OSM con precaución. Asegúrese de usar el nombre del espacio de nombres correcto ("arc-osm-system") al realizar cambios en `osm-mesh-config`.

- [Instancia de BYO-Jaeger](https://docs.openservicemesh.io/docs/guides/observability/tracing/#byo-bring-your-own)
- [Instancia de BYO-Prometheus](https://docs.openservicemesh.io/docs/guides/observability/metrics/#byo-prometheus)
- [Panel de BYO-Grafana](https://docs.openservicemesh.io/docs/guides/observability/metrics/#importing-dashboards-on-a-byo-grafana-instance)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>Supervisión de aplicaciones mediante Azure Monitor y Applications Insights

Azure Monitor y Azure Application Insights ayudan a maximizar la disponibilidad y el rendimiento de las aplicaciones y los servicios con una completa solución que permite recopilar datos de telemetría tanto en la nube como en entornos locales, así como analizarlos y actuar sobre ellos.

Open Service Mesh habilitado para Arc tendrá integraciones profundas en ambos servicios de Azure y proporcionará una experiencia de Azure aparentemente ininterrumpida para ver los KPI críticos proporcionados por las métricas de OSM y para responder a ellos. Siga los pasos que se indican a continuación para permitir que Azure Monitor extraiga puntos de conexión de Prometheus para recopilar métricas de la aplicación. 

1. Asegúrese de que los espacios de nombres de la aplicación que quiere supervisar están incorporados en la malla. Siga las instrucciones [disponibles en este vínculo](#onboard-namespaces-to-the-service-mesh).

2. Exponga los puntos de conexión de Prometheus para los espacios de nombres de la aplicación.
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```
    En el caso de la versión v0.8.4, asegúrese de que `prometheus_scraping` está establecido en `true` en el elemento ConfigMap `osm-config`.

3. Instale la extensión de Azure Monitor con las instrucciones disponibles [aquí](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json).

4. Agregue los espacios de nombres que quiere supervisar en container-azm-ms-osmconfig ConfigMap. Descargue ConfigMap desde [aquí](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml).
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

5. Ejecute el siguiente comando de kubectl
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

Las métricas pueden tardar hasta 15 minutos en aparecer en Log Analytics. Puede tratar de consultar la tabla InsightsMetrics.

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
Obtenga más información sobre la integración con Azure Monitor [aquí](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md).

### <a name="navigating-the-osm-dashboard"></a>Navegación por el panel de OSM

1. Acceda al clúster de Kubernetes conectado a Arc a través de este [vínculo](https://aka.ms/azmon/osmarcux).
2. Vaya a Azure Monitor y diríjase a la pestaña Informes para acceder al libro de OSM.
3. Seleccione el intervalo de tiempo y el espacio de nombres para el ámbito de los servicios.

![Libro de OSM](./media/tutorial-arc-enabled-open-service-mesh/osm-workbook.jpg)

#### <a name="requests-tab"></a>Pestaña Solicitudes

- Esta pestaña proporciona el resumen de todas las solicitudes HTTP enviadas mediante conexiones de servicio a servicio en OSM.
- Puede ver todos los servicios, así como todos los servicios con los que se están comunicando, si selecciona algún servicio en la cuadrícula.
- Puede ver el total de solicitudes, la tasa de errores de solicitud y la latencia P90.
- Puede explorar en profundidad el destino y ver las tendencias de códigos de error o éxito HTTP, la tasa de éxito, el uso de recursos de pod y las latencias en distintos percentiles.

#### <a name="connections-tab"></a>Pestaña Connections (Conexiones)

- Esta pestaña proporciona un resumen de todas las conexiones entre los servicios de Open Service Mesh.
- Conexiones salientes: número total de conexiones entre los servicios de origen y de destino.
- Conexiones salientes activas: último recuento de conexiones activas entre el origen y el destino durante el intervalo de tiempo seleccionado.
- Conexiones salientes con errores: número total de conexiones con errores entre los servicios de origen y de destino.

## <a name="upgrade-the-osm-extension-instance-to-a-specific-version"></a>Actualización de la instancia de extensión OSM a una versión específica

Es posible que durante las actualizaciones el plano de control esté inactivo durante algunos momentos. El plano de datos solo se verá afectado durante las actualizaciones de CRD.

### <a name="supported-upgrades"></a>Actualizaciones admitidas

La extensión OSM se puede actualizar a la siguiente versión secundaria. En este momento no se admiten las actualizaciones a versiones principales ni los cambios a versiones anteriores.

### <a name="crd-upgrades"></a>Actualizaciones de CRD

La extensión OSM no se puede actualizar a una nueva versión si dicha versión contiene actualizaciones de la versión de CRD, a menos que se eliminen primero los CRD existentes. Para comprobar si una actualización de OSM también incluye actualizaciones de versión de CRD, consulte la sección sobre actualizaciones de CRD en las [notas de la versión de OSM](https://github.com/openservicemesh/osm/releases).

Asegúrese de hacer una copia de seguridad de los recursos personalizados antes de eliminar los CRD para que puedan volver a crearse fácilmente después de la actualización. Después, siga las instrucciones de actualización capturadas a continuación.

> [!NOTE] 
> La actualización de los CRD afectará al plano de datos, ya que las directivas de SMI dejarán de existir entre el momento en que se eliminen los CRD y el momento en que se creen de nuevo.

### <a name="upgrade-instructions"></a>Instrucciones de actualización

1. Elimine los CRD antiguos y los recursos personalizados (realice la ejecución desde la raíz del [repositorio de OSM](https://github.com/openservicemesh/osm)). Asegúrese de que la etiqueta de los [CDR del OSM](https://github.com/openservicemesh/osm/tree/main/charts/osm/crds) corresponda a la nueva versión del gráfico.
    ```azurecli-interactive
    kubectl delete --ignore-not-found --recursive -f ./charts/osm/crds/

2. Install the updated CRDs.
    ```azurecli-interactive
    kubectl apply -f charts/osm/crds/
    ```

3. Configure la nueva versión del gráfico como una variable de entorno:
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
4. Ejecute az k8s-extension create con la nueva versión del gráfico
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

5. Creación de los recursos personalizados de nuevo mediante nuevos CRD

## <a name="uninstall-arc-enabled-open-service-mesh"></a>Desinstalación de Open Service Mesh habilitado para Arc

Use el comando siguiente:

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm -y
```

Compruebe que se ha eliminado la instancia de la extensión:

```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```

Esta salida no debe incluir a OSM. Si no tiene ninguna otra extensión instalada en el clúster, solo se mostrará una matriz vacía.

Cuando se usa el comando az k8s-extension para eliminar la extensión de OSM, el espacio de nombres arc-osm-system no se quita y los recursos reales dentro de dicho espacio de nombres (como la configuración de webhook de mutación y el pod osm-controller) tardarán aproximadamente 10 minutos en eliminarse.

> [!NOTE] 
> Use la CLI de az k8s-extension para desinstalar los componentes de OSM administrados por Arc. Arc no admite el uso de la CLI de OSM para las desinstalaciones y puede producirse un comportamiento no deseado.

## <a name="troubleshooting"></a>Solución de problemas

Consulte la guía de solución de problemas [disponible en este vínculo](troubleshooting.md#arc-enabled-open-service-mesh).

## <a name="next-steps"></a>Pasos siguientes

> **¿Solo desea realizar algunas pruebas?**  
> Comience a trabajar rápidamente con un escenario de [Azure Arc Jumpstart](https://aka.ms/arc-jumpstart-osm) mediante Cluster API.
