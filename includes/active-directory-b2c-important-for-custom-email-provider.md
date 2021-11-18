---
author: kengaderdus
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 18ce90a5c108dc6ff1b6a903e6fe113d33ad4797
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179523"
---
> [!IMPORTANT]
> En los pasos siguientes se muestra cómo compilar los archivos XML de directiva personalizados. Se recomienda usar una directiva personalizada de **[verificación de correo electrónico personalizado de ejemplo](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol/policy)** disponible en GitHub. `DisplayControl_TrustFrameworkExtensions.xml` usa `TrustFrameworkExtensions.xml` como su archivo base, por lo tanto, asegúrese de incluir los archivos `TrustFrameworkBase.xml`, `TrustFrameworkLocalization.xml` y `TrustFrameworkExtensions.xml` desde el [paquete de inicio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) **SocialAndLocalAccounts** en la directiva.