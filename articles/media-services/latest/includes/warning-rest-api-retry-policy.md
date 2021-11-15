---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: johndeu
ms.custom: sdk
ms.openlocfilehash: d22e280ac714df4dd0f86a50e44aad1166f084ee
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438365"
---
> [!WARNING]
> No se recomienda intentar encapsular la API REST de Media Services directamente en su propio código de biblioteca, ya que hacerlo correctamente con fines de producción requeriría la implementación de la lógica de reintentos de Azure Resource Manager completa y comprender cómo administrar operaciones de larga duración en las API de Azure Resource Manager. Esto se controla automáticamente mediante los SDK de cliente de varios lenguajes (.NET, Java, TypeScript, Python, Ruby, etc.) y reduce las posibilidades de que tenga problemas con la lógica de reintentos o las llamadas API con errores. Los SDK de cliente lo controlan todo automáticamente. La colección de Postman se proporciona más como una herramienta de enseñanza y para mostrar lo que los SDK de cliente hacen realmente en la conexión durante el desarrollo con los distintos SDK de cliente.
