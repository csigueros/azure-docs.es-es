---
title: Preguntas más frecuentes sobre Kubernetes habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artículo contiene una lista de las preguntas más frecuentes relacionadas con Kubernetes habilitado para Azure Arc.
keywords: Kubernetes, Arc, Azure, contenedores, configuración, GitOps, preguntas más frecuentes
ms.openlocfilehash: f678bd23bc4e9e40f718d72ccde8069c36673ac6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272932"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Preguntas más frecuentes: Kubernetes habilitado para Azure Arc

En este artículo se abordan las preguntas más frecuentes sobre Kubernetes habilitado para Azure Arc.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>¿Cuál es la diferencia entre Kubernetes habilitado para Azure Arc y Azure Kubernetes Service (AKS)?

AKS es la oferta de Kubernetes administrado de Azure. AKS facilita la implementación de un clúster de Kubernetes administrado en Azure al traspasar gran parte de la complejidad y sobrecarga operativa a Azure. Como Azure administra los maestros de Kubernetes, solo administra y mantiene los nodos de agente.

Kubernetes habilitado para Azure Arc permite ampliar las funcionalidades de administración de Azure (como Azure Monitor y Azure Policy) al conectar clústeres de Kubernetes a Azure. Usted mantiene el propio clúster de Kubernetes subyacente.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>¿Es necesario conectar a Azure Arc los clústeres de AKS que se ejecutan en Azure?

La conexión de un clúster de Azure Kubernetes Service (AKS) a Azure Arc solo es necesaria para ejecutar servicios habilitados para Arc como App Services y Data Services sobre el clúster. Esto se puede hacer mediante la característica de [ubicaciones personalizadas](custom-locations.md) de Kubernetes habilitado para Arc. Se trata de una limitación puntual por ahora hasta que las extensiones de clúster y las ubicaciones personalizadas se introduzcan de forma nativa sobre los clústeres de AKS.

Si no desea usar ubicaciones personalizadas y solo quiere usar características de administración como Azure Monitor y Azure Policy (Gatekeeper), están disponibles de forma nativa en AKS y no es necesaria la conexión a Azure Arc en estos casos.
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>¿Debo conectar el clúster AKS-HCI y los clústeres de Kubernetes en Azure Stack Hub y Azure Stack Edge a Azure Arc?

Sí, la conexión del clúster AKS-HCI o los clústeres de Kubernetes en Azure Stack Edge o Azure Stack Hub a Azure Arc proporciona clústeres con representación de recursos en Azure Resource Manager. Con esta representación de recursos, se amplían algunas funcionalidades, como la configuración de clúster, Azure Monitor y Azure Policy (Gatekeeper), a los clústeres de Kubernetes conectados.

Si el clúster de Kubernetes habilitado para Azure Arc está en Azure Stack Edge, AKS en Azure Stack HCI (>= actualización de abril de 2021) o AKS en Windows Server 2019 Datacenter (>= actualización de abril de 2021), la configuración de Kubernetes se incluye sin cargo alguno.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>¿Cómo tratar los recursos caducados de Kubernetes habilitado para Azure Arc?

La identidad administrada asignada por el sistema asociada con el clúster de Kubernetes habilitado para Azure Arc solo la usan los agentes de Arc para comunicarse con los servicios de Azure Arc. El certificado asociado con esta identidad administrada asignada por el sistema tiene un período de expiración de 90 días y los agentes siguen intentando renovar este certificado entre el día 46 y el día 90. Una vez que este certificado expira, el recurso se considera `Expired` y todas las características (como configuración, supervisión y directiva) dejan de funcionar en este clúster y, a continuación, deberá eliminar y conectar el clúster a Azure Arc una vez más. Por lo tanto, es aconsejable que el clúster se ponga en línea al menos una vez en todo el período de tiempo comprendido entre el día 46 y el día 90 para garantizar la renovación del certificado de identidad administrada.

Para comprobar si el certificado está a punto de expirar para cualquier clúster especificado, ejecute el siguiente comando:

```console
az connectedk8s show -n <name> -g <resource-group>
```

En la salida, el valor de `managedIdentityCertificateExpirationTime` indica cuándo expirará el certificado de identidad administrada (marca 90D para ese certificado). 

Si el valor de `managedIdentityCertificateExpirationTime` indica una marca de tiempo del pasado, el campo `connectivityStatus` de la salida anterior se establecerá en `Expired`. En tales casos, para que el clúster de Kubernetes vuelva a funcionar con Azure Arc:

1. Elimine el recurso y los agentes de Kubernetes habilitado para Azure Arc en el clúster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Para volver a crear el recurso de Kubernetes habilitado para Azure Arc, implemente los agentes en el clúster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` también eliminará las configuraciones y las extensiones de clúster situadas sobre el clúster. Después de ejecutar `az connectedk8s connect`, vuelva a crear las configuraciones y las extensiones de clúster en el clúster, ya sea manualmente o mediante Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Si ya uso canalizaciones de CI/CD, ¿puedo seguir usando configuraciones y Kubernetes habilitado para Azure Arc?

Sí, todavía puede usar configuraciones en un clúster que recibe implementaciones a través de una canalización de CI/CD. En comparación con las canalizaciones de CI/CD tradicionales, las configuraciones presentan dos ventajas adicionales:

**Conciliación de desfase**

La canalización de CI/CD aplica los cambios solo una vez durante la ejecución de la canalización. Sin embargo, el operador de GitOps del clúster sondea continuamente el repositorio de Git para capturar el estado deseado de los recursos de Kubernetes en el clúster. Si el operador de GitOps encuentra que el estado deseado de los recursos es diferente del estado real de los recursos en el clúster, se reconcilia este desfase.

**Aplicación de GitOps a escala**

Las canalizaciones de CI/CD son útiles para implementaciones controladas por eventos en el clúster de Kubernetes (por ejemplo, una inserción en un repositorio de Git). Sin embargo, si desea implementar la misma configuración en todos los clústeres de Kubernetes, debe configurar manualmente las credenciales de cada clúster de Kubernetes en la canalización de CI/CD. 

En el caso de Kubernetes habilitado para Azure Arc, como Azure Resource Manager administra las configuraciones, puede automatizar la creación de la misma configuración en todos los recursos de Kubernetes habilitado para Azure Arc mediante Azure Policy, en el ámbito de una suscripción o de un grupo de recursos. Esta funcionalidad es incluso aplicable a los recursos de Kubernetes habilitado para Azure Arc creados después de la asignación de directivas.

La característica aplica configuraciones de base de referencia (como directivas de red, enlaces de roles y directivas de seguridad de pods) en todo el inventario de clústeres de Kubernetes para satisfacer los requisitos de cumplimiento y gobernanza.

## <a name="next-steps"></a>Pasos siguientes

* Siga el inicio rápido para [conectar un clúster de Kubernetes a Azure Arc](./quickstart-connect-cluster.md).
* ¿Ya tiene un clúster de Kubernetes conectado a Azure Arc? [Cree configuraciones en el clúster de Kubernetes habilitado para Arc](./tutorial-use-gitops-connected-cluster.md).
* Aprenda a [usar Azure Policy para aplicar configuraciones a escala](./use-azure-policy.md).