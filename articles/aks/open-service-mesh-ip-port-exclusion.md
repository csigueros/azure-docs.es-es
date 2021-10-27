---
title: Exclusión del intervalo de direcciones IP y puertos
description: Implementación de la exclusión del intervalo de direcciones IP y puertos
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b12ec518aa46800778973524b4302ebed083d38f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007084"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a>Implementación de la <span class="x x-first x-last">exclusión del intervalo de direcciones IP y puertos</span>

El tráfico de salida basado en TCP de las aplicaciones se intercepta de manera predeterminada mediante las reglas de `iptables` programadas por OSM y se redirige al sidecar del proxy de Envoy. OSM proporciona un medio para especificar una lista de intervalos IP y puertos que se excluirán de la interceptación de tráfico si es necesario. Para obtener instrucciones sobre cómo excluir intervalos IP y puertos, consulte [esta documentación](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/). 

**Tenga en cuenta que para el complemento de AKS de Open Service Mesh, la exclusión de puertos solo se puede implementar después de la instalación mediante `kubectl patch` y no durante la instalación mediante la marca `--set` de la CLI de OSM.**

> [!NOTE]
> Si los pods de aplicación que forman parte de la malla necesitan acceso a IMDS, Azure DNS o al servidor de API de Kubernetes, el usuario debe agregar explícitamente estas direcciones IP a la lista de intervalos IP de salida globales mediante el comando anterior. Consulte un ejemplo de exclusión de puertos del servidor de API de Kubernetes [aquí](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services).
