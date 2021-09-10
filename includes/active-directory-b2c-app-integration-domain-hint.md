---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: bade30eb42235e65170fc59ad77fa2f4ce1114c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723215"
---
## <a name="preselect-an-identity-provider"></a>Preseleccione un proveedor de identidad

Si se ha configurado el recorrido de inicio de sesión para que la aplicación incluya cuentas de redes sociales como Facebook, LinkedIn o Google, puede especificar el parámetro `domain_hint`. Este parámetro de consulta proporciona una sugerencia a Azure AD B2C acerca del proveedor de identidades sociales que debe usarse para iniciar sesión. Por ejemplo, si la aplicación especifica `domain_hint=facebook.com`, el flujo de inicio de sesión va directamente a la página de inicio de sesión de Facebook. 

Para redirigir a los usuarios a un proveedor de identidades externo, haga lo siguiente:
