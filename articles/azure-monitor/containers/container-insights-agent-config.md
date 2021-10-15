---
title: Configuración de la recopilación de datos del agente de Container Insights | Microsoft Docs
description: En este artículo se describe cómo puede configurar el agente de Container Insights para controlar la recopilación de registros de stdout y stderr, así como de variables de entorno.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: c24b87cb35339cb0e400878579b35d5f6b963718
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388064"
---
# <a name="configure-agent-data-collection-for-container-insights"></a>Configuración de la recopilación de datos del agente de Container Insights

Container Insights recopila stdout, stderr y variables de entorno a partir de cargas de trabajo de contenedor implementadas en clústeres de Kubernetes administrados desde el agente contenedorizado. Puede definir la configuración de recopilación de datos del agente mediante la creación de ConfigMaps de Kubernetes personalizados para controlar esta experiencia. 

En este artículo se muestra cómo crear ConfigMap y configurar la recopilación de datos en función de sus requisitos.

>[!NOTE]
>Para Red Hat OpenShift en Azure V3, se crea un archivo ConfigMap de plantilla en el espacio de nombres *openshift-azure-logging*. 
>

## <a name="configmap-file-settings-overview"></a>Información general de la configuración del archivo ConfigMap

Se proporciona un archivo ConfigMap de plantilla que le permite editarlo fácilmente con sus personalizaciones sin tener que crearlo desde cero. Antes de comenzar, debe revisar la documentación de Kubernetes sobre [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) y familiarizarse con la forma de crear, configurar e implementar ConfigMaps. Esto le permitirá filtrar stderr y stdout por espacio de nombres o a través de todo el clúster, y las variables de entorno para cualquier contenedor que se ejecute en todos los pods o nodos del clúster.

