---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 8547dac26c8d3a6a286a49a6b6e12919199a994b
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220318"
---
## <a name="use-a-custom-domain"></a>Uso de un dominio personalizado

Mediante el uso de un [dominio personalizado](../articles/active-directory-b2c/custom-domain.md), puede personalizar completamente la dirección URL de autenticación. Desde el punto de vista del usuario, este permanece en el dominio durante el proceso de autenticación, en lugar de que se le redirija al nombre de dominio b2clogin.com de Azure AD B2C.

También puede reemplazar el nombre del inquilino de B2C (contoso.onmicrosoft.com) en la dirección URL de la solicitud de autenticación por el GUID del identificador de inquilino para quitar todas las referencias a "b2c" en la dirección URL. Por ejemplo, puede cambiar `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` por `https://account.contosobank.co.uk/<tenant ID GUID>/`.
