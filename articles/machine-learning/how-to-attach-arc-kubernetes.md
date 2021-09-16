---
title: Aprendizaje automático habilitado para Azure Arc (versión preliminar)
description: Configuración de un clúster de Kubernetes habilitado para Azure Arc para entrenar modelos de aprendizaje automático en Azure Machine Learning
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.openlocfilehash: 81d4d17b4efdc1dd7d59cb566f8a9ef2e6742aac
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966772"
---
# <a name="configure-azure-arc-enabled-machine-learning-preview"></a>Configuración de aprendizaje automático habilitado para Azure Arc (versión preliminar)

Aprenda a configurar el aprendizaje automático habilitado para Azure Arc para el entrenamiento.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>¿Qué es el aprendizaje automático habilitado para Azure Arc?

Azure Arc permite ejecutar servicios de Azure en cualquier entorno de Kubernetes, ya sea local, en varias nubes o en el perímetro.

El aprendizaje automático habilitado para Azure Arc le permite configurar y usar clústeres de Kubernetes habilitados para Azure Arc para entrenar y administrar modelos de aprendizaje automático en Azure Machine Learning.

El aprendizaje automático habilitado para Azure Arc admite los siguientes escenarios de entrenamiento:

* Entrenamiento de modelos con la CLI (versión 2)
  * Entrenamiento distribuido
  * Barrido de hiperparámetros
* Entrenamiento de modelos con el SDK de Azure Machine Learning para Python
  * Ajuste de hiperparámetros
