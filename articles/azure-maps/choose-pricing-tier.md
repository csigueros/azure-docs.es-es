---
title: Elección del plan de tarifa adecuado para Microsoft Azure Maps
description: Obtenga información sobre los planes de tarifa de Azure Maps. Vea qué características se ofrecen en qué niveles y consulte las consideraciones clave para elegir un plan de tarifa.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/04/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: a0035fd022945b4d073e40f3114a37356a61ed03
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846489"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Elección del plan de tarifa adecuado de Azure Maps

Azure Maps ahora ofrece dos planes de tarifa: Gen 1 y Gen 2. El nuevo plan de tarifa Gen 2 contiene todas las funcionalidades de Azure Maps con mayores límites de QPS (consultas por segundo) que Gen 1 y le permite ahorrar costos a medida que aumentan las transacciones de Azure Maps. El propósito de este artículo es ayudarle a elegir el plan de tarifa adecuado para satisfacer sus necesidades.

## <a name="pricing-tier-targeted-customers"></a>Clientes objetivo de los planes de tarifa

Consulte la **tabla de clientes objetivo de los planes de tarifa** a continuación para obtener una mejor comprensión de los planes de tarifa de Gen 1 y Gen 2.  Para más información, consulte [Precios de Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). Si ya es cliente de Azure Maps, puede aprender a cambiar los precios de Gen 1 a Gen 2 en el artículo [Administración del plan de tarifa](how-to-manage-pricing-tier.md).

| Plan de tarifa  | SKU | Clientes objetivo|
|---------------|:---:| ------------------|
|**Gen 1**|S0| El plan de tarifa S0 funciona para las aplicaciones en todas las fases de producción, desde el desarrollo de la prueba de concepto y la primera fase de pruebas hasta la puesta en producción e implementación de la aplicación. Sin embargo, este nivel está diseñado para el desarrollo a pequeña escala, para clientes con pocos usuarios simultáneos o ambos. S0 tiene una restricción de 50 QPS para todos los servicios combinados.
|         |S1| El plan de tarifa S1 está orientado a clientes con aplicaciones empresariales a gran escala, aplicaciones críticas para la empresa o grandes volúmenes de usuarios simultáneos. También es para aquellos clientes que requieren servicios geoespaciales avanzados.
| **Gen 2** | Maps/Location Insights | Los precios de Gen 2 son para clientes nuevos y actuales de Azure Maps. Gen 2 incluye un nivel mensual gratuito de transacciones que se usará para probar y compilar en Azure Maps. Las SKU de Maps y Location Insights contienen todas las funcionalidades de Azure Maps. Le permite lograr ahorros de costos a medida que aumentan las transacciones de Azure Maps. Además, tiene límites de QPS más altos que Gen 1.  
|     |  |

Para obtener más información sobre los límites de QPS, consulte [Límites de frecuencia de QPS de Azure Maps](azure-maps-qps-rate-limits.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo ver y cambiar los planes de tarifa:

> [!div class="nextstepaction"]
> [Administración de un plan de tarifa](how-to-manage-pricing-tier.md)
