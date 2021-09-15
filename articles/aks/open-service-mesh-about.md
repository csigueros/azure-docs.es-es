---
title: Open Service Mesh (versión preliminar)
description: Open Service Mesh (OSM) en Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: cd1cfde1b3c1dbeda7fb112b29e8579f32bdf05a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440248"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Complemento Open Service Mesh (versión preliminar) de AKS

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) es una malla de servicio nativa ligera, extensible y en la nube que permite a los usuarios administrar de forma uniforme características de observación para entornos de microservicios muy dinámicos, así como protegerlas y disfrutar de ellas de inmediato. 

OSM ejecuta un plano de control basado en Envoy en Kubernetes, se puede configurar con API de [SMI](https://smi-spec.io/) y funciona insertando un proxy de Envoy como un contenedor sidecar junto a cada instancia de la aplicación. El proxy de Envoy contiene y ejecuta reglas en torno a las directivas de control de acceso, implementa la configuración de enrutamiento y captura las métricas. El plano de control configura continuamente los servidores proxy para asegurarse de que las directivas y las reglas de enrutamiento están actualizadas y garantiza que los servidores proxy sean correctos.

El proyecto de OSM se originó en Microsoft y, desde entonces, ha sido donado y se rige por [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/). El proyecto de código abierto de OSM seguirá siendo una colaboración dirigida por la comunidad en torno a características y funcionalidades, y se agradecen y fomentan las contribuciones al proyecto. Consulte nuestra guía de la [Escala de colaboradores](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md) para ver cómo puede participar.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Características y funcionalidades

OSM proporciona el siguiente conjunto de funcionalidades y características para proporcionar una malla de servicio nativa en la nube para los clústeres de Azure Kubernetes Service (AKS):

- OSM se ha integrado en el servicio AKS para proporcionar una experiencia de malla de servicio administrado totalmente compatible y administrada con la comodidad del complemento de la característica de AKS

- Protección de la comunicación de servicio a servicio habilitando mTLS

- Incorporación de aplicaciones a la malla con facilidad habilitando la inserción automática del sidecar del proxy de Envoy

- Configuraciones sencillas y transparentes para el desplazamiento del tráfico en las implementaciones

- Capacidad de definir y ejecutar directivas de control de acceso detalladas de los servicios

- Observabilidad e información detallada sobre las métricas de la aplicación para los servicios de depuración y supervisión

- Integración con servicios o soluciones externos de administración de certificados con una interfaz acoplable

## <a name="scenarios"></a>Escenarios

OSM puede ayudar a las implementaciones de AKS con los siguientes escenarios:

- Provisión de comunicaciones cifradas entre los puntos de conexión de servicio implementados en el clúster

- Autorización del tráfico HTTP/HTTPS y TCP en la malla

- Configuración de controles de tráfico ponderado entre dos o más servicios para implementaciones A/B o de valor controlado

- Recopilación y visualización de KPI desde el tráfico de la aplicación

## <a name="osm-service-quotas-and-limits-preview"></a>Límites y cuotas de servicio de OSM (versión preliminar)

Las limitaciones de la versión preliminar de OSM respecto a las cuotas y los límites de servicio se pueden encontrar en la [página de cuotas y límites regionales](./quotas-skus-regions.md) de AKS.

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
