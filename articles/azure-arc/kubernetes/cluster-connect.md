---
title: Uso de Conexión de clúster para conectarse a clústeres de Kubernetes habilitados para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Uso de Conexión de clúster para conectarse de manera segura a clústeres de Kubernetes habilitados para Azure Arc
ms.openlocfilehash: e585b67495aef1fb094d157c584c6744d993b135
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997918"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Uso de Conexión de clúster para conectarse a clústeres de Kubernetes habilitados para Azure Arc

Con Conexión de clúster, puede conectarse de manera segura a los clústeres de Kubernetes habilitados para Azure Arc sin necesidad de habilitar ningún puerto de entrada en el firewall. El acceso al `apiserver` del clúster de Kubernetes habilitado para Azure Arc habilita los siguientes escenarios:
* Habilita la depuración interactiva y la solución de problemas.
* Proporciona acceso de clúster a los servicios de Azure para [ubicaciones personalizadas](custom-locations.md) y otros recursos creados en él.

Puede encontrar información general y algunos conceptos sobre esta característica en el artículo [Conexión de clúster de Kubernetes habilitado para Azure Arc](conceptual-cluster-connect.md).

## <a name="prerequisites"></a>Requisitos previos   

- [Instale o actualice la CLI de Azure](/cli/azure/install-azure-cli) a la versión 2.16.0 o posteriores.

- Instale la extensión `connectedk8s` de la CLI de Azure, versión 1.1.0 o posteriores.

    ```console
    az extension add --name connectedk8s
    ```
  
    Si ya ha instalado la extensión `connectedk8s`, actualícela a la versión más reciente:
    
    ```console
    az extension update --name connectedk8s
    ```

- Un clúster conectado de Kubernetes habilitado para Azure Arc.
    - Si no ha conectado aún un clúster, use nuestra [guía de inicio rápido](quickstart-connect-cluster.md).
    - [Actualice los agentes](agent-upgrade.md#manually-upgrade-agents) a la versión 1.1.0 o posteriores.

- Habilite los siguientes puntos de conexión, además de los que se mencionan en [Conexión de un clúster de Kubernetes a Azure Arc](quickstart-connect-cluster.md#meet-network-requirements):

    | Punto de conexión | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- Reemplace los marcadores de posición y ejecute el siguiente comando para establecer las variables de entorno que se usan en este documento:

    ```console
    CLUSTER_NAME=<cluster-name>
    RESOURCE_GROUP=<resource-group-name>
    ARM_ID_CLUSTER=$(az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
    ```


## <a name="enable-cluster-connect-feature"></a>Habilitación de la característica Conexión de clúster

Conexión de clúster se puede habilitar en cualquier clúster de Kubernetes habilitado para Azure Arc; para ello, hay que ejecutar el siguiente comando en una máquina en la que el archivo `kubeconfig` apunte al clúster correspondiente:

```console
az connectedk8s enable-features --features cluster-connect -n $CLUSTER_NAME -g $RESOURCE_GROUP
```

## <a name="azure-active-directory-authentication-option"></a>Opción de autenticación de Azure Active Directory

1. Obtenga el `objectId` asociado a la entidad de Azure AD:

    - Para la cuenta de usuario de Azure AD:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad signed-in-user show --query objectId -o tsv)
        ```

    - Para la aplicación de Azure AD:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad sp show --id <id> --query objectId -o tsv)
        ```

1. Autorice a la entidad de AAD con los permisos adecuados:

    - Si usa los objetos ClusterRoleBinding o RoleBinding nativos de Kubernetes para las comprobaciones de autorización en el clúster y el archivo `kubeconfig` apunta al `apiserver` del clúster para el acceso directo, puede crear uno asignado a la entidad de Azure AD (entidad de servicio o usuario) que necesita acceder a este clúster. Ejemplo:
    
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
        ```

    - Si usa Azure RBAC para realizar las comprobaciones de autorización en el clúster, puede crear una asignación de roles de Azure que esté asignada a la entidad de Azure AD. Ejemplo:

        ```console
        az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
        ```

## <a name="service-account-token-authentication-option"></a>Opción de autenticación de token de cuenta de servicio

1. Con el archivo `kubeconfig` que apunta al `apiserver` del clúster de Kubernetes, cree una cuenta de servicio en cualquier espacio de nombres (el siguiente comando la crea en el espacio de nombres predeterminado):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Cree un objeto ClusterRoleBinding o RoleBinding para conceder a esta [cuenta de servicio los permisos adecuados en el clúster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding). Ejemplo:

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Obtenga el token de la cuenta de servicio con los siguientes comandos.

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

## <a name="access-your-cluster"></a>Acceso al clúster

1. Configure el archivo kubeconfig basado en Conexión de clúster que se necesita para acceder a su clúster, según la opción de autenticación que se ha usado:

    - Si usa la opción de autenticación de Azure Active Directory, después de iniciar sesión en la CLI de Azure con la entidad de Azure AD correspondiente, obtenga el archivo `kubeconfig` de Conexión de clúster necesario para comunicarse con el clúster desde cualquier lugar (incluso desde fuera del firewall que rodea al clúster):

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP
        ```

    - Si usa la opción de autenticación de la cuenta de servicio, obtenga el archivo `kubeconfig` de Conexión de clúster necesario para comunicarse con el clúster desde cualquier lugar:

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP --token $TOKEN
        ```

1. Use `kubectl` para enviar solicitudes al clúster:

    ```console
    kubectl get pods
    ```
    
    Ahora debería mostrarse una respuesta del clúster que contiene la lista de todos los pods en el espacio de nombres `default`.

## <a name="known-limitations"></a>Restricciones conocidas

Cuando envíe solicitudes al clúster de Kubernetes, si la entidad Azure AD utilizada forma parte de más de 200 grupos, se presentará el siguiente error, ya que se trata de una limitación conocida:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Para eludir este error:
1. Cree una [entidad de servicio](/cli/azure/create-an-azure-service-principal-azure-cli). Es menos probable que una de estas sea miembro de más de 200 grupos.
1. [Inicie sesión](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) en la CLI de Azure con la entidad de servicio antes de ejecutar el comando `az connectedk8s proxy`.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> Configuración de [Azure AD RBAC](azure-rbac.md) en los clústeres