* Compilación y uso de canalizaciones de aprendizaje automático
* Entrenamiento del modelo en el entorno local con un servidor proxy de salida
* Entrenamiento del modelo en el entorno local con el almacén de datos NFS

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free) antes de empezar.
* Clúster de Kubernetes habilitado para Azure Arc. Para más información, consulte [Inicio rápido: conexión de un clúster de Kubernetes existente a Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md).
* Cumpla los [requisitos previos de las extensiones de clústeres de Kubernetes habilitados para Azure Arc](../azure-arc/kubernetes/extensions.md#prerequisites).
  * CLI de Azure versión 2.24.0 o posterior
  * Extensión k8s-extension de la CLI de Azure 0.4.3 o posterior
* Un área de trabajo de Azure Machine Learning. [Cree un área de trabajo](how-to-manage-workspace.md?tabs=python) antes de empezar si no la tiene todavía.
  * SDK de Azure Machine Learning para Python 1.30 o posterior

## <a name="deploy-azure-machine-learning-extension"></a>Implementación de la extensión de Azure Machine Learning

Kubernetes habilitado para Azure Arc tiene una funcionalidad de extensión de clúster que le permite instalar varios agentes, como definiciones de directiva, supervisión o aprendizaje automático de Azure, entre muchos otros. Azure Machine Learning requiere el uso de la extensión de clúster *Microsoft.AzureML.Kubernetes* para implementar el agente de Azure Machine Learning en el clúster de Kubernetes. Cuando ya está instalada la extensión de Azure Machine Learning, puede asociarse el clúster a un área de trabajo de Azure Machine Learning y usarlo para el entrenamiento.

Use la extensión `k8s-extension` de la CLI de Azure para implementar la extensión de Azure Machine Learning en el clúster de Kubernetes habilitado para Azure Arc.

1. Inicio de sesión en Azure
    
    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

1. Implementación de la extensión de Azure Machine Learning

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --configuration-settings enableTraining=True  --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
    ```

    >[!IMPORTANT]
    > Para habilitar el clúster habilitado para Azure Arc para el entrenamiento, `enableTraining` se debe establecer en **True**. Al ejecutar este comando, se crea un recurso Azure Service Bus y Azure Relay en el mismo grupo de recursos que el clúster de Arc. Estos recursos se usan para comunicarse con el clúster. Si se modifican, se interrumpirán los clústeres asociados que se usan como destinos del proceso de entrenamiento.

    También puede configurar las siguientes opciones al implementar la extensión de Azure Machine Learning para el entrenamiento del modelo:

    |Nombre de clave de opción de configuración  |Descripción  |
    |--|--|
    | ```enableTraining``` | El valor predeterminado es `False`. Establezca esta opción en `True` para crear una instancia de extensión para entrenar modelos de aprendizaje automático.  |
    |```logAnalyticsWS```  | El valor predeterminado es `False`. La extensión de Azure Machine Learning se integra con el área de trabajo de Azure LogAnalytics. Establezca esta opción en `True` para proporcionar la funcionalidad de visualización y análisis de registros mediante el área de trabajo de LogAnalytics. Se pueden aplicar costos por el área de trabajo de LogAnalytics.   |
    |```installNvidiaDevicePlugin```  | El valor predeterminado es `True`. Nvidia Device Plugin es necesario para el entrenamiento en hardware de GPU de Nvidia. La extensión de Azure Machine Learning instala Nvidia Device Plugin de forma predeterminada durante la creación de la instancia de Azure Machine Learning, con independencia de si el clúster de Kubernetes tiene hardware de GPU o no. Establezca esta opción en `False` si no tiene previsto usar una GPU para el entrenamiento o si Nvidia Device Plugin ya está instalado.  |
    |```installBlobfuseSysctl```  | El valor predeterminado es `True` si "enableTraining=True". Blobfuse 1.3.7 es necesario para el entrenamiento. Azure Machine Learning instala Blobfuse de forma predeterminada cuando se crea la instancia de la extensión. Establezca esta opción en `False` si Blobfuse 1.37 ya está instalado en el clúster de Kubernetes.   |
    |```installBlobfuseFlexvol```  | El valor predeterminado es `True` si "enableTraining=True". Blobfuse Flexvolume es necesario para el entrenamiento. Azure Machine Learning instala Blobfuse Flexvolume de forma predeterminada en la ruta de acceso predeterminada. Establezca esta opción en `False` si Blobfuse Flexvolume ya está instalado en el clúster de Kubernetes.   |
    |```volumePluginDir```  | Ruta de acceso del host para el complemento Blobfuse Flexvolume que se va a instalar. Solo se aplica si "enableTraining=True". De forma predeterminada, Azure Machine Learning instala Blobfuse Flexvolume en la ruta de acceso predeterminada */etc/kubernetes/volumeplugins*. Con esta opción de configuración puede especificar una ubicación de instalación personalizada.   |

    > [!WARNING]
    > Si Nvidia Device Plugin, Blobfuse y Blobfuse Flexvolume ya están instalados en el clúster, volver a instalarlos puede producir un error de instalación de la extensión. Establezca `installNvidiaDevicePlugin`, `installBlobfuseSysctl` y `installBlobfuseFlexvol` en `False` para evitar estos errores.

1. Comprobación de la implementación de la extensión AzureML

    ```azurecli
    az k8s-extension show --name amlarc-compute --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
    ```

    En la respuesta, busque `"extensionType": "amlarc-compute"` y `"installState": "Installed"`. Tenga en cuenta que puede aparecer `"installState": "Pending"` los primeros minutos.

    Si `installState` aparece como **Installed** (Instalado), ejecute el siguiente comando en la máquina con el archivo kubeconfig que apunta al clúster para comprobar que todos los pods del espacio de nombres *azureml* se encuentra en estado *Running* (En ejecución):

   ```bash
    kubectl get pods -n azureml
   ```

## <a name="attach-arc-cluster-studio"></a>Asociación de un clúster de Arc (Studio)

La asociación de un clúster de Kubernetes habilitado para Azure Arc hace que esté disponible en el área de trabajo para el entrenamiento.

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
1. En **Administrar**, seleccione **Proceso**.
1. Seleccione la pestaña **Attached computes** (Procesos asociados).
1. Seleccione **+New > Kubernetes (preview)** (Nuevo > Kubernetes [versión preliminar]).

   ![Asociación del clúster de Kubernetes](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. Escriba un nombre de proceso y seleccione el clúster de Kubernetes habilitado para Azure Arc en la lista desplegable.

   ![Configuración del clúster de Kubernetes](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster.png)

1. (Opcional) En escenarios avanzados, examine y cargue un archivo de configuración.

   ![Carga de un archivo de configuración](./media/how-to-attach-arc-kubernetes/upload-configuration-file.png)

1. Seleccione **Attach** (Asociar).

    En la pestaña Attached computes (Procesos asociados), el estado inicial del clúster es *Creating* (Creación en curso). Cuando el clúster se ha asociado correctamente, el estado cambia a *Succeeded* (Realizado con éxito). De lo contrario, el estado cambia a *Failed* (Error).

    ![Aprovisionamiento de recursos](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="advanced-attach-scenario"></a>Escenario de asociación avanzada

Use un archivo de configuración JSON para configurar funcionalidades avanzadas de destino de proceso en clústeres de Kubernetes habilitados para Azure Arc.

A continuación se muestra un archivo de configuración de ejemplo:

```json
{
   "namespace": "amlarc-testing",
   "defaultInstanceType": "gpu_instance",
   "instanceTypes": {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
         }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         },
         "resources": {
            "requests": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            },
            "limits": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            }
         }
      }
   }
}
```

Las siguientes propiedades personalizadas del destino de proceso se pueden configurar mediante un archivo de configuración:

* `namespace`: el valor predeterminado del espacio de nombres es `default`. Este es el espacio de nombres en el que se ejecutan los trabajos y pods. Tenga en cuenta que si establece un espacio de nombres distinto del predeterminado, debe existir ya. La creación de espacios de nombres requiere privilegios de administrador de clústeres.

* `defaultInstanceType`: el tipo de instancia en la que se ejecutan los trabajos de entrenamiento de forma predeterminada. `defaultInstanceType` es obligatorio si se especifica la propiedad `instanceTypes`. El valor de `defaultInstanceType` debe ser uno de los definidos en la propiedad `instanceTypes`.

    > [!IMPORTANT]
    > Actualmente, solo se admiten envíos de trabajos con el nombre del destino de proceso. Por lo tanto, la configuración siempre tendrá como valor predeterminado defaultInstanceType.

* `instanceTypes`: lista de tipos de instancia usados para los trabajos de entrenamiento. Cada tipo de instancia se define mediante las propiedades `nodeSelector` y `resources requests/limits`:

  * `nodeSelector`: una o varias etiquetas de nodo que se usan para identificar los nodos de un clúster. Se necesitan privilegios de administrador de clústeres para crear etiquetas para los nodos de clúster. Si se especifica esta propiedad, los trabajos de entrenamiento se programan para ejecutarse en los nodos que tienen las etiquetas especificadas. Puede usar `nodeSelector` para seleccionar como destino un subconjunto de nodos para entrenar la ubicación de la carga de trabajo. Esto puede ser útil en escenarios en los que un clúster tiene SKU diferentes o diferentes tipos de nodos, como nodos de CPU o GPU. Por ejemplo, podría crear etiquetas de nodo para todos los nodos de GPU y definir un valor de `instanceType` para el grupo de nodos de GPU. De este modo se establece como destino exclusivamente el grupo de nodos de GPU al programar trabajos de entrenamiento. 

  * `resources requests/limits`: especifica las solicitudes de recursos y limita la ejecución a un pod de trabajo de entrenamiento. El valor predeterminado es 1 CPU y 4 GB de memoria.

    >[!IMPORTANT]
    > De forma predeterminada, se implementa un recurso de clúster con 1 CPU y 4 GB de memoria. Si un clúster se configura con recursos menores, se producirá un error en la ejecución del trabajo. Para garantizar la finalización correcta del trabajo, se recomienda especificar siempre las solicitudes o límites de recursos según las necesidades del trabajo de entrenamiento. A continuación se muestra un ejemplo del archivo de configuración predeterminado:
    >
    > ```json
    > {
    >    "namespace": "default",
    >    "defaultInstanceType": "defaultInstanceType",
    >    "instanceTypes": {
    >       "defaultInstanceType": {
    >          "nodeSelector": null,
    >          "resources": {
    >             "requests": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             },
    >             "limits": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             }
    >          }
    >       }
    >    }
    > }
    > ```

## <a name="attach-arc-cluster-python-sdk"></a>Asociación de un clúster de Arc (SDK para Python)

El siguiente código de Python muestra cómo asociar un clúster de Kubernetes habilitado para Azure Arc y cómo usarlo como destino de proceso para el entrenamiento:

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
    print("creating new compute target...")

    amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id) 
    amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
    amlarc_compute.wait_for_completion(show_output=True)
    
     # For a more detailed view of current KubernetesCompute status, use get_status()
    print(amlarc_compute.get_status().serialize())
```

