---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 3d4bb5ff840a2f4ee5c4c33e2cc51dfe440866a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779875"
---
Una vez completada la autenticación, los usuarios interactúan con la aplicación, que invoca una API web protegida. La API web usa la autenticación de [token de portador](https://datatracker.ietf.org/doc/html/rfc6750). El token de portador es el token de acceso que la aplicación ha obtenido de Azure Active Directory B2C. La aplicación pasa el token en el encabezado de autorización de la solicitud HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si el ámbito del token de acceso no coincide con los ámbitos de la API web, la biblioteca de autenticación obtiene un nuevo token de acceso con los ámbitos correctos.
