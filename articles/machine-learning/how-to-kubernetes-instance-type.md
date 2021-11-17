---
title: Creación y selección de tipos de instancias de Kubernetes (versión preliminar)
description: Cree y seleccione tipos de instancias de clúster de Kubernetes habilitadas para Azure Arc, y así poder entrenar e inferir cargas de trabajo en Azure Machine Learning.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46ae66658d5f07e1a44322bf64997a80d66db1c0
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485176"
---
# <a name="create-and-select-kubernetes-instance-types-preview"></a>Creación y selección de tipos de instancias de Kubernetes (versión preliminar)

Obtenga información sobre cómo crear y seleccionar instancias de Kubernetes para realizar el entrenamiento y la inferencia de cargas de trabajo de Azure Machine Learning en clústeres de Kubernetes habilitados para Azure Arc.

## <a name="what-are-instance-types"></a>¿Qué son los tipos de instancias?

Los tipos de instancias son un concepto de Azure Machine Learning que le permite concentrarse en determinados tipos de nodos de proceso para cargas de trabajo de entrenamiento e inferencia.  En el caso de una VM de Azure, un ejemplo para un tipo de instancia es `STANDARD_D2_V3`.

En los clústeres de Kubernetes, los tipos de instancias se definen mediante dos elementos:

* [nodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector): el elemento `nodeSelector` le permite especificar en qué nodo debe ejecutarse un pod.  El nodo debe tener una etiqueta correspondiente.
* [resources](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/): la sección `resources` define los recursos de proceso (CPU, memoria y GPU de Nvidia) para el pod.

## <a name="create-instance-types"></a>Creación de tipos de instancias

Los tipos de instancias se representan en una definición de recursos personalizados (CRD) que se instala con la extensión personalizada de Azure Machine Learning.  

### <a name="create-a-single-instance-type"></a>Creación de un solo tipo de instancia

Para crear un nuevo tipo de instancia, cree un nuevo recurso personalizado para el tipo de instancia CRD.  

Por ejemplo, según el CRD `my_instance_type.yaml`:

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceType
metadata:
  name: myinstancetypename
spec:
  nodeSelector:
    mylabel: mylabelvalue
  resources:
    limits:
      cpu: "1"
      nvidia.com/gpu: 1
      memory: "2Gi"
    requests:
      cpu: "700m"
      memory: "1500Mi"
```

Utilice el comando `kubectl apply` para crear un tipo de instancia nuevo.

```bash
kubectl apply -f my_instance_type.yaml
```

Esta operación crea un tipo de instancia con las siguientes propiedades:

- Los pods solo se programan en nodos con la etiqueta `mylabel: mylabelvalue`.
- A los pods se les asignan solicitudes de recursos de CPU `700m` y de memoria `1500Mi`. 
- A los pods se les asignan límites de recursos de CPU `1`, memoria `2Gi` y GPU `1` de Nvidia.

> [!NOTE]
> Al especificar el CRD, considere la posibilidad de tomar nota de las convenciones siguientes:
> - Los recursos de GPU de Nvidia solo se especifican en la sección `limits`.  Para obtener más información, consulte la [documentación de Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/#using-device-plugins).
> - Los recursos de CPU y memoria son valores de cadena.
> - La CPU se puede especificar en mil núcleos (`100m`) o en números completos (`"1"` que equivale a `1000m`).
> - La memoria se puede especificar como un número completo y un sufijo (`1024Mi` para `1024 MiB`).

### <a name="create-multiple-instance-types"></a>Creación de varios tipos de instancia

También puede usar CRD para crear varios tipos de instancia a la vez.

Por ejemplo, según el CRD `my_instance_type.yaml`:

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceTypeList
items:
  - metadata:
      name: cpusmall
    spec:
      resources:
        limits:
          cpu: "100m"
          nvidia.com/gpu: 0
          memory: "10Mi"
        requests:
          cpu: "100m"
          memory: "1Gi"

  - metadata:
      name: defaultinstancetype
    spec:
      resources:
        limits:
          cpu: "1"
          nvidia.com/gpu: 0
          memory: "1Gi"
        requests:
          cpu: "1"
          memory: "1Gi" 
```

Utilice el comando `kubectl apply` para crear varios tipos de instancias.

```bash
kubectl apply -f my_instance_type.yaml
``` 

Esta operación crea dos tipos de instancia: uno llamado `defaultinstancetype` y el otro denominado `cpusmall`, con especificaciones de recursos diferentes. Para obtener más información sobre los tipos de instancia predeterminados, consulte la sección [tipos de instancias predeterminados](#default-instance-types) de este documento.

## <a name="default-instance-types"></a>Tipos de instancias predeterminados

Cuando se envía una carga de trabajo de entrenamiento o inferencia sin un tipo de instancia, se usa el tipo de instancia predeterminado.  

Para especificar un tipo de instancia predeterminado para un clúster de Kubernetes, cree un tipo de instancia con el nombre `defaultinstancetype` y defina las propiedades respectivas `nodeSelector` y `resources` como cualquier otro tipo de instancia.  El tipo de instancia se reconoce automáticamente como valor predeterminado.

Si no se define ningún tipo de instancia predeterminado, se aplica el siguiente comportamiento predeterminado:

* No se aplica nodeSelector, lo que significa que el pod se puede programar en cualquier nodo.
* A los pods de la carga de trabajo se les asignan los recursos predeterminados:

    ```yaml
    resources:
        limits:
        cpu: "0.6"
        memory: "1536Mi"
        requests:
        cpu: "0.6"
        memory: "1536Mi"
    ```

> [!IMPORTANT]
> El tipo de instancia predeterminado no aparecerá como un recurso personalizado InstanceType en el clúster, pero aparecerá en todos los clientes (Estudio de Azure Machine Learning, CLI de Azure, SDK de Python).

## <a name="select-an-instance-type-for-training-workloads"></a>Selección de un tipo de instancia para cargas de trabajo de entrenamiento

Para seleccionar un tipo de instancia para un trabajo de entrenamiento mediante la CLI de Azure Machine Learning 2.0, especifique su nombre como parte de la sección `compute` del archivo de especificación de YAML.  

```yaml
command: python -c "print('Hello world!')"
environment:
  docker:
    image: python
compute:
  target: azureml:<compute_target_name>
  instance_type: <instance_type_name>
```

En el ejemplo anterior, reemplace `<compute_target_name>` por el nombre del destino de proceso de Kubernetes y `<instance_type_name>` por el nombre del tipo de instancia que quiere seleccionar.

> [!TIP]
> El tipo de instancia predeterminado usa a propósito pocos recursos. Para asegurarse de que todas las cargas de trabajo de aprendizaje automático se ejecutan correctamente con los recursos adecuados, se recomienda encarecidamente crear tipos de instancia personalizados.

## <a name="select-an-instance-type-for-inferencing-workloads"></a>Selección de un tipo de instancia para la inferencia de cargas de trabajo

Para seleccionar un tipo de instancia para deducir cargas de trabajo mediante la CLI de Azure Machine Learning 2.0, especifique su nombre como parte de la sección `deployments`.  Por ejemplo:

```yaml
type: online
auth_mode: key
target: azureml:<your compute target name>
traffic:
  blue: 100

deployments:
  - name: blue
    app_insights_enabled: true
    model: 
      name: sklearn_mnist_model
      version: 1
      local_path: ./model/sklearn_mnist_model.pkl
    code_configuration:
      code: 
        local_path: ./script/
      scoring_script: score.py
    instance_type: <instance_type_name>
    environment: 
      name: sklearn-mnist-env
      version: 1
      path: .
      conda_file: file:./model/conda.yml
      docker:
        image: mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210727.v1
```

En el ejemplo anterior, reemplace `<instance_type_name>` por el nombre del tipo de instancia que quiere seleccionar.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del aprendizaje automático habilitado para Azure Arc (versión preliminar)](how-to-attach-arc-kubernetes.md).
- [Entrenamiento de modelos (crear trabajos) con la CLI (v2)](how-to-train-cli.md)
- [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea (versión preliminar)](how-to-deploy-managed-online-endpoints.md)
