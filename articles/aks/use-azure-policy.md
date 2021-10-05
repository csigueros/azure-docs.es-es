---
title: Uso de Azure Policy para proteger el clúster
description: Use Azure Policy para proteger un clúster de Azure Kubernetes Service (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 09/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eb2f85064413f3d4700fea01aa4ead81508dd8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631502"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Protección del clúster con Azure Policy

Para mejorar la seguridad del clúster de Azure Kubernetes Service (AKS), puede aplicar directivas de seguridad integradas en el clúster mediante Azure Policy. [Azure Policy][azure-policy] ayuda a aplicar los estándares de la organización y a evaluar el cumplimiento a gran escala. Después de instalar el [complemento de Azure Policy para AKS][kubernetes-policy-reference], puede aplicar definiciones de directiva individuales o grupos de definiciones de directiva denominados iniciativas (a veces conocidos como conjuntos de directivas) al clúster. Para ver una lista completa de definiciones de directiva e iniciativa de AKS, consulte [Definiciones integradas de Azure Policy para Azure Kubernetes Service][aks-policies].

En este artículo se muestra cómo aplicar definiciones de directiva al clúster y comprobar que se aplican esas asignaciones.

## <a name="prerequisites"></a>Requisitos previos

- Un clúster de AKS existente Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].
- El complemento de Azure Policy para AKS instalado en un clúster de AKS. Siga estos [pasos para instalar el complemento de Azure Policy][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Asignación de una definición o iniciativa de directiva integrada

Para aplicar una definición o iniciativa de directiva, use Azure Portal.

1. En Azure Portal, vaya al servicio Azure Policy.
1. En el panel izquierdo de la página de Azure Policy, seleccione **Definitions** (Definiciones).
1. En **Categorías**, seleccione `Kubernetes`.
1. Elija la definición o iniciativa de directiva que quiera aplicar. En este ejemplo, seleccione la iniciativa `Kubernetes cluster pod security baseline standards for Linux-based workloads`.
1. Seleccione **Asignar**.
1. Establezca el valor de **Ámbito** en el grupo de recursos del clúster de AKS con el complemento de Azure Policy habilitado.
1. Seleccione la página **Parámetros** y actualice **Efecto** de `audit` a `deny` para impedir que las nuevas implementaciones infrinjan la iniciativa de línea de base. También puede agregar espacios de nombres adicionales para excluirlos de la evaluación. En este ejemplo, conserve los valores predeterminados.
1. Seleccione **Revisar y crear** y, luego, **Crear** para enviar la asignación de directiva.

## <a name="create-and-assign-a-custom-policy-definition-preview"></a>Creación y asignación de una definición de directiva personalizada (versión preliminar)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Las directivas personalizadas permiten definir reglas para usar Azure. Por ejemplo, puede aplicar:
- Prácticas de seguridad
- Administración de costos
- Reglas específicas de la organización (como nombres o ubicaciones)

Antes de crear una directiva personalizada, compruebe la [lista de patrones y ejemplos comunes][azure-policy-samples] para ver si ya incluye su caso.

Las definiciones de directivas personalizadas se escriben en JSON. Para más información sobre cómo crear una directiva personalizada, consulte [Estructura de definición de Azure Policy][azure-policy-definition-structure] y [Creación de una definición de directiva personalizada][custom-policy-tutorial-create].

> [!NOTE]
> Azure Policy usa ahora una nueva propiedad conocida como *templateInfo* que permite a los usuarios definir el tipo de origen para la plantilla de restricción. Mediante la definición de *templateInfo* en las definiciones de directivas, los usuarios no tienen que definir las propiedades *constraintTemplate* ni *constraint*. No obstante, los usuarios tienen que definir *apiGroups* y *kinds*. Para más información, consulte [Descripción de los efectos de Azure Policy][azure-policy-effects-audit].

Una vez creada la definición de directiva personalizada, vea [Asignación de una definición de directiva][custom-policy-tutorial-assign] para ver un tutorial detallado sobre la asignación de la directiva al clúster de Kubernetes.

## <a name="validate-a-azure-policy-is-running"></a>Validación de que se ejecuta Azure Policy

Confirme que las asignaciones de directiva se aplican al clúster; para ello, ejecute lo siguiente:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Las asignaciones de directivas pueden tardar [hasta 20 minutos en sincronizarse][azure-policy-assign-policy] con cada clúster.

La salida debe ser similar a:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Validación del rechazo de un pod con privilegios

Vamos a probar primero lo que sucede cuando se programa un pod con el contexto de seguridad `privileged: true`. Este contexto de seguridad eleva los privilegios del pod. La iniciativa no permite pods con privilegios, por lo que se denegará la solicitud, lo que provocará que se rechace la implementación.

Cree un archivo denominado `nginx-privileged.yaml` y pegue el siguiente manifiesto de YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Cree el pod con el comando [kubectl apply][kubectl-apply] y especifique el nombre del manifiesto de YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Como se esperaba, el pod no se puede incluir en la programación, como se muestra en la salida del ejemplo siguiente:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

El pod no llega a la fase de programación, por lo que no hay recursos que eliminar para continuar.

### <a name="test-creation-of-an-unprivileged-pod"></a>Prueba de la creación de un pod sin privilegios

En el ejemplo anterior la imagen de contenedor intentó automáticamente utilizar la raíz para enlazar NGINX al puerto 80. La iniciativa de directiva denegó esta solicitud, por lo que no se puede iniciar el pod. Vamos a probar ahora a ejecutar ese mismo pod con NGINX sin acceso con privilegios.

Cree un archivo denominado `nginx-unprivileged.yaml` y pegue el siguiente manifiesto de YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Cree el pod mediante el comando [kubectl apply][kubectl-apply] y especifique el nombre del manifiesto de YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Se ha programado correctamente el pod. Al activar el estado del pod mediante el comando [kubectl get pods][kubectl-get], el pod tiene el estado *En ejecución*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

En este ejemplo se muestra que la iniciativa de línea de base solo afecta a las implementaciones que infringen las directivas de la colección. Las implementaciones permitidas continúan funcionando.

Elimine el pod NGIX sin privilegios mediante el comando [kubectl delete][kubectl-delete] y especifique el nombre del manifiesto de YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Deshabilitación de una directiva o iniciativa

Para eliminar la iniciativa de línea de base:

1. Vaya al panel Directiva en Azure Portal.
1. Seleccione **Asignaciones** en el panel izquierdo.
1. Haga clic en el botón **...** situado junto a la iniciativa `Kubernetes cluster pod security baseline standards for Linux-based workloads`.
1. Seleccione **Eliminar asignación**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo funciona Azure Policy, consulte:

- [Introducción a Azure Policy][azure-policy]
- [Iniciativas y directivas de Azure Policy para AKS][aks-policies]
- Quite el [complemento de Azure Policy][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[azure-policy-effects-audit]: ../governance/policy/concepts/effects.md#audit-properties
[custom-policy-tutorial-create]: ../governance/policy/tutorials/create-custom-policy-definition.md
[custom-policy-tutorial-assign]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-policy-definition
[azure-policy-samples]: ../governance/policy/samples/index.md
[azure-policy-definition-structure]: ../governance/policy/concepts/definition-structure.md