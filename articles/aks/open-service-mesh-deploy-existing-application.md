---
title: Incorporación de aplicaciones a Open Service Mesh
description: Incorporación de una aplicación a Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 202702623353462bafd77002662a35e7c7b7d2f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066774"
---
# <a name="onboarding-applications-to-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Incorporación de aplicaciones al complemento Open Service Mesh (OSM) para Azure Kubernetes Service (AKS)

En la guía siguiente se describe cómo incorporar un microservicio de Kubernetes a OSM.

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este tutorial se supone que ya ha habilitado el complemento OSM de AKS para el clúster de AKS. Si no es así, revise el artículo [Implementación del complemento OSM de AKS](./open-service-mesh-deploy-addon-az-cli.md) antes de continuar. Además, el clúster de AKS debe ser de la versión Kubernetes `1.19+` o posterior, tener habilitado RBAC de Kubernetes y haber establecido una conexión `kubectl` con el clúster (si necesita ayuda con cualquiera de estos elementos, consulte el [inicio rápido de AKS](./kubernetes-walkthrough.md)), además de haber instalado el complemento OSM de AKS.

Debe tener instalados los siguientes recursos:

- CLI de Azure, versión 2.20.0 o posterior
- Versión del complemento de OSM 0.11.1 o posterior
- Versión de la CLI de OSM 0.11.1 o posterior

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Comprobación de la directiva de modo de tráfico permisivo de Open Service Mesh (OSM)

El modo de la directiva de tráfico permisivo de OSM es un modo en el que se omite la aplicación de directivas de tráfico de [SMI](https://smi-spec.io/). En este modo, OSM detecta automáticamente los servicios que forman parte de la malla de servicio y programa reglas de la directiva de tráfico en cada sidecar del proxy de Envoy para comunicarse con estos servicios.

Para comprobar el modo de tráfico permisivo actual de OSM para el clúster, ejecute el siguiente comando:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

Si **enablePermissiveTrafficPolicyMode** está configurado en **true**, puede incorporar los espacios de nombres de forma segura sin que se produzca ninguna interrupción en las comunicaciones entre servicios. Si **enablePermissiveTrafficPolicyMode** está configurado en **false**, deberá asegurarse de que tiene implementados los manifiestos de directiva de acceso de tráfico [SMI](https://smi-spec.io/) correctos. También deberá asegurarse de que tiene una cuenta de servicio que representa cada servicio implementado en el espacio de nombres. Para obtener más información sobre el modo de tráfico permisivo, visite y lea el artículo [Modo de directiva de tráfico permisivo](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="onboard-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Incorporación de aplicaciones con la directiva de tráfico permisiva de Open Service Mesh (OSM) configurada como True

1. Consulte la guía de [requisitos de la aplicación](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/prereqs/) antes de incorporar aplicaciones.

1. Si una aplicación de la malla necesita comunicarse con el servidor de API de Kubernetes, el usuario debe permitirlo excluyendo explícitamente el intervalo IP o creando una directiva de salida.

1. Incorporación de espacios de nombres de Kubernetes a OSM

    Para incorporar un espacio de nombres que contiene las aplicaciones que va a administrar OSM, ejecute el comando `osm namespace add`:

    ```console
    $ osm namespace add <namespace>
    ```

    De forma predeterminada, el comando `osm namespace add` habilita la inserción automática de sidecar para pods en el espacio de nombres.

    Para deshabilitar la inserción automática de sidecar como parte de la inscripción de un espacio de nombres en la malla, use `osm namespace add <namespace> --disable-sidecar-injection`.
    Una vez incorporado un espacio de nombres, los pods se pueden inscribir en la malla mediante la configuración de la inserción automática de sidecar. Consulte el documento de [inserción de sidecar](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) para obtener más detalles.

1.  Implementación de nuevas aplicaciones o reimplementación de aplicaciones existentes

    De forma predeterminada, las nuevas implementaciones en los espacios de nombres incorporados están habilitadas para la inserción automática de sidecar. Esto significa que cuando se crea un nuevo pod en un espacio de nombres administrado, OSM insertará automáticamente el proxy sidecar en el pod.
    Las implementaciones existentes deben reiniciarse para que OSM pueda insertar automáticamente el proxy sidecar al volver a crear el pod. Los pods administrados por una implementación se pueden reiniciar mediante el comando `kubectl rollout restart deploy`.

    Para enrutar correctamente el tráfico específico del protocolo a los puertos de servicio, configure el protocolo de aplicación que se usará. Consulte la [guía de selección del protocolo de aplicación](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/app_protocol_selection/) para obtener más información.


## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Incorporación de aplicaciones implementadas existentes con la directiva de tráfico permisivo de Open Service Mesh (OSM) configurada como False

Cuando la configuración de OSM para la directiva de tráfico permisivo está establecida en `false`, OSM requerirá directivas de acceso de tráfico de [SMI](https://smi-spec.io/) explícitas implementadas para que la comunicación entre servicios se produzca en el clúster. Puesto que OSM usa cuentas de servicio de Kubernetes para implementar directivas de control de acceso entre aplicaciones de la malla, aplique directivas de acceso al tráfico de [SMI](https://smi-spec.io/) para autorizar el flujo de tráfico entre aplicaciones.

Para ver directivas de SMI de ejemplo, consulte los ejemplos siguientes:
    - [demo/deploy-traffic-specs.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-specs.sh)
    - [demo/deploy-traffic-split.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-split.sh)
    - [demo/deploy-traffic-target.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-target.sh)


#### <a name="removing-namespaces"></a>Eliminación de espacios de nombres
Los espacios de nombres se pueden quitar de la malla de OSM con el comando `osm namespace remove`:

```console
$ osm namespace remove <namespace>
```

> [!NOTE]
>
> - El comando **`osm namespace remove`** solo indica a OSM que deje de aplicar actualizaciones a las configuraciones de proxy sidecar en el espacio de nombres. **No quita** los sidecar de programa. Esto significa que se seguirá usando la configuración de proxy existente, pero el plano de control de OSM no la actualizará. Si desea quitar los servidores proxy de todos los pods, quite los espacios de nombres de los pods de la malla mediante OSM LCI y vuelva a implementar los pods o implementaciones correspondientes.
