---
title: Archivo de inclusión
description: archivo de inclusión
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/17/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 5c4a4ab57e2e4a63579cc753e183cd9cecc4b765
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868163"
---
Proporcionamos diferentes plantillas para abordar escenarios de incorporación específicos. Elija la opción que mejor funcione y asegúrese de modificar el archivo de parámetros para que refleje su entorno. Para más información sobre cómo usar estos archivos en su implementación, consulte [Incorporación de un cliente a Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Plantilla** | **Descripción** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Incorpore la suscripción de un cliente a Azure Lighthouse. Se debe realizar una implementación independiente para cada suscripción. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | Incorpore uno o varios de grupos de recursos de un cliente a Azure Lighthouse. Use **rgDelegatedResourceManagement** para un solo grupo de recursos o **multipleRgDelegatedResourceManagement** para incorporar varios grupos de recursos en la misma suscripción. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Si ha [publicado una oferta de servicios administrados en Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md) también puede usar esta plantilla para incorporar recursos para los clientes que han aceptado la oferta. Los valores de Marketplace en el archivo de parámetros deben coincidir con los valores que usó al publicar la oferta. |

Si desea incluir [autorizaciones elegibles](../articles/lighthouse/how-to/create-eligible-authorizations.md) (actualmente en versión preliminar pública), seleccione la plantilla correspondiente en la sección [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) de nuestro repositorio de ejemplos.

Normalmente, se requiere una implementación independiente para cada suscripción que se incorpora, pero también puede implementar plantillas en varias suscripciones.

| **Plantilla** | **Descripción** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implementa plantillas de Azure Resource Manager en varias suscripciones. |


