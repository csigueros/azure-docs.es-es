---
title: Acceso a recursos de Kubernetes en Azure Portal
description: Descubra cómo interactuar con recursos de Kubernetes para administrar un clúster de Azure Kubernetes Service (AKS) en Azure Portal.
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: d48a11a3897a4de8f613e015ffc87c84bea08950
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779102"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Acceso a recursos de Kubernetes en Azure Portal

Azure Portal incluye un visor de recursos de Kubernetes para facilitar el acceso a los recursos de Kubernetes en el clúster de Azure Kubernetes Service (AKS). Ver los recursos de Kubernetes en Azure Portal reduce la necesidad de cambiar de contexto entre el Azure Portal y la herramienta de línea de comandos `kubectl`. De este modo, se simplifican la visualización y edición de recursos de Kubernetes. El visor de recursos actualmente incluye varios tipos de recursos, como implementaciones, pods y conjuntos de réplicas.

La vista de recursos de Kubernetes en Azure Portal reemplaza al [complemento del panel de AKS][kubernetes-dashboard], que ha quedado en desuso.

## <a name="prerequisites"></a>Prerrequisitos

Para visualizar los recursos de Kubernetes en Azure Portal, necesita un clúster de AKS. Cualquier clúster puede valer, pero si se utiliza la integración de Azure Active Directory (Azure AD), el clúster tiene que utilizar la [integración de Azure AD administrado por AKS][aks-managed-aad]. Si su clúster utiliza la integración de Azure AD heredada, puede actualizarlo en el portal o mediante la [CLI de Azure][cli-aad-upgrade]. También puede [usar Azure Portal][portal-cluster] para crear un nuevo clúster de AKS.

## <a name="view-kubernetes-resources"></a>Visualización de recursos de Kubernetes

Para ver los recursos de Kubernetes, acceda al clúster de AKS en Azure Portal. El panel de navegación de la izquierda se usa para acceder a los recursos. Los recursos incluyen:

- **Espacios de nombres** muestra los espacios de nombres del clúster. El filtro situado en la parte superior de la lista de espacios de nombres permite filtrar y visualizar de forma rápida los recursos del espacio de nombres.
- **Cargas de trabajo** muestra información sobre implementaciones, pods, conjuntos de réplicas, objetos StatefulSet, conjuntos de demonios, trabajos y trabajos cron que se han implementado en el clúster. La siguiente captura de pantalla muestra los pods del sistema predeterminados en un clúster de AKS de ejemplo.
- **Servicios y entradas** muestra todos los recursos de entrada y servicio del clúster.
- **Almacenamiento** muestra las clases de almacenamiento de Azure y la información del volumen persistente.
- **Configuración** muestra los secretos y objetos ConfigMap del clúster.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Información del pod de Kubernetes en Azure Portal." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Implementar una aplicación

En este ejemplo, usaremos el clúster de AKS de ejemplo para implementar la aplicación Azure Vote desde el [inicio rápido de AKS][portal-quickstart].

1. Seleccione **Agregar** desde cualquiera de las vistas de recursos (espacio de nombres, cargas de trabajo, servicios y entradas, almacenamiento o configuración).
1. Pegue el archivo YAML de la aplicación Azure Vote desde el [inicio rápido de AKS][portal-quickstart].
1. Seleccione **Agregar** en la parte inferior del editor YAML para implementar la aplicación. 

Una vez que se añade el archivo YAML, el visor de recursos muestra los dos servicios de Kubernetes creados: el servicio interno (azure-vote-back) y el servicio externo (azure-vote-front) para acceder a la aplicación Azure Vote. El servicio externo incluye una dirección IP externa vinculada para que pueda ver fácilmente la aplicación en el explorador.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Información de la aplicación Azure Vote mostrada en Azure Portal." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Supervisión de la información de implementación

Los clústeres de AKS que tengan activado [Azure Monitor para contenedores][enable-monitor] pueden ver rápidamente la implementación y otra información. En la vista de recursos de Kubernetes, los usuarios pueden ver el estado activo de implementaciones individuales, incluido el uso de la CPU y de la memoria, además de cambiar a Azure Monitor para ver información más detallada sobre contenedores y nodos específicos. A continuación, se incluye un ejemplo de información de implementación de un clúster de AKS de ejemplo:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Información de implementación en Azure Portal." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Edición de YAML

La vista de recursos de Kubernetes también incluye un editor YAML. Tener un editor YAML integrado significa que puede actualizar o crear servicios e implementaciones desde el portal y aplicar los cambios inmediatamente.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Editor YAML para un servicio de Kubernetes en Azure Portal.":::

Después de editar el archivo YAML, los cambios se aplicarán al seleccionar **revisar + guardar**, confirmarlos y, a continuación, volver a guardar.

>[!WARNING]
> No se recomienda hacer cambios de producción directamente a través de la interfaz de usuario o la CLI. Siga los [procedimientos recomendados de integración continua (CI) y de implementación continua (CD)](kubernetes-action.md). La funcionalidad de administración de Kubernetes en Azure Portal y el editor YAML se han creado para aprender sobre nuevas implementaciones y lanzarlas en una configuración de desarrollo y pruebas.

## <a name="troubleshooting"></a>Solución de problemas

En esta sección se abordan problemas comunes y los pasos para solucionarlos.

### <a name="unauthorized-access"></a>Acceso no autorizado

Para acceder a recursos de Kubernetes, debe tener acceso al clúster, la API y los objetos de Kubernetes. El usuario debe ser administrador de clústeres o tener los permisos adecuados para acceder al clúster de AKS. Para obtener más información sobre la seguridad de clúster, consulte [Opciones de acceso e identidad en Azure Kubernetes Service (AKS)][concepts-identity].

>[!NOTE]
> La vista de recursos de Kubernetes en Azure Portal solo se admite en [clústeres habilitados administrados con AAD](managed-aad.md) o en clústeres habilitados no administrados con AAD. Si usa un clúster habilitado administrado con AAD, el usuario o la identidad de AAD debe tener los roles o enlaces de rol correspondientes para tener acceso a la API de Kubernetes, además de los permisos para extraer el [usuario`kubeconfig`](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Activación de la vista de recursos

En el caso de clústeres existentes, es posible que tenga que activar la vista de recursos de Kubernetes. Para activar la vista de recursos, siga las indicaciones del portal para el clúster.

> [!TIP]
> La característica de [**intervalos de IP autorizados del servidor de API**](api-server-authorized-ip-ranges.md) de AKS se puede agregar para limitar el acceso del servidor de API solo al punto de conexión público del firewall. Otra opción para estos clústeres es actualizar `--api-server-authorized-ip-ranges` para incluir el acceso de un equipo cliente local o un intervalo de direcciones IP (desde la que se examina el portal). Para permitir este acceso, necesita la dirección IPv4 pública del equipo. Para encontrar esta dirección, use el siguiente comando o busque "cuál es mi dirección IP" en un explorador de Internet.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig +short myip.opendns.com @resolver1.opendns.com)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Pasos siguientes

En este artículo se ha mostrado cómo acceder a recursos de Kubernetes para el clúster de AKS. Consulte [Implementaciones y manifiestos YAML][deployments] para familiarizarse con los recursos de clúster y los archivos YAML a los que accede mediante el visor de recursos de Kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md
