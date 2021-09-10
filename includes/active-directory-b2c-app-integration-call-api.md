---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: ebc113e993eadc41c2b1c58c9130908727101a6f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227785"
---
Una vez completada la autenticación, los usuarios interactúan con la aplicación, que invoca una API web protegida. La API web usa la autenticación de [token de portador](https://datatracker.ietf.org/doc/html/rfc6750). El token de portador es el token de acceso que la aplicación ha obtenido de Azure Active Directory B2C. La aplicación pasa el token en el encabezado de autorización de la solicitud HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si el ámbito del token de acceso no coincide con los ámbitos de la API web, la biblioteca de autenticación obtiene un nuevo token de acceso con los ámbitos correctos.
