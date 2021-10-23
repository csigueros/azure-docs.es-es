---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 8363f7d13c2a991df031e3a2ca53efb8c1b11761
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037142"
---
## <a name="preselect-an-identity-provider"></a>Preseleccione un proveedor de identidad

Si se ha configurado el recorrido de inicio de sesión para que la aplicación incluya cuentas de redes sociales como Facebook, LinkedIn o Google, puede especificar el parámetro `domain_hint`. Este parámetro de consulta proporciona una sugerencia a Azure AD B2C acerca del proveedor de identidades sociales que debe usarse para iniciar sesión. Por ejemplo, si la aplicación especifica `domain_hint=facebook.com`, el flujo de inicio de sesión va directamente a la página de inicio de sesión de Facebook. 

Para redirigir a los usuarios a un proveedor de identidades externo, haga lo siguiente:
