---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 9de7912b426d659a03e8a7653c690cb16f83010e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007636"
---
Una vez completada la autenticación, los usuarios interactúan con la aplicación, que invoca una API web protegida. La API web usa la autenticación de [token de portador](https://datatracker.ietf.org/doc/html/rfc6750). El token de portador es el token de acceso que la aplicación ha obtenido de Azure Active Directory B2C. La aplicación pasa el token en el encabezado de autorización de la solicitud HTTPS. 
    
```http
Authorization: Bearer <token>
```

Si el ámbito del token de acceso no coincide con los ámbitos de la API web, la biblioteca de autenticación obtiene un nuevo token de acceso con los ámbitos correctos.