>[!IMPORTANT]
>La versión de agente mínima admitida para recopilar los registros stdout, stderr y las variables de entorno de las cargas de trabajo de contenedor es ciprod06142019 o posterior. Para comprobar la versión del agente, en la pestaña **Nodo** seleccione un nodo y, en el panel de propiedades, anote el valor de la propiedad **Etiqueta de imagen del agente**. Para más información sobre las versiones del agente y lo que se incluye en cada versión, vea las [notas de la versión del agente](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Configuración de la colección de datos

En la tabla siguiente se describen los valores que puede configurar para controlar la recopilación de datos:

| Clave | Tipo de datos | Value | Descripción |
|--|--|--|--|
| `schema-version` | Cadena (distingue mayúsculas de minúsculas) | v1 | Se trata de la versión del esquema que el agente usa<br> al analizar este ConfigMap.<br> La versión de esquema que se admite actualmente es v1.<br> No se admite la modificación de este valor y<br> se rechazará cuando ConfigMap se evalúe. |
| `config-version` | String |  | Permite realizar un seguimiento de la versión de este archivo de configuración en el sistema o repositorio de control de código fuente.<br> El número máximo de caracteres permitido es 10 y todos los demás caracteres se truncan. |
| `[log_collection_settings.stdout] enabled =` | Boolean | true o false | Controla si está habilitada la recopilación de registros de contenedor de stdout. Cuando se establece en `true` y los espacios de nombres no se excluyen de la recopilación de registros de stdout<br> (configuración `log_collection_settings.stdout.exclude_namespaces` más abajo), los registros de stdout se recopilarán a partir de todos los contenedores en todos los pods o nodos del clúster. Si no se especifica en ConfigMaps,<br> el valor predeterminado es `enabled = true`. |
| `[log_collection_settings.stdout] exclude_namespaces =` | String | Matriz separada por comas | Matriz de espacios de nombres de Kubernetes para la que no se recopilarán los registros de stdout. Esta configuración es efectiva únicamente<br> `log_collection_settings.stdout.enabled`<br> si se establece en `true`.<br> Si no se especifica en ConfigMap, el valor predeterminado es<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Boolean | true o false | Controla si está habilitada la recopilación de registros de contenedor de stderr.<br> Cuando se establece en `true` y los espacios de nombres no se excluyen de la recopilación de registros de stdout<br> (configuración `log_collection_settings.stderr.exclude_namespaces`), los registros de stderr se recopilarán a partir de todos los contenedores en todos los pods o nodos del clúster.<br> Si no se especifica en ConfigMaps, el valor predeterminado es<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | String | Matriz separada por comas | Matriz de espacios de nombres de Kubernetes para la que no se recopilarán los registros de stderr.<br> Esta configuración es efectiva únicamente<br> `log_collection_settings.stdout.enabled` se establece en `true`.<br> Si no se especifica en ConfigMap, el valor predeterminado es<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Boolean | true o false | Esta configuración controla la colección de variables de entorno<br> en todos los pods/nodos del clúster<br> y el valor predeterminado es `enabled = true` cuando no se especifica<br> en ConfigMaps.<br> Si la colección de variables de entorno está habilitada globalmente, puede deshabilitarla para un contenedor específico<br> al establecer la variable de entorno<br> `AZMON_COLLECT_ENV` en **False** con un valor de Dockerfile o en el [archivo de configuración para el Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) en la sección **env:** .<br> Si la colección de variables de entorno está deshabilitada globalmente, no puede habilitar la colección para un contenedor específico (es decir, la única invalidación que se puede aplicar en el nivel de contenedor es deshabilitar la colección si ya está habilitada globalmente). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Boolean | true o false | Esta configuración controla el enriquecimiento de los registros de contenedor para rellenar los valores de propiedad de nombre e imagen<br> de cada entrada de registro escrita en la tabla ContainerLog de todos los registros de contenedor del clúster.<br> El valor predeterminado es `enabled = false` cuando no se especifica en ConfigMap. |
| `[log_collection_settings.collect_all_kube_events] enabled =` | Boolean | true o false | Esta configuración permite la recopilación de eventos de Kube de todos los tipos.<br> De forma predeterminada, no se recopilan los eventos de Kube con el tipo *Normal*. Cuando esta opción se establece en `true`, ya no se filtran los eventos con el tipo *Normal* y se recopilan todos los eventos.<br> El valor predeterminado es `enabled = false` cuando no se especifica en ConfigMap. |

### <a name="metric-collection-settings"></a>Configuración de la recopilación de métricas

En la tabla siguiente se describen los valores que puede configurar para controlar la recopilación de métricas:

| Clave | Tipo de datos | Value | Descripción |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boolean | true o false | Esta configuración permite recopilar métricas de uso de volumen persistente en el espacio de nombres kube-system. De forma predeterminada, no se recopilan las métricas de uso de los volúmenes persistentes con notificaciones de volumen persistentes en el espacio de nombres kube-system. Si esta opción se establece en `true`, se recopilarán las métricas de uso de volumen persistente para todos los espacios de nombres. De manera predeterminada, se establece en `false`. |

ConfigMaps es una lista global y solo puede haber una instancia de ConfigMap aplicada al agente. No puede tener otra instancia de ConfigMaps que anule las colecciones.

## <a name="configure-and-deploy-configmaps"></a>Configuración e implementación de ConfigMaps

Realice los pasos siguientes para configurar e implementar el archivo de configuración ConfigMap en el clúster.

1. Descargue el [archivo YAML ConfigMap de plantilla](https://aka.ms/container-azm-ms-agentconfig) y guárdelo como container-azm-ms-agentconfig.yaml. 

   > [!NOTE]
   > Este paso no es necesario cuando se trabaja con Red Hat OpenShift en Azure V3 porque la plantilla ConfigMap ya existe en el clúster.

2. Edite el archivo yaml ConfigMap con sus personalizaciones para recopilar variables de entorno, stderr o stdout. Si va a editar el archivo YAML de ConfigMap para Red Hat OpenShift en Azure V3, ejecute primero el comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir el archivo en un editor de texto.

    - Para excluir espacios de nombres específicos para la recopilación de registros de stdout, configure el par clave/valor en el ejemplo siguiente: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Para deshabilitar la recopilación de variables de entorno para un contenedor específico, establezca el par clave/valor `[log_collection_settings.env_var] enabled = true` para habilitar la recopilación de variables globalmente y luego siga [estos](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) pasos para completar la configuración para el contenedor específico.
    
    - Para deshabilitar la recopilación de registros de stderr en todo el clúster, configure el par clave/valor mediante el siguiente ejemplo: `[log_collection_settings.stderr] enabled = false`.
    
    Guarde los cambios en el editor.

3. Para los clústeres que no son Red Hat OpenShift en Azure V3, cree el archivo ConfigMap mediante la ejecución del siguiente comando de kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`. 
    
    Ejemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

El cambio de configuración puede tardar unos minutos en finalizar antes de que surta efecto, y todos los pods de omsagent del clúster se reiniciarán. El reinicio es un reinicio gradual para todos los pods de omsagent; no todos se reinician al mismo tiempo. Cuando los reinicios finalizan, se muestra un mensaje similar a lo siguiente e incluye el resultado: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Comprobación de la configuración

Para comprobar que la configuración se ha aplicado correctamente en un clúster que no es de Red Hat OpenShift en Azure V3, use el comando siguiente para revisar los registros de un pod de agente: `kubectl logs omsagent-fdf58 -n kube-system`. Si hay errores de configuración de los pods de osmagent, la salida mostrará errores similares a los siguientes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Los errores relacionados con la aplicación de cambios de configuración también están disponibles para su revisión. Las siguientes opciones están disponibles para solucionar problemas adicionales de los cambios de configuración:

- Desde los registros de pod del agente que usan el mismo comando `kubectl logs`. 

    >[!NOTE]
    >Este comando no es aplicable al clúster de Red Hat OpenShift en Azure V3.
    > 

- Desde los registros en directo. Los registros activos muestran errores similares a los siguientes:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Desde la tabla **KubeMonAgentEvents** en el área de trabajo de Log Analytics. Los datos se envían cada hora con la gravedad *Error* para los errores de configuración. Si no hay ningún error, la entrada de la tabla tendrá datos con gravedad *Info*, que no notifica errores. La propiedad **Tags** contiene más información sobre el pod y el identificador de contenedor en el que se produjo el error, así como la primera aparición, la última aparición y la cantidad en la última hora.

- En el caso de Red Hat OpenShift en Azure V3, compruebe los registros de omsagent; para ello, busque en la tabla **ContainerLog** para confirmar que está habilitada la recopilación de registros de openshift-azure-logging.

Después de corregir los errores en ConfigMap en los clústeres que no son de Red Hat OpenShift en Azure V3, guarde el archivo YAML y aplique la versión actualizada de ConfigMaps mediante la ejecución del comando `kubectl apply -f <configmap_yaml_file.yaml`. En Red Hat OpenShift en Azure V3, edite y guarde la versión actualizada de ConfigMaps mediante la ejecución del comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Aplicación de ConfigMap actualizado

Si ya ha implementado ConfigMap en clústeres que no son de Red Hat OpenShift en Azure V3 y quiere actualizarlo con una configuración más reciente, puede editar el archivo ConfigMap que usó antes y después aplicarlo usando el mismo comando, `kubectl apply -f <configmap_yaml_file.yaml`. En Red Hat OpenShift en Azure V3, edite y guarde la versión actualizada de ConfigMaps mediante la ejecución del comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

El cambio de configuración puede tardar unos minutos en finalizar antes de que surta efecto, y todos los pods de omsagent del clúster se reiniciarán. El reinicio es un reinicio gradual para todos los pods de omsagent; no todos se reinician al mismo tiempo. Cuando los reinicios finalizan, se muestra un mensaje similar a lo siguiente e incluye el resultado: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Comprobación de la versión del esquema

Las versiones de esquema de configuración admitidas están disponibles como anotación de pod (versiones de esquema) en el pod de omsagent. Puede verlas con el siguiente comando kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

La salida será similar a la siguiente con las versiones de esquema de anotación:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Pasos siguientes

- Container Insights no incluye un conjunto de alertas predefinido. Revise [Creación de alertas de rendimiento con Container Insights](./container-insights-log-alerts.md) para obtener información sobre cómo crear alertas recomendadas en caso de uso elevado de CPU y memoria a fin de permitir los procesos y procedimientos operativos o de DevOps.

- Con la supervisión habilitada para recopilar el estado y la utilización de recursos del clúster de AKS o híbrido y las cargas de trabajo que se ejecutan en estos, aprenda [cómo usar](container-insights-analyze.md) Container Insights.

- En los [ejemplos de consultas de registro](container-insights-log-query.md) encontrará consultas predefinidas y ejemplos para evaluar o personalizar las alertas, la visualización o el análisis de los clústeres.
