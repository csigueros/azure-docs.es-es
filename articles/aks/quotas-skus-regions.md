---
title: Límites de recursos, SKU, regiones
titleSuffix: Azure Kubernetes Service
description: Obtenga información sobre las cuotas predeterminadas, los tamaños de SKU de VM del nodo restringido y la disponibilidad de la región de Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: ea32c0e58894fe57a51cd22fccce8b2fb2fb0489
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762307"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Cuotas, restricciones de tamaño de máquinas virtuales y disponibilidad de regiones en Azure Kubernetes Service (AKS)

Todos los servicios de Azure establecen límites y cuotas predeterminados para recursos y características, incluidas restricciones de uso para determinadas SKU de máquina virtual (VM).

En este artículo se detallan los límites de recursos predeterminados para los recursos de Azure Kubernetes Service (AKS) y la disponibilidad de AKS en las regiones de Azure.

## <a name="service-quotas-and-limits"></a>Límites y cuotas del servicio

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestructura aprovisionada

A la infraestructura aprovisionada se le aplican las demás limitaciones de red, proceso y almacenamiento. Consulte [límites de suscripción y servicios de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) para conocer los límites pertinentes.

> [!IMPORTANT]
> Al actualizar un clúster de AKS, se consumen recursos adicionales temporalmente. Estos recursos incluyen las direcciones IP disponibles en una subred de red virtual o una cuota de vCPU de máquina virtual. 
>
> En el caso de los contenedores de Windows Server, puede realizar una operación de actualización para aplicar las actualizaciones de nodo más recientes. Si no tiene el espacio de direcciones IP disponible o la cuota de vCPU necesaria para administrar estos recursos temporales, se producirá un error en el proceso de actualización del clúster. Para obtener más información sobre el proceso de actualización del nodo de Windows Server, consulte [Actualización de un grupo de nodos en AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tamaños de VM restringidos

Cada nodo en un clúster de AKS contiene una cantidad fija de recursos de proceso, como la vCPU y la memoria. Si un nodo de AKS no tiene suficientes recursos de proceso, es posible que los pods no se ejecuten correctamente. Para asegurarse de que los pods de *kube-system*  necesarios y las aplicaciones puedan programarse de manera confiable, **no use las siguientes SKU de máquina virtual en AKS**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1ls
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s
- Standard_A2
- Standard_D1
- Standard_D1_v2
- Standard_DS1
- Standard_DS1_v2

Para obtener más información sobre los tipos de VM y sus recursos de proceso, consulte [Tamaños de las máquinas virtuales en Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidad en regiones

Para obtener la lista más reciente con los lugares donde puede implementar y ejecutar clústeres, consulte [Disponibilidad de regiones de AKS][region-availability].

## <a name="cluster-configuration-presets-in-the-azure-portal"></a>Valores preestablecidos de configuración del clúster en Azure Portal

Al crear un clúster mediante Azure Portal, puede elegir una configuración preestablecida para personalizarlo rápidamente en función de su escenario. Puede modificar cualquiera de los valores preestablecidos en cualquier momento.

| Valor preestablecido           | Descripción                                                            |
|------------------|------------------------------------------------------------------------|
| Estándar         | Mejor si no está seguro de qué elegir. Funciona bien con la mayoría de las aplicaciones. |
| Desarrollo/pruebas         | Lo mejor es experimentar con AKS o implementar una aplicación de prueba. |
| Optimizado para costos   | Lo mejor es reducir los costos de las cargas de trabajo de producción que pueden tolerar interrupciones. |
| Procesamiento por lotes | Lo mejor para cargas de trabajo de aprendizaje automático, proceso intensivo y uso intensivo de gráficos. Adecuado para aplicaciones que requieren escalabilidad horizontal y vertical rápida del clúster. |
| Acceso protegido  | La mejor opción para grandes empresas que necesitan un control total de la seguridad y la estabilidad. |

## <a name="next-steps"></a>Pasos siguientes

Puede aumentar ciertas cuotas y límites predeterminados. Si su recurso admite un aumento, solicítelo mediante una [solicitud de soporte técnico de Azure][azure-support] (en **Tipo de problema**, seleccione **Cuota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
