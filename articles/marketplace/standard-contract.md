---
title: Contrato estándar para el Marketplace comercial de Microsoft
description: Contrato estándar para Azure Marketplace y AppSource en el Centro de partners
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/26/2021
ms.openlocfilehash: 4313a9a6478c38ccb6611ea7c0008bd7fa9ebcbf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848256"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contrato estándar para el Marketplace comercial de Microsoft

Microsoft ofrece un Contrato estándar para el Marketplace comercial de Microsoft. Esto ayuda a simplificar el proceso de adquisición de los clientes, reducir la complejidad legal de los proveedores de software y facilitar las transacciones en Marketplace. En lugar de crear términos y condiciones personalizados, los anunciantes del Marketplace comercial pueden ofrecer su software en virtud del [Contrato estándar](https://go.microsoft.com/fwlink/?linkid=2041178), que los clientes solo tienen que revisar y aceptar una vez.

Los términos y condiciones de una oferta se definen al crearla en el Centro de partners. Puede optar por usar el Contrato estándar para el Marketplace comercial de Microsoft en lugar de proporcionar sus propios términos y condiciones personalizados.

>[!Note]
>Después de publicar una oferta con el Contrato estándar para el Marketplace comercial de Microsoft, no podrá usar sus propios términos y condiciones personalizados. Puede ofrecer su solución con el Contrato estándar *o* con sus propios términos y condiciones. Los términos y condiciones personalizados se definen en el nivel de oferta y se aplican a todos los planes; escriba los términos y condiciones personalizados en la página **Propiedades** de la oferta en el Centro de partners. Si quiere modificar los términos del contrato estándar, puede hacerlo mediante las Modificaciones del Contrato estándar.

> [!TIP]
> Para obtener la vista del cliente de los contratos legales en Azure Marketplace, consulte [Contratos legales](/marketplace/legal-contracts).

## <a name="standard-contract-amendments"></a>Modificaciones del Contrato estándar

Las modificaciones del Contrato estándar permiten a los anunciantes seleccionar el Contrato estándar por motivos de simplicidad y personalizar los términos para su producto o empresa. Los clientes solo necesitan revisar las modificaciones del contrato, si ya han revisado y aceptado el Contrato estándar de Microsoft.

Hay dos tipos de modificaciones para los anunciantes de Marketplace comercial:

* Modificaciones universales: Estas modificaciones se aplican universalmente al Contrato estándar para todos los clientes. Las modificaciones universales se muestran a cada cliente de la oferta en el flujo de compra. Los clientes deben aceptar los términos del Contrato estándar y la modificación antes de que puedan usar su oferta.

* Modificaciones personalizadas: Estas son modificaciones especiales al Contrato estándar que están dirigidas a cliente únicamente a clientes específicos a través de los Id. de inquilino de Azure. Los anunciantes pueden elegir al inquilino al que desean dirigirse. Solo los clientes del inquilino recibirán los términos con modificaciones personalizadas en el flujo de compra de la oferta.  Los clientes deben aceptar los términos del Contrato estándar y las modificaciones antes de que puedan usar su oferta.

>[!Note]
>Estos dos tipos de modificaciones se superponen entre sí. Los clientes específicos con modificaciones personalizadas también obtendrán la modificación universal al Contrato estándar durante la compra. Las modificaciones se limitan a 4000 caracteres, incluidos los espacios.

Puede aprovechar el Contrato estándar del marketplace comercial de Microsoft para los siguientes tipos de oferta: aplicaciones de Azure (plantillas de soluciones y aplicaciones administradas), contenedores de Azure, aplicaciones de contenedor, máquinas virtuales y SaaS.

## <a name="customer-experience"></a>Experiencia del cliente

Durante la experiencia de búsqueda en Azure Marketplace o AppSource, los clientes podrán ver los términos asociados con la oferta en la forma de un Contrato estándar para el Marketplace comercial de Microsoft y todas las modificaciones universales.

![Experiencia de búsqueda del cliente de Azure Portal.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante el proceso de compra en Azure Portal, los clientes podrán ver los términos asociados con la oferta en la forma de un Contrato estándar para el Marketplace comercial de Microsoft y todas las modificaciones universales o específicas de un inquilino.

![Experiencia de compra para el cliente de Azure Portal.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Los clientes pueden usar Get-AzureRmMarketplaceTerms para recuperar los términos de una oferta y aceptarlos. El Contrato estándar y las modificaciones asociadas se devolverán en la salida del cmdlet.
