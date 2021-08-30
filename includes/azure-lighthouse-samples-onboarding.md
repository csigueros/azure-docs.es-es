---
title: Archivo de inclusión
description: archivo de inclusión
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/26/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 3fec700c30f33d72d632c805bf2874d7d1d9dd02
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035747"
---
Proporcionamos diferentes plantillas para abordar escenarios de incorporación específicos. Elija la opción que mejor funcione y asegúrese de modificar el archivo de parámetros para que refleje su entorno. Para más información sobre cómo usar estos archivos en su implementación, consulte [Incorporación de un cliente a Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Plantilla** | **Descripción** |
|---------|---------|
| [suscripción](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/subscription) | Incorpore la suscripción de un cliente a Azure Lighthouse. Se debe realizar una implementación independiente para cada suscripción. |
| [rg y multi-rg](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | Incorpore uno o varios de grupos de recursos de un cliente a Azure Lighthouse. Use rg.js para incorporar un solo grupo de recursos o multi-rg.js para incorporar varios dentro de una suscripción. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Si ha [publicado una oferta de servicios administrados en Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md) también puede usar esta plantilla para incorporar recursos para los clientes que han aceptado la oferta. Los valores de Marketplace en el archivo de parámetros deben coincidir con los valores que usó al publicar la oferta. |

Para incluir [autorizaciones elegibles](../articles/lighthouse/how-to/create-eligible-authorizations.md) (actualmente en versión preliminar pública), seleccione la plantilla correspondiente en la sección [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) de nuestro repositorio de ejemplos.

Normalmente, se requiere una implementación independiente para cada suscripción que se incorpora, pero también puede implementar plantillas en varias suscripciones.

| **Plantilla** | **Descripción** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implementa plantillas de Azure Resource Manager en varias suscripciones. |


