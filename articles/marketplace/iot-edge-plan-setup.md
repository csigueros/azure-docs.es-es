---
title: Configuración de planes para una oferta de módulo IoT Edge en Azure Marketplace
description: Configure planes para una oferta de módulo IoT Edge en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: f0b6ac3f704adb0070b6cb817da47174b44c8dfa
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111539927"
---
# <a name="set-up-plans-for-an-iot-edge-module-offer"></a>Configuración de planes para una oferta de módulo IoT Edge

La página **Configuración del plan** le permite configurar las nubes en las que el plan está disponible. Sus respuestas de esta pestaña afectan a los campos que se muestran en otras pestañas.

## <a name="azure-regions"></a>Regiones de Azure

Realice su selección en Azure global o Azure Government.

### <a name="azure-global"></a>Azure Global

Todos los planes de las ofertas del módulo de IoT Edge están disponibles automáticamente en **Azure global**.  Los clientes pueden usar su plan en todas las regiones globales de Azure que usan el marketplace. Para obtener más información, consulte [Disponibilidad geográfica y soporte técnico de moneda](marketplace-geo-availability-currencies.md).

### <a name="azure-government"></a>Azure Government

Seleccione **[Azure Government](../azure-government/documentation-government-welcome.md)** para que la oferta aparezca ahí. Es una nube de la comunidad gubernamental con acceso controlado para los clientes de agencias gubernamentales tribales, locales, estatales o federales de Estados Unidos, así como para los asociados aptos para abastecerlos. Como publicador, es responsable de los controles de cumplimiento, las medidas de seguridad y los procedimientos recomendados para esta comunidad en la nube. Azure Government usa redes y centros de datos aislados físicamente (ubicados solo en Estados Unidos). Antes de la [publicación](../azure-government/documentation-government-manage-marketplace-partners.md) en Azure Government, pruebe y valide la solución en esa área, ya que los resultados pueden ser diferentes. Para almacenar y realizar una copia intermedia de la solución, solicite una cuenta de prueba desde [Prueba de Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Una vez que el plan esté publicado y disponible en una región específica, no se puede quitar esa región.

#### <a name="azure-government-certifications"></a>Certificaciones de Azure Government

Si selecciona **Azure Government**, agregue las **certificaciones**. Los servicios de Azure Government controlan datos que están sujetos a determinados reglamentos y requisitos gubernamentales. Por ejemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 y CJIS. Para dar a conocer sus certificaciones de estos programas, puede proporcionar hasta 100 vínculos que las describan. Pueden ser vínculos a sus listas en el programa directamente o a su propio sitio web. Estos vínculos solo son visibles para los clientes de Azure Government.

Seleccione **Guardar borrador** antes de pasar a la pestaña siguiente del menú de navegación de la izquierda **Información general del plan**, **Lista del plan**.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la lista del plan](iot-edge-plan-listing.md)