### <a name="advanced-attach-scenario"></a>Escenario de asociación avanzada

En el código siguiente se muestra cómo configurar propiedades avanzadas del destino de proceso como el espacio de nombres, nodeSelector, o solicitudes o límites de recursos:

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
   amlarc_compute = ws.compute_targets[amlarc_compute_name]
   if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
      print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")
   ns = "amlarc-testing"
    
   instance_types = {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
        }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         }
      }
   }

   amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns, default_instance_type="gpu_instance", instance_types = instance_types)
 
   amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
   amlarc_compute.wait_for_completion(show_output=True)
    
   # For a more detailed view of current KubernetesCompute status, use get_status()
   print(amlarc_compute.get_status().serialize())
```

## <a name="next-steps"></a>Pasos siguientes

- [Entrenamiento de modelos con la CLI (versión 2)](how-to-train-cli.md)
- [Envío de una ejecución de entrenamiento a un destino de proceso](how-to-set-up-training-targets.md)
- [Ajustar los hiperparámetros](how-to-tune-hyperparameters.md)
- [Entrenamiento de modelos de scikit-learn a gran escala con Azure Machine Learning](how-to-train-scikit-learn.md)
- [Entrenamiento de un modelo de TensorFlow](how-to-train-tensorflow.md)
- [Entrenamiento de modelos de PyTorch a gran escala con Azure Machine Learning](how-to-train-pytorch.md)
- [Creación y ejecución de canalizaciones de Machine Learning con el SDK de Azure Machine Learning](how-to-create-machine-learning-pipelines.md)
- [Inicio rápido: conexión de un clúster de Kubernetes existente a Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)
