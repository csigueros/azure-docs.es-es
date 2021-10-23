---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: bf7295f4cc7b2010e7c700fbde25840f7a7f0df1
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037132"
---
## <a name="pass-an-id-token-hint"></a>Paso de una sugerencia de token de identificador

Una aplicación de usuario de confianza puede enviar un token JSON Web Token (JWT) de entrada como parte de la solicitud de autorización de OAuth2.  El token de entrada es una sugerencia sobre el usuario o la solicitud de autorización. Azure AD B2C valida el token y, a continuación, extrae la notificación.

Para incluir una sugerencia de token de identificador en la solicitud de autenticación, haga lo siguiente: