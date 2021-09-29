---
title: Creación de un entrenamiento e implementación de los procesos (Studio)
titleSuffix: Azure Machine Learning
description: Uso de Studio para crear recursos de proceso de entrenamiento e implementación (destinos de proceso) para el aprendizaje automático
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/16/2021
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.openlocfilehash: be6bc9166ffc0bddae5433734d3213b53e6f6f0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673661"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Creación de destinos de proceso para la implementación y el entrenamiento de modelos en Azure Machine Learning Studio

En este artículo, aprenderá a crear y administrar destinos de proceso en Azure Machine Studio.  También puede crear y administrar destinos de proceso con:

* Extensión de la CLI o del SDK de Azure Machine Learning para Azure Machine Learning
  * [Instancia de proceso](how-to-create-manage-compute-instance.md)
  * [Clúster de proceso](how-to-create-attach-compute-cluster.md)
  * [Clúster de Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
  * [Otros recursos de proceso](how-to-attach-compute-targets.md)
* La [extensión de VS Code](how-to-manage-resources-vscode.md#compute-clusters) para Azure Machine Learning.

> [!IMPORTANT]
> Los elementos marcados (versión preliminar) en este artículo se encuentran actualmente en versión preliminar pública.
> Se ofrece la versión preliminar sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
* Un [área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

## <a name="whats-a-compute-target"></a>¿Qué es un destino de proceso? 

Con Azure Machine Learning, puede entrenar el modelo en una variedad de recursos o entornos, denominados colectivamente [__destinos de proceso__](concept-azure-machine-learning-architecture.md#compute-targets). Un destino de proceso puede ser una máquina local o un recurso de nube, como una instancia de proceso de Azure Machine Learning, Azure HDInsight o una máquina virtual remota.  También puede crear destinos de proceso para la implementación de modelos tal como se describe en ["Cómo y dónde implementar los modelos"](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Visualización de destinos de proceso

Para ver todos los destinos de proceso del área de trabajo, siga estos pasos:

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
 
1. En __Administrar__, seleccione __Proceso__.

1. Seleccione las pestañas de la parte superior para mostrar cada tipo de destino de proceso.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Visualización de la lista de destinos de proceso":::

## <a name="start-creation-process"></a><a id="portal-create"></a>Inicio del proceso de creación

Siga los pasos anteriores para ver la lista de destinos de proceso. A continuación, siga estos pasos para crear un destino de proceso:

1. Seleccione la pestaña de la parte superior correspondiente al tipo de proceso que va a crear.

1. Si no tiene ningún destino de proceso, seleccione **Crear** en el área central de la página.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Crear el destino de proceso":::

1. Si ve una lista de recursos de proceso, seleccione **+ Nuevo** encima de la lista.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Seleccionar Nuevo":::


1. Rellene el formulario para el tipo de proceso:

    * [Instancia de proceso](how-to-create-manage-compute-instance.md?tabs=azure-studio#create)
    * [Clústeres de proceso](#amlcompute)
    * [Clústeres de inferencia](#inference-clusters)
    * [Proceso adjunto](#attached-compute)

1. Seleccione __Crear__.

1. Vea el estado de la operación de creación seleccionando el destino de proceso en la lista:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Visualización del estado del proceso en una lista":::

Siga los pasos descritos en [Creación y administración de una instancia de proceso de Azure Machine Learning](how-to-create-manage-compute-instance.md?tabs=azure-studio#create).


## <a name="create-compute-clusters"></a><a name="amlcompute"></a>Creación de clústeres de proceso

Cree un clúster de proceso de uno o varios nodos para el entrenamiento, la inferencia por lotes o el refuerzo de las cargas de trabajo de aprendizaje. Utilice los [pasos anteriores](#portal-create) para crear el clúster de proceso.  Después, rellene el formulario de la manera siguiente:

|Campo  |Descripción  |
|---------|---------|
| Location | Región de Azure donde se creará el clúster de proceso. De forma predeterminada, se trata de la misma ubicación que el área de trabajo. Establecer la ubicación en una región distinta a la del área de trabajo es una opción que se encuentra en __versión preliminar__ y solo está disponible para __clústeres de proceso__, no para instancias de proceso.</br>Al usar una región diferente a la del área de trabajo o los almacenes de datos, es posible que vea un aumento de los costos de transferencia de datos y latencia de red. La latencia y los costos pueden producirse al crear el clúster y al ejecutar trabajos en él. |
|Tipo de máquina virtual |  Elija CPU o GPU. Este tipo no se puede cambiar después de la creación     |
|Prioridad de la máquina virtual | Elija **Dedicado** o **Prioridad baja**.  Las máquinas virtuales de prioridad baja son más económicas, pero no garantizan nodos de proceso. Es posible que se dé prioridad a su trabajo.
|Tamaño de la máquina virtual     |  Los tamaños de máquina virtual admitidos podrían estar restringidos en su región. Comprobación de la [lista de disponibilidad](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |

Seleccione **Siguiente** para ir a la **Configuración avanzada** y rellene el formulario como se indica a continuación:

|Campo  |Descripción  |
|---------|---------|
|Nombre del proceso     |  <li>El nombre es obligatorio y debe tener una longitud de entre 3 y 24 caracteres.</li><li>Los caracteres válidos son mayúsculas y minúsculas, dígitos y el carácter **-** .</li><li>El nombre debe empezar con una letra</li><li>El nombre debe ser único en todos los procesos existentes dentro de una región de Azure. Verá una alerta si el nombre elegido no es único</li><li>Si se usa el carácter **-** , debe ir seguido de al menos una letra más adelante en el nombre</li>     |
|Número mínimo de nodos | Número mínimo de nodos que quiere aprovisionar. Si quiere un número de nodos dedicado, establezca este número aquí. Ahorre dinero estableciendo el valor mínimo en 0, por lo que no pagará por ningún nodo cuando el clúster esté inactivo. |
|Número máximo de nodos | Número máximo de nodos que quiere aprovisionar. El proceso se escala automáticamente hasta este número máximo de nodos cuando se envía un trabajo. |
| Segundos de inactividad antes de la reducción vertical | Tiempo de inactividad antes de la reducción vertical del clúster al número mínimo de nodos. |
| Habilitación del acceso SSH | Use las mismas instrucciones que [Habilitación del acceso SSH](#enable-ssh) para una instancia de proceso (anterior). |
|Configuración avanzada     |  Opcional. Configurar una red virtual. Especifique el **Grupo de recursos**, **Red virtual** y **Subred** para crear la instancia de proceso dentro de una Azure Virtual Network (vnet). Para más información, consulte estos [requisitos de red](./how-to-secure-training-vnet.md) para la red virtual.   Adjunte también [identidades administradas](#managed-identity) para conceder acceso a los recursos.     |

### <a name="enable-ssh-access"></a><a name="enable-ssh"></a> Habilitación del acceso SSH

El acceso SSH está deshabilitado de forma predeterminada.  El acceso SSH no se puede cambiar después de la creación. Asegúrese de habilitar el acceso si tiene previsto depurar de forma interactiva con [VS Code Remote](how-to-set-up-vs-code-remote.md)  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

Una vez creado y en ejecución el clúster de proceso, consulte [Conexión con acceso SSH](#ssh-access).

### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Configuración de la identidad administrada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Durante la creación del clúster de proceso o la edición de los detalles del clúster de proceso, en **Configuración avanzada**, cambie a **Assign a managed identity** (Asignar una identidad administrada) y especifique una identidad asignada por el sistema o una identidad asignada por el usuario.

### <a name="managed-identity-usage"></a>Uso de la identidad administrada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="create-inference-clusters"></a><a name="inference-clusters"></a> Creación de un clúster de inferencia

> [!IMPORTANT]
> El uso de Azure Kubernetes Service con Azure Machine Learning tiene varias opciones de configuración. Algunos escenarios, como las redes, requieren instalación y configuración adicionales. Para obtener más información sobre el uso de AKS con Azure ML, consulte [Creación y conexión de un clúster de Azure Kubernetes Service](how-to-create-attach-kubernetes.md).

Cree o adjunte un clúster de Azure Kubernetes Service (AKS) para la inferencia a gran escala. Utilice los [pasos anteriores](#portal-create) para crear el clúster de AKS.  Después, rellene el formulario de la manera siguiente:


|Campo  |Descripción  |
|---------|---------|
|Nombre del proceso     |  <li>Se requiere el nombre. Debe tener entre 2 y 16 caracteres. </li><li>Los caracteres válidos son mayúsculas y minúsculas, dígitos y el carácter **-** .</li><li>El nombre debe empezar con una letra</li><li>El nombre debe ser único en todos los procesos existentes dentro de una región de Azure. Verá una alerta si el nombre elegido no es único</li><li>Si se usa el carácter **-** , debe ir seguido de al menos una letra más adelante en el nombre</li>     |
|Kubernetes Service | Seleccione **Crear nuevo** y rellene el resto del formulario.  O seleccione **Usar existente** y, a continuación, seleccione un clúster de AKS existente de su suscripción.
|Region |  Seleccione la región en la que se creará el clúster. |
|Tamaño de la máquina virtual     |  Los tamaños de máquina virtual admitidos podrían estar restringidos en su región. Comprobación de la [lista de disponibilidad](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Propósito del clúster  | Seleccione **Producción** o **Desarrollo/pruebas**. |
|Número de nodos | El número de nodos multiplicado por el número de núcleos de máquina virtual (vCPU) tiene que ser mayor o igual que 12. |
| Network configuration (Configuración de red) | Seleccione **Avanzada** para crear el proceso en una red virtual existente. Para más información sobre AKS en una red virtual, consulte [Aislamiento de red durante el entrenamiento y la inferencia con puntos de conexión privados y redes virtuales](./how-to-secure-inferencing-vnet.md). |
| Habilitación de la configuración de SSL | Use esta opción para configurar el certificado SSL en el proceso. |

## <a name="attach-other-compute"></a><a name="attached-compute"></a> Asociación de otro proceso

Para usar destinos de proceso creados fuera del área de trabajo de Azure Machine Learning, debe adjuntarlos. Adjuntar un destino de proceso hace que esté disponible para el área de trabajo.  Use **Attached compute** (Proceso adjunto) para adjuntar un destino de proceso para el **entrenamiento**.  Use **Inference clusters** (Clústeres de inferencia) para adjuntar un clúster de AKS para la **inferencia**.

Use los [pasos anteriores](#portal-create) para adjuntar un proceso.  Después, rellene el formulario de la manera siguiente:

1. Escriba un nombre para el destino de proceso. 
1. Seleccione el tipo de proceso que va a adjuntar. No todos los tipos de proceso se pueden adjuntar desde Azure Machine Learning Studio. Los tipos de proceso que se pueden adjuntar actualmente para entrenamiento incluyen:
    * Una máquina virtual de Azure (para asociar una instancia de Data Science Virtual Machine)
    * Azure Databricks (para su uso en las canalizaciones de aprendizaje automático)
    * Azure Data Lake Analytics (para su uso en las canalizaciones de aprendizaje automático)
    * HDInsight de Azure
    * Kubernetes (versión preliminar)

1. Rellene el formulario y proporcione valores para las propiedades necesarias.

    > [!NOTE]
    > Microsoft recomienda que use claves SSH, que son más seguras que las contraseñas. Las contraseñas son vulnerables a ataques por fuerza bruta. Las claves SSH se basan en las firmas criptográficas. Para información sobre cómo crear claves SSH para usarlas con Azure Virtual Machines, consulte los siguientes documentos:
    >
    > * [Creación y uso de claves SSH en Linux o macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Creación y uso de claves SSH en Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Seleccione __Adjuntar__.

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

> [!IMPORTANT]
> Para asociar un clúster de Azure Kubernetes Service (AKS) o un clúster de Kubernetes habilitado para Arc, debe ser propietario de la suscripción o tener permiso para acceder a los recursos del clúster de AKS en la suscripción. De lo contrario, la lista de clústeres de la página "attach new compute" (Adjuntar nuevo proceso) estará en blanco.

Para desasociar el proceso, siga estos pasos:

1. En Estudio de Azure Machine Learning, seleccione __Proceso__, __Proceso adjunto__ y el proceso que quiere quitar.
1. Use el vínculo __Desasociar__ para desasociar el proceso.

## <a name="connect-with-ssh-access"></a><a name="ssh-access"></a> Conexión con acceso SSH

Si ha creado la instancia de proceso o el clúster de proceso con el acceso SSH habilitado, siga estos pasos para acceder.

1. Busque el proceso en los recursos del área de trabajo:
    1. Seleccione **Proceso** a la izquierda.
    1. Use las pestañas de la parte superior para seleccionar **Instancia de proceso** o **Clúster de proceso** para encontrar la máquina.
1. Seleccione el nombre del proceso en la lista de recursos.
1. Busque la cadena de conexión:

    * Para una **Instancia de proceso**, seleccione **Conectar** en la parte superior de la sección **Detalles**.

        :::image type="content" source="media/how-to-create-attach-studio/details.png" alt-text="Captura de pantalla: herramienta Conectar en la parte superior de la página Detalles":::

    * Para un **clúster de proceso**, seleccione **Nodos** en la parte superior y, a continuación, seleccione la **Cadena de conexión** en la tabla del nodo.
        :::image type="content" source="media/how-to-create-attach-studio/compute-nodes.png" alt-text="Captura de pantalla: Cadena de conexión para un nodo en un clúster de proceso":::.

1. Copie la cadena de conexión.
1. Para Windows, abra PowerShell o un símbolo del sistema:
   1. Vaya al directorio o la carpeta donde se almacena la clave.
   1. Agregue la marca -i a la cadena de conexión para buscar la clave privada y apuntar a donde se almacena:
    
      `ssh -i <keyname.pem> azureuser@... (rest of connection string)`

1. Para los usuarios de Linux, siga los pasos de [Creación y uso de un par de claves SSH para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="next-steps"></a>Pasos siguientes

Después de crear un destino y de adjuntarlo al área de trabajo, lo usará en la [configuración de ejecución](how-to-set-up-training-targets.md) con un objeto `ComputeTarget`:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Use el recurso de proceso para [enviar una ejecución de entrenamiento](how-to-set-up-training-targets.md).
* [Tutorial: Entrenamiento de un modelo](tutorial-train-models-with-aml.md) utiliza un destino de proceso administrado para entrenar un modelo.
* Obtenga información sobre cómo [ajustar los hiperparámetros eficazmente](how-to-tune-hyperparameters.md) para crear modelos mejores.
* Cuando tenga un modelo entrenado, aprenda [cómo y dónde implementar los modelos](how-to-deploy-and-where.md).
* [Uso de Azure Machine Learning con Azure Virtual Networks](./how-to-network-security-overview.md)
