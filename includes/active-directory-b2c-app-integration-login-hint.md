---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: c2409ef7d846ff7d4d67d7bf79ab96760375da81
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037119"
---
## <a name="prepopulate-the-sign-in-name"></a>Rellenar previamente el nombre de inicio de sesión

Durante el recorrido de inicio de sesión de un usuario, la aplicación podría tener como destino un usuario específico. Cuando una aplicación se dirige a un usuario, puede especificar, en la solicitud de autorización, el parámetro de consulta `login_hint` con el nombre de inicio de sesión de usuario. Azure AD B2C rellena automáticamente el nombre de inicio de sesión y solo es necesario que el usuario proporcione la contraseña. 

Para rellenar previamente el nombre de inicio de sesión, haga lo siguiente: