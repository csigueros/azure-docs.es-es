---
title: Cree un experimento que use un error de Chaos Mesh de AKS mediante Azure Chaos Studio con Azure Portal
description: Cree un experimento que use un error de Chaos Mesh de AKS con Azure Portal
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7ae8da70739ded629753bdf3e3288a6b4ed279b8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373450"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-to-kill-aks-pods-with-the-azure-portal"></a>Cree un experimento de caos que use un error de Chaos Mesh para acabar con los pods de AKS con Azure Portal

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En esta guía, provocará errores de pod de Azure Kubernetes Service periódicos en un espacio de nombres mediante un experimento de caos y Azure Chaos Studio. La ejecución de este experimento puede ayudarle a defenderse contra la falta de disponibilidad del servicio cuando hay errores esporádicos.

Azure Chaos Studio usa [Chaos Mesh](https://chaos-mesh.org/), una plataforma gratuita de ingeniería de caos de código abierto para Kubernetes para insertar errores en un clúster de AKS. Los errores de Chaos Mesh son errores [directos de servicio](chaos-studio-tutorial-aks-portal.md) que requieren que Chaos Mesh esté instalado en el clúster de AKS. Estos mismos pasos se pueden usar para configurar y ejecutar un experimento para cualquier error de Chaos Mesh de AKS.

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Un clúster de AKS. Si no tiene un clúster de AKS, puede [seguir estos pasos para crear uno](../aks/kubernetes-walkthrough-portal.md).

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>Configuración de Chaos Mesh en el clúster de AKS

Para poder ejecutar errores de Chaos Mesh en Chaos Studio, debe instalar Chaos Mesh en el clúster de AKS.

1. Ejecute los siguientes comandos en una ventana [Azure Cloud Shell](../cloud-shell/overview.md) donde tenga la suscripción activa establecida para que sea la suscripción donde se implementa el clúster de AKS. Reemplace `$RESOURCE_GROUP` y `$CLUSTER_NAME` por el grupo de recursos y asigne un nombre al recurso del clúster.

```bash
az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
kubectl create ns chaos-testing
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
```

2. Ejecute el siguiente comando para comprobar que los pods de Chaos Mesh están instalados:

```bash
kubectl get po -n chaos-testing
```

Debe ver una salida similar a la siguiente (un chaos-controller-manager y uno o varios chaos-daemons):

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

También puede [usar las instrucciones de instalación en el sitio web de Chaos Mesh](https://chaos-mesh.org/docs/production-installation-using-helm/).


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>Habilitación de Chaos Studio en el clúster de AKS

Chaos Studio no puede insertar errores en un recurso a menos que ese recurso se haya incorporado primero a Chaos Studio. Incorpore un recurso a Chaos Studio mediante la creación de un [destino y funcionalidades](chaos-studio-targets-capabilities.md) en el recurso. Los clústeres de AKS solo tienen un tipo de destino (directo de servicio), pero otros recursos pueden tener hasta dos tipos de destino: uno para los errores directos de servicio y otro para los errores basados en agente. Cada tipo de error de Chaos Mesh se representa como una funcionalidad (PodChaos, NetworkChaos, IOChaos, etc.).

1. Abra [Azure Portal](https://portal.azure.com).
2. Busque **Chaos Studio (versión preliminar)** en la barra de búsqueda.
3. Haga clic en **Destinos** y vaya hasta el clúster de AKS.
![Visualización de los destinos en Azure Portal](images/tutorial-aks-targets.png)
4. Active la casilla situada junto al clúster de AKS y haga clic en **Habilitar destinos** y, después, en **Habilitar destinos directos de servicio** en el menú desplegable.
![Habilitación de destinos en Azure Portal](images/tutorial-aks-targets-enable.png)
5. Aparecerá una notificación que indica que los recursos seleccionados se habilitaron correctamente.
![Notificación que muestra el destino habilitado correctamente](images/tutorial-aks-targets-enable-confirm.png)

Ya ha incorporado correctamente el clúster de AKS a Chaos Studio. En la vista **Destinos** también puede administrar las funcionalidades habilitadas en este recurso. Al hacer clic en el vínculo **Administrar acciones** junto a un recurso, se mostrarán las funcionalidades habilitadas para ese recurso.

## <a name="create-an-experiment"></a>Creación de un experimento
Con el clúster de AKS ya incorporado, puede crear el experimento. Un experimento de caos define las acciones que desea realizar en los recursos de destino, organizados en pasos, que se ejecutan secuencialmente, y ramas, que se ejecutan en paralelo.

1. Haga clic en la pestaña **Experimentos** en el panel de navegación de Chaos Studio. En esta vista, puede ver y administrar todos los experimentos de caos. Haga clic en la vista **Agregar un experimento**
![ Experimentos en Azure Portal](images/tutorial-aks-add.png)
2. Rellene la **Suscripción**,el **Grupo de recursos** y la **Ubicación** donde desea implementar el experimento de caos. Ponga un **Nombre** al experimento. Haga clic en **Siguiente: Diseñador de experimentos >** 
![Agregar los detalles básicos del experimento](images/tutorial-aks-add-basics.png)
3. Ahora está en el diseñador de experimentos de Chaos Studio. El diseñador de experimentos permite compilar el experimento mediante la adición de pasos, ramas y errores. Asigne un nombre descriptivo a su **Paso** y **Rama** y,a continuación, haga clic en **Agregar error**.
![Diseñador de experimentos](images/tutorial-aks-add-designer.png)
4. Seleccione la opción de **caos de pod de Chaos Mesh de AKS** en la lista desplegable y rellene la **duración** con el número de minutos que quiere que el error dure y **jsonSpec** con la información siguiente:

    Para formular jsonSpec de Chaos Mesh:
    1. Visite la documentación de Chaos Mesh para ver un tipo de error, [por ejemplo, el tipo PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files).
    2. Formule la configuración de YAML para ese tipo de error mediante la documentación de Chaos Mesh.

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: one
          duration: '30s'
          selector:
            namespaces:
              - default
        ```
    3. Quite cualquier YAML fuera de `spec` (incluido el nombre de la propiedad de especificación) y quite la sangría de los detalles de la especificación.

        ```yaml
        action: pod-failure
        mode: one
        duration: '30s'
        selector:
          namespaces:
            - default
        ```
    4. Use un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON y minimizarlo.

        ```json
        {"action":"pod-failure","mode":"one","duration":"30s","selector":{"namespaces":["default"]}}
        ```
    5. Pegue el JSON minimizado en el campo **json** del portal.




Haga clic en **Siguiente: Recursos de destino >** 
![Propiedades del error](images/tutorial-aks-add-fault.png)
5. Seleccione el clúster de AKS y haga clic en **Siguiente**
![Agregar un destino](images/tutorial-aks-add-targets.png)
6. Compruebe que el experimento tiene el aspecto correcto y, a continuación, haga clic en **Revisar y crear** y, después, en **Crear.** 
![Revisar y crear el experimento](images/tutorial-aks-add-review.png)

## <a name="give-experiment-permission-to-your-aks-cluster"></a>Conceder permiso de experimento al clúster de AKS
Al crear un experimento de caos, Chaos Studio crea una identidad administrada asignada por el sistema que ejecuta errores en los recursos de destino. Esta identidad debe tener los [permisos adecuados](chaos-studio-fault-providers.md) en el recurso de destino para que el experimento se ejecute correctamente.

1. Vaya al clúster de AKS y haga clic en **Control de acceso (IAM)** .
![Página de información general de AKS](images/tutorial-aks-access-resource.png)
2. Haga clic en **Agregar** y, a continuación, en **Agregar asignación de roles**.
![Introducción al control de acceso](images/tutorial-aks-access-iam.png)
3. Busque el rol de **administrador de clúster de Azure Kubernetes Service** y seleccione el rol. Haga clic en **Siguiente**
![Asignación del rol de administrador de clúster de AKS](images/tutorial-aks-access-role.png)
4. Haga clic en **Seleccionar miembros** y busque el nombre del experimento. Seleccione el experimento y haga clic en **Seleccionar**. Si hay varios experimentos en el mismo inquilino con el mismo nombre, el nombre del experimento se truncará con caracteres aleatorios agregados.
![Adición de un experimento al rol](images/tutorial-aks-access-experiment.png)
5. Haga clic en **Revisar y asignar** y, a continuación, en **Revisar y asignar**.

## <a name="run-your-experiment"></a>Ejecutar el experimento
Ahora ya puede ejecutar el experimento. Para ver el impacto, se recomienda abrir la información general del clúster de AKS e ir a **Información** en una pestaña del explorador independiente. Los datos en directo del **número de pods activos** mostrarán el impacto tras la ejecución el experimento.

1. En la vista **Experimentos,** haga clic en el experimento y, a continuación, haga clic en **Iniciar** y, a continuación, haga clic en **Aceptar**.
![Inicio de un experimento](images/tutorial-aks-start.png)
2. Cuando el **estado** cambie a **En ejecución**,haga clic en **Detalles** de la última ejecución en **Historial** para ver los detalles del experimento en ejecución.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento directo del servicio de Chaos Mesh de AKS, está listo para:
- [Crear un experimento que usa errores basados en agente](chaos-studio-tutorial-agent-based-portal.md)
- [Administrar el experimento](chaos-studio-run-experiment.md)
