---
title: Archivo de inclusión
description: archivo de inclusión
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 928c4669ec7b1a04874f760cc14234cfa874e99a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437904"
---
| Recurso                                                                                                           | Límite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Número máximo de clústeres por suscripción                                                                                  | 5000                                                                                                                                                                                                        |
| Número máximo de nodos por clúster con los conjuntos de disponibilidad de máquina virtual y el SKU básico de Load Balancer                       | 100                                                                                                                                                                                                         |
| Número máximo de nodos por clúster con Virtual Machine Scale Sets y el [SKU estándar de Load Balancer][standard-load-balancer] | 1000 (entre todos los [grupos de nodos][node-pool])                                            |
| Número máximo de grupos de nodos por clúster                                                                                     | 100                                                                                  |
| Número máximo de pods por nodo: [redes básicas][basic-networking] con Kubenet                                           | Máximo: 250 <br /> Valor predeterminado de la CLI de Azure: 110 <br /> Valor predeterminado de la plantilla de Azure Resource Manager: 110 <br /> Valor predeterminado de la implementación de Azure Portal: 30          |
| Número máximo de pods por nodo: [redes avanzadas][advanced-networking] con Azure Container Networking Interface        | Máximo: 250 <br /> Valor predeterminado: 30                                                      |
| Versión preliminar del complemento Open Service Mesh (OSM) AKS                                                                          | Versión del clúster de Kubernetes: 1.19+<sup>1</sup><br />Controladores OSM por clúster: 1<sup>1</sup><br />Pods por controlador OSM: 500<sup>1</sup><br />Cuentas de servicio de Kubernetes administradas por OSM: 50<sup>1</sup> |

<sup>1</sup> El complemento OSM para AKS se encuentra en versión preliminar y se someterá a mejoras adicionales antes de su disponibilidad con carácter general. Durante la fase de versión preliminar, se recomienda no superar los límites indicados.<br />

<br />

| Límites del plano de control de Kubernetes                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nivel de pago                                                                                  | Se escala horizontalmente automáticamente en función de la carga                                                                                                                                                                                                        |
| Nivel gratis                       | Recursos limitados con un [límite de solicitudes en proceso](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) de 50 de mutación y de 100 llamadas de solo lectura                                                                                                                                                                                                         |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
