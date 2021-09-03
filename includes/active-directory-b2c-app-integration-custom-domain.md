---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: e6ab23e9d98d2bb1dfea21a7b4f681dbd5be6e14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803170"
---
## <a name="use-a-custom-domain"></a>Uso de un dominio personalizado

Mediante el uso de un [dominio personalizado](../articles/active-directory-b2c/custom-domain.md), puede personalizar completamente la dirección URL de autenticación. Desde el punto de vista del usuario, este permanece en su dominio durante el proceso de autenticación, en lugar de que se le redirija al nombre de dominio Azure AD B2C b2clogin.com.

También puede reemplazar el nombre del inquilino de B2C (contoso.onmicrosoft.com) en la dirección URL de la solicitud de autenticación por el GUID del identificador de inquilino a fin de quitar todas las referencias a "b2c" en la dirección URL. Por ejemplo, puede cambiar `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` por `https://account.contosobank.co.uk/<tenant ID GUID>/`.
