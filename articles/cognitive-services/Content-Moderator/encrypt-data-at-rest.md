---
title: Cifrado de datos en reposo de Content Moderator
titleSuffix: Azure Cognitive Services
description: Cifrado de datos en reposo de Content Moderator.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 7beb615307b602789b845e10cee28ca402acf9e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217189"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Cifrado de datos en reposo de Content Moderator

Content Moderator cifra automáticamente sus datos cuando se guardan en la nube, lo que ayuda a cumplir los objetivos de seguridad y cumplimiento normativo de la organización.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Las claves administradas por el cliente solo están disponibles en el plan de tarifa E0. Para solicitar la capacidad de usar claves administradas por el cliente, rellene y envíe el [formulario de solicitud de claves administradas por el cliente de Content Moderator ](https://aka.ms/cogsvc-cmk). Tardará de 3 a 5 días hábiles aproximadamente en recibir una respuesta sobre el estado de la solicitud. En función de la demanda, es posible que se coloque en una cola y se apruebe a medida que haya espacio disponible. Una vez recibida la aprobación para usar CMK con el servicio Content Moderator, deberá crear un nuevo recurso de Content Moderator y seleccionar el plan de tarifa E0. Una vez creado el recurso de Content Moderator con el plan de tarifa E0, puede usar Azure Key Vault para configurar la identidad administrada.

Las claves administradas por el cliente están disponibles en todas las regiones de Azure.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Habilitación del cifrado de datos para el equipo de Content Moderator

Para habilitar el cifrado de datos para su equipo de revisión de Content Moderator, consulte [Inicio rápido: Cómo familiarizarse con Content Moderator](quick-start.md#create-a-review-team).  

> [!NOTE]
> Tendrá que proporcionar un _identificador de recurso_ con el plan de tarifa E0 de Content Moderator.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una lista completa de los servicios que admiten CMK, consulte [Claves administradas por el cliente para Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md).
* [¿Qué es Azure Key Vault?](../../key-vault/general/overview.md)
* [Formulario de solicitud de claves administradas por el cliente de Cognitive Services](https://aka.ms/cogsvc-cmk)