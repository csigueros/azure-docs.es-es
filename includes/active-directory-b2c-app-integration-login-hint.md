---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 9fb96a0427ecaa3431eddb47ca89458a6dcf5901
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723214"
---
## <a name="prepopulate-the-sign-in-name"></a>Rellenar previamente el nombre de inicio de sesión

Durante el recorrido de inicio de sesión de un usuario, la aplicación podría tener como destino un usuario específico. Cuando una aplicación se dirige a un usuario, puede especificar, en la solicitud de autorización, el parámetro de consulta `login_hint` con el nombre de inicio de sesión de usuario. Azure AD B2C rellena automáticamente el nombre de inicio de sesión y solo es necesario que el usuario proporcione la contraseña. 

Para rellenar previamente el nombre de inicio de sesión, haga lo siguiente: