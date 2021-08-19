---
title: Cifrado de datos en reposo del servicio Form Recognizer
titleSuffix: Azure Applied AI Services
description: Microsoft ofrece claves de cifrado administradas por Microsoft y también le permite administrar las suscripciones de Cognitive Services con sus propias claves, llamadas claves administradas por el cliente (CMK). En este artículo se trata el cifrado de datos en reposo de Form Recognizer y cómo habilitar y administrar las CMK.
author: erindormier
manager: venkyv
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: fad7440c82ee22a370bfecc13c60f3d4d4828b07
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326505"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Cifrado de datos en reposo de Form Recognizer

Form Recognizer de Azure cifra automáticamente los datos al guardarlos en la nube. El cifrado de Form Recognizer protege los datos para ayudarlo a satisfacer los requisitos de cumplimiento normativo y de seguridad de la organización.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Las claves que administre el cliente solo serán aquellos recursos disponibles que se hayan creado después del 11 de mayo de 2020. Para usar CMK con Form Recognizer, deberá crear un nuevo recurso de Form Recognizer. Una vez creado el recurso, puede usar Azure Key Vault para configurar la identidad administrada.

[!INCLUDE [cognitive-services-cmk](../../cognitive-services/includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Formulario de solicitud de claves que administra el cliente de Form Recognizer](https://aka.ms/cogsvc-cmk)
* [Más información sobre Azure Key Vault](../../key-vault/general/overview.md)