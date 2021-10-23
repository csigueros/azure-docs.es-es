---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 33b42af7804c2c48a0112e0f8eb502c253813f47
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037135"
---
## <a name="use-a-custom-domain"></a>Uso de un dominio personalizado

Mediante el uso de un [dominio personalizado](../articles/active-directory-b2c/custom-domain.md), puede personalizar completamente la dirección URL de autenticación. Desde el punto de vista del usuario, este permanece en el dominio durante el proceso de autenticación, en lugar de que se le redirija al nombre de dominio b2clogin.com de Azure AD B2C.

También puede reemplazar el nombre del inquilino de B2C (contoso.onmicrosoft.com) en la dirección URL de la solicitud de autenticación por el GUID del identificador de inquilino para quitar todas las referencias a "b2c" en la dirección URL. Por ejemplo, puede cambiar `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` por `https://account.contosobank.co.uk/<tenant ID GUID>/`.
