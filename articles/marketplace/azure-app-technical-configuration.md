---
title: Incorporación de detalles técnicos para una oferta de aplicación de Azure
description: Agregue detalles técnicos para una oferta de aplicación de Azure en el Centro de partners (Azure Marketplace).
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 373dd2c3f0b107b0a910af138570c9cf1782e68c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542814"
---
# <a name="add-technical-details-for-an-azure-application-offer"></a>Incorporación de detalles técnicos para una oferta de aplicación de Azure

En este artículo se describe cómo especificar detalles técnicos que ayuden al marketplace comercial de Microsoft a conectarse a su solución. Esta conexión nos permite aprovisionar su oferta para el cliente, en caso de que este elija adquirirla y administrarla.

Complete esta sección solo si la oferta incluye una aplicación administrada que emitirá eventos de medición con las [API de facturación de uso medido de Marketplace](marketplace-metering-service-apis.md), y tiene un servicio que se autenticará con un token de seguridad de Azure AD. Para más información, consulte [Estrategias de autenticación del servicio de medición de Marketplace](marketplace-metering-service-authentication.md) sobre las distintas opciones de autenticación.

## <a name="technical-configuration-offer-level"></a>Configuración técnica (nivel de oferta)

La pestaña **Technical configuration** (Configuración técnica) solo se aplica si se va a crear una aplicación administrada que emite eventos de medición mediante las [API de facturación de uso medido de Marketplace](marketplace-metering-service-apis.md). Si este es el caso, realice los siguientes pasos. Si no, vaya a [Pasos siguientes](#next-steps). 

Para más información sobre estos campos, consulte [Planeamiento de una oferta de Aplicación de Azure para el marketplace comercial](plan-azure-application-offer.md#technical-configuration).

1. En la pestaña **Technical configuration** (Configuración técnica), proporcione el **identificador de inquilino de Azure Active Directory** y el **identificador de aplicación de Azure Active Directory** usados para validar que la conexión entre los dos servicios está detrás de una comunicación autenticada.

1. Seleccione **Guardar borrador** antes de continuar con la siguiente pestaña: Información general del plan.

## <a name="next-steps"></a>Pasos siguientes

- [Crear planes para esta oferta](azure-app-plans.md)
