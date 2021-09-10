---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f66eac86f4267a7b824eb551cdd877d3d34472a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723211"
---
## <a name="pass-an-id-token-hint"></a>Paso de una sugerencia de token de identificador

Una aplicación de usuario de confianza puede enviar un token JSON Web Token (JWT) de entrada como parte de la solicitud de autorización de OAuth2.  El token de entrada es una sugerencia sobre el usuario o la solicitud de autorización. Azure AD B2C valida el token y, a continuación, extrae la notificación.

Para incluir una sugerencia de token de identificador en la solicitud de autenticación, haga lo siguiente: