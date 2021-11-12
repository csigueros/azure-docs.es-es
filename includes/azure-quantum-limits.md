---
title: Incluir archivo
description: Incluir archivo
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 10/27/2021
ms.author: dasto
ms.openlocfilehash: 1811faeeb5de3e300a35f7107c5cdf23bf20151c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520546"
---
### <a name="provider-limits--quota"></a>Límites y cuotas de proveedor

El servicio Azure Quantum admite proveedores de servicios tanto propios como de terceros. Los proveedores de terceros poseen sus límites y cuotas. Los usuarios pueden ver las ofertas y los límites de Azure Portal al configurar proveedores de terceros. 

A continuación puede encontrar los límites de cuota publicados para el proveedor de soluciones de optimización propio de Microsoft. 

#### <a name="learn--develop-sku"></a>SKU Aprender y desarrollar

| Resource | Límite |
| --- | --- |
| Trabajos simultáneos basados en CPU | Hasta 5<sup>1</sup> trabajos simultáneos |
| Trabajos simultáneos basados en FPGA | Hasta 2<sup>1</sup> trabajos simultáneos |
| Horas de solucionador basadas en CPU | Veinte horas al mes  |
| Horas de solucionador basadas en FPGA | Una hora al mes  |

Aunque en la SKU Aprender y desarrollar, **no puede**  solicitar un aumento de los límites de cuota. En su lugar, debe cambiar a la SKU Rendimiento a escala.

#### <a name="performance-at-scale-sku"></a>SKU Rendimiento a escala

| Resource | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| Trabajos simultáneos basados en CPU | Hasta 100<sup>1</sup> trabajos simultáneos | Igual que el límite predeterminado |
| Trabajos simultáneos basados en FPGA | Hasta 10<sup>1</sup> trabajos simultáneos | Igual que el límite predeterminado |
| Horas de solucionador | 1000 horas al mes  | Hasta 50 000 horas al mes |

Póngase en contacto con el Soporte técnico de Azure para solicitar un aumento del límite.

Para más información, consulte la [página de precios de Azure Quantum](https://aka.ms/AQ/Pricing).
Revise las páginas de precios pertinentes de proveedores de Azure Portal para obtener más información sobre las ofertas de terceros.

<sup>1</sup> Describe el número de trabajos que se pueden poner en cola al mismo tiempo.
