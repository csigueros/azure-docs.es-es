---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: eric-urban
ms.openlocfilehash: 7600bae5191c5230764b4f75cb93f20049c4b963
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506865"
---
Una de las características principales del servicio de voz es la capacidad para reconocer la voz humana y traducirla a otros idiomas. En este inicio rápido, aprenderá a usar Speech SDK en sus aplicaciones y productos para realizar la traducción de voz de alta calidad. Este inicio rápido traduce la voz que sale por el micrófono en texto en otro idioma.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por sentado que tiene una cuenta de Azure y una suscripción al servicio de voz. Si no dispone de una cuenta y una suscripción, [pruebe el servicio de voz de forma gratuita](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Establecimiento del idioma de origen y destino

Este comando llama a la CLI de voz para traducir la voz del micrófono de italiano a francés.

```shell
 spx translate --microphone --source it-IT --target fr
```
